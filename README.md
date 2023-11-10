"# mybatis-sample" 



データベースのクライアントから以下のクエリを実行して下さい。

```
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
```
implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:3.0.2'
```

# 概要
テーブルに対してマッピングするORマッパーの場合、多くのテーブルを結合するために長いコードを書く必要があるが、  
MyBatisであればテーブル結合を前提としたSQLを作成するだけで済む  

## Entityクラスを作成する
クラスに`@Entity`アノテーションを付与する必要がある  
本プロジェクトにおけるItemクラス  
***MyBatisはテーブルではなくSQLの実行結果に対してORマッピングを行う***ので、  
問合せ結果に対応するフィールドと、それに対するgetter, setter等を持つ  





## Mapperインタフェースを作成する
作成したインターフェースには`@Mapper`を付与する必要がある  
本プロジェクトにおけるItemMapperインターフェース  
このインターフェースを定義することで、データベースへアクセスする機能が自動で生成される  

## マッピングファイルを作成する
慣例ではMapperインターフェースのパッケージと同じとなるようにフォルダを作成する  
このファイルでは問合せ結果とEntityクラスのマッピング、  
問合せ結果のカラムと対応するフィールドのマッピングを行う  

`select`、`insert`、`update`、`delete`等のタグを使用する場合、  
これらのid属性に指定する値がMapperインターフェースのメソッド名となる  
以下の場合は`selectAll`がメソッド名  
```
<select id="selectAll" resultMap="userMap">
	SELECT ID, NAME, AGE FROM USER
</select>
```


`if`タグを使用することで***動的SQL***を作成することも可能

```
List<User> search(String name);
```
上記のメソッドにおいて、  
以下の場合は引数nameがnullでなければwhere句が追加される
```Mapperファイル
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
