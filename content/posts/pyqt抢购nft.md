---
title: pyqt抢购nft
date: 2022-06-19
tags: ["gui"]
---

# pyqt抢购nft

core

```

# -*- coding: utf-8 -*-
import multiprocessing
import sys
import time
from PyQt5.QtCore import QThread, pyqtSignal
from PyQt5.QtWidgets import QApplication, QMainWindow
from qiankunui import Ui_MainWindow
import requests
import cgitb
from datetime import datetime
from apscheduler.schedulers.blocking import BlockingScheduler
from apscheduler.schedulers.qt import QtScheduler
from itertools import cycle

from requests.packages import urllib3

urllib3.disable_warnings()
cgitb.enable(format='text')


class MyMainForm(QMainWindow, Ui_MainWindow):
    def __init__(self, parent=None):
        super(MyMainForm, self).__init__(parent)
        self.setupUi(self)
        # 2.手动多线程发包
        self.work = WorkThread(self)
        self.start.clicked.connect(self.execute)
        self.stop.clicked.connect(self.stoping)

        # 1.登录
        self.ui_login = Login(self)
        self.login.clicked.connect(self.start_login)

        # 3.auto
        self.auto_buy.clicked.connect(self.auto_buy1)

    def auto_buy1(self):
        self.work.auto_buy()
        self.work.trigger.connect(self.display)

    def start_login(self):
        self.ui_login.start()
        self.ui_login.trigger.connect(self.display)

    def stoping(self):
        self.work.stop()
        self.work.trigger.connect(self.display)

    def execute(self):
        # 启动线程
        self.work.start()
        # 线程自定义信号连接的槽函数
        self.work.trigger.connect(self.display)

    def display(self, str):
        # 由于自定义信号时自动传递一个字符串参数，所以在这个槽函数中要接受一个参数
        self.listWidget.addItem(str)


class Login(QThread):
    trigger = pyqtSignal(str)

    def __init__(self, demo):
        super(Login, self).__init__()
        self.demo = demo

    def run(self):
        username = self.demo.username.text().strip()
        password = self.demo.password.text().strip()

        try:
            burp0_url = "https://x.com"
            burp0_headers = {"Sec-Ch-Ua": "\"(Not(A:Brand\";v=\"8\", \"Chromium\";v=\"101\"", "Sid": "35001800000",
                             "Sec-Ch-Ua-Mobile": "?0",
                             "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.54 Safari/537.36",
                             "Content-Type": "application/x-www-form-urlencoded", "Newversion": "H5_1.0",
                             "Sec-Ch-Ua-Platform": "\"Windows\"", "Source": "218", "Accept": "*/*",
                             "Origin": "http://nt.fengkuangtiyu.cn", "Sec-Fetch-Site": "cross-site",
                             "Sec-Fetch-Mode": "cors", "Sec-Fetch-Dest": "empty",
                             "Referer": "http://nt.fengkuangtiyu.cn/",
                             "Accept-Encoding": "gzip, deflate", "Accept-Language": "zh-CN,zh;q=0.9",
                             "Connection": "close"}
            burp0_data = {"phone": f"{username}", "loginPwd": f"{password}"}
            res = requests.post(burp0_url, headers=burp0_headers, data=burp0_data, verify=False)
        except Exception as e:
            print(e)

        res_json = res.json()
        print(res_json["data"]["loginSign"])

        self.trigger.emit(res.text)
        self.demo.token.setText(res_json["data"]["loginSign"])
        self.demo.userid.setText(res_json["data"]["userId"])


class WorkThread(QThread):
    # 自定义信号对象。参数str就代表这个信号可以传一个字符串
    trigger = pyqtSignal(str)

    def __init__(self, demo):  # 3
        super(WorkThread, self).__init__()
        self.demo = demo
        self.pool = None

    def callback(self, x):
        self.trigger.emit(x)

    # 主要启动函数
    def run(self):
        # 重写线程执行的run函数
        # 触发自定义信号

        userid = self.demo.userid.text().strip()
        token = self.demo.token.text().strip()
        goodsid = self.demo.goodsid.text().strip()
        issueid = self.demo.issueid.text().strip()
        _proxy = self.demo.proxy_pool.text().strip()
        try:
            res = requests.get(_proxy)
            self.ips = res.text.splitlines()

        except Exception as e:
            print(e)

        self.pool = multiprocessing.Pool()
        for ip in cycle(self.ips):
            self.pool.apply_async(self.work, (userid, token, goodsid, issueid, ip), error_callback=self.callback,
                                  callback=self.callback)
        self.pool.close()
        self.pool.join()

    @staticmethod
    def work(userid, token, goodsid, issueid, ip=None):
        proxies = {"http": ip, "https": ip}
        burp0_url = "https://x"
        burp0_headers = {"Sec-Ch-Ua": "\"(Not(A:Brand\";v=\"8\", \"Chromium\";v=\"101\"", "Sid": "35001800000",
                         "Sec-Ch-Ua-Mobile": "?0",
                         "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.54 Safari/537.36",
                         "Content-Type": "application/x-www-form-urlencoded", "Newversion": "H5_1.0",
                         "Loginsign": f"{token}", "Sec-Ch-Ua-Platform": "\"Windows\"",
                         "Source": "218", "Accept": "*/*", "Origin": "http://nt.fengkuangtiyu.cn",
                         "Sec-Fetch-Site": "cross-site", "Sec-Fetch-Mode": "cors", "Sec-Fetch-Dest": "empty",
                         "Referer": "http://nt.fengkuangtiyu.cn/", "Accept-Encoding": "gzip, deflate",
                         "Accept-Language": "zh-CN,zh;q=0.9", "Connection": "close"}
        burp0_data = {"userId": f"{userid}", "goodsId": f"{goodsid}", "issueId": f"{issueid}"}
        try:
            res = requests.post(burp0_url, headers=burp0_headers, data=burp0_data, proxies=proxies)
        except:
            pass
        time.sleep(0.1)
        # 通过自定义信号把待显示的字符串传递给槽函数
        print(res.text)
        return res.text

    # 停止
    def stop(self):
        print('stop')
        self.pool.terminate()

        self.is_running = False
        self.terminate()
        self.trigger.emit("stop")

    # 定时抢购
    def auto_buy(self):
        ss = self.demo.ss.text().strip()
        y = self.demo.year.text().strip()
        mon = self.demo.mon.text().strip()
        days = self.demo.days.text().strip()
        hours = self.demo.hours.text().strip()
        mins = self.demo.mins.text().strip()

        scheduler = QtScheduler()
        scheduler.add_job(self.run, 'date',
                          run_date=datetime(int(y), int(mon), int(days), int(hours), int(mins), int(ss)),
                          )

        scheduler.start()


if __name__ == "__main__":
    app = QApplication(sys.argv)
    myWin = MyMainForm()
    myWin.show()
    sys.exit(app.exec_())

```

ui

```
# -*- coding: utf-8 -*-


from PyQt5 import QtCore, QtGui, QtWidgets


class Ui_MainWindow(object):
    def setupUi(self, MainWindow):
        MainWindow.setObjectName("MainWindow")
        MainWindow.resize(754, 415)
        self.centralwidget = QtWidgets.QWidget(MainWindow)
        self.centralwidget.setObjectName("centralwidget")
        self.listWidget = QtWidgets.QListWidget(self.centralwidget)
        self.listWidget.setGeometry(QtCore.QRect(340, 10, 371, 311))
        self.listWidget.setObjectName("listWidget")
        self.code = QtWidgets.QPushButton(self.centralwidget)
        self.code.setGeometry(QtCore.QRect(250, 70, 75, 23))
        self.code.setObjectName("code")
        self.label = QtWidgets.QLabel(self.centralwidget)
        self.label.setGeometry(QtCore.QRect(50, 70, 54, 20))
        self.label.setObjectName("label")
        self.label_2 = QtWidgets.QLabel(self.centralwidget)
        self.label_2.setGeometry(QtCore.QRect(20, 100, 71, 20))
        self.label_2.setObjectName("label_2")
        self.username = QtWidgets.QLineEdit(self.centralwidget)
        self.username.setGeometry(QtCore.QRect(100, 70, 113, 20))
        self.username.setObjectName("username")
        self.password = QtWidgets.QLineEdit(self.centralwidget)
        self.password.setGeometry(QtCore.QRect(100, 100, 113, 20))
        self.password.setObjectName("password")
        self.login = QtWidgets.QPushButton(self.centralwidget)
        self.login.setGeometry(QtCore.QRect(250, 100, 75, 23))
        self.login.setObjectName("login")
        self.start = QtWidgets.QPushButton(self.centralwidget)
        self.start.setGeometry(QtCore.QRect(550, 340, 75, 23))
        self.start.setObjectName("start")
        self.stop = QtWidgets.QPushButton(self.centralwidget)
        self.stop.setGeometry(QtCore.QRect(640, 340, 75, 23))
        self.stop.setObjectName("stop")
        self.label_3 = QtWidgets.QLabel(self.centralwidget)
        self.label_3.setGeometry(QtCore.QRect(20, 130, 71, 20))
        self.label_3.setObjectName("label_3")
        self.token = QtWidgets.QLineEdit(self.centralwidget)
        self.token.setGeometry(QtCore.QRect(100, 130, 113, 20))
        self.token.setObjectName("token")
        self.userid = QtWidgets.QLineEdit(self.centralwidget)
        self.userid.setGeometry(QtCore.QRect(100, 170, 113, 20))
        self.userid.setObjectName("userid")
        self.issueid = QtWidgets.QLineEdit(self.centralwidget)
        self.issueid.setGeometry(QtCore.QRect(100, 210, 113, 20))
        self.issueid.setObjectName("issueid")
        self.label_4 = QtWidgets.QLabel(self.centralwidget)
        self.label_4.setGeometry(QtCore.QRect(20, 170, 71, 20))
        self.label_4.setObjectName("label_4")
        self.label_5 = QtWidgets.QLabel(self.centralwidget)
        self.label_5.setGeometry(QtCore.QRect(20, 210, 71, 20))
        self.label_5.setObjectName("label_5")
        self.label_6 = QtWidgets.QLabel(self.centralwidget)
        self.label_6.setGeometry(QtCore.QRect(20, 240, 71, 20))
        self.label_6.setObjectName("label_6")
        self.goodsid = QtWidgets.QLineEdit(self.centralwidget)
        self.goodsid.setGeometry(QtCore.QRect(100, 240, 113, 20))
        self.goodsid.setObjectName("goodsid")
        self.label_7 = QtWidgets.QLabel(self.centralwidget)
        self.label_7.setGeometry(QtCore.QRect(20, 270, 71, 20))
        self.label_7.setObjectName("label_7")
        self.proxy_pool = QtWidgets.QLineEdit(self.centralwidget)
        self.proxy_pool.setGeometry(QtCore.QRect(100, 270, 113, 20))
        self.proxy_pool.setObjectName("proxy_pool")
        self.year = QtWidgets.QLineEdit(self.centralwidget)
        self.year.setGeometry(QtCore.QRect(40, 340, 31, 21))
        self.year.setText("")
        self.year.setObjectName("year")
        self.label_8 = QtWidgets.QLabel(self.centralwidget)
        self.label_8.setGeometry(QtCore.QRect(20, 340, 21, 21))
        self.label_8.setObjectName("label_8")
        self.mon = QtWidgets.QLineEdit(self.centralwidget)
        self.mon.setGeometry(QtCore.QRect(100, 340, 31, 21))
        self.mon.setText("")
        self.mon.setObjectName("mon")
        self.label_9 = QtWidgets.QLabel(self.centralwidget)
        self.label_9.setGeometry(QtCore.QRect(80, 340, 21, 21))
        self.label_9.setObjectName("label_9")
        self.days = QtWidgets.QLineEdit(self.centralwidget)
        self.days.setGeometry(QtCore.QRect(160, 340, 31, 21))
        self.days.setText("")
        self.days.setObjectName("days")
        self.label_10 = QtWidgets.QLabel(self.centralwidget)
        self.label_10.setGeometry(QtCore.QRect(140, 340, 21, 21))
        self.label_10.setObjectName("label_10")
        self.hours = QtWidgets.QLineEdit(self.centralwidget)
        self.hours.setGeometry(QtCore.QRect(220, 340, 31, 21))
        self.hours.setText("")
        self.hours.setObjectName("hours")
        self.label_11 = QtWidgets.QLabel(self.centralwidget)
        self.label_11.setGeometry(QtCore.QRect(200, 340, 21, 21))
        self.label_11.setObjectName("label_11")
        self.mins = QtWidgets.QLineEdit(self.centralwidget)
        self.mins.setGeometry(QtCore.QRect(280, 340, 31, 21))
        self.mins.setText("")
        self.mins.setObjectName("mins")
        self.label_12 = QtWidgets.QLabel(self.centralwidget)
        self.label_12.setGeometry(QtCore.QRect(260, 340, 21, 21))
        self.label_12.setObjectName("label_12")
        self.ss = QtWidgets.QLineEdit(self.centralwidget)
        self.ss.setGeometry(QtCore.QRect(340, 340, 31, 21))
        self.ss.setText("")
        self.ss.setObjectName("ss")
        self.label_13 = QtWidgets.QLabel(self.centralwidget)
        self.label_13.setGeometry(QtCore.QRect(320, 340, 21, 21))
        self.label_13.setObjectName("label_13")
        self.auto_buy = QtWidgets.QPushButton(self.centralwidget)
        self.auto_buy.setGeometry(QtCore.QRect(390, 340, 75, 23))
        self.auto_buy.setObjectName("auto_buy")
        MainWindow.setCentralWidget(self.centralwidget)
        self.menubar = QtWidgets.QMenuBar(MainWindow)
        self.menubar.setGeometry(QtCore.QRect(0, 0, 754, 23))
        self.menubar.setObjectName("menubar")
        MainWindow.setMenuBar(self.menubar)
        self.statusbar = QtWidgets.QStatusBar(MainWindow)
        self.statusbar.setObjectName("statusbar")
        MainWindow.setStatusBar(self.statusbar)

        self.retranslateUi(MainWindow)
        QtCore.QMetaObject.connectSlotsByName(MainWindow)

    def retranslateUi(self, MainWindow):
        _translate = QtCore.QCoreApplication.translate
        MainWindow.setWindowTitle(_translate("MainWindow", "MainWindow"))
        self.code.setText(_translate("MainWindow", "发送验证码"))
        self.label.setText(_translate("MainWindow", "账号"))
        self.label_2.setText(_translate("MainWindow", "密码|验证码"))
        self.username.setText(_translate("MainWindow", ""))
        self.password.setText(_translate("MainWindow", ""))
        self.login.setText(_translate("MainWindow", "登录"))
        self.start.setText(_translate("MainWindow", "手动"))
        self.stop.setText(_translate("MainWindow", "停止"))
        self.label_3.setText(_translate("MainWindow", "token"))
        self.label_4.setText(_translate("MainWindow", "userid"))
        self.label_5.setText(_translate("MainWindow", "issueid"))
        self.label_6.setText(_translate("MainWindow", "goodsid"))
        self.label_7.setText(_translate("MainWindow", "proxy_pool"))
        self.label_8.setText(_translate("MainWindow", "年"))
        self.label_9.setText(_translate("MainWindow", "月"))
        self.label_10.setText(_translate("MainWindow", "日"))
        self.label_11.setText(_translate("MainWindow", "时"))
        self.label_12.setText(_translate("MainWindow", "分"))
        self.label_13.setText(_translate("MainWindow", "秒"))
        self.auto_buy.setText(_translate("MainWindow", "定时抢购"))

```