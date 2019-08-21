import requests
from bs4 import BeautifulSoup
from lxml import etree
#抓取豆瓣小说的 书名、评分；
 
page = 0            #定义页数初始值；
lists_book = []     #定义书名列表；
lists_grade = []    #定义评分的列表；
for u in range(0,20):   #循环20次，每一次循环爬取一页，即：抓取20页；
    basic_url = 'https://book.douban.com/tag/%E5%B0%8F%E8%AF%B4?start=' + str(page) + '&type=T'
    page += 20      #每循环一次 +20，适应链接变化；
 
    headers = {
        'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36'
    }
    #发送请求
    response = requests.get(basic_url, headers=headers, timeout=10)     #requests请求；
    response.encoding = 'utf-8'     #设置编码
    htm = response.text     #返回text文本；
 
    #解析请求
    selector = etree.HTML(htm)      #利用 etree.HTML 初始化
    book_name = selector.xpath('//*[@id="subject_list"]/ul/li/div[2]/h2/a/text()')  #获取书名
    grade = selector.xpath('//*[@id="subject_list"]/ul/li/div[2]/div[2]/span[2]/text()')    #获取评分；
 
    #将书名存入到lists_book列表；
    for i in book_name:
        lists_book.append(i.strip())     #去除字符串空格，存入列表；
        while '' in lists_book:      #如果列表中有空元素，则删除空元素；
            lists_book.remove('')
    #将评分存入到lists_grade列表；
    for i in grade:
        lists_grade.append(i.strip())     #去除字符串空格，存入列表；
        while '' in lists_grade:      #如果列表中有空元素，则删除空元素；
            lists_grade.remove('')
 
print(lists_book)           #输出爬取的书名列表；
print(len(lists_book))      #输出列表的长度，即：爬取了多少本书
print(lists_grade)          #输出评分的列表；
print(len(lists_grade))     #输出评分列表的长度；为了和书的数目核对，防止偏差；
print("评分最高: "+ str(max(lists_grade)) + "\n" + "书名: " + lists_book[lists_grade.index(max(lists_grade))])
