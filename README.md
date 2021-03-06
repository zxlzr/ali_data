# 操作流程 #
### 数据提供 ###
> tianchi_mobile_recommend_train_item.csv
> tianchi_mobile_recommend_train_user.zip

将以上数据上传到odps，表明分别为
> recommend_train_item
> 
> recommend_train_user

**下载odps和dship，进行sql编写和数据上传下载**

## 数据说明 ##
竞赛数据包含两个部分。第一部分是用户在商品全集上的移动端行为数据（D）,表名为tianchi_mobile_recommend_train_user，包含如下字段：
##竞赛题目 
在真实的业务场景下，我们往往需要对所有商品的一个子集构建个性化推荐模型。在完成这件任务的过程中，我们不仅需要利用用户在这个商品子集上的行为数据，往往还需要利用更丰富的用户行为数据。定义如下的符号：

*U——用户集合

*I——商品全集

*P——商品子集，P ⊆ I

*D——用户对商品全集的行为数据集合

那么我们的目标是利用D来构造U中用户对P中商品的推荐模型。

##数据说明
竞赛数据包含两个部分。第一部分是用户在商品全集上的移动端行为数据（D）,表名为recommend_train_user，包含如下字段：
> 
> - 字段 字段说明   
> - user_id用户标识 
> - item_id商品标识 
> - behavior_type  用户对商品的行为类型 
> - user_geohash   用户位置的空间标识，可以为空  
> - item_category  商品分类标识
> - time   行为时间                                           

第二个部分是商品子集（P）,表名为recommend_train_item，包含如下字段： 

> - 字段                             字段说明
> - item_id                         商品标识  
> - item_ geohash                   商品位置的空间标识，可以为空
> - item_category                   商品分类标识 

## 建模流程 ##
1. 位置信息说明，[GEO算法说明](http://www.alivenode.com/index.php/archives/300 "LBS的球面距离计算及Geohash方案探讨")，将位置信息补齐。
2. 提取11月18号到12月16号的行为信息，字段包括user_id,item_id,item_category,geo_hash,click,cart,collect，及12月17号的购买记录信息，并进行采样数据平衡
3. 进行建模，[LR建模](http://oilbeater.com/%E9%98%BF%E9%87%8C%E5%A4%A7%E6%95%B0%E6%8D%AE%E6%AF%94%E8%B5%9B/2014/04/04/the-bigdata-race-3.html "阿里大数据竞赛非官方指南第三弹-- LR入门")

>     import pandas as pd
>     import statsmodels.api as sm  
>     train = pd.read_csv("sample.csv")
>     train_cols = train.columns[1:] 
>     logit = sm.Logit(train['target'], train[train_cols])
>     result = logit.fit()
>     combos = pd.read_csv("vectors.csv")
>     train_cols = combos.columns[2:]
>     combos['prediction'] = result.predict(combos[train_cols])  

将预测模型进行12月17号的购买记录进行验证，及特征调整。并预测12月18号改天的购买。
提取11月18号到12月18号的行为信息，建立预测模型后，预测12月19号的购买。
将预测情况与recommend_train_item进行内关联。

**以上情况，未考虑位置信息。我们有必要先考虑将位置进行分类，后分别进行预测。**




