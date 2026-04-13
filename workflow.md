# Capstone Workflow

## Research Question: 

1. 在游戏排位赛这样高压的团队配合环境中,如果有人carrier会不会引起更多的social loafing的现象出现
2. 如果有carrier的话会不会导致团队内的经济分配不均匀，导致资源倾斜
3. 前两个RQ的结论是否适用于更理性的团队 -- 职业赛场

## Steps

### Step1: data collection（done）

通过henrikdev的api来爬取游戏相关数据，先通过6个地区天梯榜靠前的玩家进行爬取（这个过程存在player_collection.ipynb中）。然后随机对每个地区进行250个玩家的抽取，每个玩家爬取最近一场数据。再通过爬到的1500场数据每场10个玩家形成一个15000个target_match_dataset.csv,i.e 1500场游戏的数据库(由于North American的服务器同时监管NA/Brazil/Latin America的玩家所以NA的玩家数据大约为7500行)（这个过程存在match_collection.ipynb中）

### Step2: data checking

对于爬取到的数据我需要一个notebook（data_quality_check.ipynb）对target_match_dataset.csv进行一个简单的检查：
* 是否存在重复对局
* 是否有对局存在少于10位玩家（玩家缺失的情况）
* 是否存在有玩家的数据是缺失的

### Step3：first analysis on data

在这个步骤我们需要定义carrier和potential social loafer。

**what is carrier?对于carrier的定义我认为比较直接：**
- ADR(i.e. average damage per round)
- ACS(i.e. average score per round) -- 官方对于表现好坏的直接定义
- KAST -- 官方定义的团队贡献指标
- 击杀数
当一个玩家这些指标显著高于团队内剩余玩家的时候，这个玩家就可以被我们定义为一个carrier

**what is potential social loafer？**
对于这个的定义会比较困难，首先我认为可以进行一波筛选 -- 每个队中的acs倒数前二会被我们特别关注，然后我们设定一个多个维度的指标。
- afk_rounds and stay_in_spawn：这两个指标是最直观的，如果有出现多轮afk或者stay in spawn则是比较直观的摆烂行为
- damage on teammates如果有这个指标的话也是很直观的，如果这个值比较小则可以忽视，但是如果这个值比较大则也是比较直观的摆烂
- KAST -- 官方定义的团队贡献指标，有时即便击杀比较少，但是只要为队伍做出了贡献这个值就不会特别低
- 各技能使用次数, 这个点我做了一个agent_team_skills.csv，在这个表格中标注出了每个agent对团队作用比较大的技能但是如果有玩家不购买并使用这些技能则需要特别关注，特别是有经济但是不买（如果可以体现的话）
- ACS and damage: 因为在valorant的官方定义中acs和damage直接强相关，所以这个也可以作为一个点

只要有人符合这几个指标中的2个及2个以上的指标，则我们就会将其mark为高度疑似的摆烂玩家，这个过程会用carrier_&_loafer.ipynb实现，修改现有的notebook来达成上方的要求。

### Step4:deep seeking

找到那些同时有carrier和摆烂玩家的典型对局，然后写一个函数放在player_searching.ipynb中，我会专注于那些典型对局，然后纵向比对，看他们是否在其他的局中表现良好 -- 这样就可以排除本身就实力不足或者是因为手感不佳导致的数据显现比较差。


