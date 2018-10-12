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



* 화면에 쓸 query(v1)

```SQL
select case when a.gds_cd=1 then 'sangpum1'
	when a.gds_cd=2 then 'sangpum2'
	when a.gds_cd=3 then 'sangpum3'
	when a.gds_cd=4 then 'sangpum4'
		END as '상품명', 
	a.inv_prod as '투자기간(개월)',
	b.prf_rto as '수익률', 
	a.my_inv_prc as '투자금액'
from my_inv_lst a, inv_gds_lst b 
where a.gds_cd = b.gds_cd;
```



* ArcDao.java

```java
package my.examples.arc.dao;

import my.examples.arc.servlet.ArcDto;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.util.ArrayList;
import java.util.List;

public class ArcDao {
    private String dbUrl = "jdbc:mysql://localhost:3306/test?userSSL=false";
    private String dbId = "****";
    private String dbPassword = "****";

    public List<ArcDto> getArcDtoList() {
        List<ArcDto> list = new ArrayList<>();
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;

        try{
            conn = DbUtil.connect(dbUrl, dbId, dbPassword);
            String sql = "select case when a.gds_cd=1 then 'sangpum1'\n" +
                    "\twhen a.gds_cd=2 then 'sangpum2'\n" +
                    "\twhen a.gds_cd=3 then 'sangpum3'\n" +
                    "\twhen a.gds_cd=4 then 'sangpum4'\n" +
                    "\t\tEND as '상품코드', \n" +
                    "\ta.inv_prod as '투자기간(개월)',\n" +
                    "\tb.prf_rto as '수익률', \n" +
                    "\ta.my_inv_prc as '투자금액'\n" +
                    "from my_inv_lst a, inv_gds_lst b \n" +
                    "where a.gds_cd = b.gds_cd";
            ps = conn.prepareStatement(sql);
            rs = ps.executeQuery();

            while(rs.next()) {
                ArcDto arcDto = new ArcDto();
                arcDto.setGdsNm(rs.getString(1));
                arcDto.setInvestPeriod(rs.getInt(2));
                arcDto.setPrfRto(rs.getLong(3));
                arcDto.setMyPrice(rs.getInt(4));
                list.add(arcDto);
            }

        }catch (Exception ex) {
            ex.printStackTrace();
        }finally {
            DbUtil.close(conn, ps, rs);
        }
        return list;
    }
}
```



* DbUtil

```java
package my.examples.arc.dao;

import java.sql.*;

public class DbUtil {
    public static Connection connect(String dbUrl, String dbId, String dbPassword)
        throws RuntimeException {
        Connection conn = null;
        try{
            Class.forName("com.mysql.cj.jdbc.Driver");
            conn = DriverManager.getConnection(dbUrl, dbId, dbPassword);
        }catch (Exception ex) {
            throw new RuntimeException(ex);
        }
        return conn;
    }

    //insert, update, delete
    public static void close(Connection conn, PreparedStatement ps) {
        if(ps != null) {
            try{
                ps.close();
            }catch (SQLException ex) {}
        }
        if(conn != null) {
            try{
               conn.close();
            }catch (SQLException ex) {}
        }
    } //close

    public static void close(Connection conn, PreparedStatement ps, ResultSet rs) {
        if(rs != null) {
            try{
                rs.close();
            }catch (SQLException ex) {}
        }
        close(conn, ps);
    } //close
}
```



* ArcDto.java

```java
package my.examples.arc.servlet;

public class ArcDto {
    private String gdsNm;
    private int investPeriod;
    private Long prfRto;
    private int myPrice;
    private int idx;
    private String id;
    private int idxCode;


    /*public ArcDto(int idx, String id, int idxCode, int investPeriod, int myPrice) {
        this.idx = idx;
        this.id = id;
        this.idxCode = idxCode;
        this.investPeriod = investPeriod;
        this.myPrice = myPrice;
    }*/

    public ArcDto() {
    }

    public ArcDto(String gdsNm, int investPeriod, Long prfRto, int myPrice) {
        this.gdsNm = gdsNm;
        this.investPeriod = investPeriod;
        this.prfRto = prfRto;
        this.myPrice = myPrice;
    }

    public String getGdsNm() {
        return gdsNm;
    }

    public void setGdsNm(String gdsNm) {
        this.gdsNm = gdsNm;
    }

    public Long getPrfRto() {
        return prfRto;
    }

    public void setPrfRto(Long prfRto) {
        this.prfRto = prfRto;
    }

    public int getIdx() {
        return idx;
    }

    public void setIdx(int idx) {
        this.idx = idx;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public int getIdxCode() {
        return idxCode;
    }

    public void setIdxCode(int idxCode) {
        this.idxCode = idxCode;
    }

    public int getInvestPeriod() {
        return investPeriod;
    }

    public void setInvestPeriod(int investPeriod) {
        this.investPeriod = investPeriod;
    }

    public int getMyPrice() {
        return myPrice;
    }

    public void setMyPrice(int myPrice) {
        this.myPrice = myPrice;
    }
}
```



* ARCListServlet.java

```java
package my.examples.arc.servlet;

import my.examples.arc.dao.ArcDao;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.List;

@WebServlet("/list")
public class ARCListServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ArcDao arcDao = new ArcDao();
        List<ArcDto> list = arcDao.getArcDtoList();

        req.setAttribute("arcList", list);

        RequestDispatcher dispatcher = req.getRequestDispatcher("/WEB-INF/views/list.jsp");
        dispatcher.forward(req, resp);
    }
}

```

