# 수익률 계산 DB 만들기



* create database

```sql
create database arc;
```



* goods master table

```sql
create table gds_mst (
 gds_cd int(10) not null,
 gds_nm varchar(255)
);
```

> sample data

```sql
insert into gds_mst (gds_cd, gds_nm) values (1, 'sangpum1');
insert into gds_mst (gds_cd, gds_nm) values (2, 'sangpum2');
insert into gds_mst (gds_cd, gds_nm) values (3, 'sangpum3');
insert into gds_mst (gds_cd, gds_nm) values (4, 'sangpum4');
```





* member table

```sql
create table member (
 mb_idx int unsigned NOT NULL AUTO_INCREMENT,
 id varchar(20) not null,
 name varchar(20),
 pw varchar(20),
 email varchar(50),
 primary key (mb_idx)
);
```

> sample data

```sql
insert into member(id, name, pw, email) values ('kimId', 'kim', '123', 'kim@a.com');
insert into member(id, name, pw, email) values ('jangId', 'jang', '1234', 'jang@a.com');
insert into member(id, name, pw, email) values ('shinId', 'shin', '12', 'shin@a.com');
insert into member(id, name, pw, email) values ('baeId', 'bae', '11', 'bae@a.com');
```





* investment good list

```sql
create table inv_gds_lst (
	igl_idx int unsigned not null auto_increment,
    gds_cd int(10),
    prf_rto float(7,2),
    cms double(10,2), 
    primary key (igl_idx)
);
```

> sample data

```sql
insert into inv_gds_lst (gds_cd, prf_rto, cms) values (1, 10.2, 140);
insert into inv_gds_lst (gds_cd, prf_rto, cms) values (2, 20.1, 100);
insert into inv_gds_lst (gds_cd, prf_rto, cms) values (3, 50.5, 20);
insert into inv_gds_lst (gds_cd, prf_rto, cms) values (4, 70.7, 60);
```





* my investment list

```sql
create table my_inv_lst (
	my_idx int unsigned not null auto_increment,
    id varchar(20),
    gds_cd varchar(20),
    inv_prod int(10), --초기버전(개월수로)
    my_inv_prc double(10,2),
    primary key (my_idx)
);
```

> sample data

```sql
insert into my_inv_lst (id, gds_cd, inv_prod, my_inv_prc) values ('kimId', 1, 10, 1000);
insert into my_inv_lst (id, gds_cd, inv_prod, my_inv_prc) values ('jangId', 2, 9, 1000);
insert into my_inv_lst (id, gds_cd, inv_prod, my_inv_prc) values ('shinId', 3, 7, 1000);
insert into my_inv_lst (id, gds_cd, inv_prod, my_inv_prc) values ('baeId', 4, 10, 1000);
```

