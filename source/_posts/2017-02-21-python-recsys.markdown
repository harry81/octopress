---
layout: post
title: "python-recsys"
date: 2017-02-21 16:53:36 +0900
comments: true
categories: python-recsys tutorial
---
python-recsys 따라하기  
http://ocelma.net/software/python-recsys/build/html/quickstart.html

[ratings.csv 다운받는다](https://grouplens.org/datasets/movielens/)

```python 사용자의 입력 모델화 하여 파일로 저장
import recsys.algorithm
recsys.algorithm.VERBOSE = True

from recsys.algorithm.factorize import SVD
svd = SVD()
svd.load_data(filename='data/ml-20m/ratings.csv', sep=',', format={'col':0, 'row':1, 'value':2, 'ids': int})

k = 100
svd.compute(k=k, min_values=10, pre_normalize=None, mean_center=True, post_normalize=True, savefile='/tmp/movielens')
```

역시나 책대로 되지는 않는다. 위의 7번째 라인이 약 3시간 30분을 잡아먹었는데, 결국은 아래와 같이 `MemoryError' 오류를 뱉어내버리고야 말았다. 아무래도 실행머신이 고급 성능을 가지지 않은 개인용 노트북이라고 결론을 짓기로 한다.

``` bash
/home/harry/.virt_env/recommends/lib/python2.7/site-packages/divisi2/sparse.pyc in squish(self, cutoff)
   1005                       for (val, row, col) in entries
   1006                       if row in rows and col in cols]
-> 1007         return SparseMatrix.from_named_entries(newentries).squish()
   1008 
   1009     # adds compatibility with SciPy-based methods

/home/harry/.virt_env/recommends/lib/python2.7/site-packages/divisi2/sparse.pyc in from_named_entries(tuples)
    418         If possible, use ``from_named_lists``, because it's faster.
    419         """
--> 420         lists = zip(*tuples)
    421         if not lists: return SparseMatrix((0,0))
    422         return SparseMatrix.from_named_lists(*lists)

MemoryError: 
``` 


``` python 파일로 저장된 모델을 객체로 읽어들인다
from recsys.algorithm.factorize import SVD
svd2 = SVD(filename='/tmp/movielens') # Loading already computed SVD model
ITEMID1 = 1    # Toy Story (1995)
ITEMID2 = 2355 # A bug's life (1998)
svd2.similarity(ITEMID1, ITEMID2)
```
