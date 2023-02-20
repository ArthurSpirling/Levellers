<p align="center">
<img src="https://github.com/ArthurSpirling/Levellers/blob/main/lev_banner.jpg" width = "800" title="Levellers image">
</p>
 
# The Levellers and Their Contemporaries in Context: 'Equality' at the Time of the English Civil War(s), 1638–1666

This is the public facing project site for the work by Melissa Schwartzberg and Arthur Spirling on the Levellers. Specifically, the current version of their paper  *The Levellers and Their Contemporaries in Context: 'Equality' at the Time of the English Civil War(s), 1638–1666* is stored here.  The abstract for that paper is

> We consider the evolution of Leveller thought relative to their contemporaries during the English Civil War(s). We compile a new data set of hundreds of 17th Century pamphlets to chart themes related to rights, elections and suffrage for a diverse set of thinkers for the period 1638–1666. We combine this with novel word embedding techniques trained on millions of Early Modern English documents to make statements about how actors of the time understood key issues of interest to modern democratic theory. Our quantitative and qualitative results show that inter alia (1) John Lilburne created the key rhetoric of “freeborn Englishman” but that its evolution is more complex than assumed; (2) the Leveller interest in "equality" is distinctively legal rather than "social" in nature; (3) this legal conception also extends to Leveller concerns with elections, but on this topic they become more aligned with their contemporaries over time than in the case of equality.


The paper uses a new machine readable [dataset](https://oll.libertyfund.org/collection/the-levellers) of pamphlets written by the Levellers and their contemporaries, and produces *a la carte* embeddings (in the sense of [Rodriguez, Spirling and Stewart](https://github.com/prodriguezsosa/EmbeddingRegression)) of the same.  These rely on a set of 17th Century parliamentary journals as a training corpus, courtesy of [Tom Paskhalis](https://tom.paskhal.is/) and [Toni Rodon](https://tonirodon.cat/)---see their paper, [here](https://osf.io/qgu9c).  

Given the period, the embeddings are in [*Early Modern English*](https://en.wikipedia.org/wiki/Early_Modern_English) and are freely available to anyone who needs them. You can find them in [this gdrive folder](https://drive.google.com/drive/folders/1oGCMSnmcZG-uxO-gxj3otyT8QXlhmTp8?usp=sharing), and we discuss how to access them below.  We simply ask that you cite our paper as something like


> Melissa Schwartzberg and Arthur Spirling. "The Levellers and Their Contemporaries in Context: 'Equality' at the Time of the English Civil War(s), 1638–1666". Working Paper. New York University. URL: https://github.com/ArthurSpirling/Levellers


### Accessing the Embeddings

1. go to [this Google Drive folder](https://drive.google.com/drive/folders/1oGCMSnmcZG-uxO-gxj3otyT8QXlhmTp8?usp=sharing), and download  `embeddings.rdata`.  The other object, `glove.rds` is simply one version of the [GloVe embeddings](https://nlp.stanford.edu/projects/glove/) that provide an interesting counterpoint to ours, but you don't need them if all you want is the Early Modern English ones.
2. The `embeddings.rdata` object contains two things: 
- `all_embed` which is a 53431 (term) by 300 (dimension) matrix of embeddings of various words from the 17th Century corpus
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

Finally, we might want to compare these neighbors to those of similar terms from a corpus of modern (internet) English.  The GloVe embeddings can help with that.  Download the `glove.rds` object and try

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
