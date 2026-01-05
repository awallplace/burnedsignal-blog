---
title: "Qingbao Jichu: Cong Yuanshi Shuju Dao Kexing Qingbao"
slug: "intelligence-fundamentals-part-0"
date: 2026-01-05
draft: false
series: "Qingbao Jichu"
series_order: 0
tags: ["intelligence", "OSINT", "SIGINT", "HUMINT", "threat-intelligence", "CTI", "fundamentals", "analysis"]
categories: ["Intelligence"]
keywords: ["intelligence cycle", "DIKW pyramid", "threat intelligence", "OSINT", "SIGINT", "HUMINT", "source reliability", "cognitive bias", "attribution"]
description: "Shenru tanjiu qingbao gongzuo de jichu. Lijie shuju dao zhihui de liucheng, shouji xueke (INT), qingbao zhouqi, laiyuan pinggu, renzhi pianjian, yi ji zhexie gainian ruhe yingyong yu xiandai wangluo weixie qingbao."
author: "burnedsignal"
toc: true
reading_time: "25 min"
---

## TL;DR

Qingbao bujin shi shuju shouji — ta shi jiang yuanshi xinxi xitongdi zhuanhua wei zhidao juece de kexing jianshi. Zhe pian jichu wenzhang hangai:

- **DIKW Jinzita**: Shuju → Xinxi → Zhishi → Zhihui de jinbu
- **Qingbao Cengci**: Zhanlue, Zuozhan he Zhanshu de qubie
- **Shouji Xueke (INT)**: HUMINT, SIGINT, OSINT, GEOINT, MASINT, FININT
- **Qingbao Zhouqi**: Cong xuqiu dao fankui de 6 jieduan lianxu guocheng
- **Laiyuan Pinggu**: Yongyu pingding kekaoxing he kexindu de Haijunbu Xitong
- **Renzhi Pianjian**: Youliang qingbao fenxi de wusheng sharen zhe
- **Guishu Tiaozhan**: Weishenme "shui gan de" bi kan qilai geng nan
- **Yingyong yu Wangluo Weixie Qingbao (CTI)**: Chuantong qingbao gainian ruhe yingshe dao xiandai weixie jiance

---

## Shenme Shi Qingbao?

Qingbao jingchang bei wujie. Ta bujin shi shishi de jilei, ye bu deng tong yu shuju huo xinxi. Qingbao daibiao le yange fenxi guocheng de **jingji chanpin** — jiang fensan de shujudian zhuanhua wei lianguan, you shangxiawen de lijie, shi de renmen neng gou zuo chu mingzhi de juece.

> "Qingbao shi yi jing shouji, zhenghe, pinggu, fenxi he jieshi de xinxi."
> — Joint Publication 2-0, Joint Intelligence

Zhe zhong qubie hen zhongyao. Jiance wangluo liuliang de chuanganqi chansheng **shuju**. Dang gai shuju bei jiexi he guanlian hou, ta chengwei **xinxi**. Dang fenxishi queding liuliang moshi yu yizhi de minglingkongzhi xingwei xiangpipei shi, ta chengwei **zhishi**. Dang lingdao ceng shiyong gai zhishi lai pizhun fangyu cuoshi huo jiang huodong guishu yu teshu de weixie xingweizhe shi, women jiejin le **zhihui**.

**Guanjian Dian**: Qingbao bu shi zhenli — ta shi jiyu bu wanzheng xinxi de zhenli *guji*. Mei ge qingbao chanpin dou dai you guyu de bu queding xing, zhe jiu shi weishenme xinxin shuiping he laiyuan pinggu dui xueke shi jibenxing de.

---

## DIKW Jinzita: Cong Zaosheng Dao Dongcha

Shuju-Xinxi-Zhishi-Zhihui (DIKW) cengji jiegou tigong le yige jichu kuangjia, yong yu lijie yuanshi shuru ruhe zhuanhua wei kexing qingbao.

![DIKW Pyramid](/images/intelligence-fundamentals/dikw-pyramid.svg)

### Shuju (Shenme?)

Yuan de, wei jiagong de shishi, meiyou shangxiawen. Zai wangluo caozuo zhong, zhe baokuo:
- Shuju bao zhuaqu
- Rizhi tiaoma
- Wenjian haxiyezhi
- IP dizhi
- DNS chaxun

Dan dushuju huida "Fasheng le shenme?", dan bu tigong yiyi.

**Shili**: `192.168.1.105 connected to 45.33.32.156:443 at 03:42:17 UTC`

### Xinxi (Shui, Shenme Shihou, Nali?)

Dang shuju bei zuzhi, jiegouhua bing shouyu shangxiawen shi, shuju chengwei xinxi. Xinxi huida jiben wenti.

**Shili**: "Gongzuozhan WS-105, fenpei gei Caiwu bumen de yonghu john.doe, zai fei gongzuo shijian yu gelokalihua dao Eluosi Shengbidebao de IP dizhi jianli le jiami lianjie."

### Zhishi (Ruhe? Weishenme?)

Zhishi cong fenxi moshi, guanlian duoge xinxi laiyuan he yingyong zhuanye zhishi zhong chansheng. Ta tigong dui jizhi he dongji de lijie.

**Shili**: "Zhe zhong lianjie moshi yu yizhi de Cobalt Strike xinhao xingwei xiangpipei. Mubiao IP yu yiqian guishu yu APT29 de jichu sheshi xiangguan. Caiwu bumen keyi fangwen mingan de M&A wendang. Zhe keneng daibiao yige fuzhi weixie xingweizhe jinxing jingji jiandie de chushi fangwen."

### Zhihui (Women Yinggai Zenme Zuo?)

Zhihui jiang zhishi yu jingyan, daode kaolü he zhanlue shangxiawen xiang zonghe, yi zhi dao juece. Ta shixian yuejian he zuiyou xingdong xuanze.

**Shili**: "Jiyu guishu xinxin shuiping (ZHONGDENG), qiankai de yewu yingxiang he diyuan zhengzhi shangxiawen, women jianyi: (1) li ji geli shouyingxiang xitong de wangluo, (2) qiyong shijian xiangying, (3) tongzhi falü guwen guanyu qianzai guojia canyu de keneng xing, (4) yu keneng mianlin leisi mubiaodingwei de hangye ISAC huoban xietiao."

---

## Kexing Qingbao de Si Ge Pinzhi

Bing fei suoyou qingbao dou shi pingdeng de. Youxiao de qingbao zhanxian si ge guanjian tedian:

| Pinzhi | Dingyi | Fan Shili |
|--------|--------|-----------|
| **Kexing** | Shixian juti juece huo xingdong | "Huai ren cunzai yu hulianwang shang" |
| **Jishi** | Zai reng neng yingxiang jieguo shi jiaofai | Guishu baogao zai ru qin 6 ge yue hou daoda |
| **Xiangguan** | Jiejue xiaofei zhe de juti xuqiu | Jiang ICS/SCADA weixie qingbao fasong gei SaaS gongsi |
| **Zhunque** | Shishi zhengque, xinxin shuiping shidang | Cuowu di jiang shangpin eyi ruanjian guishu yu APT |

Suoxie **ATRA** (Actionable, Timely, Relevant, Accurate) tigong le yige youyong de jiyi fa lai pinggu qingbao zhiliang.

---

## Laiyuan Pinggu: Haijunbu Xitong

![Source Reliability Matrix](/images/intelligence-fundamentals/source-reliability-matrix.svg)

Zai renhe xinxi chengwei qingbao zhiqian, bixu dui qi jinxing pinggu. Qingbao jie shiyong biaozhunhua de fengshu xitong lai pinggu **laiyuan de kekaoxing** he **xinxi de kexindu**.

### Haijunbu/NATO Xitong

Zhe zhong shuangzhou pinggu xitong, ye chengwei "6x6 xitong" huo "NATO Xitong," zi Di Er Ci Shijie Dazhan yilai yizhi shi biaozhun:

#### Laiyuan Kekaoxing (A-F)

| Dengji | Miaoshu | Biaozhun |
|--------|---------|----------|
| **A** | Wanquan Kekao | Dui laiyuan de zhenshixing, kekaoxing he nengli meiyou yiwen. Wanquan kekaoxing de lishi. |
| **B** | Tongchang Kekao | Shao liang yiwen. Dabufen youxiao xinxi de lishi. |
| **C** | Xiangdang Kekao | Cunzai yiwen. Guoqu tigong guo youxiao xinxi. |
| **D** | Tongchang Bu Kekao | Zhongda yiwen. Bufen youxiao, bufen wuxiao xinxi de lishi. |
| **E** | Bu Kekao | Quefa zhenshixing, kekaoxing huo nengli. Wuxiao xinxi de lishi. |
| **F** | Wufa Panduan | Meiyou pinggu kekaoxing de jichu. Xin huo weizhi laiyuan. |

#### Xinxi Kexindu (1-6)

| Dengji | Miaoshu | Biaozhun |
|--------|---------|----------|
| **1** | Yi Queren | Yi you duli laiyuan queren. Heli, yu qita xinxi yizhi. |
| **2** | Keneng Weizhen | Wei queren, dan heli bing yu qita xinxi yizhi. |
| **3** | Keneng Zhengque | Wei queren. Xiangdang heli dan yanzhen youxian. |
| **4** | Zhide Huaiyi | Wei queren. Keneng dan bu heli. Meiyou qita zhichi xinxi. |
| **5** | Bu Da Keneng | Wei queren. Bu heli. Yu qita xinxi xiangmaodun. |
| **6** | Wufa Panduan | Meiyou pinggu kexindu de jichu. |

---

## Renzhi Pianjian: Wusheng Sharen Zhe

![Cognitive Biases](/images/intelligence-fundamentals/cognitive-biases.svg)

Qingbao shibai hen shao shi yinwei quefa xinxi — ermen shi fenxi shibai. Renzhi pianjian shi yingxiang juece he panduan de xitong siwei cuowu.

> "Women kan dao de shi women qiwang kan dao de, er bu shi zhen zheng cunzai de."
> — Richards Heuer, *Psychology of Intelligence Analysis*

### Qingbao Fenxishi de Guanjian Pianjian

#### Queren Pianjian
**Dingyi**: Xunqiu, jieshi he jiyi queren yuan you xinnyuàn de xinxi.

**Lishi Shili**: Yilake WMD Pinggu (2002-2003). Fenxishi zhuanzhu yu zhichi WMD jihua cunzai de xinxi, er hushi le maodun zhengju.

**Huanjie**: Mogui Bianhuzhe, Jingzheng Jiashe Fenxi (ACH)

---

#### Maoding Pianjian
**Dingyi**: Guodu yilai shouci huode de xinxi ("maodin").

**CTI Shili**: Dui tedu weixie xingweizhe de chushi guishu chengwei maoding. Suoyou houxu zhengju dou tonguo gai jinghuang jin xing jieshi.

**Huanjie**: Mingque jilu chushi jiashe, dingqi chongxin shenshi

---

#### Jingxiang Yingshe
**Dingyi**: Jiading duishou de sikao he xingdong fangshi yu women zai qi qingkuang xia de fangshi xiangtong.

**Lishi Shili**: Zhenzhuwan (1941). Meiguo fenxishi renwei Riben bu hui jingong, yinwei zai Meiguo junshi youshi xia zhe jiang shi "bu heli de". Tamen meiyou lijie Riben de zhanlue suanpan.

**Huanjie**: Hong Dui Fenxi, Wenhua zhuanye zhishi

---

#### Qunti Siwei
**Dingyi**: Qunti nei de congzhong yali yizhi le butong yijian he tixua fenxi.

**Lishi Shili**: Zhu Wan (1961). CIA guihuazhe shuofu zijii rudeng jiang hui chenggong; butong yijian bei yayin huo paichi.

**Huanjie**: Jiegouxua yiyi (zhiding "mogui bianhuzhe" juese)

---

#### Keyongxing Qifa
**Dingyi**: Guodu zhongshi rongyi xiangqi de xinxi (zuijin, xiju huo qinzi jingli de shijian).

**CTI Shili**: Zai yici zhiming ransomware gongjii hou, fenxishi keneng hui yin wei gai weixie chu yu toumu er guodu jiang houxu shijian guishu yu tongyii xingweizhe.

**Huanjie**: Jibenlü fenxi, jiegouxua jianchadan

---

### Jiegouhua Fenxi Jishu (SAT)

Qingbao jie zhuanmen kaifa le SAT lai yingdui renzhi pianjian:

| Jishu | Mudi | Shenme Shihou Shiyong |
|-------|------|----------------------|
| **Jingzheng Jiashe Fenxi (ACH)** | Xitong di pinggu duoge jieshi yu zhengju de guanxi | Guishu, fuza pinggu |
| **Guanjian Jiashe Jiancha** | Shibie he jiancha qianzai jiashe | Renhe zhongda pinggu |
| **Hong Dui Fenxi** | Xiang duishou yiyang sikaov | Weixie pinggu, loutu fenxi |
| **Mogui Bianhuzhe** | Fandui zhuliu guandian | Zai zuizhong queding pinggu zhiqian |
| **Zhibiao he Jinggao (I&W)** | Dingyi biaoming bianhua de ke guancha shijian | Jiance, yuce |

---

## Qingbao Cengci: Zhanlue, Zuozhan, Zhanshu

Qingbao xuqiu he chanpin genju xiaofei zhe de juece shiyu er you hen da chayi.

![Intelligence Levels](/images/intelligence-fundamentals/intelligence-levels.svg)

### Zhanlue Qingbao
- **Shouxhong**: Gaoguan, Dongshihui, Zhengce Zhiding Zhe
- **Shiyu**: 12-36 ge yue
- **Jiaodian**: Weixie qushii, fengxian taishi, touzi youxianji
- **Shili**: "Minzuxuejia yuelaiyue duo di mubiao women hangye de gongyinglian. Women jianyi shi gongyingshang duoyanghua."

### Zuozhan Qingbao
- **Shouxhong**: Anquan Jingli, IR Tuandui, Liesha Tuandui
- **Shiyu**: Ji zhou dao ji ge yue
- **Jiaodian**: Huodong fenxi, weixie xingweizhe tuxiang, TTP yanhua
- **Shili**: "APT41 zai Q4 cong dingzhi eyi ruanjian zhuanxiang zaidili jishu. Liesha tuandui yinggai youxian jiance LOLBins lanyong."

### Zhanshu Qingbao
- **Shouxhong**: SOC Fenxishi, Jiance Gongchengshi, Shijian Xiangying Zhe
- **Shiyu**: Ji xiaoshi dao ji tian
- **Jiaodian**: IOC, jiance tezheng, juti TTP
- **Shili**: "Zudang hash `abc123...`, IP `45.33.32.156`, bing jiance shiyong `schtasks.exe /create` de jihua renwu chijiuxing."

**Guanjian Dongcha**: Zuzhi jingchang guodu touzi zhanshu qingbao (IOC fid) er duixu touzi zhanlue he zuozhan qingbao. IOC benshen yi xiao — tamen daibiao gongjii de *rengongzhi pin*, er bu shi *xingwei*.

---

## Qingbao Shouji Xueke ("INT")

Qingbao shouji zuzhi cheng zhuanmen xueke, mei ge xueke dou you butong de laiyuan, fangfa he fenxi xuqiu.

![Intelligence Disciplines](/images/intelligence-fundamentals/intelligence-disciplines.svg)

### Zhuyao Shouji Xueke

#### HUMINT (Ren Yuan Qingbao)

**Dingyi**: Tonguo renjijiefang cong renlei laiyuan huode de qingbao.

**Laiyuan**:
- Xianren he tejei
- Waijiao baogao
- Lüxingzhe huibao
- Pantao zhe
- Shenxun
- Yinci (wangluo shangxiawen zhong de shehui gongcheng)

**Tedian**:
- Zui gǔlao de qingbao xueke
- Tigong yitu he dongji ("weishenme")
- Gao jiazhi, gao fengxian
- Nan yi kuozhan

**Wangluo Yingyong**: Zai dixia luntan yu weixie xingweizhe hudong, zai fanzui zuzhi nei zhaoxu laiyuan, shehui gongcheng pinggu.

---

#### SIGINT (Xinhao Qingbao)

**Dingyi**: Cong jieting xinhao (baokuo tongxin he dianzi fance) huode de qingbao.

**Zi Xueke**:

| Suoxie | Mingcheng | Jiaodian |
|--------|----------|----------|
| **COMINT** | Tongxin Qingbao | Yuyin, wenben, xiaxi jieting |
| **ELINT** | Dianzi Qingbao | Fei tongxin xinhao (leida, yaokong) |
| **FISINT** | Waiguo Yiqi Xinhao Qingbao | Wuqi xitong, hangtian qi yaokong |

**Tedian**:
- Jishu, ke kuozhan de shouji
- Shuliang chansheng fenxi tiaozhan
- Jiami shi zhongda zhangai

**Wangluo Yingyong**: Wangluo liuliang fenxi, eyi ruanjian C2 xieyi fenxi, jiami liuliang yuanshuju fenxi.

---

#### OSINT (Kaiyuan Qingbao)

**Dingyi**: Cong gonggong ke yong laiyuan huode de qingbao.

**Laiyuan**:
- Meiti (yinshua, guangbo, zaixian)
- Xueshu chubanwu
- Zhengfu baogao
- Shejiao meiti
- Shangye shujuku
- Jishu wendang

**Tedian**:
- Ke huode qie chengben you xiao
- Shuliang xuqiu fuzha guolü
- Yanzheng tiaozhan (xu xinxi, jiaxu xinxi)
- Guji tigong 60-80% de qingbao xuqiu

**Wangluo Yingyong**: Weixie xingweizhe yanjiu, loutu qingbao, xielu pingzheng jiance, pinpai baohu, gongjii mianji yingshe.

---

#### GEOINT (Dikong Qingbao)

**Dingyi**: Cong tuxiang he dikong xinxi fenxi zhong huode de qingbao.

**Laiyuan**:
- Weixing tuxiang
- Hangkong sheying
- Ditu shuju
- Jiyu weizhi de fuwu
- Dilidingwei yuanshuju

**Wangluo Yingyong**: Wuli jichu sheshi yingshe, shuju zhongxin shibie, gongyinglian yanzheng, guishu zhichi.

---

#### MASINT (Celiang he Tezheng Qingbao)

**Dingyi**: Cong chuanganqi huode de shuju fenxi zhong huode de qingbao, yong yu shibie yu laiyuan xiangguan de dute tezheng.

**Jiaodian Lingyu**:
- Leida tezheng
- Shengxue tezheng
- He fushe
- Huaxue/Shengwu jiance

**Wangluo Yingyong**: Dianchi fuase fenxi (TEMPEST), cexinxu gongjii, yingjian zhiwen.

---

### Qita Xueke

| INT | Quancheng | Jiaodian |
|-----|-----------|----------|
| **FININT** | Jinrong Qingbao | Huobi jiaoyi, zhicai taopi, xiqian, jiami huobi zhuizong |
| **SOCMINT** | Shejiao Meiti Qingbao | Shejiao pingtai fenxi, yingxiang xingdong |
| **TECHINT** | Jishu Qingbao | Waiguo wuqi fenxi, eyi ruanjian ni xiang |
| **IMINT** | Tuxiang Qingbao | GEOINT de zi ji, zhuanzhu yu tuxiang fenxi |
| **MEDINT** | Yiliao Qingbao | Yu jiankang xiangguan de qingbao, shengwu weixie |

---

## Qingbao Zhouqi

Qingbao zhouqi miaoshu le jiang yuanshi xinxi zhuanhua wei wancheng qingbao bing jiaofai gei xiaofei zhe de guocheng.

![Intelligence Cycle](/images/intelligence-fundamentals/intelligence-cycle.svg)

### Jieduan 1: Xuqiu (Guihua he Zhidao)

Qingbao cong yige wenti kaishi. Xuqiu dingyi:
- **Youxian Qingbao Xuqiu (PIR)**: Lingdao ceng xuqiu huida de guanjian wenti
- **Jiben Xinxi Yaosu (EEI)**: Jiejue PIR suo xu de juti shujudian
- **Shouji zhongdian**: Youxian kaolü naxie INT
- **Qingbao Quefa**: Women bu zhidao dan xuqiu zhidao de

**PIR Shili**: "Na xie weixie xingweizhe zui keneng zai weilai 12 ge yue nei duixu women zuzhi, tamen shiyong shenme TTP?"

### Jieduan 2: Shouji

Shiyong shidang INT xitong shouji yuanshi xinxi. Youxiao shouji:
- Yu yidingyi de xuqiu yizhi
- Caiyong duoge INT jinxing yanzheng
- Jilu laiyuan he fangfa
- Baochi jianguanlian

### Jieduan 3: Chuli (Kaifa)

Jiang yuan shouji cailiao zhuanhua wei ke yong xingshi:
- Fanyi
- Jiemi
- Geshi zhuanhuai
- Shuju biaozhunhua
- Quchong
- Chubu yanzheng
- Laiyuan pinggu

### Jieduan 4: Fenxi (Zhizao)

Qingbao gongzuo de zhili hexin. Fenxi baokuo:
- Duoge laiyuan guanlian
- Moshi shibie
- Jiashe kaifa he ceshi
- Yingyong Jiegouhua Fenxi Jishu (SAT)
- Kekaoxing he kexindu pinggu
- Xinxin shuiping zhiding
- Shengchan wancheng de qingbao chanpin

### Jieduan 5: Chuanbo

Wancheng de qingbao bixu tonguo shidang geshi he anquan qudao daoda xiaofei zhe:
- Shumian baogao
- Jinggao he tongzhi
- Jiqji ke du fid (STIX/TAXII)
- Yibiaopan he keshihua
- Koutu jianshu

**Guanjian Yuanze**: Bu neng zai zhengque shijian daoda zhengque ren de qingbao shi wuyong de.

### Jieduan 6: Fankui (Pinggu)

Xiaofei zhe tigong guanyu yixia neirong de fankui:
- Yu qi xuqiu de xiangguan xing
- Jiaofai de jishi xing
- Jianyi de kexing xing
- Zhunquexing (suizhe shijian tuiyi)

Zhe zhong fankui youhua weilai xuqiu, wancheng zhouqi.

---

## Guishu: Xinxin Puxi

Guishu — queding shui dui xingdong fuze — shi qingbao gongzuo zhong zui junnai de fangmian zhiyi, tebie shi zai wangluo lingyu.

### Weishenme Guishu Hen Nan

| Tiaozhan | Miaoshu |
|----------|---------|
| **Xu Qi** | Duishou guyi zhongzhi zhixiang qita ren de zhengju |
| **Gonggong Gongju** | Duoge xingweizhe shiyong xiangtong de eyi ruanjian jiacu (Cobalt Strike, Mimikatz) |
| **Daili Xingdong** | Chengebao shang, gufeifan yayin le zhenzheng de zanzhizhe |
| **Jichu Sheshi Chongdie** | Gonggong tuoguan, bulletproof tigongshang, beigongji xitong |

### Guishu Xinxin Shuiping

| Shuiping | Miaoshu | Jichu |
|----------|---------|-------|
| **GAO** | Women yi gao xinxin pinggu... | Duoge duli laiyuan, kuaqu INT de yanzheng zhengju |
| **ZHONG** | Women yi zhong xinxin pinggu... | Jiaoshao laiyuan de lianghao zhengju, yixie fenxi quefa |
| **DI** | Women yi di xinxin pinggu... | Youxian zhengju, zhongda quefa, ke neng dan bu queding |

**Guanjian Dian**: Jishi "GAO xinxin" ye bu shi queding. 2002 nian Yilake WMD NIE yi "gao xinxin" pinggu Yilake you WMD jihua — jieguo shi cuowu de.

---

## Yingyong yu Wangluo Weixie Qingbao (CTI)

Chuantong qingbao kuangjia zhijie yingshe dao wangluo weixie qingbao:

| Chuantong Gainian | CTI Yingyong |
|-------------------|--------------|
| PIR | "Naxie ransomware tuandui mubiao women hangye?" |
| HUMINT | Anxu luntan hudong, neibu weixie jihua |
| SIGINT | Wangluo liuliang fenxi, C2 xieyi yanjiu |
| OSINT | Weixie xingweizhe boke, paste zhandian, daima ku |
| GEOINT | Jichu sheshi dingwei, shuju zhusuo heguixing |
| Laiyuan Pinggu | Weixie fid zhiliang pinggu, zhibiao xinxin |

### Tongku Jinzita

David Bianco de Tongku Jinzita (2013) shuoming le zhibiao leixing yu dang zheixie zhibiao bei jujue shi duishou de chengben zhijian de guanxi:

![Pyramid of Pain](/images/intelligence-fundamentals/pyramid-of-pain.svg)

| Cengci | Zhibiao Leixing | Duishou Tongku | Beizhu |
|--------|----------------|----------------|--------|
| **Ding** | TTP | HEN NAN! | Bixu gaibian xingwei, jiqiao |
| | Gongju | Kunnan | Bixu zhaodao/kaifa xin gongju |
| | Zhuji Rengongzhi Pin | Fanren | Zhuce jian, wenjian lujing, huchisuoce |
| | Wangluo Rengongzhi Pin | Fanren | URI moshi, C2 xieyi, JA3 |
| | Yu Ming | Jiandan | DNS pianyi, dan xuqiu shezhi |
| | IP Dizhi | Rongyi | Jichu sheshi ke huihuan |
| **Di** | Hash Zhi | Weibuzudao | Chongxin bianyi = xin hash |

**Guanjian Dongcha**: Zhuanzhu yu Zhanshu, Jishu he Chengxu (TTP) er fei yuanzi zhibiao de zuzhi wei duishou chuangzao le xianxu geng gao de chengben. Zhe yu MITRE ATT&CK de jiyu xingwei fangfa yizhi.

---

## Anli Yanjiu: Cong Shuju Dao Qingbao

Rang women zhuizong yuanshi shuju ruhe tonguo zhengque de fenxi guocheng chengwei kexing qingbao:

### Shuju

```
timestamp: 2024-11-15T03:42:17Z
src_ip: 192.168.1.105
dst_ip: 45.33.32.156
dst_port: 443
bytes_out: 2847
bytes_in: 156892
```

### Xinxi

- **Laiyuan**: Gongzuozhan WS-105 (Caiwu, yonghu: john.doe)
- **Mubiao**: IP dingwei yu Eluosi Shengbidebao
- **Tigongshang**: You jilu de lanyong lishi de VPS tigongshang
- **Shijian**: 03:42 UTC (Meiguo Dongbu feigongzuo shijian)
- **Liuliang Moshi**: Xiao qingqiu, da xiangying (keneng de shuju waixi huo xinhao dengjii)

### Fenxi Guocheng

**Buzhou 1: Laiyuan Pinggu**
- Wangluo yaokong: A1 (women ziji de chuanganqi, yuan shuju)
- Mubiao IP de weixie qingbao: B2 (youxin yangshangjia, wei duli queren)
- ISAC baogao: C2 (tongxing baogao, youxian xiangqing)

**Buzhou 2: Jiashe Shengcheng**
1. Eyi C2 tongxin (APT)
2. Eyi C2 tongxin (fanzui)
3. Hefa dan yichang de yewu huodong
4. Beigongji de disanfang yingyong
5. Jia yangxing (CDN, yun fuwu)

**Buzhou 3: Zhengju Pinggu**

| Zhengju | H1 (APT) | H2 (Fanzui) | H3 (Hefa) | H4 (Disanfang) | H5 (FP) |
|---------|----------|-------------|-----------|----------------|---------|
| Eluosi IP | ++ | + | - | N | N |
| Feigongzuo Shijian | + | + | - | N | N |
| Liuliang Moshi | ++ | ++ | N | + | - |
| Xianqian Diaoyou | ++ | + | -- | N | -- |
| ISAC Guanlian | ++ | N | -- | N | -- |
| Caiwu Bumen Mubiao | ++ | + | N | N | N |

**Buzhou 4: Pinggu**

Zai yingyong ACH fangfalun hou:

> Women yi **ZHONG xinxin** pinggu, ci huodong daibiao fuzha weixie xingweizhe de minglingkongzhi tongxin, keneng shi APT29 huo guanlian shiti. Ci pinggu jiyu: yu xianqian guishu caozuo de jichu sheshi guanlian (B2), yu jilu de guojiaxuejia shouce de TTP yizhixing, yi ji yu jingji jiandie mubiao de mubiao yizhixing.

### Zhihui

**Jianyi**:
1. Qidong shijian xiangying chengxu (GAO youxianji)
2. Baocun fafa zhengju, yi bei qianzai de zhifa hudong
3. Jiuyu guojia guishu yiyi, xiezhu falü guwen
4. Yu hangye ISAC xietiao
5. Xiang zhixing lingdao ceng jianshu qianzai de yewu yingxiang
6. Kuazhan zhengge qiye de xiangguan TTP jiance

---

## Zhanwang Weilai

Zhe pian jichu wenzhang jianli le qingbao caozuo de gainian kuangjia. Xilie zhong de houxu wenzhang jiang tigong geng shen ru de tanjiu:

- **Di 1 Bufen**: OSINT Shen Qian — Laiyuan, Gongju, Jiqiao he Falü Kaolü
- **Di 2 Bufen**: SIGINT Jichu — Cong Wuxiandian Dao Shuju Bao
- **Di 3 Bufen**: Wangluo Caozuo Zhong de HUMINT — Shehui Gongcheng Ji Qita
- **Di 4 Bufen**: Wangluo de GEOINT — Wuli-Shuzi Ronghe
- **Di 5 Bufen**: Jiegouhua Fenxi — ACH, Hong Dui he Renzhi Pianjian Huanjie

---

## Shuyu Biao

| Shuyu | Dingyi |
|-------|--------|
| **ACH** | Jingzheng Jiashe Fenxi — pinggu duoge jieshi de jiegouhua jishu |
| **COMINT** | Tongxin Qingbao — SIGINT de zi ji |
| **CTI** | Wangluo Weixie Qingbao |
| **EEI** | Jiben Xinxi Yaosu |
| **ELINT** | Dianzi Qingbao — SIGINT de zi ji |
| **FININT** | Jinrong Qingbao |
| **GEOINT** | Dikong Qingbao |
| **HUMINT** | Ren Yuan Qingbao |
| **I&W** | Zhibiao he Jinggao |
| **IOC** | Tuoxie Zhibiao |
| **ISAC** | Xinxi Gongxiang he Fenxi Zhongxin |
| **MASINT** | Celiang he Tezheng Qingbao |
| **OSINT** | Kaiyuan Qingbao |
| **PIR** | Youxian Qingbao Xuqiu |
| **SAT** | Jiegouhua Fenxi Jishu |
| **SIGINT** | Xinhao Qingbao |
| **TTP** | Zhanshu, Jishu he Chengxu |

---

## Cankao Wenxian he Yuedu Tuijian

### Guanfang Chubanwu
- Joint Publication 2-0: Joint Intelligence (US DoD)
- Intelligence Community Directive 203: Analytic Standards
- NIST SP 800-150: Guide to Cyber Threat Information Sharing

### Jichu Wenben
- Heuer, R. (1999). *Psychology of Intelligence Analysis*
- Lowenthal, M. (2019). *Intelligence: From Secrets to Policy*
- Clark, R. (2019). *Intelligence Analysis: A Target-Centric Approach*

### CTI Ziyuan
- MITRE ATT&CK Framework: https://attack.mitre.org
- Bianco, D. (2013). *The Pyramid of Pain*: https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html
- STIX/TAXII Standards: https://oasis-open.github.io/cti-documentation/
- FIRST Traffic Light Protocol: https://www.first.org/tlp/

---

*Zhe pian wenzhang shi Qingbao Jichu xilie de yibufen. Gai xilie zhizai jiehe chuantong qingbao jiqiao yu xiandai wangluo weixie qingbao caozuo.*
