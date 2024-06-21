<p align="center">
<img src="https://github.com/ArthurSpirling/Levellers/blob/main/lev_banner.jpg" width = "860" title="Levellers image">
</p>
 
# Peers, Equals, and Jurors: New Data and Methods on The Role of Legal Equality in Leveller Thought 

This is the public facing project site for the work by [Melissa Schwartzberg](https://melissaschwartzberg.wordpress.com/) and [Arthur Spirling](http://arthurspirling.org/) on the [Levellers](https://en.wikipedia.org/wiki/Levellers). Specifically, the current version of their paper  *Peers, Equals, and Jurors: New Data and Methods on The Role of Legal Equality in Leveller Thought* is stored [here](https://github.com/ArthurSpirling/Levellers/blob/main/Levellers_Schwartzberg_Spirling_June_2024.pdf).  The abstract for that paper is

> We consider Leveller thought on equality relative to their contemporaries during the Civil War(s) period. We compile a corpus of hundreds of seventeenth century pamphlets and combine this with novel word embedding techniques trained on millions of Early Modern English documents to make statements about word "meanings". We focus on understandings of the phrase "peers and equals" (and its variants).  We provide quantitative and qualitative evidence, in line with extant literature, that the Levellers---John Lilburne specifically---had a prevailing interest in equality in a way that is different to that expressed by other groups of the time.  But contrary to current scholarship, we show that the Levellers and Lilburne were animated primarily by notions of *legal* rather than social equality, and that their interest in parity or the status of peers primarily pertained to juries, an institution that did indeed aim to level down legal judgment


The paper uses a new machine readable [dataset](https://oll.libertyfund.org/collection/the-levellers) of pamphlets written by the Levellers and their contemporaries, and produces *a la carte* embeddings (in the sense of [Rodriguez, Spirling and Stewart](https://github.com/prodriguezsosa/EmbeddingRegression)) of the same.  These rely on a set of seventeenth century parliamentary journals as a training corpus, courtesy of [Tom Paskhalis](https://tom.paskhal.is/) and [Toni Rodon](https://tonirodon.cat/)---see their paper, [here](https://osf.io/qgu9c).  

As an example of a pamphlet, consider *The Grand Concernments of England ensured* published anonymously, in 1659.  In its original form, it looks like this: 
<p align="center">
<kbd>
<img src="https://github.com/ArthurSpirling/Levellers/blob/main/grand_concernments.jpg"  width = "200" title="Levellers image">
  </kbd> 
</p> 

Once digitized, it contains passages such as: 

> IF thou art prejudicate, save thy purse and thy paines; ’tis the considerate man, he that ponders his wayes, I had rather deal with: I promise thee thus much, I have no design to seduce thee, but whatsoever I have written, is my very thought: it may be thou art perswaded thou maist better employ thy time in Reading; I believe no lesse, however thou shalt finde some things here, not altogether unworthy of Consideration.

That is, it is written in [*Early Modern English* (EME)](https://en.wikipedia.org/wiki/Early_Modern_English). Consequently, the embeddings are also in EME, and we make them freely available to anyone who needs them. You can find them in [this Google Drive folder](https://drive.google.com/drive/folders/1oGCMSnmcZG-uxO-gxj3otyT8QXlhmTp8?usp=sharing), and we discuss how to access them below.  If you use them, we simply ask that you cite our paper as something like


> Melissa Schwartzberg and Arthur Spirling. "The Levellers in Context: Equals, Peers and Free-born Englishmen". Working Paper. New York University. URL: https://github.com/ArthurSpirling/Levellers


### Accessing the Embeddings

1. go to [this Google Drive folder](https://drive.google.com/drive/folders/1oGCMSnmcZG-uxO-gxj3otyT8QXlhmTp8?usp=sharing), and download  `embeddings.rdata`.  The other object, `glove.rds` is simply one version of the [GloVe embeddings](https://nlp.stanford.edu/projects/glove/) that provide an interesting counterpoint to ours, but you don't need them if all you want is the Early Modern English ones.
2. The `embeddings.rdata` object contains two things: 
- `all_embed` which is a 53431 (term) $\times$ 300 (embedding dimension) matrix of embeddings of various words from the 17th Century corpus
- `all_transform` which a 300 $\times$ 300 "transformation matrix" as described by [Rodriguez, Spirling and Stewart](https://github.com/prodriguezsosa/EmbeddingRegression) and part of the *a la carte* (ALC) embedding model from [Khodak et al](https://arxiv.org/abs/1805.05388).  In both those papers, this is the relevant **A** matrix. 


It is possible to work directly with `all_embed`.  For example, you might want to check the nearest neighbors of some terms like `cromwell` or `sovereign`.  In which case, you can use code like the following: first, install and then load the [`conText` package](https://cran.r-project.org/web/packages/conText/index.html) from CRAN 
```
library(conText)
```
Then, try
```
find_nns(all_embed["sovereign",], all_embed, N = 10, norm = "l2")
```
which should return something like
```
[1] "sovereign" "reign"     "gracious"  "loyal"     "subjects"  "dutiful"  
 [7] "majesty's" "memory"    "sacred"    "queen"
```
These make sense as terms close to `sovereign` in meaning or connotation for 17th Century political English.  You can also multiply by the transformation matrix which might give you even "better" (more Leveller focussed) terms: 
```
 LEm <- all_embed %*% all_transform
```
And then
```
find_nns(LEm["sovereign",], LEm, N = 10, norm = "l2")

 [1] "sovereign" "gracious"  "reign"     "loyal"     "subjects"  "followeth"
 [7] "close"     "majesty's" "year"      "dutiful"
```
which are obviously very similar in this case.  Finally, we might want to compare these neighbors to those of similar terms from a corpus of modern (internet) English.  The GloVe embeddings can help with that.  Download the `glove.rds` object and try

```
glove <- readRDS("glove.rds")
find_nns(glove["sovereign",], glove, N = 10, norm = "l2") )
```
This should return something like
```
 [1] "sovereign"        "sovereignty"      "entity"          
 [4] "debt"             "status"           "downgrade"       
 [7] "creditworthiness" "entities"         "affirmed"        
[10] "monarch"         
```
The point here is that these terms reflect more contemporary understandings of what "sovereign" connotes---including notions of debt and credit---relative to our Early Modern English version.
