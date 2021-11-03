ElasticSearch使用

* 创建Index  类似与创建表

​	![创建index](https://i.imgur.com/Ly77RyM.png)

* 查看Index的属性

  ![index属性](https://i.imgur.com/BHGVqNm.png)

* 查看所有索引

  ![所有索引](https://i.imgur.com/TaiHonh.png)

* 新增文档[即增加数据]

  - 不指定ID

    ![不指定ID](https://i.imgur.com/lVRh1zd.png)

  - 指定ID

    ![指定ID](https://i.imgur.com/DCr3oeo.png)

* 通过ID查询document

  ![查询document](https://i.imgur.com/oRv09JZ.png)

* 查询所有的document

  ![查询](https://i.imgur.com/5Ctf4Ok.png)

* 整个文档更新

  ```
  http://127.0.0.1:9200/shopping/_doc/1001
  
  PUT [幂等性的]
  
  {
  	"title": "红米手机",
  	"category": "小米",
  	"images": "http://www.abc.com/xm.png",
  	"price": 5998.00
  }
  ```

  

* 文档局部更新

  ```
  http://127.0.0.1:9200/shopping/_update/1001
  
  POST
  
  {
  	"doc": {
  		"title": "one plus 手机"
  	}
  }
  ```

  