# DB-day7
SELECT gubun
      , SUM(loan_jan_amt) as 합계
FROM kor_loan_status
GROUP BY ROLLUP(gubun);
-- member 직업별 마일리지의 합계와 전체 총계를 구하시오
SELECT mem_job
     , SUM(mem_mileage) 합계
FROM member
GROUP BY ROLLUP(mem_job);

SELECT period
     , gubun
     , sum(loan_jan_amt) 합계
FROM kor_loan_status
WHERE period LIKE '2013%'
GROUP BY ROLLUP(period, gubun); -- period별 소개


SELECT gubun
     , period
     , sum(loan_jan_amt) 합계
FROM kor_loan_status
WHERE period LIKE '2013%'
GROUP BY ROLLUP(gubun, period);

--employees 테이블의 직원의 각
-- 부서별 직원수와 전체 직원수를 출력하시오
-- grouping_id  null 값을 바꿀 수 있다.
SELECT CASE WHEN department_id IS NULL AND grouping_id(department_id) =0 THEN '부서없음'
            WHEN department_id IS NULL AND grouping_id(department_id) =1 THEN '총계'
            ELSE TO_CHAR(department_id)
            END as 부서
     , count(*) as  직원수
FROM employees
GROUP BY ROLLUP(department_id);

-- 직원별 이번 월급을 commission_pct 만큼 추가지급 하려고 합니다.
-- 월급과, 추가금액, 합산금액을 출력하시오
-- NVL(대상건, 변경값) 대상건이 null 일 경우 변경값으로 대체
SELECT emp_name
     , salary 월급
     , salary * NVL(commission_pct,0) as 상여
     , salary + (salary * NVL(commission_pct,0)) as 합산금액
FROM employees;

-- member 회원중 대전지역의 회원의 직업별 인원수와 마일리지 합계 금액을 출력하시오(총계)
SELECT *
FROM member;

SELECT NVL(mem_job,'합 계') as 직업
     , COUNT(*) as 회원수
     , SUM(mem_mileage) as 마일리지합계
FROM member
WHERE mem_add1 LIKE '%대전%'
GROUP BY ROLLUP(mem_job);

-- 집합 UNION (합집합), UNION ALL 전체집합, MINUS 차집합 INTERSECT 교집합
-- 조회결과의 컬럼수아 타입이 같으면 집합적용가능(정렬은 마지막만 가능)
SELECT seq, goods -- seq???
FROM exp_goods_asia
WHERE country ='한국'
INTERSECT  
SELECT seq, goodS
FROM exp_goods_asia
WHERE country ='일본';

SELECT seq, goods
FROM exp_goods_asia
WHERE country ='한국'
UNION
SELECT 1, 'hi'
FROM dual;

SELECT mem_job
     , SUM(mem_mileage) as 합계
FROM member
GROUP BY mem_job
UNION
SELECT '합계'
     , SUM(mem_mileage)
FROM member
ORDER BY 2 asc;

--조인
SELECT *
FROM member
WHERE mem_name ='탁원재';

SELECT *
FROM cart
WHERE cart_member ='n001';
-- INNER JOIN (내부조인) (동등조인이라고도 함.)
SELECT member.mem_id
     , member.mem_name
     , cart.cart_member
     , cart.cart_prod
     , cart.cart_qty
FROM member, cart
WHERE member.mem_id=cart.cart_member 
AND member.mem_name ='탁원재';-- mem_id 와 cart_member 값이 동등할때 결합

--SELECT member.mem_id
--     , member.mem_name
--     , NVL(SUM(cart.cart_qty), 0) 상품구매수
--FROM member, cart
--WHERE member.mem_id=cart.cart_member(+) -- outer join 외부조인
--                                        -- null값을 포함시켜야 할때 (+) 사용  NVL 추가함 
--AND member.mem_name ='탁원재'
--GROUP BY member.mem_id
--     , member.mem_name;

SELECT member.mem_id
     , member.mem_name
     , cart.cart_member
     , cart.cart_qty 상품구매수
FROM member, cart
WHERE member.mem_id=cart.cart_member(+) -- outer join 외부조인                     
;

-- 직원의 이름과 부서명을 출력하시오
SELECT employees.emp_name
     , employees.department_id
FROM employees;
SELECT departments.department_id
     , departments.department_name
FROM departments;
-- 조인을 할때는 먼저 각 테이블에서 필요한 컬럼 조회 select문 작성
-- 각 쿼리의 데이터가 맞는지 확인 후
-- 조일을 이용한 select문 작성
SELECT employees.emp_name
     , departments.department_name
FROM employees, departments
WHERE employees.department_id= departments.department_id;


SELECT a.emp_name 
     , b.department_name
FROM employees a, departments b -- 테이블 별칭
WHERE a.department_id= b.department_id;



SELECT emp_name -- 각 테이블 한쪽에만 있는 컬럼은 테이블명을 쓰지 않아도됨
     , department_name
     , a.department_name -- 두테이블에 동일한 컬럼명 있다면 어느한쪽을 정해줘야함
FROM employees a, departments b -- 테이블 별칭
WHERE a.department_id= b.department_id;


-- employees, jobs 테이블을 이용하여
-- 직원의 직무를 출력하시오
SELECT a.employee_id  /*사번*/
     , a.emp_name       /*이름*/
     , a.salary         /*급여*/
     , b.job_title          /*직업명*/
FROM employees a           -- 직원테이블
    , jobs b               -- 직업테이블
WHERE a.job_id = b.job_id
ORDER BY 1;
