
# _type
* 在 5.X 版本中，一个 index 下可以创建多个 type；
* 在 6.X 版本中，一个 index 下只能存在一个 type；
* 在 7.X 版本中，直接去除了 type 的概念，就是说 index 不再会有 type。

* 原因分析
1、为何要去除 type 的概念？
答： 因为 Elasticsearch 设计初期，是直接查考了关系型数据库的设计模式，存在了 type（数据表）的概念。
但是，其搜索引擎是基于 Lucene 的，这种 “基因”决定了 type 是多余的。 Lucene 的全文检索功能之所以快，是因为 倒序索引 的存在。
而这种 倒序索引 的生成是基于 index 的，而并非 type。多个type 反而会减慢搜索的速度。
为了保持 Elasticsearch “一切为了搜索” 的宗旨，适当的做些改变（去除 type）也是无可厚非的，也是值得的。


# docker安装
### elasticsearch
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.8.0
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:7.8.0
### kibana 
docker pull docker.elastic.co/kibana/kibana:7.8.0
docker run --link YOUR_ELASTICSEARCH_CONTAINER_NAME_OR_ID:elasticsearch -p 5601:5601 {docker-repo}:{version}
docker run --link 547cc8897765:elasticsearch -p 5601:5601 docker.elastic.co/kibana/kibana:7.8.0
547cc8897765
# entrance
http://129.204.59.139:9200/
http://129.204.59.139:5601/


# /_analyze分词
Content-Type application/json;charset:utf-8
http://134.175.231.104:9200 /test-index/_analyze
{"analyzer" : "standard",  "text" : "Elasticsearch"}

_analyze是Elasticsearch一个非常有用的API，它可以帮助你分析每一个field或者某个analyzer/tokenizer是如何分析和索引一段文字。

token是一个实际被存储在索引中的词
position指明词在原文本中是第几个出现的
start_offset和end_offset表示词在原文本中占据的位置。

也可以用指定的analyzer来分析使用whitspace
{"analyzer":"whitespace", "text":"床前 明月光"}

使用ik分析器  "analyzer": "ik_max_word",

安装ik
https://github.com/medcl/elasticsearch-analysis-ik/releases/tag/v7.5.1
将文件复制到 es的安装目录/plugin/ik下面即可 (docker:/usr/share/elasticsearch/plugins)

ik_max_word：会将文本做最细粒度的拆分，比如会将“中华人民共和国国歌”拆分为“中华人民共和国,中华人民,中华,华人,人民共和国,人民,人,民,共和国,共和,和,国国,国歌”，会穷尽各种可能的组合；
ik_smart：会做最粗粒度的拆分，比如会将“中华人民共和国国歌”拆分为“中华人民共和国,国歌”

PUT /school_index
{
    "settings" : {
        "index" : {
            "analysis.analyzer.default.type": "ik_max_word"
        }
    }
}


# 测试分词
在kibana的Dev Tools中

POST /test3/_analyze
{
  "text": "中国是中国人的"
}
返回
{
  "tokens" : [
    {
      "token" : "中",
      "start_offset" : 0,
      "end_offset" : 1,
      "type" : "<IDEOGRAPHIC>",
      "position" : 0
    }
......
















