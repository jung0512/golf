# golf
golf
# index
``` html
<header>
	<jsp:include page="layout/header.jsp"></jsp:include>
</header>
<nav>
	<jsp:include page="layout/nav.jsp"></jsp:include>
</nav>
<section>
	<jsp:include page="layout/section.jsp"></jsp:include>
</section>
<footer>
	<jsp:include page="layout/footer.jsp"></jsp:include>
</footer>
```
결과 화면<br>
![image](https://user-images.githubusercontent.com/102035198/207210951-a8a836f6-a443-4722-b7b3-c86d9b9d0ebd.png)<br>
# 강사조회 
``` html
<header>
<jsp:include page="layout/header.jsp"></jsp:include>
</header>
<nav>
<jsp:include page="layout/nav.jsp"></jsp:include>
</nav>
<section class="section">
   <h2 align="center">강사조회</h2>
   <table class="table_line">
      <tr>
         <th>강사코드</th>
         <th>강사명</th>
         <th>강의명</th>
         <th>수강료</th>
         <th>강사자격취득일</th>
      </tr>
      <%while(rs.next()) {%>
         <tr>
            <td><%= rs.getString(1) %></td>
            <td><%= rs.getString(2) %></td>
            <td><%= rs.getString(3) %></td>
            <td><%= rs.getString(4) %></td>
            <td><%= rs.getString(5) %></td>
         </tr>
      <%} %>
   </table>
</section>
<footer>
<jsp:include page="layout/footer.jsp"></jsp:include>
</footer>
```
while문을 돌려 마지막 값까지 표를 만들어 값을 넣어준다
```javascript
<%
   
   String sql = "select teacher_code, teacher_name, class_name, to_char(class_price, 'L999,999')" 
   				+ "class_price, substr(teach_resist_date,1,4) || '년' || substr(teach_resist_date,5,2)" 
   				+ "|| '월' || substr(teach_resist_date,7,2)|| '일' " 
   				+ "teach_resist_date from tbl_teacher_202201";
   
   Connection conn = DBConnect.getConnection();
   PreparedStatement pstmt = conn.prepareStatement(sql);
   ResultSet rs = pstmt.executeQuery();
   
%>
```
강사코드와 강사명,강의명을 불러오고 수강료와 강사자격취득일은 각자 수강료는 to_char를 이용하여 ￦로 바꾸고<br>
강사자격 취득일은 substr를 이용하여 년, 월, 일로 표시형식을 바꾸어준다 <br>
결과화면<br>
![image](https://user-images.githubusercontent.com/102035198/207216140-b46545cb-079e-4be9-82c3-f83768c6444d.png)


# 수강 신청
``` html
<section class="section">
		<h2>투표 하기</h2>
		<form name="classData" action="join_p.jsp" method="post" onsubmit="return chkVal()">
			<table class="table_line">
				<tr>
					<th>수강월</th>
					<td><input type="text" name="resist_month" size="20"><span> 2022년03월 예)202203</span></td>
				</tr>
				<tr>
					<th>회원명</th>
					<td>
						<select name="c_name" onchange="vDisplay(this.value)">
							<option value="none">회원명</option>
							<% while (rs.next()){ %>
								<option value="<%=rs.getString("c_no")%>">
									<%=rs.getString("c_name")%>
								</option>
							<% } %>
						</select>
					</td>
				</tr>
				<tr>
					<th>회원번호</th>
					<td><input type="text" name="c_no" size="20" readonly><span>예)10001</span></td>
				</tr>
				<tr>
					<th>강의장소</th>
					<td><input type="text" name="class_area" size="20"></td>
				</tr>
				<tr>
					<th>강의명</th>
					<td>
						<select name="class_name" onchange="calTuition(this.value)">
							<option value="none">강의신청</option>
							<% while (rs2.next()){ %>
								<option value="<%=rs2.getString("teacher_code")%>">
									<%=rs2.getString("class_name")%>
								</option>
							<% } %>
						</select>
					</td>
				</tr>
				<tr>
					<th>수강료</th>
					<td><input type="text" name="tuition" size="20" readonly></td>
				</tr>
				<tr>
					<td colspan="2">
						<input type="submit" value="수강신청">
						<input type="button" value="다시쓰기" onclick="re()">
					</td>
				</tr>
			</table>
		</form>
	</section>
```
회원명과 강의명은 데이터베이스에서 가져와 체크박스식으로 선택한다 <br>
```javascript
function chkVal() {
		var cls = document.classData;
		
		if(!cls.resist_month.value) {
			alert("수강월이 입력되지 않았습니다!");
			cls.resist_month.focus();
			return false;
		}
		if(cls.c_name.value=="none") {
			alert("회원명이 선택되지 않았습니다!");
			cls.c_name.focus();
			return false;
		}
		if(!cls.class_area.value) {
			alert("강의장소가 입력되지 않았습니다!");
			cls.class_area.focus();
			return false;
		}
		if(cls.class_name.value=="none") {
			alert("강의명이 선택되지 않았습니다!");
			cls.class_name.focus();
			return false;
		}
	}
```
입력되지 않은 칸이 있을 경우 경고창을 띄워주고 포커스를 이동시켜준다
```script
function vDisplay(code) {
		document.classData.c_no.value = code;
		document.classData.class_name.value = "none";
		document.classData.tuition.value = "";
	}
```
회원명을 선택시 회원번호를 회원명에 맞는 회원번호를 자동으로 입력해준다<br>
![image](https://user-images.githubusercontent.com/102035198/207220940-045090b6-3cc7-4988-a0c8-a4a893b484ba.png)<br>
![image](https://user-images.githubusercontent.com/102035198/207220962-f5c6f5e2-7aa5-4297-b18b-bffef9a0f85e.png)<br>
```
	function calTuition(tcode) {
		var mbr = document.classData.c_no.value;
		if(!mbr) {
			alert("회원명을 먼저 선택하세요.");
			document.classData.class_name[0].selected = true;
			document.classData.c_name.focus();
		} else {
			
			var salePrice = 0;
			switch (tcode) {
				case "100":
					salePrice = 100000;
					break;
				case "200":
					salePrice = 200000;
					break;
				case "300":
					salePrice = 300000;
					break;
				case "400":
					salePrice = 400000;
					break;
			}
			
			if(mbr.charAt(0)=='2') {
				alert("수강료가 50% 할인 되었습니다.");
				salePrice = salePrice / 2;
			}
			
			document.classData.tuition.value = salePrice;
		}
	}
```
회원명 선택 후 강의명을 선택하면 수강료가 강의명의 수강료로 입력된다<br>
회원번호가 20000이상인 경우 알림창이 나오면서 50%로 할인된다<br>
![image](https://user-images.githubusercontent.com/102035198/207220790-ffb120d7-db63-491e-b085-c465b962ae12.png)<br>
![image](https://user-images.githubusercontent.com/102035198/207220885-2c971e90-a60d-4b2d-ba31-b62b6ace238e.png)<br>
![image](https://user-images.githubusercontent.com/102035198/207220832-e5b8f7ee-4e82-4d35-8d39-2d9a547bf379.png)<br>
```
	function re() {
		alert("정보를 지우고 처음부터 다시 입력 합니다!");
		document.classData.reset();
		document.classData.resist_month.focus();
	}
```
