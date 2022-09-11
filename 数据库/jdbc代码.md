```java
import java.sql.*;//这个必须导入
 
public class Connect {
    public static void main(String[] args)throws Exception{
        Class.forName("com.mysql.jdbc.Driver");
        //连接驱动  注："?useUnicode=true&amp;characterEncoding=utf-8"是为了防止乱码
        String url="jdbc:mysql://127.0.0.1/page?useUnicode=true&amp;characterEncoding=utf-8";
        //数据库账号密码
        Connection con=DriverManager.getConnection(url,"root","1234");
        
        Statement cmd=con.createStatement();
        String sql="select * from t_student";
        ResultSet rs=cmd.executeQuery(sql);
        
        while(rs.next()) {
            int id=rs.getInt(1);
            String stu_name=rs.getString(2);
            String gender=rs.getString(3);
            int age=rs.getInt(4);
            String address=rs.getString(5);
            // %-?d 输出n列左对齐
            System.out.printf("%-5d %-4s %-3s %-4d %-4s\n",id,stu_name,gender,age,address);
        }
        con.close();
    }
}
```

