---
title: tortoise和sqlalchemy写法对比
date: 2023-01-01
categories: ["开发"]
tags: ["tortoise","sqlalchemy"]
---

# tortoise和sqlalchemy写法对比

初步对比了增删改查，sqlalchemy可以改为半自动commit

```
from fastapi import APIRouter
from fastapi.encoders import jsonable_encoder
from sqlalchemy import select, update, delete

from common.schemas import R
from common.utils import list2tree
from db.midd import db
from models.menu import Menu
from models.sys_menu import SysMenu
from schemas.menu import MenuInfo, MenuSchema, MenuTree

router = APIRouter(prefix="/menu", tags=["菜单管理"])


@router.get("", summary="菜单🌲", response_model=R[MenuTree])
async def query_menu():
    """
    菜单列表-tree
    :return:
    """
    # data1 = await Menu.all().values()
    # print(data1)

    # print(db.session.execute(select(SysMenu)).scalars().all())
    # print(db.session.execute(select(SysMenu)).all())
    data = db.session.execute(select(SysMenu)).scalars().all()
    # tuple转为dict
    data = jsonable_encoder(data)
    return R.success(data=list2tree(data))


@router.post("", summary="创建菜单", response_model=R[MenuInfo])
async def add_menu(menu_schema: MenuSchema):
    """
    新增菜单
    :param menu_schema:
    :return:
    """
    # obj = await Menu.create(**menu_schema.dict())
    obj = SysMenu(**menu_schema.dict())
    db.session.add(obj)
    db.session.commit()
    print(obj)

    return R.success(data=obj)


@router.put("/{id}", summary="更新菜单", response_model=R[MenuInfo])
async def edit_menu(id: int, menu_schema: MenuSchema):
    """
    更新菜单
    :param id:
    :param menu_schema:
    :return:
    """
    # await Menu.filter(id=id).update(**menu_schema.dict())
    # data = await Menu.get_or_none(id=id)
    stmt = update(SysMenu).filter(SysMenu.id == id).values(**menu_schema.dict())
    db.session.execute(stmt)
    db.session.commit()

    data = db.session.execute(select(SysMenu).filter(SysMenu.id == id)).scalar()
    print(data)
    return R.success(data=data)


@router.delete("/{id}", summary="删除菜单", response_model=R)
async def del_menu(id: int):
    """
    删除菜单
    :param id:
    :return:
    逻辑删除 修改状态
    """
    # 伪删除
    # await Menu.filter(id=id).update(status=9)

    stmt = update(SysMenu).filter(SysMenu.id == id).values(status=9)
    db.session.execute(stmt)
    db.session.commit()

    # 删除
    # stmt = delete(SysMenu).filter(SysMenu.id == id).filter()
    # db.session.execute(stmt)
    # db.session.commit()

    return R.success()

```