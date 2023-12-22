# 環境

データベースのクライアントから以下のクエリを実行して下さい。

```SQL
CREATE DATABASE IF NOT EXISTS item;

USE item;

CREATE TABLE IF NOT EXISTS item (
  id bigint(20) NOT NULL AUTO_INCREMENT,
  name varchar(255),
  price int,
  vendor varchar(255),
  PRIMARY KEY (id)
);
```


build.gradleのdependenciesに以下を追加
```JAVA
implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:3.0.2'
```

# MyBatis概要
テーブルに対してマッピングするO/Rマッパーの場合、多くのテーブルを結合するために長いコードを書く必要があるが、  
MyBatisであればテーブル結合を前提としたSQLを作成するだけで済む  
O/Rマッパーとは、オブジェクト（Object）とデータベース（RDB）を容易に変換（マッピング）するための機能  


MyBatisは大規模開発に向いている
ORマッパーの場合、多くのテーブルを結合するために長いコードを書く必要があるが、  
MyBatisであればテーブル結合を前提としたSQLを作成するだけで済む



# 使用方法

---
#### Entityクラスを作成する
---

本プロジェクトにおけるItemクラス  
***MyBatisはテーブルではなくSQLの実行結果に対してORマッピングを行う***ので、  
問合せ結果に対応するフィールドと、それに対するgetter, setter等を持つ  




---
#### Mapperインタフェースを作成する
---

作成したインターフェースには`@Mapper`を付与する必要がある  
本プロジェクトにおけるItemMapperインターフェース  
このインターフェースを定義することで、データベースへアクセスする機能が自動で生成される  

---
#### マッピングファイルを作成する
---

本プロジェクトにおけるItemMapper.xml
慣例ではMapperインターフェースのパッケージと同じとなるようにフォルダを作成する  
このファイルでは問合せ結果とEntityクラスのマッピング、  
問合せ結果のカラムと対応するフィールドのマッピングを行う  

`select`、`insert`、`update`、`delete`等のタグを使用する場合、  
これらのid属性に指定する値がMapperインターフェースのメソッド名となる  
以下の場合は`selectAll`がメソッド名  
```XML
<select id="selectAll" resultMap="userMap">
	SELECT ID, NAME, AGE FROM USER
</select>
```


`if`タグを使用することで***動的SQL***を作成することも可能

```JAVA
List<User> search(String name);
```
上記のメソッドにおいて、  
以下の場合は引数nameがnullでなければwhere句が追加される
```XML
<select id="search" resultMap="userMap">
    SELECT ID, NAME, AGE FROM USER
    <if test="name != null">
    WHERE
        NAME LIKE concat('%', #{name}, '%')
    </if>
</select>
```
その他にも以下のようなタグが用意されている
- choose
- where
- when 
- foreach
