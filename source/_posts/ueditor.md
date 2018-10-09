---
title: 百度富文本编辑器ueditor在vue中的使用指南
date: 
tags: ueditor
categories: 
- 前端
---

**本文主要讲的是在vue搭建的后台管理系统项目中如何使用ueditor，以及在使用过程中遇到的问题和解决方案。主要从以下几个方面：**

> 1.引用步骤
> 2.封装vue ueditor的步骤
> 3.ueditor单张图片上传到的服务器地址不能跨域，所以只能修改源码来实现上传图片到阿里云服务器。
> 4.过程中遇到的问题
> 5.缺点／优点总结

<!-- more -->
<br>

###  一. [ueditor官网](https://ueditor.baidu.com/website/index.html)下载ueditor源码。

由于后端开发使用的是php，所以使用的是1.4.3.3的php开发版本。

1.将源码放入前端项目中。

![项目目录图片](/images/ueditor_01.jpg)

2.源码中php文件夹（后端配置部分）由后端开发按需修改后放到服务器上。

3.前端项目中修改ueditor.config.js中涉及到路径的配置的部分。

![路径配置部分的修改](/images/ueditor_config.jpg)

> 注：UEDITOR_HOME_URL为前端项目中源码的路径；serverUrl为服务端ueditor配置项部分源码的路径。

<!-- **注：UEDITOR_HOME_URL为前端项目中源码的路径；serverUrl为服务端ueditor配置项部分源码的路径。** -->

<br>

### 二. 将ueditor封装为vue组件

1.ueditor组件 —— ueditor.vue代码

```
<template>
    <div class="ueitor-wrapper">
        <script id="ueditor" name="content" type="text/plain"></script>
    </div>
</template>

<script>
import '../../static/ueditor_1.4.3.3/ueditor.config'
import '../../static/ueditor_1.4.3.3/ueditor.all'

export default {
    name: 'ueditor',
    data() {
        return {
            ue: null,
            defaultConfig: {
                initialFrameWidth: '100%',
                initialFrameHeight: ((document && document.body && document.body.offsetHeight) || 700) - 300,
                autoHeightEnabled: false,
                autoFloatEnabled: false // 取消工具栏悬浮
            }
        }
    },
    components: {
        mixConfig() {
            return Object.assign(this.defaultConfig, this.config)
        }
    },
    props: {
        value: String,
        config: {
            type: Object,
            default: () => { }
        },
        destroyed: Boolean,
        defaultValue: String,
        hasDefaultValue: Boolean
    },
    watch: {
        defaultValue: {
            handler(val) {
                this.initUeditor()
            }
        }
    },
    methods: {
        initUeditor() {
            this.$nextTick(() => {
                this.ue = window.UE.getEditor('ueditor', this.mixConfig)
                this.ue.addListener('ready', () => {
                    this.ue.focus()
                    this.$emit('ready', this.ue)
                    this.ue.setContent(this.defaultValue)
                    this.ue.addListener('contentchange', () => {
                        this.$emit('input', this.ue, this.ue.getContent())
                    })
                })
            })
        }
    },
    mounted() {
        if (!this.hasDefaultValue) {
            this.initUeditor()
        }
    },
    destroyed() {
        if (this.ue && this.destroyed) {
            this.ue.destroy()
        }
    }
}
</script>

<style lang="less">
</style>
```
<br>
2.父组件引用代码

```
<template>
    <div id="add-article">
        <ueditor class="article-item" v-model="content" :hasDefaultValue="hasDefaultValue" :defaultValue="defaultContent" :destroyed="true" @ready="ready" @input="contentChange"></ueditor>
        <div class="article-item preview" v-html="content"></div>
    </div>
</template>

<script>
import Ueditor from '../components/ueditor.vue'

export default {
    data() {
        return {
            ue: null,
            content: '',
            defaultContent: ''
        }
    },
    components: {
        Ueditor
    },
    computed: {
        hasDefaultValue() {
            if (this.$route.query.id) {
                return true
            }
            return false
        }
    },
    methods: {
        ready(ue) {
            this.ue = ue
        },
        contentChange(ue, value) {
            this.ue = ue
            this.content = value
        },
        getArticleDetails() {
            // 模拟接口调用
            setTimeout(() => {
                this.defaultContent = '<section><section><section><p style="text-align: justify; line-height: 2em; margin-top: 25px;"><strong><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(63, 63, 63);">导读：</span></strong></p><p style="text-align: justify; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(63, 63, 63);">宫颈癌是女性生殖系统最常见的恶性肿瘤之一，在发展中国家已成为女性中仅次于乳腺癌的第二杀手。宫颈癌发病率呈现逐年上升趋势，死亡率下降不明显，不孕的妇女做IVF助孕前常规会做宫颈癌筛查。</span></p><p style="text-align: center; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(63, 63, 63);"><img src="http://futurefertile.oss-cn-hangzhou.aliyuncs.com/15373426556398.jpg" title="" alt="" width="250" height="170"/></span></p><p style="text-align: center; line-height: 2em; margin-top: 25px;"><strong><span style="background-color: rgb(146, 205, 220); color: rgb(255, 255, 255);">为什么IVF前做宫颈癌筛查</span></strong></p></section></section></section><section><p style="text-align: left; line-height: 2em; margin-top: 25px;"><img src="/ueditor/php/upload/image/20180919/1537342551849975.png" title="" alt=""/><span style="color: rgb(63, 63, 63); font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; text-align: justify;">国际癌症研究署（IARC）2012年数据表明全球每年新发宫颈癌病例52.8万、死亡26.6万，其中约85%发生在发展中国家。2015年中国子宫颈癌新发病例9.89万，死亡3.05万人。我国从2009年启动了“农村妇女子宫颈癌检查项目”至今已有6000万(35～64岁妇女)参与。</span></p></section><section><p style="text-align: justify; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(63, 63, 63);">目前常用的子宫颈癌筛查、分流、转诊技术有子宫颈脱落细胞学（TCT检查）、人乳头瘤病毒（HPV）检测、醋酸染色试验（VIA）、p16/ki-67双染和阴道镜检查等。《2013 年WHO宫颈癌筛查及处理方案指南》推荐3种筛查方法：</span></p><p style="text-align: justify; line-height: 2em; margin-top: 25px;">HPV检测<br/></p><p style="line-height: 2em; margin-top: 25px;">细胞学</p><p style="line-height: 2em; margin-top: 25px;">宫颈醋酸染色试验（VIA）</p><p style="line-height: 2em; margin-top: 25px; text-align: center;"><img src="http://futurefertile.oss-cn-hangzhou.aliyuncs.com/15373426822531.jpg" title="" alt="" width="250" height="150"/></p><p style="text-align: justify; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(63, 63, 63);"></span><strong style="color: rgb(146, 205, 220); font-family: 微软雅黑, &quot;Microsoft YaHei&quot;;">一、HPV 检测</strong></p><section><section></section></section><p style="text-align: justify; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(63, 63, 63);">HPV是指人乳头瘤病毒，HPV是有很多亚型，其中有些与恶性肿瘤的发生有关，称为高危型HPV。IARC发布常见的高危型有：16、18、31、33、35、39、45、51、52、56、58、59共12个型别；疑似高危型有：26、53、66、67、68、70、73、82共8个型别；低危型有：6、11等。其中HPV16和18亚型与恶性肿瘤的发生最为密切。</span></p><p style="text-align: justify; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(63, 63, 63);">HPV感染很常见，但绝大多数（超过80%）HPV感染在8个月内自然清除。只有少数持续性的高危型HPV感染才有可能致癌。但HPV检测值高低和病变严重程度之间无绝对对应关系。即HPV值检测数值越高，并不完全说明病变越严重。年龄＜30岁的妇女中2年91%的清除率；＞30岁妇女中79%～80%是一过性感染，这说明HPV感染自然转阴率与年龄相关。当年轻妇女无法清除HPV时便进入持续感染阶段，处于持续感染状态的妇女随着年龄增大，感染比例也增加，所以对年龄较大、或性生活时间较长的妇女进行HPV检测更有价值。HPV检测结果比较客观，受人为因素小，重复性好。</span></p><p style="text-align: left; line-height: 2em; margin-top: 25px;"><span style="color: rgb(63, 63, 63); font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; text-align: justify;">对于宫颈癌，HPV疫苗是一级预防，宫颈癌筛查是二级预防。目前，HPV疫苗有HPV二价疫苗、四价疫苗和九价疫苗。HPV四价疫苗：HPV6、11、16、18型，主要针对20～45岁女性；二价疫苗：HPV16、18型，主要针对9～45岁女性；九价疫苗：6、11、16、18、31、33、45、52、58型，主要针对16～26岁女性。</span><span style="text-align: left; font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; color: rgb(63, 63, 63);"><img src="/ueditor/php/upload/image/20180918/1537243950459736.png" title="" alt=""/></span><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(63, 63, 63);"><img src="/ueditor/php/upload/image/20180919/1537342552445290.png" title="" alt=""/></span></p><p style="text-align: justify; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(63, 63, 63);">建议人群接种：13～15岁女孩；接种程序：0、1(2)、6月。HPV疫苗接种后，根据特定年龄的推荐方案同非疫苗接种者一样定期接受子宫颈癌筛查。建议妊娠妇女孕前进行子宫颈癌筛查，或在第一次产检时进行筛查。</span></p><section><p style="text-align: justify; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(146, 205, 220);"><strong>二、细胞学</strong></span></p><section></section></section><p style="text-align: justify; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(63, 63, 63);">通过对宫颈口部位脱落细胞进行分类诊断，观察细胞学形态改变，以此判断细胞是否存在异常，如癌前病变、病原微生物感染、以及癌变。常见的为液基薄层细胞检测（TCT检查）和巴氏染色。</span></p><p style="text-align: justify; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(63, 63, 63);">TCT检查如果为ASCUS，应结合高危型HPV检查结果来判断：如果高危型HPV阳性，建议行阴道镜评估；如果高危型HPV阴性，建议复查。TCT结果ASC-H、LSIL、HSIL、AGC等，均应行阴道镜评估。巴氏涂片染色共分为5级，I级正常，II级炎症引起，III级可疑，IV级可疑阳性，V级阳性。III、IV、V级涂片者应重复刮片行宫颈活组织检查，II级涂片先按炎症处理后重复涂片进一步检查。</span></p><p style="text-align: left; line-height: 2em; margin-top: 25px;"><img src="/ueditor/php/upload/image/20180919/1537342552580912.png" title="" alt="" width="268" height="166"/><strong style="color: rgb(146, 205, 220); font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; text-align: justify;">三、醋酸染色试验（VIA）</strong></p><section><section></section></section><p style="text-align: left; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(63, 63, 63);">4%醋酸溶液可使细胞脱水及核蛋白发生可逆性凝固，使宫颈上皮内瘤样病变上皮核密度增大、DNA容量的增加显现出来。普通光源的光线穿透基质减少，更多的光线从上皮反射回来，肉眼观察可出现白色改变。</span></p><section><p style="text-align: justify; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(146, 205, 220);"><strong>四、子宫颈癌筛查的起始年龄和终止年龄</strong></span></p><section></section></section><p style="text-align: justify; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 16px; color: rgb(63, 63, 63);">育龄女性要注意远离宫颈癌的危险因素，尤其是准备要宝宝的女性，保持健康的生活方式，尤其是戒烟。及时检查，早期发现、及时诊治宫颈内病变。早期发现并治疗预后较好。</span></p><p style="text-align: justify; line-height: 2em; margin-top: 25px;"><span style="font-family: 微软雅黑, &quot;Microsoft YaHei&quot;; font-size: 12px; color: rgb(127, 127, 127);">免责声明：本文转载自江苏省人医生殖中心，文章版权属归原作者所有，部分图片来源于网络，若涉及版权问题，请及时联系删除。</span></p></section>'
            }, 1000)
        }
    },
    mounted() {
        if (this.$route.query.id) {
            this.getArticleDetails()
        }
    }
}
</script>

<style lang="less">
#add-article {
  display: flex;
  flex-direction: row;
  .article-item {
    width: 50%;
    &.preview {
      border: 2px solid #ccc;
      padding: 20px;
    }
  }
}
</style>
```

<br>

### 三. ueditor单张上传图片到阿里云服务器

找到ueditor.all.js的源码中以下三行代码并注释掉：

```
domUtils.on(iframe, 'load', callback);
form.action = utils.formatUrl(imageActionUrl + (imageActionUrl.indexOf('?') == -1 ? '?':'&') + params);
form.submit();
```

添加通过ajax调用将图片上传到阿里云服务器的接口：

![修改simpleupload上传图片](/images/ueditor_simpleupload.png)

### 四. 问题

1.单张图片上传时，上传接口返回成功，但是ueditor提示上传错误。原因在官方文档上已有说明：

![单张上传关于跨域的官方说明](/images/ueditor_simpleupload_remark.png)

本文使用的方法参考第三条提到的直接修改ueditor.all.js的源码。

2.**图片上传成功之后**，虽然编辑器能成功插入图片，但是ueditor监听的值不会立刻改变，即 **不能立刻触发contentchange**。操作上可以解决的办法是再点击一下图片，这时才会触发contentchange事件。暂时没有时间看源码解决这个问题，只能先通过手动点击图片触发contentchange的笨办法解决。

3.设置iframe固定高度并且overflow: auto时，内容可滚动，此时点击图片，**图片的选中区域位置会发生偏移**，导致不能通过拖拽边框来伸缩图片，这种情况仅仅在内容超出设定iframe高度时出现。代码层面暂时没研究解决办法，手动操作方面的解决办法是使用悬浮工具框来实现图片的尺寸等的修改。

![修改图片尺寸](/images/ueditor_edit_img.png)

4.ueditor.config.js中serverUrl配置出现问题时，会报错 **后台配置项返回格式出错，上传功能将不能正常使用！** 解决办法就是仔细检查serverurl的路径是否正确。

5.有时候上传图片不能触发上传的接口。（复现频率比较低，暂时没找到原因）

> 以上描述若有任何错误或不当，请在评论里告知，非常感谢～









