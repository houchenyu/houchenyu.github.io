---
title: 获取城市行政边界经纬度(python实现)
---

## 动机
由于公司项目需要，需要获取珠三角9个城市（'广州市', '深圳市', '佛山市', '东莞市', '惠州市', '中山市', '珠海市', '江门市', '肇庆市') 的行政边界经纬度。 后发现高德地图提供相应的api接口， 可以轻松获取。

```
高德地图api说明网址： https://lbs.amap.com/api/webservice/guide/api/district
```

## 准备工作
1. 高德地图开发者密钥key: https://lbs.amap.com/api/webservice/guide/create-project/get-key
    
    按照里面的说明直接申请就可以了。
    
2. python包

在这里我们需要用到 requests、json包，所以请先自行安装。

`requests`包是爬虫常用的包，能够指定url发送get请求获得网页内容。

`json`包则用于解析获得的内容。

## Let's get it
### 1.发送请求
首先，我们查看高德地图api手册，了解下该怎么使用获取行政边界的api。

首先，调用的url为： 

```
restapi.amap.com/v3/config/district?key=***&keywords=***&subdistrict=*&extensions=***
```

在高德地图的api手册中有详细的参数说明，但是在我的项目中需要用到的参数主要是：
* key: 你的个人开发者key
* keywords: 想要查询的省份或者城市， 可以是名称或者adcode。 adcode是高德地图中每个城市对应的代码 (不知道是不是行业统一的)
* subdistrict： 可选值：0、1、2、3等数字，并以此类推

0：不返回下级行政区；

1：返回下一级行政区；

2：返回下两级行政区；

3：返回下三级行政区；
* extensions： 取值在{base, all}中二选一。 当为all时，返回 keywords的行政边界；当为base时，则不返回。

所以，我要查询珠江三角洲9个城市的边界时，可以先查询广东省各个地级市的信息。

```
key = '不能告诉你'
url = 'http://restapi.amap.com/v3/config/district?keywords={address}&subdistrict=1&key={key}'.format(
        address=address, key=key)
req = requests.get(url)
```



### 2.解析结果：
上一步请求网页得到的结果如下：

{"status":"1","info":"OK","infocode":"10000","count":"1","suggestion":{"keywords":[],"cities":[]},"districts":[{"citycode":[],"adcode":"440000","name":"广东省","polyline": *****篇幅限制，省略展示*******形如117.28794,23.253204;117.286849,23.25332;,"level":"province","districts":[{"citycode":"0754","adcode":"440500","name":"汕头市","center":"116.708463,23.37102","level":"city","districts":[]},{"citycode":"0757","adcode":"440600","name":"佛山市","center":"113.122717,23.028762","level":"city","districts":[]},{"citycode":"0756","adcode":"440400","name":"珠海市","center":"113.553986,22.224979","level":"city","districts":[]},{"citycode":"0750","adcode":"440700","name":"江门市","center":"113.094942,22.590431","level":"city","districts":[]},{"citycode":"0759","adcode":"440800","name":"湛江市","center":"110.364977,21.274898","level":"city","districts":[]},{"citycode":"0752","adcode":"441300","name":"惠州市","center":"114.412599,23.079404","level":"city","districts":[]},{"citycode":"0758","adcode":"441200","name":"肇庆市","center":"112.472529,23.051546","level":"city","districts":[]},{"citycode":"0660","adcode":"441500","name":"汕尾市","center":"115.364238,22.774485","level":"city","districts":[]},{"citycode":"0668","adcode":"440900","name":"茂名市","center":"110.919229,21.659751","level":"city","districts":[]},{"citycode":"0755","adcode":"440300","name":"深圳市","center":"114.085947,22.547","level":"city","districts":[]},{"citycode":"0662","adcode":"441700","name":"阳江市","center":"111.975107,21.859222","level":"city","districts":[]},{"citycode":"0768","adcode":"445100","name":"潮州市","center":"116.632301,23.661701","level":"city","districts":[]},{"citycode":"0751","adcode":"440200","name":"韶关市","center":"113.591544,24.801322","level":"city","districts":[]},{"citycode":"0753","adcode":"441400","name":"梅州市","center":"116.117582,24.299112","level":"city","districts":[]},{"citycode":"0762","adcode":"441600","name":"河源市","center":"114.697802,23.746266","level":"city","districts":[]},{"citycode":"0763","adcode":"441800","name":"清远市","center":"113.051227,23.685022","level":"city","districts":[]},{"citycode":"0769","adcode":"441900","name":"东莞市","center":"113.746262,23.046237","level":"city","districts":[]},{"citycode":"0766","adcode":"445300","name":"云浮市","center":"112.044439,22.929801","level":"city","districts":[]},{"citycode":"0663","adcode":"445200","name":"揭阳市","center":"116.355733,23.543778","level":"city","districts":[]},{"citycode":"020","adcode":"440100","name":"广州市","center":"113.280637,23.125178","level":"city","districts":[]},{"citycode":"0760","adcode":"442000","name":"中山市","center":"113.382391,22.521113","level":"city","districts":[]},{"citycode":[],"adcode":"442100","name":"东沙群岛","center":"116.887312,20.617512","level":"city","districts":[]}]}]}

返回的结果是一个json字符串， 这里关注几个关键的key:
1. `status` 表示状态， 1为成功
2. 第一个`districts` 是一个length为1的list，里面的包含很多信息。其中`polyline`返回的是广东省的行政边界的经纬度。 
3. `['districts'][0]['districts']`中则包含了所有广东省的地级市信息。 包括 `adcode`，城市名称`name`, 城市中心经纬度等。

```
def getSubDistricts(address):
    """"
    :param address: 要求的城市， 名称 或者 adcode
    :return: {city, adcode}的 DataFrame
    """
    key = '不能告诉你'
    url = 'http://restapi.amap.com/v3/config/district?keywords={address}&subdistrict=1&key={key}'.format(
        address=address, key=key)
    req = requests.get(url)
    result = json.loads(req.text)
    status = result['status']

    city = []
    adcode = []
    if status == '1':
        districts = result['districts'][0]['districts']
        num = len(districts)
        for i in range(num):
            name = districts[i]['name']
            city.append(name)
            adcode.append(districts[i]['adcode'])

    output = pd.DataFrame({'city': city, 'adcode': adcode})
    return output
```
在这里，我的想法是提取广东省所有的地级市的adcode和name，再与珠江三角洲的9个城市join，则可以得到珠江三角洲9个城市的adcode，在利用相似的方法得到各个城市的经纬度信息。相似的代码在这里就不赘述了。

### 3.解析经纬度
解析经纬度本来没有什么好讲的，直接提取`polyline`的值就好了。但是！！！！在本人实践的过程中，发现一个非常坑爹的玩意！！！而且在高德地图的官方api说明上根本没有任何说明。

从高德api里提取出行政边界的数据格式大概是：

`lng1, lat1; lng2, lat2; lng3, lat3……`

这里每个`;`分隔不同的点的经纬度, `,`分隔具体的经度和维度 (btw, 高德使用的坐标系是gcj02, 请注意自行转化成需要的坐标系)。 本来这样的结果就皆大欢喜了，但是在处理的时候发现有`|`这样的字符出现！！！ 

在这里我不再赘述自己被坑的惨痛经历，直接告诉大家出现`|`的原因。 这是因为高德地图的行政边界划分非常的精细（这个优点反而成了缺点），对于广东省这种靠海的省份， 包含很多小岛，所以就会出现很多个独立不相邻的行政边界。 比如说，深圳市的行政边界除了主要陆地板块，还有很多个小岛的边界。 这样为了区分，高德地图返回的结果中使用`|`来标记。 也就是说：`经纬度集合1 | 经纬度结合2`代表了2个独立不相交的行政边界。

```python
def getBoundary(address):
    """
    返回指定城市的行政边界
    :param address: 城市名称或者adcode
    :return: polygon(经纬度坐标list)
    """
    key = '不能告诉你'
    url = 'http://restapi.amap.com/v3/config/district?keywords={address}&extensions=all&subdistrict=1&key={key}'.format(
        address=address, key=key)
    req = requests.get(url)
    result = json.loads(req.text)
    status = result['status']
    if status == '1':
        polygon = result['districts'][0]['polyline'].strip()
        # 高德地图返回的经纬度里 有时候会出现 '|'分隔符，出现该分隔符表示该地区有多个相离的polygon
        # polygons是一个list，如果行政边界中没有'|'分隔符，则只有一个元素
        polygons = polygon.split('|')
        return polygons

def getFirstDistricts(address, target):
    """
    输入要查询的省份， 返回省份->城市的边界
    :param address: 省份
    :return: {city, adcode, polygon} DataFrame
    """
    first_districts = getSubDistricts(address)
    city = []
    polygon = []
    adcode = []
    for i in range(len(first_districts)):
        name = first_districts['city'][i]
        if name in target:
            ps = getBoundary(first_districts.iloc[i]['adcode'])
            # 根据边界的个数添加多个相同的cityname和对应的 boundary
            for bound_num in range(len(ps)):
                city.append(name)
                adcode.append(first_districts.iloc[i]['adcode'])
                polygon.append(ps[bound_num])

    output = pd.DataFrame({'city': city, 'adcode': adcode, 'polygon': polygon})
    return output
```

## 结语
有了高德地图的api获取城市边界并不难，只是中间有一点小坑需要大家注意了。 其实获取行政边界的经纬度只是第一步工作，后续利用这个经纬度进行数据处理才是关键。比如求一个具体经纬度点落在哪一个城市？预知后事如何，请听下回分解。

