## 需求分析
### 项目背景
在大数据时代，分析网站的发展情况和热度趋势对于制定公司未来发展策略至关重要。因此，本项目应运而生，通过对茶杯狐电影网站的数据进行分析，可以直观展现以下核心信息：网站引入的电影类型分布、月度更新频率、电影导演参演排行、用户整体评价关键词，以及电影产地热度分布。

随着大数据技术的快速发展，利用数据分析洞察市场趋势已成为企业提升竞争力的重要手段，深入挖掘并展现了内容更新规律、电影资源分布、导演影响力以及用户评价特征等关键指标，为平台优化资源配置和制定发展策略提供了科学依据。

### 项目功能需求
#### 数据采集及清洗与数据存储
使用requests库发起HTTP请求，从目标网站爬取HTML页面内容，并使用BeautifulSoup（bs4库）对HTML页面进行解析。通过指定的选择器或查找方法，定位并提取页面中需要的元素和数据。提取后的数据经过进一步处理，例如格式转换、去重、数据清洗等操作，最终以结构化的形式存储到数据库中，便于后续的查询和分析。

数据存储部分采用pymongo第三方库与MongoDB数据库进行交互，将爬取并经过数据清洗后的结果存储到MongoDB集群中。写操作主要通过主节点完成，而读取操作则由从节点执行，这种读写分离的方式大幅提高了系统的可用性和效率，能够有效避免单点压力过大导致服务崩溃的情况发生。同时，从节点会对主节点的数据进行备份，确保即使在主节点发生故障或意外情况下，数据仍能被保留并恢复，从而提升系统的可靠性和容错能力。



#### 数据分析与数据可视化
在数据分析环节，通过pymongo从数据库中提取目标数据，并将查询到的数据导入代码中进行分析处理。分析代码对数据进行统计、整理和挖掘，提取出具有价值的信息。随后，这些处理后的数据会被传递给pyecharts，用于生成具有直观表现力的图表，以便后续展示和进一步解读。

数据可视化部分基于Flask框架和pyecharts库进行实现。使用pyecharts生成的图表对象，通过调用render_embed()方法将图表渲染为嵌入式HTML片段。随后，这些HTML片段会被传递到Flask提供的前端模板中展示，形成用户友好的可视化界面，让用户能够在网页中直观地查看数据分析结果。

#### 自动化部署
自动化部署是指通过自动化工具和流程实现代码的构建、测试、打包和部署，减少人工操作，确保开发和生产环境的一致性。本项目使用Jenkins与Gitea实现了自动化部署流程。通过编写Jenkinsfile，设置CI/CD流水线，能够在代码提交后自动执行构建、测试和部署任务。构建后的项目会被打包成Docker容器镜像，并推送到容器镜像仓库中，随后部署到生产环境中的Docker容器中。该流程可以保证每次版本更新后，应用在不同环境中的一致性，并大大提高了部署效率和系统的稳定性。此外，自动化部署还支持快速回滚，方便在发生故障时及时恢复到稳定版本，确保系统的高可用性。

## 系统设计
### 流程设计
数据采集清洗与数据存储流程设计

1.使用requests库发起HTTP请求，获取目标网站的HTML页面内容。将获取的HTML页面传递给BeautifulSoup（bs4库）进行解析，定位并提取需要的数据元素（如文本、图片链接等）。

2.对提取到的数据进行初步清洗，包括去除无用的字符、处理缺失值、去除重复数据等。格式化数据，使其符合存储结构（例如日期格式统一、数值型数据转换等）。

3.使用pymongo与MongoDB数据库交互，将清洗后的数据存入数据库。数据存储时采用MongoDB的主从复制机制，主节点进行数据写入，从节点进行数据读取，保障读写分离，提高系统性能和数据安全性。从节点会对主节点的数据进行备份，确保数据在故障时的可恢复性。



数据采集清洗与数据存储流程如下图所示。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1732368315229-5b2dc248-3c10-4ace-a031-ded77e8a5185.png)



数据分析与数据可视化流程设计如下



1.使用pymongo从数据库中查询所需的数据。对数据进行处理和分析，进行统计、分类、聚合等操作，提取关键指标和洞察。分析结果可以通过编写Python代码进一步优化、筛选和整理，以便于后续可视化展示。

2.使用pyecharts库生成图表对象（如柱状图、折线图、饼图等），将分析结果可视化。通过render_embed()方法将生成的图表嵌入到HTML模板中。使用Flask框架将图表渲染并传输到前端，展示给用户。用户可以在Web界面上交互式地查看图表，了解数据的动态变化和趋势。

数据分析与数据可视化流程如下图所示。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1732368583149-bbf2f869-377a-4bc6-a473-d92d0c472f45.png)

自动化流程设计



代码管理与版本控制

Gitea：作为代码托管平台，团队通过Gitea进行源代码的管理和版本控制。所有代码变更（如新功能开发、Bug修复）都通过Git提交，并同步到Gitea仓库中。

分支管理：采用Git的分支策略，开发人员在不同的分支上进行开发工作（如feature、bugfix、release等），确保主分支（main）保持稳定，避免开发中断。

持续集成

Jenkins：使用Jenkins实现持续集成，自动化构建和测试代码。当代码提交到Gitea仓库后，Jenkins会检测到变更并自动启动构建流程。

Jenkinsfile：通过编写Jenkinsfile脚本定义CI/CD流水线，确保每次提交代码时都进行构建、编译、单元测试等任务，确保代码质量。

容器化与自动化构建

Docker：项目采用Docker容器化技术，将应用及其所有依赖项打包成容器镜像，确保应用在任何环境下都能一致运行。

Dockerfile：使用Dockerfile定义容器镜像的构建过程，包括操作系统、运行时环境、依赖包等的配置。

自动化构建：Jenkins自动触发Docker镜像构建过程，每次代码提交后，自动创建新的容器镜像，并将其推送到镜像仓库。

自动化流程图下图所示。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1732370213213-4bb6f656-43cf-4929-ac83-1320b3994fad.png)

总体流程

整个项目流程覆盖了从数据采集、存储、分析、展示到自动化部署和安全保障的各个方面，确保了系统的高效性、可扩展性和安全性。 

总体流程如下图所示。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1732369690084-45ed5e82-ffc3-4506-b874-1371975eb7bf.png)

### 图表设计
系统从以下几个方面进行设计与实现：

1. 电影更新频率分析

图表类型：折线图

用途：折线图适合展示随时间变化的电影更新频率，尤其是月度更新频率的变化趋势。通过折线图，平台可以清晰地看到电影更新的波动情况，并分析哪些时间段更新较为频繁。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1732371051145-eea9c62f-14ca-4c56-88e6-0af1fb1a09e5.png)

2. 电影产地热度分析

图表类型：地图

用途：地图适用于展示电影产地的热度分布。通过将不同产地的电影数量和观看量等指标映射到地图上，可以直观地看到各个地区的电影资源热度。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1732371197312-97cc4a2c-72c2-412c-8433-87438f4edff6.png)

3. 用户评论关键词提取分析

图表类型：词云图

用途：词云图通过可视化用户评论中的高频关键词，能够帮助发现用户的偏好和需求。关键词出现频率越高，字形越大，能够直观显示哪些主题在评论中讨论得最多。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1732371224968-0b6207b8-5cad-4bcb-999b-d8f06a88a292.png)

4. 导演热度分析

图表类型：柱状图

用途：柱状图适合展示不同导演的影片数量和受欢迎程度，能够帮助平台了解哪些导演的作品更受欢迎。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1732371300764-59a8e8da-a879-4fec-b410-94f5148c6abe.png)

5. 影片类型占比分析

图表类型：饼图

用途：饼图适合展示平台中各类型电影的资源占比，能够清晰地展示各类型电影在平台中的分布。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1732371321441-a2f74f0f-c606-4c64-b0ba-c061d2e0ff8f.png)





### 
### 项目优势
本项目采用Python3.8编写，具备以下几个主要优势：



Flask Web可视化页面

本项目使用Flask框架开发的Web可视化页面，相较于传统的静态HTML页面，Flask提供了更强的动态交互能力和灵活性。Flask支持模板渲染、路由管理及RESTful接口，使得前后端分离和数据交互更加便捷高效。

Docker容器技术

使用Docker容器技术部署项目，避免了繁琐的环境配置操作，显著简化了项目迁移和部署的复杂度。Docker容器提供了一种轻量级的虚拟化方式，可以确保应用在不同环境中具有一致的运行效果。此外，容器与宿主机之间实现隔离，提升了服务器的安全性和稳定性，减少了环境配置对生产环境的影响。

MongoDB复制集机制

本项目使用了MongoDB的复制集机制，构建了MongoDB集群，支持读写分离和故障转移。主节点负责写入操作，而从节点用于读取，确保系统在高负载情况下的稳定性和响应速度。复制集机制不仅提升了系统的可用性，还增强了数据的安全性，通过数据备份机制防止数据丢失和服务器故障对业务的影响。

DevOps自动化流程

集成Jenkins与Gitea实现了DevOps流程，能够自动化管理代码的构建、测试和部署。结合Docker容器化技术，通过编写Jenkinsfile流水线脚本，将代码打包为Docker容器镜像，支持自动化部署。此流程确保了团队开发的高效协作，同时在多平台（如Kubernetes）上均可运行，大大提升了开发部署和版本更新的效率。

Linux服务器部署与Nginx反向代理

项目部署在Linux服务器上，采用Nginx进行反向代理Flask应用。Nginx不仅能够高效处理大量并发请求，还通过负载均衡和反向代理提高了系统的可扩展性。同时，Nginx还提供了额外的安全性保障，如SSL加密和请求过滤，确保应用的安全性和稳定运行。

### 工具版本
使用的工具与工具版本如下表x所示。

| 描述 | 工具 | 版本 |
| :---: | :---: | :---: |
| 解释器 | Python | 3.8 |
| Python库 | Flask | 3.0.3 |
| Python库 | jieba | 0.42.1 |
| Python库 | pandas | 2.0.3 |
| Python库 | pyecharts | 2.0.6 |
| Python库 | beautifulsoup4 | 4.12.3 |
| Python库 | pymongo | 4.10.1 |
| Python库 | Requests | 2.32.3 |
| 容器运行时 | Docker-ce | 27.0.3 |
| 数据库 （主从架构）   | mongodb | 7.0.12 |
| <font style="color:rgb(77, 77, 77);">CI/CD平台</font> | jenkins | 2.462.3 |
| <font style="color:rgb(77, 77, 77);">代码仓库</font> | gitea | 22.3.1 |
| <font style="color:rgb(77, 77, 77);">操作系统</font> | Rocky Linux | 9.4 |
| <font style="color:rgb(77, 77, 77);">web服务器</font> | nginx | 1.25 |






## 系统实现
### 数据采集数据清洗实现
#### 获取所有电影详情页url的关键代码
网站带有请求速度检测反爬，带上请求头，使用time.sleep(random.randint(1, 5))，随机模拟人类访问时间，防止网站拉黑IP。

通过url_get(self,begin_page=1,end_page=3)方法，保存所有的url数据到data_type列表中，返回给video_data_get()方法。

get_url()方法关键代码如下：

```python
for i in range(begin_page, end_page):
    index = i  # 页面数
    url = self.headurl + f"/cupfoxshow/dianying--------{index}---.html"
    
    try:
        data = requests.get(url, headers=self.headers)
        soup = BeautifulSoup(data.text, 'html.parser')
        # 查找所有的 a 标签每页30个url
        elements = soup.find_all('a')
        # 输出每个元素的 href 属性
        for element in elements:
            href = element.get('href')
            if href and '/cupfox/' in href: 
                self.url_path.append(self.headurl + href) -
        print(f"page {i} url add to list succeed")
        time.sleep(random.randint(1, 5))
        
    except Exception as e:
        print("出现异常:", e)

return self.url_path
```



#### 电影信息数据清洗
requests爬取到网页信息后使用bs4库解析网页，提取需要的数据存入到字典中。

video_data_get()方法关键代码如下：

```python
'''电影信息提取'''
element_info = soup.select(
    'body > div.details > div.container.flex > div.mobile-main.mobile-main-type > div.movie.bj.br.card.border-shadow > div.cf.b-t')

tree2 = element_info[0]

movie_info['名称'] = tree2.find('h1').get_text()
movie_info['图片'] = tree2.find('img').get("src")
movie_info['别名'] = tree2.find('p', class_='cr3').get_text(strip=True).replace("别名：", '').split(",")
movie_info['标签'] = [a.get_text() for a in
                    tree2.select('.scroll-content')[0].find_all('a', target="_blank")]
movie_info['连载'] = tree2.find('p', class_='cr3').find_next('p').get_text(strip=True).replace("连载：", "")
movie_info['导演'] = [a.get_text() for a in
                    tree2.find('p', class_='cr3').find_next('p').find_next('p').find_all("a",target="_blank")]
movie_info['演员'] = [a.get_text() for a in tree2.find_all('p', class_='cr3 starLink')[0].find_all('a')]
movie_info['类型'] = [a.get_text() for a in
                    tree2.find('p', class_='cr3 starLink').find_next("p").find_all('a')]
movie_info['分类'] = tree2.find('p', class_='cr3 starLink').find_next("p").find_next("p").get_text(
    strip=True).replace("分类：", "")
movie_info['更新时间'] = tree2.find('p', class_='cr3 starLink').find_next("p").find_next("p").find_next(
    "p").get_text(strip=True).replace("更新时间：", "")

# 单独提取出影片描述
miaoshu = soup.select(
    "body > div.details > div.container.flex > div.mobile-main.mobile-main-type > div.movie.bj.br.card.border-shadow > div.summary.detailsTxt")
b_cleaned = re.sub(r'\s+', ' ', miaoshu[0].get_text(strip=True))
movie_info['影片描述'] = b_cleaned.replace('展开', '')



'''网友评论提取'''
element = soup.select(
    'body > div.details > div.container.flex > div.mobile-main.mobile-main-type > div:nth-child(6) > ul')
tree1 = element[0]

b_tags = tree1.find_all('b')
p_tags = tree1.find_all('p')
```





### 数据存储实现
编写sava_db()类实现数据存储模块，通过传入dict列表，一次性插入多条数据到mongodb中。

其中构造函数通过os库中的getenv方法获取系统中的环境变量修改默认值，能更好的在Linux（容器）中读取环境变量值，而不用修改对应代码来连接mongodb数据库，sava_db()关键代码如下：

```python
class sava_db():
    def __init__(self):
        # 从环境变量中读取配置信息，若未设置则使用默认值
        self.username = os.getenv("MONGO_USERNAME", "root")  # 默认用户名为 root
        self.password = os.getenv("MONGO_PASSWORD", "password123")  # 默认密码为 password123
        self.host = os.getenv("MONGO_HOST", "192.168.100.120")  # 默认主机为 192.168.100.120
        self.port = int(os.getenv("MONGO_PORT", 27017))  # 默认端口为 27017
        self.db = os.getenv("MONGO_DB", "cup_fox_movie")  # 默认数据库为 cup_fox_movie
        self.table = os.getenv("MONGO_TABLE", "movie_data")  # 默认表为 movie_data

    def save_more_data(self, json_list):
        json_list_data = json_list
        myclient = MongoClient(f'mongodb://{self.username}:{self.password}@{self.host}:{self.port}/')
        mydb = myclient[self.db]  # 选中或者创建库
        mycol = mydb[self.table]  # 选中或者创建表
        mydict = json_list_data
        controller_more = mycol.insert_many(mydict)  # 一次插入多条数据
        
        return controller_more
```



### 数据分析实现
##### 影片地区与发布年份数据查询
从数据库中查询标签列的所有数据（其中标签列中的数据类型为list），其中包含所有影片地区与影片发布年份数据，可视化部分可以使用“年份”字段做条形图、使用“国家”字段做世界地图，本项目使用“国家”字段做世界地图，关键代码如下：

```python
# 影片地区与发布年份数据
def get_movie_label():
    """
    return: dict
    """
    label_data_list_year = []
    label_data_list_guoji = []
    label_dict = {}
    label_name = collection.find({}, {'标签': 1, '_id': 0})
    for x in label_name:
        if len(x["标签"][0]) == 4:
            label_data_list_year.append(x["标签"][0])
            label_data_list_guoji.append(x["标签"][1])

    label_dict["年份"] = label_data_list_year
    label_dict["国家"] = label_data_list_guoji

    return label_dict
```



##### 导演参影数据查询
从数据库中查询导演列，（其中导演列中的数据类型为list），一个影片可能包含多个导演，将所有导演存入一个列表，返回包含所有导演的列表，数据可视化部分统计导演参影的top10数据，生成柱状图，关键代码如下：

```python
# 导演参影数据返回
def get_movie_daoyan():
    """
    return: list
    """
    daoyan_data_list = []
    daoyan_name = collection.find({}, {'导演': 1, '_id': 0})
    for x in daoyan_name:
        for y in x["导演"]:
            daoyan_data_list.append(y)

    return daoyan_data_list
```



##### 影片类型数据查询
从数据库中查询类型列，（其中类型列中的数据类型为list），一个影片可能包含多种类型，将所有类型存入一个列表，返回包含所有类型的列表，数据可视化部分过滤掉占比非常少的类型，生成饼图可以直观的看到网站内电影类型占比，关键代码如下：

```python
# 影片类型占比分布
def get_movie_type():
    """
    return: list
    """
    type_data_list = []
    type_name = collection.find({}, {'类型': 1, '_id': 0})
    for x in type_name:
        for y in x["类型"]:
            type_data_list.append(y)

    return type_data_list
```





##### 用户评论数据查询
从数据库中查询所有列，获取“【星辰影院】网友评价”的所有数据，将所有评论存入一个字符串，返回包含所有评论的字符串数据，在数据可视化部分使用jieba库分词，提取评论中的关键字，生成词云图，关键代码如下：

```python
# 网友评论 整体拼接成字符串
def get_movie_remark():
    """
    return: string
    """
    remarks = ""
    remark_name = collection.find({})

    for x in remark_name:
        remark_sources = [x.get("【星辰影院】网友评价", "")] #所有评论插入列表
        for remark in remark_sources:
            if remark:
                remarks += remark

    return remarks
```



##### 电影网站更新时间查询
从数据库中查询更新时间列，获取“更新时间”的所有数据，将所有数据以“-”分割获取月份数据，返回包含所有月份数据的字符串列表，在可视化部分生成折线图，关键代码如下：

```python
# 电影网站更新时间分布
def get_movie_date():
    """
    return: list
    """
    data_list = []
    update_time = collection.find({}, {'更新时间': 1, '_id': 0})
    for i in update_time:
        data_list.append(i['更新时间'].split("-")[1])  # 获取月份数据
        
    return data_list
```





### 数据可视化实现
#### 影片产地国家分布图
地图可视化数据获取，关键代码如下：

```python
guojia = show_mongo_data.get_movie_label()["国家"]
city_data = [1] * len(show_mongo_data.get_movie_label()["国家"])
```

地图可视化效果，显示各国电影资源分布，如图所示。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1731935743596-a2a3e946-0f62-44ea-a449-b69478a8cd91.png)



#### 导演参影Top10数量图
导演参影柱状图可视化数据获取，关键代码如下：

```python
directors = show_mongo_data.get_movie_daoyan()
directors = [i for i in directors if len(i) > 0]
director_count = pd.Series(directors).value_counts().head(10)
```

导演参影柱状图效果，展示热门导演参影频率，如图所示。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1731935759863-10070b88-d9ad-4429-bc21-8c85337cef69.png)



#### 影片类型占比饼图
影片类型饼图可视化数据获取，关键代码如下：

```python
film_types = show_mongo_data.get_movie_type()
film_type_counts = pd.Series(film_types).value_counts().head(10)
data = [(index, count) for index, count in film_type_counts.items()]
```

影片类型占比饼图效果，反映网站内资源类型分布，如图所示。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1731936791700-5cda211f-c738-4571-abd6-e72e1f88a5a7.png)



#### 用户评论关键词分析图
用户评论关键词词云图可视化数据获取，关键代码如下：

```python
reviews = jieba.lcut(show_mongo_data.get_movie_remark())
word_counts = Counter(reviews)

#进一步清洗jieba库提取的数据
word_data = [(word, count) for word, count in word_counts.items() if
         len(word) > 1 and not re.match(r'^[a-zA-Z0-9]+$', word)]
word_data = [(word, count) for word, count in word_data if count > 5]
word_data = sorted(word_data, key=lambda x: x[1], reverse=True)[:500]
```

用户评论关键词词云图效果，生成词云图展示用户关注点，如图所示。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1731935781471-bd5d404d-d0d3-451a-9ba0-05b274ead00b.png)





#### 电影网站影片更新时间分布图
电影网站影片更新时间分布可视化数据获取，关键代码如下：

```python
monther = dict(Counter(show_mongo_data.get_movie_date()))
dates = [f"2024-{item}" for item in monther.keys()]
yearly_counts = list(monther.values())
```

电影网站影片更新时间分布折线图效果，以折线图呈现更新规律，如图所示。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1731935789110-ead5331a-4151-4e9b-b6ee-01f8952e1029.png)



#### 可视化大屏
![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1731935818140-b5e82023-638e-4d74-8810-abdc793b0e34.png)

### 自动化流程实现
使用git命令将代码推送到gitea代码仓库中，jenkins会读取gitea中的Jenkinsfile文件，并检查语法，通过后会自动运行stages中声明的shell脚本。

#### 效果图
爬虫模块自动部署，效果图如图所示。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1732103912195-dc2942c7-886f-4bf1-a0fe-f3d2072b1821.png)





数据可视化模块自动部署，效果图如图所示。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1732104613815-870884c7-95c9-4eaf-9fd2-2eb42023f006.png)





#### Jenkinsfile流水线脚本代码
爬虫模块自动构建与部署，流水线脚本代码如下：

```bash
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'cupfox_project'
        IMAGE_VERSION='v5'
        CONTAINER_NAME='cupfox'
        MONGO_CONTAINER_NETWORK='mongodb_data_set_db_network'
    }

    stages {
        stage('Build') {
            steps {
                echo "构建爬虫模块镜像..."
                sh 'docker build -t $IMAGE_HUB/$IMAGE_NAME:$IMAGE_VERSION .'
            }
        }

        stage('Push Image') {
            steps {
                echo "推送爬虫模块容器镜像到注册中心..."
                sh 'docker images $IMAGE_HUB/$IMAGE_NAME:$IMAGE_VERSION'
                sh 'docker push $IMAGE_HUB/$IMAGE_NAME:$IMAGE_VERSION'
            }
        }

        stage('Deploy') {
            steps {
                echo "部署爬虫模块到服务器..."
                // 停止并删除已存在的容器
                sh '''
                    docker ps -q -f name=$CONTAINER_NAME | xargs -r docker stop
                    docker ps -aq -f name=$CONTAINER_NAME | xargs -r docker rm
                '''
                // 启动新的容器
                sh 'docker run -id --network $MONGO_CONTAINER_NETWORK --name $CONTAINER_NAME  $IMAGE_HUB/$IMAGE_NAME:$IMAGE_VERSION'
                // 查看容器状态和日志
                sh 'docker ps -a'
                sh 'sleep 10'
                sh 'docker logs $CONTAINER_NAME'
            }
        }
    }
}
```

数据可视化模块，动态更新并快速发布，流水线脚本代码如下：

```bash
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'cupfox_project_visuali'
        IMAGE_VERSION='v5'
        CONTAINER_NAME='cupfox_visuali'
        MONGO_CONTAINER_NETWORK='mongodb_data_set_db_network'
    }

    stages {
        stage('Build') {
            steps {
                echo "构建数据可视化模块镜像..."
                sh 'docker build -t $IMAGE_HUB/$IMAGE_NAME:$IMAGE_VERSION .'
            }
        }

        stage('Push Image') {
            steps {
                echo "推送数据可视化模块容器镜像到注册中心......"
                sh 'docker images $IMAGE_HUB/$IMAGE_NAME:$IMAGE_VERSION'
                sh 'docker push $IMAGE_HUB/$IMAGE_NAME:$IMAGE_VERSION'
            }
        }

        stage('Deploy') {
            steps {
                echo "部署数据可视化模块到服务器..."
                // 停止并删除已存在的容器
                sh '''
                    docker ps -q -f name=$CONTAINER_NAME | xargs -r docker stop
                    docker ps -aq -f name=$CONTAINER_NAME | xargs -r docker rm
                '''
                // 启动新的容器
                sh 'docker run -id  --network $MONGO_CONTAINER_NETWORK  --name $CONTAINER_NAME -p 38000:5000 $IMAGE_HUB/$IMAGE_NAME:$IMAGE_VERSION'
                // 查看容器状态和日志
                sh 'docker ps -a'
                sh 'sleep 10'
                sh 'docker logs $CONTAINER_NAME'
            }
        }
    }
}
```

#### Dockerfile编写
爬虫模块容器构建代码如下：

```python
FROM python:3.8-slim-buster

COPY ./requirements.txt /data/


RUN apt-get update && apt-get install -y  net-tools \
    && pip install -r /data/requirements.txt -i https://mirrors.aliyun.com/pypi/simple/ \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/lists/* /root/.cache/pip


ENV MONGO_USERNAME="root"
ENV MONGO_PASSWORD="password123"
ENV MONGO_HOST="172.25.0.2"
ENV MONGO_PORT=27017
ENV MONGO_DB="cup_fox_movie"
ENV MONGO_TABLE="movie_data"

WORKDIR /data

COPY . /data/

ENTRYPOINT ["python", "-u", "/data/main.py"]
```

数据可视化模块容器构建代码如下：

```python
FROM python:3.8-slim-buster

COPY ./requirements.txt /data/

RUN apt-get update && apt-get install -y  net-tools \
    && pip install -r /data/requirements.txt -i https://mirrors.aliyun.com/pypi/simple/ \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/lists/* /root/.cache/pip

ENV MONGO_USERNAME="root"
ENV MONGO_PASSWORD="password123"
ENV MONGO_HOST="172.25.0.3"
ENV MONGO_PORT=27017
ENV MONGO_DB="cup_fox_movie"
ENV MONGO_TABLE="movie_data"

WORKDIR /data

COPY . /data/

EXPOSE 5000

ENTRYPOINT ["python", "-u", "/data/app.py"]
```



### NginxWeb服务器配置
#### 配置文件
nginx反向代理服务器38000端口，重定向到443https端口，flask_38000.conf具体配置代码如下：

```python

server {
    listen 80;
    server_name flask.localserver.local;

    location / {
        return 301 https://$host$request_uri;  # 将 HTTP 请求重定向到 HTTPS
    }
}

server {
    listen 443 ssl;
    server_name flask.localserver.local;

    ssl_certificate /etc/nginx/ssl/server.crt;  # 证书文件路径
    ssl_certificate_key /etc/nginx/ssl/server.key;  # 私钥文件路径

    ssl_session_cache shared:SSL:5m;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';

    location / {
        proxy_pass http://127.0.0.1:38000; #反向代理到本地38000 Docker映射端口
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

```

#### 数据可视化大屏
通过配置Nginx反向代理，将流量从HTTP重定向至HTTPS，提升安全性，修改本地hosts文件解析服务器IP到“flask.localserver.local”访问结果如图所示。

![](https://cdn.nlark.com/yuque/0/2024/png/35492615/1732106855249-58c18b76-8c20-447e-9340-1491671e3341.png)









## 总结
通过本项目的实施，深化了对大数据分析技术和可视化工具的理解与应用能力。项目实现过程中，经历了从需求调研、技术选型、算法优化到功能集成的全过程，不仅提升了专业能力，也培养了面向实际问题解决的工程思维。未来，可进一步扩展到其他类型网站的分析中，探索更多应用场景的价值。本设计验证了数据分析在商业决策中的重要性，并通过案例实践展示了数据驱动创新的可能性，能够帮助决策者快速洞察关键数据特征，从而优化内容策略。通过项目实践，进一步深化了对大数据分析技术、数据挖掘工具及可视化方法的掌握，未来，本系统可扩展至其他类型网站的数据分析领域，为更多企业提供数据驱动的决策支持。本项目的完成不仅验证了大数据分析的实际价值，也展示了其在商业决策中的广阔应用前景。
