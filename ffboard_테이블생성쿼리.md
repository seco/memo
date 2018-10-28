# CREATE Fantastic Four board

## create database

```mysql
create database ffboard;
use ffboard;
```

## create table

- 게시판

```mysql
CREATE TABLE category (
	category_id INT(10) UNSIGNED NOT NULL AUTO_INCREMENT , 
	category_name VARCHAR(255) NOT NULL,
	PRIMARY KEY(category_id));
```

- 권한

```mysql
CREATE TABLE auth (
    -- auth_id INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
    auth_name VARCHAR(50) NOT NULL,
    auth_level INT(10),
    category_id INT(10) UNSIGNED,
    PRIMARY KEY(auth_name),
    FOREIGN KEY(category_id) REFERENCES category (category_id)
);
```

- 회원 테이블(member)

```mysql
CREATE TABLE member (
	member_id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
	name VARCHAR(20) UNIQUE KEY,
	password VARCHAR(20),
	email VARCHAR(50),
	auth_name VARCHAR(50) DEFAULT "일반회원",
    FOREIGN KEY(auth_name) REFERENCES auth (auth_name),
	PRIMARY KEY(member_id));
```

- 아티클

```mysql
CREATE TABLE article (
	article_id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
	title VARCHAR(255) NOT NULL,
    hit INT(10) DEFAULT 0,
    name VARCHAR(20) NOT NULL,
    group_id BIGINT DEFAULT 0, 
    depth_level INT(5),
    regdate DATE,
    category_id INT(10) UNSIGNED NOT NULL,
    PRIMARY KEY(article_id),
    FOREIGN KEY(category_id) REFERENCES category (category_id)
	);
```

- 댓글

```mysql
CREATE TABLE comment (
	comment_id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
	article_id BIGINT UNSIGNED NOT NULL,
	name VARCHAR(20) not null,
	content VARCHAR(255),
	group_id BIGINT NOT NULL,
	regdate DATE,
	PRIMARY KEY(comment_id),
	FOREIGN KEY(article_id) REFERENCES article (article_id));
```

- 페이지

```mysql
CREATE TABLE page (
	category_id INT(10) UNSIGNED,
	page_count INT(10), 
	FOREIGN KEY(category_id) REFERENCES category (category_id));
```

- 내용

```mysql
CREATE TABLE content (
	article_id BIGINT UNSIGNED,
	content LONGTEXT,
	image_url varchar(255),
	FOREIGN KEY(article_id) REFERENCES article (article_id));
```

- 테이블 삭제

```mysql
drop table article;
drop table auth;
drop table category;
drop table comment;
drop table content;
drop table member;
drop table page;
commit;
```