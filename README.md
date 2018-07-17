# data-transfer
# -*- coding: utf-8 -*-
import MySQLdb
import sqlite3


def convertDB(fromDict, toDict):
    print(fromDict['connect_parameters'])
    if fromDict['type'] == 'sqlite':
        conn_from = sqlite3.connect(fromDict['connect_parameters'])
        # 使用execute方法执行SQL语句
        cur_from = conn_from.execute("select rowid,TargetName,Level,Description,iValue from k_target_value")
        list = cur_from.fetchall()
    elif fromDict['type'] == 'mysql':
        conn_from = MySQLdb.connect(host=fromDict['connect_parameters']['host'],
                                    user=fromDict['connect_parameters']['username'],
                                    passwd=fromDict['connect_parameters']['password'],
                                    db=fromDict['connect_parameters']['dname'],
                                    port=fromDict['connect_parameters']['port'],
                                    charset='utf8')
        cur_from = conn_from.cursor()
        cur_from.execute("select rowid,TargetName,Level,Description,iValue from k_target_value")
        list = cur_from.fetchall()
    for each in list:
        print(each)

    if toDict['type'] == 'sqlite':
        conn_to = sqlite3.connect(toDict['connect_parameters'])
    elif toDict['type'] == 'mysql':
        conn_to = MySQLdb.connect(host=toDict['connect_parameters']['host'],
                                  user=toDict['connect_parameters']['username'],
                                  passwd=toDict['connect_parameters']['password'],
                                  db=toDict['connect_parameters']['dname'],
                                  port=toDict['connect_parameters']['port'],
                                  charset='utf8')

    cur_to = conn_to.cursor()
    cur_to.execute('''CREATE TABLE k_target_value
    (rowid INT NOT NULL,
    TargetName varchar(20) NULL,
    Level INT NOT NULL,
    Description varchar(20)  NULL,
    iValue INT  NULL);''')
    if toDict['type'] == 'sqlite':
        cur_to.executemany("INSERT INTO k_target_value (rowid,TargetName,Level,Description,iValue) \
            VALUES (%s,%s,%s,%s,%s)",
                           list);
    elif toDict['type'] == 'mysql':
        cur_to.executemany("INSERT INTO k_target_value (rowid,TargetName,Level,Description,iValue) \
            VALUES (%s,%s,%s,%s,%s)",
                           list);
    conn_to.commit()


# 将sqlite数据库test转换到mysql
fromDict = {'type': 'sqlite', 'connect_parameters': r'C:\Users\sxchuyang\Desktop\新建文件夹\Data\History\20180710.db'}
toDict = {'type': 'mysql', 'connect_parameters': {'host': 'localhost', 'username': 'root', 'password': '123456',
                                                  'port': 3306, 'dname': 'db20180710'}}
convertDB(fromDict, toDict)
# # 将mysql数据库转换到sqlite数据库test1
# fromDict = {'type': 'mysql', 'connect_parameters': {'host': '192.168.48.10', 'username': 'test', 'password': 'mysql',
#                                                     'port': 3306, 'dname': 'test'}}
# toDict = {'type': 'sqlite', 'connect_parameters': 'C:\Users\sxchuyang\Desktop\新建文件夹\Data\History\20180709.db'}
# convertDB(fromDict, toDict)

