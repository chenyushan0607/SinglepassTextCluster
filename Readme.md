# 项目的背景
SinglepassTextCluster, an TextCluster tool based on Singlepass cluster algorithm that use tfidf vector and doc2vec，which can be used for individual real-time corpus cluster task。基于single-pass算法思想的自动文本聚类小组件，内置tfidf和doc2vec两种文本向量方法，可自动输出聚类数目、类簇文档集合和簇类大小，用于自有实时数据的聚类任务。

# 项目的由来
实时热点话题、事件的发现，是针对实时信息流的一个典型应用场，如如HistoryHotEvent项目。地址：https://github.com/liuhuanyong/HistoryHotEventBase。其中包括了从2004年至2019年共16年的每日热点事件项目(004年至今共16年的历时热点标题数据库)。如何高效、快速地从大规模实时文本数据中发现具有代表性的新闻标题，并根据时间顺序挖掘出热点话题或事件的演化脉络具有十分重要的现实意义。
在热点挖掘这个方向上，笔者主要已经进行了若干项目的探索，并从这些项目中，可以总结出一个热点事件生成和演化脉络的挖掘，可以分成四个步骤：  
1、面向实时文本流的文本获取。可以针对特定的主题词过滤的方式进行文本语料获取（参考EventMonitor项目，地址：https://github.com/liuhuanyong/EventMonitor），也可以以无过滤的方式获取实时的文本数据流，通过步骤2、3进一步得到特定主题的文本集合。         
2、面向实时文本流的文本聚类。目标是将众多个文本进行文本聚类，包括文本的去重，聚类成不同的文本主题（粗分类），如将偷税、漏税文本事件聚合在一起。（话题聚类、文本聚类）。     
3、聚类文本中的文本主题进行事件细分。针对每一类话题，进一步细分为不同的事件，形成不同主题下的事件，如A偷税漏税、B偷税漏税。（话题事件切分，参考TopicCluster项目。地址：https://github.com/liuhuanyong/TopicCluster)。   
4、对细分的事件进行故事里程碑划分。借助时间信息，设定时间窗口，将同一个事件进一步划分成若干个时间事件切片，并挖掘出该事件切片中的代表性事件名称。(事件重要性计算，参考：ImportantEventExtractor项目。地址：https://github.com/liuhuanyong/ImportantEventExtractor)。  
5、子事件演化脉络识别。对识别好的代表性事件名称，通过事件演化的特征（方向性特征、时间先后顺序特征、语义转移特征），构建事件演化脉络链。(事件演化脉络识别) 。  

目前，关于步骤4和5目前还没有对应的项目，因此，为了填补这一空白，本项目选择步骤4，以Single-Pass算法的实现为例，进行实践说明：  
Single-Pass算法又称单通道法或单遍法，是流式数据聚类的增量式方法，算法的时间效率高，适合对流数据进行挖掘,而。对于依次到达的文本数据流,该方法按输入顺序每次处理一个数据,依据当前数据与已有类的匹配度大小,将该数据判为已有类或者创建一个新的数据类,实现流式数据的增量和动态聚类。

# 项目的构成 
1、data：待处理文本，如train.txt。  
2、model: token_vector.bin，预先训练好的字向量，可用于文本的快速向量化。  
3、result: 最终聚类结果文件，文件以json格式保存，记录聚类结果id、类簇中文本数量、类簇文本集合信息。  
4、cluster_demo.py：聚类主控脚本，在该脚本中可直接指定聚类阈值，聚类的方法和聚类输入、输出文本路径。
5、cluster_doc2vec.py：基于doc2vec的主控聚类接口。  
6、cluster_tfidf.py：基于tfidf的主控聚类接口。  
7、singlepass_cluster_tfidf.py：基于tfidf的single-pass聚类算法实现细节。   
8、singlepass_cluster_doc2vec.py：基于doc2vec的single-pass聚类算法实现细节。   

# 项目的运行效果
1、项目运行方式：python cluster_demo.py
2、处理输入文本：train.txt，其中选择了1000条汽车需求评论语料，每条为1行，内容如下：

    """
    但在一个周一晚上，州监管机构还是要求他们停止所有测试
    问题是持久发展，需要市场投融资这个循环
    所以买车前需要试驾
    五菱荣光V(参数|询价)，现在跑了30000多公里了，需要做哪些常规保养
    总成3000，应该需要换码，我的灯还没到，等到了换完我上作业
    建议在车内保险丝盒里面找一下，看位置图
    车辆还有一年多的保修期，现在需要处理吗
    现在根据修理店建议，我已经换了燃油泵总成，燃油压力调阀，问题依旧
    个人建议质保期内还是在4S店保养吧
    房车必须要足够大，才能住得舒服，因此房车的拓展应该成为房车的主流

    """
3、参数设置。使用doc2vec向量化方法，theta选择0.85。    
4、聚类运行结果： cluster_doc2vec.json，共得到779个聚类结果，示例如下：

    """
    {"cluster_id": 77, "cluster_nums": 5, "cluster_docs": [{"doc_id": 1, "doc_content": "那时候，冬天停车必须防水，根本没有防冻液这一说"}, {"doc_id": 2, "doc_content": "这两款比较的话，建议入手瑞风s3"}, {"doc_id": 3, "doc_content": "这里我们建议，首先将这几款车型上市的时间进行对比"}, {"doc_id": 4, "doc_content": "这东西，必须自己试驾"}, {"doc_id": 5, "doc_content": "希望大家可以透过这篇文章了解一下，轮胎的寿命…"}]}
    
    """
# 项目的总结
1、针对用户自有实时文本数据，利用聚类方法进行自动的文本聚类，得到聚类主题以及聚类的相关文档，可以为数据集文本的分析提供较好的帮助。基于single-pass算法思想的自动文本聚类小组件，可以直接使用。   
2、singlepass聚类算法是一个简单、快速的聚类算法，其核心在于文档向量化方法以及聚类阈值，本工具内置tfidf和doc2vec两种文本向量方法，阈值可作为超参供用户指定输入。   
3、文档向量化方法，有其他的更好的建模方法，如LDA聚类方法、bert向量化、word2vec向量化方法，大家可以在向量化阶段根据实际需求自动地进行向量化模块替换。   
4、singlepass聚类方法对输入数据的顺序、阈值敏感，同一份数据，不同的输入顺序，不同的阈值，会得到不同的聚类结果。   
5、singlepass聚类方法与输入数据的规模敏感，随着簇规模的增加，其计算量会越来越大(新文本要对所有簇中心进行比对)，因此针对大的数据，需要设计高效的选型，如mapreduce或者根据某些特征过滤部分类簇，再进行相识度计算，减少计算量。   
6、在处理具有时间序列信息的聚类任务（如实时热点挖掘）中，单独使用文本语义相似度的方法进行聚类是不够的，还需要考虑时间因素，因此可以在聚类的过程中设置时间聚类的阈值(一个新的超参)， 在么每个聚类中心加入时间片段参数(记录该文簇中文档的第一个时间以及最后一个时间)，如果新加的文本时间大于设定的时间，则不再加入该簇。   
7、具体聚类的效果还与输入语料文本情况有关，不同的语料在同一个算法下，可能得出不同的聚类效果。  

# 关于作者
刘焕勇，liuhuanyong，现任360人工智能研究院算法专家，前中科院软件所工程师，主要研究方向为知识图谱、事件图谱在实际业务中的落地应用。
得语言者得天下，得语言资源者，分得天下，得语言逻辑者，争得天下。  
1、个人主页：https://liuhuanyong.github.io  
2、个人博客：https://blog.csdn.net/lhy2014/  
3、个人公众号：老刘说NLP  
欢迎对自然语言处理、知识图谱、事件图谱理论技术、技术实践等落地应用的朋友一同交流。