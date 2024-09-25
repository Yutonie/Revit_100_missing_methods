# python logging


## **🏆Goal**

使用python logging 套件來紀錄日誌，並理解logging的基本用法及流程原理。


## **📖Reference**

python official doc: https://docs.python.org/zh-tw/3/library/logging.html
python logging tutorial: https://docs.python.org/3/howto/logging.html#logging-basic-tutorial
real python: https://realpython.com/python-logging/

  

## **🧑‍💻Process**

1. Create a local new git branch from dev branch.
(Remember set the Github & Clickup integration on this task)
```
$ git branch GT-1807_use-python-logging
$ git switch GT-1807_use-python-logging
$ git status
$ git push --set-upstream origin GT-1807_use-python-logging
```
2. Following the [real python](https://realpython.com/python-logging/) or [python logging tutorial](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)  
4. Create github pull request to waiting review.  
5. If grant, rebase and merge to dev branch, then delete the branch GT-1807_use-python-logging

  

## **🖼️Screenshot/Recording**

* The log(not in git trace) style like:
```
2024-03-15 03:28:56,661 INFO ================
2024-03-15 03:28:56,661 INFO Started
2024-03-15 03:28:56,708 INFO gdal_convertdxf success : sample.dxf
2024-03-15 03:28:56,708 INFO Finished
```
* A Level Is Just an int

| Level    | Numeric value |
| -------- |:------------- |
| CRITICAL | 50            |
| ERROR    | 40            |
| WARNING  | 30            |
| INFO     | 20            |
| DEBUG    | 10            |
| NOTSET   | 0             |


* Loggers(紀錄器) v.s Handler(處理器)
Logger 建立記錄器，用 logging.basicConfig() 來設定訊息的格式樣貌。
Handlers 可以將訊息傳送到不同檔案(例如傳送standard output stream螢幕或file檔案)。
* Filter(篩選): 可指定特地Level輸出，例如只要WARNING的訊息，不要ERROR 和CRITICAL ，關鍵字過濾，...
  

## **😏Nice To Have**

* use custom logger name
* custom logger format, include line number and function
* color full log
```
import logging

class CustomFormatter(logging.Formatter):
    grey = "\x1b[38;21m"
    yellow = "\x1b[33;21m"
    red = "\x1b[31;21m"
    bold_red = "\x1b[31;1m"
    reset = "\x1b[0m"

    format = "%(asctime)s - %(name)s - %(levelname)s - %(message)s (%(filename)s:%(lineno)d)"

    FORMATS = {
        logging.DEBUG: grey + format + reset,
        logging.INFO: grey + format + reset,
        logging.WARNING: yellow + format + reset,
        logging.ERROR: red + format + reset,
        logging.CRITICAL: bold_red + format + reset,
    }

    def format(self, record):
        log_fmt = self.FORMATS.get(record.levelno)
        formatter = logging.Formatter(log_fmt)
        return formatter.format(record)
    
logger = logging.getLogger('my_logger')
logger.setLevel(logging.DEBUG)

handler = logging.StreamHandler()
handler.setFormatter(CustomFormatter())
logger.addHandler(handler)

logger.debug('This is a debug message.')
logger.info('This is an info message.')
logger.warning('This is a warning message.')
logger.error('This is an error message.')
logger.critical('This is a critical message.')
```
  

## **📋Summary**

Python logging 會學習到以下幾點:
* Work with Python’s logging module
* Set up a basic logging configuration
* Leverage log levels
* Style your log messages with formatters

  

## 😱Trouble/Debug

Note: 小三角形可展開

* ❌The logging not include the ogr2ogr.main() process information.
Casue the [ogr2ogr source code](https://github.com/OSGeo/gdal/blob/master/swig/python/gdal-utils/osgeo_utils/samples/ogr2ogr.py) use print( ) method to warning in console.
I had tried this [stackoverflow](https://stackoverflow.com/questions/63965865/capture-output-from-non-python-third-party-library-with-python-logger) method, but failed.
      
    

  

  

## 🐣Other

**範例一:** 不同level的信息
```
import logging

#基本配置
logging.basicConfig(filename='log.log', filemode='w', 
                    format="%(asctime)s %(levelname)s %(message)s")
# 詳https://docs.python.org/zh-tw/3.8/library/logging.html#logging.basicConfig

# 預設setLevel(logging.warning)，只會輸出warning等級以上信息
logging.debug('細節信息')
logging.info('確認程序如期完成')
logging.warning('程序發生意外，如磁盤不足')
logging.error('某些功能無法執行')
logging.critical('嚴重錯誤導致程式崩壞')
```
輸出內容到log檔案裡
```
2023-08-16 12:25:24,859 WARNING 程序發生意外，如磁盤不足
2023-08-16 12:25:24,859 ERROR 某些功能無法執行
2023-08-16 12:25:24,859 CRITICAL 嚴重錯誤導致程式崩壞
```
註:其中的859是毫秒的意思

----

**範例二:** 自訂logger
```
import logging

# create Logger
mylog = logging.getLogger('mylog')
mylog.setLevel(logging.WARNING)

# create console handler
ch = logging.StreamHandler()
ch.setLevel(logging.DEBUG)

# set formatter
formatter = logging.Formatter('%(asctime)s %(levelname)s %(message)s')

# add formatter to ch
ch.setFormatter(formatter)

# add ch to mylog
mylog.addHandler(ch)

mylog.debug('debug message')
mylog.info('info message')
mylog.warning('warn message')
mylog.error('error message')
mylog.critical('critical message')

```

----

**範例三:** 截取python錯誤訊息
```
import logging

logging.basicConfig(filename='log.log', filemode='w', 
                    format="%(asctime)s %(levelname)s %(message)s")

# 截取錯誤內容，並紀錄到log
try:
    x = x/2
except Exception as e:
    logging.warning('warning message' + str(e))    #簡化的錯誤訊息
    logging.exception('Error message :')    #詳細的錯誤訊息
```

----

**範例四:** 初始化，啟動程式時開啟log
```
# myapp.py
import logging
import mylib

def main():
    logging.basicConfig(filename='myapp.log', level=logging.INFO)
    logging.info('Started')
    mylib.do_something()
    logging.info('Finished')

if __name__ == '__main__':
    main()
```
```
# mylib.py
import logging

def do_something():
    logging.info('Doing something')
```


* * *

  

Author: YuTonie

  

Operating System: Windows 11

  

Tool Version:

e.g.