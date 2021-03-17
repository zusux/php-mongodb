php7 版本的mongodb orm

安装 
```
composer require zusux/php-mongodb
```


使用
```

创建实例
$mongo = PhpMongodb::getInstance();

```

```
插入数据
$data = $mongo
    ->database("shop")
    ->table("users")
    ->insert(["id"=>3,"name"=>"张三","age"=>20,"x"=>1],false,$insertId);
var_dump($data);
var_dump($insertId);

//用原先的id作为主键
$data = $mongo
    ->database("shop")
    ->table("users")
    ->insert(["id"=>4,"name"=>"张三","age"=>20,"x"=>2],"id",$insertId);
var_dump($data);
var_dump($insertId);
```





```
查询一条数据
$data = $mongo->database("test")
    ->table("sites")
    ->field(["name"]) //查询字段
    ->unfield(["_id"]) //去除字段
    ->sort("x",true) //排序
    ->offset(0) //跳过第几个
    ->find(
        ["name"=>"aaa"],true
    );

//使用mongodb主键查询
$data = $mongo
    ->database("shop")
    ->unfield(["_id"])
    ->table("users")
    ->find(['_id'=>new MongoDB\BSON\ObjectID("60515b433c540000ff007902")]);
var_dump($data);


//使用自定义主键查询 
$data = $mongo
    ->database("shop")
    ->unfield(["_id"])
    ->table("users")
    ->find(['_id'=>intval(4)]);
var_dump($data);

```

```
查询多条数据
$data = $mongo->database("shop")
    ->table("users")
    ->field(["name"])
    ->unfield(["_id"])
    ->limit(1)
    ->page(0)
    ->sort("x",true)
    ->select(
        ["name"=>"aaa"],true
    );
```


```
统计数据数量

$data = $mongo->database("shop")
    ->table("users")
    ->field(["name"])
    ->unfield(["_id"])
    ->limit(1)
    ->page(0)
    ->sort("x",true)
    ->count(
        ["name"=>"aaa"]
    );
var_dump($data); // int(1)

```

```
//删除数据
$data = $mongo->database("shop")
    ->table("users")
    ->delete(
        ["name"=>"李四"],
        1,
        2
    );
var_dump($data);

```

```
//更新数据
$data = $mongo
    ->database("shop")
    ->unfield(["_id"])
    ->table("users")
    ->update(
        ['_id'=> intval(4)],
        [
            "abc"=>111
        ]
    );
var_dump($data);

```

```
//aggregate
$pipeline = [
    ['$match' => ['age' => 25]], //where 语句
    ['$group' => ['_id' => '$age',"sum"=>['$sum'=> 1]]], //select count(*) from xx where age = 25 group by age
];
$data = $mongo
    ->database("shop")
    ->table("users")
    ->aggregate($pipeline);
var_dump($data);

```


```
执行 command

$opts = [
    'insert'=>'users',// collection表名
    'documents'=> [
        [ 'name'=> "abc123", 'status'=> "A" ],
        [ 'name'=> "abc12322", 'status'=> "B" ],
    ],
    'ordered'=>true,
    'writeConcern'=>[
        "w"=>"majority",
        "wtimeout"=>5000
    ]
];
$data = $mongo->execCommand("shop",$opts);
var_dump($data);
```