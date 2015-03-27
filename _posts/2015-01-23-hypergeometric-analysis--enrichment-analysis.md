---
layout: post
title: "超几何分析和GO富集分析"
categories:
- R
tags:
- GO
- KEGG
- Hypergeometric
- Gene expression

comments: true
---

1. 关于超几何分布（Hypergeometric distribution）  

> 超几何分布是统计学上一种离散概率分布，它描述了由有限个物件 *(N)* 中抽取 (n) 个物件，成功抽出制定种类物件的个数 *(k)* （不归还）。  
例如在有N个样品，其中m个是不及格的。超几何分布描述了在该N个样本中抽出n个，其中k个是不及格的几率。  

>f(k;n,m,N) = C(m,k)*C(N-m, n-k)/C(N,n)  


>
若n=1，超几何分布还原为伯努利分布。
若n接近∞，超几何分布可视为二项式分布。  
 From: [Wikipedia](http://zh.wikipedia.org/wiki/%E8%B6%85%E5%87%A0%E4%BD%95%E5%88%86%E5%B8%83)


转换到生物学分析（GO）中，某样品具有GO注释的基因数 (N), 其中属于TermA的基因数目为 (M), （举例）差异表达分析后得到的基因数量为 (n)，其中有 (k) 个基因属于TermA，现在需要使用超几何分析判断该TermA是否富集？！

f(k,N,M,n) = C(k,M)*C(n-k,N-M)/C(n,N)  
N = size of population  
M = # of items in population with property "E"  
N-M = # of items in population without property "E"  
n = number of items sampled  
k = number of items in sample with property "E"  

解读为：先计算出k-1的累积可能性，再用1减去该数，即可得到获得 **>=k** 的可能性。（个人理解）

R中的函数  

```{R}  
1-phyper(k-1,M,N-M,n)
```  


2. 关于富集性分析（Enrichment analysis）

需要知道有几种方法执行富集分析：  
  1. 超几何分布（常用软件都使用该方法）。  
  2. fisher's exact test (也是使用超几何分布）.  
  3. chi-sauared test.  
 

------------


1.《超几何分析》   
Posted by ygc on April 28, 2012  
原文链接：[http://ygc.name/2008/08/20/hypergeometric-distribution/](http://ygc.name/2008/08/20/hypergeometric-distribution/)

简单点说，超几何分布就是有限样本的无放回抽样。不同于有放回抽样的二项分布（每次贝努里试验成功概率是一样的），每次的概率不相等。
随机变量X的超几何概率分布：  
f(k,N,M,n) = C(k,M)*C(n-k,N-M)/C(n,N)  
N = size of population  
M = # of items in population with property "E"  
N-M = # of items in population without property "E"  
n = number of items sampled  
k = number of items in sample with property "E"    

这个公式可以理解为有C(n,N)种可能的样本，有C(k,M)种方法得到k个属于M的抽样、有C(n-k,N-M)种方法得到n-k个不属于M的抽样。  
X服从参数n,N,M的超几何分布记为 X~H(n,N,M).  


参考：[http://en.wikipedia.org/wiki/Hypergeometric_distribution](http://en.wikipedia.org/wiki/Hypergeometric_distribution)

对于基因进行GO注释，看基因集在某个GO子类中是否富集，富集的概率服从超几何分布。
* N为GO注释的总基因数。
* M为属于某个GO子类的基因数。
* n为进行GO富集分析的基因集的数目。
* k为n中属于M的数目。
* 基因集n是否在M类中富集的概率


```{R}
1-phyper(k-1,M,N-M,n)  ##R代码
```


或者是

```{R}
phyper(k-1,M,N-M,n, lower.tail=FALSE)
```


在已知总体分布下，抽样n个中出现M类的个数是k以及k以上个数的概率。

```{R}
lower.tail: logical; if TRUE (default), probabilities are P[X <= x],
          otherwise, P[X > x]. 
```

2.《富集性分析》  
Posted by ygc on April 28, 2012

原文链接： [http://ygc.name/2012/04/28/enrichment-analysis/](http://ygc.name/2012/04/28/enrichment-analysis/)


当我们用组学测定了一大堆分子之后，我们希望站在更高的角度去看这些分子和那些生物学过程相关。那么通常各种注释，对这些基因/蛋白进行分类，那么从分类的比例上，是不能草率下结论，正如上面有钱人学历分布的例子一样。我们需要把总体的分布考虑进去。  
和某个注释/分类是否有相关性，把基因分成属于这一类，和不属于这一类两种，这就好比经典统计学中的白球和黑球的抽样问题。也可以列一个２x２的表，进行独立性分析。  
以文章Gene Expression in Ovarian Cancer Reflects Both Morphology and Biological Behavior, Distinguishing Clear Cell from Other Poor-Prognosis Ovarian Carcinomas: [URL](http://cancerres.aacrjournals.org/content/62/16/4722) 所鉴定的差异基因为例。

73个差异基因的Symbol，我把它转为 entrezgene ID得到57个（漏掉的不管它，只是做为一个例子）:


```{R}  
eg [1] 

#下面测试一下这些基因和化学刺激响应的相关性。

goid <- “GO:0042221” # response to chemical stimulus

#那么做为背景，总体基因为N，属于“化学刺激响应”这个分类的基因有M个。

allgeneInCategory <- unique(get(goid, org.Hs.egGO2ALLEGS))
M <- length(allgeneInCategory)
N <- length(mappedkeys(org.Hs.egGO))

#样本的大小是n，属于“化学刺激响应”这个分类的基因有k个。

n <- length(eg)
k <- sum(eg %in% allgeneInCategory)

#白球黑球问题，最简单的莫过于用二项式分布，从总体上看，要拿到一个基因属于“化学刺激响应”这个分类的概率是M/N。那么现在抽了n个基因，里面有k个基于这个分类，p值为：

> 1-sum(sapply(0:k-1, function(i) choose(n,i) * (M/N)^i * (1-M/N)^(n-i)))
[1] 8.561432e-10

#二项式分布，是有放回的抽样，你可以多次抽到同一基因，这是不符合的。所以这个计算只能说是做为近似的估计值，无放回的抽样，符合超几何分布，通过超几何分布的计算，p值为：
 
> phyper(k-1,M, N-M, n, lower.tail=FALSE)
[1] 7.879194e-10

#如果用2x2表做独立性分析，表如下：

> d <- data.frame(gene.not.interest=c(M-k, N-M-n+k), gene.in.interest=c(k, n-k))
> row.names(d) <- c("In_category", "not_in_category")
> d
                gene.not.interest gene.in.interest
In_category                  2613               28
not_in_category             15310               29

#这个也有很多方法可以做检验，经典的有卡方检验和fisher's exact test。
如果用卡方检验来做。  

> chisq.test(d, )

	Pearson's Chi-squared test with Yates' continuity correction

data:  d 
X-squared = 51.3849, df = 1, p-value = 7.592e-13
```

对于２x２表来说，卡方检验通常也只能做为近似估计值，特别是当sample size或expected ell count比较小的时候，计算并不准确。
fisher's exact test，名副其实，真的就比较exact，因为它使用的是超几何分布来计算p值。这也是为什么fisher's exact test和超几何模式计算的p-值是一样的，

```{R}
> fisher.test(d)
	Fisher's Exact Test for Count Data
data:  d 
p-value = 7.879e-10
alternative hypothesis: true odds ratio is not equal to 1 
95 percent confidence interval:
 0.1013210 0.3089718 
sample estimates:
odds ratio 
 0.1767937 
```

通常各种软件做GO富集性分析，都是使用超几何分布进行计算。
IPA软件则是使用fisher's exact test来检验基因在某个网络中是否富集。  
从这个例子上看，chi-squared test的表现最差，binomial做为p值的近似估计还是不错的，因为计算较为简单。
富集性分析应用范围非常广，从[Disease Ontology](http://bioconductor.org/packages/devel/bioc/html/DOSE.html), [Gene Ontology, KEGG](http://bioconductor.org/packages/devel/bioc/html/clusterProfiler.html), 到[Reactome Pathway](http://bioconductor.org/packages/devel/bioc/html/ReactomePA.html)等等。




