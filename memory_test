#coding=utf-8
#。—————————————————————————————————————————— 
#。                                           
#。  main.py                               
#。                                           
#。 @Time    : 2019-02-28 21:46                
#。 @Author  : capton                        
#。 @Software: PyCharm                
#。 @Blog    : http://ccapton.cn              
#。 @Github  : https://github.com/ccapton     
#。 @Email   : chenweibin1125@foxmail.com     
#。__________________________________________
from threading import Thread
import psutil
import re
import sys

def get_percent(svmem=''):
    try:
        return get_long_data('percent', svmem)
    except Exception as e:
        print(e)
    return None


def get_long_data(start_word='', str=''):
    if start_word == '' or str == '':
        return ''
    result = re.search(start_word + '=[\d\.]+[\w]{0,1}', str)
    final_result = re.search('[\d\.]+', result.group()).group()
    return final_result

class FatherThread(Thread):

    def __init__(self):
        Thread.__init__(self)
        self.running_forever = True
        self.thread_index= 0
        self.thread_list = []
        self.words_list = []

    def run(self):
        import time
        print('初始内存占用%f' % float(get_percent(str(psutil.virtual_memory()))))
        while self.running_forever:
            if len(self.words_list) % 1000 == 0:
                percent = float(get_percent(str(psutil.virtual_memory())))
                print(percent)
                if percent >= 95:
                    break
            self.words_list.append('Hello World' * 10)
        print('退出内存增加循环')
        while self.running_forever:
            time.sleep(1)

    def handle_sigusr1(self, signum, frame):
        print('handle_sigusr1', signum)
        self.handle_sigterm(signum, frame)

    def handle_sigusr2(self, signum, frame):
        print('handle_sigusr2', signum)
        self.handle_sigterm(signum, frame)

    def handle_sigterm(self, signum, frame):
        print('handle_sigterm', signum)
        self.running_forever = False
        for thread in self.thread_list:
            thread.running_forever = False


class SonThread(Thread):

    def __init__(self, thread_index):
        Thread.__init__(self)
        self.running_forever = True
        self.thread_index  = thread_index

    def run(self):
        import time
        second = 0
        while self.running_forever:
            print('son thread %d working %d' % (self.thread_index, second))
            second += 1
            time.sleep(1)

    def __del__(self):
        print('thread %d exit' % self.thread_index)




if __name__ == '__main__':

    father_thread = FatherThread()

    import signal

    signal.signal(signal.SIGTERM, father_thread.handle_sigterm)

    signal.signal(signal.SIGUSR1, father_thread.handle_sigusr1)

    signal.signal(signal.SIGUSR2, father_thread.handle_sigusr2)

    signal.signal(signal.SIGINT, father_thread.handle_sigterm)

    father_thread.start()
