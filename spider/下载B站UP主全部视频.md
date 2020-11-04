# 功能

- 下载B站UP主的全部视频

# 用法

- 进入UP主页，在浏览器地址栏找到up_id，替换代码里的up_id，运行。

# 代码

```python
import sys
import requests
import you_get

list_bvID = []

def you_get_down(url,path):
    sys.argv = ['you-get','-l', '-o', path, url]
    you_get.main()
if __name__ == '__main__':
	
    up_id = '592177200'  #B站UP 提琴夫人
    
    header = {
        'user-agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.111 Safari/537.36'

    }
    param = {
        'type':'1',
        'biz_id':up_id,
        'bvid':'',
        'mobi_app':'web',
        'ps':'40',
        'direction':'false',
    }
    url = 'https://api.bilibili.com/x/v2/medialist/resource/list?'
    response = requests.get(url=url, headers=header, params=param)
    for i in response.json()['data']['media_list']:
        list_bvID.append({'title':i['title'],'bv_id':i['bv_id']})
    print(len(list_bvID))
    print(list_bvID)
    l = len(list_bvID)
    num = 1
    for i in list_bvID:
        try:
            print('第%s/%s个视频：%s'%(str(num),str(l) ,i['title']))
            you_url = 'https://www.bilibili.com/video/' + i['bv_id']
            # 视频输出的位置
            path = 'M:\\视频\\B站\\提琴夫人'
            you_get_down(you_url, path)
            num += 1
        except:
            num += 1
```

