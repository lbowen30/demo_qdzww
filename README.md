# demo_qdzww
import  requests
from lxml import etree

#目标对象的网址
url1 = 'https://www.qidian.com/all/chanId22/'

#UA伪装
headers = {
    'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.74 Safari/537.36'
}

response = requests.get(url=url1,headers=headers).content
root = etree.HTML(response)
# print(root)

# web_class_list = root.xpath('//div[@class="select-list"]/div[@data-l2="3"]/div[@class="sub-type"]/dl[4]/dd[@data-subtype="18"]/a/@href')
# print(web_class_list)

web_class_list = root.xpath('//div[@class="select-list"]/div[@data-l2="3"]/div[@class="sub-type"]/dl[4]/dd')
for a in web_class_list:
    web_class = a.xpath('a/@href')[0]
    # print(web_class)
    web_class_name = a.xpath('a/text()')[0]
    #每一个分类网站的网址
    class_web_url = 'https:' + web_class
    # print(class_web_url)

    for page in range(1,6):
        # 分页操作
        class_web_url = class_web_url + 'page{}/'.format(page)
        response = requests.get(url=class_web_url,headers=headers).content
        root = etree.HTML(response)

        novels = root.xpath('//ul[@class="all-img-list cf"]/li')
        for novel in novels:
            #获取小说名称
            novel_name = novel.xpath('div[@class="book-mid-info"]/h2/a/text()')[0]
            # print(novel_name)

            #获取作者名称
            author_name = novel.xpath('div/p/a/text()')[0]
            # print(author_name)

            #获取大分类
            novel_big_class = novel.xpath('div/p/a/text()')[1]
            # print(novel_big_class)

            #获取小分类
            novel_small_class = novel.xpath('div/p/a/text()')[2]
            # print(novel_small_class)

            #获取小说简介
            novel_intro =novel.xpath('div/p[@class="intro"]/text()')[0]
            # print(novel_intro)

            #保存数据
            file = open('起点中文之武侠.csv','a',encoding='gb18030')
            file.write(novel_name+','+author_name+','+novel_big_class+','+novel_small_class+','+novel_intro)
            file.write('\n')
            file.close()

        print(web_class_name,"小类中的第{}页爬取成功！！".format(page))
