# MySQL-Stydy-3

*join이란? : 두 테이블의 공통된 정보(key값)를 기준으로 테이블을 연결해서 한 테이블 처럼 보는 것
ex) user_id 필드를 기준으로 users 테이블과 orders 테이블을 연결해서 한 눈에 보고 싶을 때

1)Left join (왼쪽에 붙임)

2)Inner join (교집합)

quiz1. orders 테이블에 users 테이블 연결해보기
답: SELECT * from orders o 
inner join users u 
on o.user_id = u.user_id 

quiz2. checkins 테이블에 users 테이블 연결해보기
답:SELECT * from checkins c  
inner join users u 
on c.user_id  = u.user_id 

quiz3. enrolleds 테이블에 courses 테이블 연결해보기
답:SELECT * from enrolleds e 
inner join courses c 
on e.course_id = c.course_id 


*tip: 항상 from에 들어간 테이블을 기준으로, 다른 테이블이 붙는다고 생각하면 편하다.



Main Quiz1. checkins 테이블에 courses 테이블 연결해서 통계치 내보기
'오늘의 다짐' 정보에 과목 정보를 연결해 과목별 '오늘의 다짐' 갯수를 세어보자!
답:SELECT c1.course_id, c2.title, count(*) as cnt FROM checkins c1 
inner join courses c2
on c1.course_id = c2.course_id 
GROUP by c1.course_id 

Main Quiz2. point_users 테이블에 users 테이블 연결해서 순서대로 정렬해보기
많은 포인트를 얻은 순서대로 유저 데이터 정렬해서 보기
답:SELECT pu.user_id, u.name, u.email, pu.point from point_users pu 
inner join users u 
on pu.user_id = u.user_id 
order by pu.point desc

Main Quiz3. orders 테이블에 users 테이블 연결해서 통계치 내보기
네이버 이메일 사용하는 유저의 성씨별 주문건수 세어보기
답: SELECT u.name,COUNT(*) as cnt from orders o 
inner join users u 
on o.user_id = u.user_id  
where o.email like '%naver.com'
group by u.name 

->위 쿼리가 실행되는 순서: from → join → where → group by → select
1. from orders o: orders 테이블 데이터 전체를 가져오고 o라는 별칭을 붙입니다.
2. inner join users u on o.user_id = u.user_id : users 테이블을 orders 테이블에 붙이는데, orders 테이블의 user_id와 동일한 user_id를 갖는 users 테이블 데이터를 붙입니다. (*users 테이블에 u라는 별칭을 붙입니다)
3. where u.email like '%naver.com': users 테이블 email 필드값이 naver.com으로 끝나는 값만 가져옵니다.
4. group by u.name: users 테이블의 name값이 같은 값들을 뭉쳐줍니다.
5. select u.name, count(u.name) as count_name : users 테이블의 name필드와 name 필드를 기준으로 뭉쳐진 갯수를 세어서 출력해줍니다.

*Union: 결과물을 합칠 때 사용
ex) select c1.title, c2.week, count(*) as cnt from courses c1
inner join checkins c2 on c1.course_id = c2.course_id
inner join orders o on c2.user_id = o.user_id
where o.created_at >= '2020-08-01'
group by c1.title, c2.week
order by c1.title, c2.week 
와
select '7월' as month, c.title, c2.week, count(*) as cnt from checkins c2
inner join courses c on c2.course_id = c.course_id
inner join orders o on o.user_id = c2.user_id
where o.created_at < '2020-08-01'
group by c2.course_id, c2.week
order by c2.course_id, c2.week

를 합치려면 중간에 union all을 넣어서

(
	select '7월' as month, c.title, c2.week, count(*) as cnt from checkins c2
	inner join courses c on c2.course_id = c.course_id
	inner join orders o on o.user_id = c2.user_id
	where o.created_at < '2020-08-01'
	group by c2.course_id, c2.week
  order by c2.course_id, c2.week
)
union all
(
	select '8월' as month, c.title, c2.week, count(*) as cnt from checkins c2
	inner join courses c on c2.course_id = c.course_id
	inner join orders o on o.user_id = c2.user_id
	where o.created_at > '2020-08-01'
	group by c2.course_id, c2.week
  order by c2.course_id, c2.week
)

이렇게 붙여준다. 
*tip: Union에서는 코드안에 orders를 넣어도 정렬이 되지 않으므로 괄호 밖에서 해줘야함.
