# 作业一：员工sql练习

```sql
#1. 查询所有员工的姓名、邮箱和工作岗位。
select first_name,last_name,email,job_title
from employees;
# 2. 查询所有部门的名称和位置。
select dept_name,location
from departments;
# 3. 查询工资超过70000的员工姓名和工资。
select employees.first_name,employees.last_name,employees.salary
from employees
where salary>70000;
# 4. 查询IT部门的所有员工。
select *
from employees
where dept_id in (select dept_id from departments where dept_name='IT');
# 5. 查询入职日期在2020年之后的员工信息。
select *
from employees
where hire_date>'2019-12-31';
# 6. 计算每个部门的平均工资。
select dept_id, avg(salary)
from employees
group by dept_id;
# 7. 查询工资最高的前3名员工信息。
select *
from employees
order by salary desc
limit 3;
# 8. 查询每个部门员工数量。
select dept_id, sum(emp_id)
from employees
group by dept_id;
# 9. 查询没有分配部门的员工。
select *
from employees
where dept_id is null ;
# 10. 查询参与项目数量最多的员工。
select emp_id,sumpro
from (
    select emp_id,count(project_id) sumpro,
    dense_rank() over (order by count(project_id)  desc) as sumpro_rank
    from employee_projects
    group by emp_id
     ) ps
where ps.sumpro_rank=1;
# 13. 查询即将在半年内到期的项目。
select projects.project_id,end_date
from projects
where end_date<=curdate()+interval 6 month and end_date>curdate();
# 14. 查询至少参与了两个项目的员工。
select emp_id,count(project_id) copro
from employee_projects
group by emp_id
having copro>=2;
# 17. 查询工资第二高的员工信息。
select *
from (select emp_id,salary,
             dense_rank() over (order by salary desc ) salary_rank
       from employees
     )ps
where salary_rank=2;
# 18. 查询每个部门工资最高的员工。
select emp_id,dept_id,salary
from (select emp_id,dept_id,salary,dense_rank() over (partition by dept_id order by salary desc ) sa_rank
      from employees
     )ps
where sa_rank=1;
# 21. 查询每个员工的上级主管(假设emp_id小的是上级)。
select emp_id,superior
 from employees ,(select dept_id, min(emp_id) superior
                    from employees
                    group by dept_id) ps
where employees.dept_id=ps.dept_id
order by emp_id;
# 26. 查询跨部门的项目(参与员工来自不同部门)。
select project_id,count(distinct dept_id) codept
from employees,employee_projects
where employee_projects.emp_id=employees.emp_id
group by project_id
having count(distinct dept_id)>1;
# 28. 查询本月过生日的员工(假设hire_date是生日)。
select emp_id,hire_date birthday
from employees
where month(hire_date)=month(curdate());
# 29. 查询即将在90天内到期的项目和负责该项目的员工。
select projects.project_id,emp_id
from projects,employee_projects
where projects.project_id=employee_projects.project_id and end_date <= curdate()+ interval 90 day and end_date>curdate();
# 30. 计算每个项目的持续时间(天数)。
select *,timestampdiff(day,projects.start_date,projects.end_date) as workdate
from projects;
# 34. 计算每个员工的薪资涨幅(假设每年涨5%)。
select emp_id,employees.salary-(salary/power(1.05,datediff(curdate(),employees.hire_date)/365)) as salary_growth
from employees;
# 38. 查询哪些部门的平均工资高于公司的平均工资。
select employees.dept_id,avg(salary)
from employees
group by dept_id
having avg(salary)>(select avg(salary)
                    from employees);
# 42. 查询每个经理(假设job_title包含'Manager'的都是经理)管理的员工数量。
select ps.dept_id, count(employees.emp_id)-1 man_em_count
from  (select emp_id manger_id,dept_id from employees where job_title like '%Manger%') ps left join employee.employees on ps.dept_id=employees.dept_id
group by ps.dept_id;
# 44. 计算每个部门的男女比例(假设以名字首字母A-M为女性,N-Z为男性)。
SELECT dept_id,
       SUM(CASE WHEN SUBSTRING(first_name, 1, 1) BETWEEN 'A' AND 'M' THEN 1 ELSE 0 END) AS female_count,
       SUM(CASE WHEN SUBSTRING(first_name, 1, 1) BETWEEN 'N' AND 'Z' THEN 1 ELSE 0 END) AS male_count,
       SUM(CASE WHEN SUBSTRING(first_name, 1, 1) BETWEEN 'A' AND 'M' THEN 1 ELSE 0 END) / NULLIF(SUM(CASE WHEN SUBSTRING(first_name, 1, 1) BETWEEN 'N' AND 'Z' THEN 1 ELSE 0 END), 0) AS female_to_male_ratio
FROM employees
GROUP BY dept_id;
# 46. 查询连续3天都有员工入职的日期。
SELECT t1.hire_date
FROM employees t1
         JOIN employees t2 ON t2.hire_date= t1.hire_date+ INTERVAL 1 DAY
         JOIN employees t3 ON t3.hire_date= t1.hire_date+ INTERVAL 2 DAY
GROUP BY t1.hire_date;
# 49. 计算每个员工的工资与其所在部门平均工资的差值。
select emp_id,salary-avgsa
from employees ,(SELECT employees.dept_id,avg(salary) avgsa
                 from employees
                 group by dept_id

)PS
where PS.dept_id=employees.dept_id

```

# 作业二：选课sql练习

```sql
# 1. 查询所有学生的信息。
SELECT * FROM student;

# 2. 查询所有课程的信息。
SELECT * FROM course;

# 3. 查询所有学生的姓名、学号和班级。
SELECT name, student_id, my_class FROM student;

# 4. 查询所有教师的姓名和职称。
SELECT name, title FROM teacher;

# 5. 查询不同课程的平均分数。
SELECT course_id, AVG(score) AS average_score FROM score GROUP BY course_id;

# 6. 查询每个学生的平均分数。
SELECT student_id, AVG(score) AS average_score FROM score GROUP BY student_id;

# 7. 查询分数大于85分的学生学号和课程号。
SELECT student_id, course_id FROM score WHERE score > 85;

# 8. 查询每门课程的选课人数。
SELECT course_id, COUNT(student_id) AS number_of_students FROM score GROUP BY course_id;

# 9. 查询选修了"高等数学"课程的学生姓名和分数。
SELECT s.name, sc.score
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
         JOIN course c ON sc.course_id = c.course_id
WHERE c.course_name = '高等数学';

# 10. 查询没有选修"大学物理"课程的学生姓名。
SELECT name
FROM student
WHERE student_id NOT IN (
    SELECT sc.student_id
    FROM score sc
             JOIN course c ON sc.course_id = c.course_id
    WHERE c.course_name = '大学物理'
);

# 11. 查询C001比C002课程成绩高的学生信息及课程分数。
SELECT s.*, sc1.score AS C001_score, sc2.score AS C002_score
FROM student s
         JOIN score sc1 ON s.student_id = sc1.student_id AND sc1.course_id = 'C001'
         JOIN score sc2 ON s.student_id = sc2.student_id AND sc2.course_id = 'C002'
WHERE sc1.score > sc2.score;

# 12. 统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比
SELECT
    c.course_id,
    c.course_name,
    SUM(CASE WHEN sc.score BETWEEN 85 AND 100 THEN 1 ELSE 0 END) AS "[100-85]",
    SUM(CASE WHEN sc.score BETWEEN 70 AND 84 THEN 1 ELSE 0 END) AS "[85-70]",
    SUM(CASE WHEN sc.score BETWEEN 60 AND 69 THEN 1 ELSE 0 END) AS "[70-60]",
    SUM(CASE WHEN sc.score BETWEEN 0 AND 59 THEN 1 ELSE 0 END) AS "[60-0]",
    ROUND(SUM(CASE WHEN sc.score BETWEEN 85 AND 100 THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS "[100-85]_percent",
    ROUND(SUM(CASE WHEN sc.score BETWEEN 70 AND 84 THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS "[85-70]_percent",
    ROUND(SUM(CASE WHEN sc.score BETWEEN 60 AND 69 THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS "[70-60]_percent",
    ROUND(SUM(CASE WHEN sc.score BETWEEN 0 AND 59 THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS "[60-0]_percent"
FROM course c
         JOIN score sc ON c.course_id = sc.course_id
GROUP BY c.course_id, c.course_name;

# 13. 查询选择C002课程但没选择C004课程的成绩情况(不存在时显示为 null )。
SELECT s.student_id, s.name, sc.score AS C002_score
FROM student s
         JOIN score sc ON s.student_id = sc.student_id AND sc.course_id = 'C002'
         LEFT JOIN score sc2 ON s.student_id = sc2.student_id AND sc2.course_id = 'C004'
WHERE sc2.student_id IS NULL;

# 14. 查询平均分数最高的学生姓名和平均分数。
SELECT s.name, AVG(sc.score) AS average_score
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
GROUP BY s.student_id, s.name
ORDER BY average_score DESC
LIMIT 1;

# 15. 查询总分最高的前三名学生的姓名和总分。
SELECT s.name, SUM(sc.score) AS total_score
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
GROUP BY s.student_id, s.name
ORDER BY total_score DESC
LIMIT 3;

# 16. 查询各科成绩最高分、最低分和平均分。
SELECT
    c.course_id,
    c.course_name,
    MAX(sc.score) AS highest_score,
    MIN(sc.score) AS lowest_score,
    AVG(sc.score) AS average_score,
    ROUND(SUM(CASE WHEN sc.score >= 60 THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS pass_rate,
    ROUND(SUM(CASE WHEN sc.score BETWEEN 70 AND 80 THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS medium_rate,
    ROUND(SUM(CASE WHEN sc.score BETWEEN 80 AND 90 THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS good_rate,
    ROUND(SUM(CASE WHEN sc.score >= 90 THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS excellent_rate,
    COUNT(*) AS number_of_students
FROM course c
         JOIN score sc ON c.course_id = sc.course_id
GROUP BY c.course_id, c.course_name
ORDER BY number_of_students DESC, c.course_id ASC;

# 17. 查询男生和女生的人数。
SELECT gender, COUNT(*) AS number
FROM student
GROUP BY gender;

# 18. 查询年龄最大的学生姓名。
SELECT name
FROM student
ORDER BY age DESC
LIMIT 1;

# 19. 查询年龄最小的教师姓名。
SELECT name
FROM teacher
ORDER BY birthdate DESC
LIMIT 1;

# 20. 查询学过「张教授」授课的同学的信息。
SELECT DISTINCT s.*
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
         JOIN course c ON sc.course_id = c.course_id
         JOIN teacher t ON c.teacher_id = t.teacher_id
WHERE t.name = '张教授';

# 21. 查询至少有一门课与学号为"2021001"的同学所学相同的同学的信息
SELECT DISTINCT s.*
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
WHERE sc.course_id IN (
    SELECT course_id
    FROM score
    WHERE student_id = '2021001'
) AND s.student_id != '2021001';

# 22. 查询每门课程的平均分数，并按平均分数降序排列
SELECT c.course_name, AVG(sc.score) AS avg_score
FROM course c
         JOIN score sc ON c.course_id = sc.course_id
GROUP BY c.course_id
ORDER BY avg_score DESC;

# 23. 查询学号为"2021001"的学生所有课程的分数
SELECT c.course_name, sc.score
FROM score sc
         JOIN course c ON sc.course_id = c.course_id
WHERE sc.student_id = '2021001';

# 24. 查询所有学生的姓名、选修的课程名称和分数
SELECT s.name, c.course_name, sc.score
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
         JOIN course c ON sc.course_id = c.course_id;

# 25. 查询每个教师所教授课程的平均分数
SELECT t.name, AVG(sc.score) AS avg_score
FROM teacher t
         JOIN course c ON t.teacher_id = c.teacher_id
         JOIN score sc ON c.course_id = sc.course_id
GROUP BY t.teacher_id;

# 26. 查询分数在80到90之间的学生姓名和课程名称
SELECT s.name, c.course_name
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
         JOIN course c ON sc.course_id = c.course_id
WHERE sc.score BETWEEN 80 AND 90;

# 27. 查询每个班级的平均分数
SELECT s.class, AVG(sc.score) AS avg_score
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
GROUP BY s.class;

# 28. 查询没学过"王讲师"老师讲授的任一门课程的学生姓名
SELECT DISTINCT s.name
FROM student s
WHERE s.student_id NOT IN (
    SELECT sc.student_id
    FROM score sc
             JOIN course c ON sc.course_id = c.course_id
             JOIN teacher t ON c.teacher_id = t.teacher_id
    WHERE t.name = '王讲师'
);

# 29. 查询两门及其以上小于85分的同学的学号，姓名及其平均成绩
SELECT s.student_id, s.name, AVG(sc.score) AS avg_score
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
WHERE sc.score < 85
GROUP BY s.student_id
HAVING COUNT(sc.course_id) >= 2;

# 30. 查询所有学生的总分并按降序排列
SELECT s.student_id, s.name, SUM(sc.score) AS total_score
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
GROUP BY s.student_id
ORDER BY total_score DESC;

# 31. 查询平均分数超过85分的课程名称。
SELECT c.course_name
FROM course c
         JOIN score sc ON c.course_id = sc.course_id
GROUP BY c.course_id
HAVING AVG(sc.score) > 85;

# 32. 查询每个学生的平均成绩排名。
SELECT s.student_id, s.name, AVG(sc.score) AS avg_score,
       RANK() OVER (ORDER BY AVG(sc.score) DESC) AS rank
FROM student s
    JOIN score sc ON s.student_id = sc.student_id
GROUP BY s.student_id;

# 33. 查询每门课程分数最高的学生姓名和分数。
SELECT c.course_name, s.name, MAX(sc.score) AS max_score
FROM course c
         JOIN score sc ON c.course_id = sc.course_id
         JOIN student s ON sc.student_id = s.student_id
GROUP BY c.course_id;

# 34. 查询选修了"高等数学"和"大学物理"的学生姓名。
SELECT DISTINCT s.name
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
         JOIN course c ON sc.course_id = c.course_id
WHERE c.course_name IN ('高等数学', '大学物理')
GROUP BY s.student_id
HAVING COUNT(DISTINCT c.course_name) = 2;

# 35. 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩（没有选课则为空）。
SELECT s.student_id, s.name, c.course_name, sc.score,
       AVG(sc.score) OVER (PARTITION BY s.student_id) AS avg_score
FROM student s
         LEFT JOIN score sc ON s.student_id = sc.student_id
         LEFT JOIN course c ON sc.course_id = c.course_id
ORDER BY avg_score DESC;

# 36. 查询分数最高和最低的学生姓名及其分数。
SELECT s.name, sc.score
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
WHERE sc.score = (SELECT MAX(score) FROM score)
UNION
SELECT s.name, sc.score
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
WHERE sc.score = (SELECT MIN(score) FROM score);

# 37. 查询每个班级的最高分和最低分。
SELECT s.class, MAX(sc.score) AS max_score, MIN(sc.score) AS min_score
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
GROUP BY s.class;

# 38. 查询每门课程的优秀率（优秀为90分）。
SELECT c.course_name,
       SUM(CASE WHEN sc.score >= 90 THEN 1 ELSE 0 END) * 100.0 / COUNT(*) AS excellent_rate
FROM course c
         JOIN score sc ON c.course_id = sc.course_id
GROUP BY c.course_id;

# 39. 查询平均分数超过班级平均分数的学生。
SELECT s.student_id, s.name, AVG(sc.score) AS avg_score
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
GROUP BY s.student_id
HAVING AVG(sc.score) > (
    SELECT AVG(sc.score)
    FROM score sc
             JOIN student st ON sc.student_id = st.student_id
    WHERE st.class = s.class
);

# 40. 查询每个学生的分数及其与课程平均分的差值。
SELECT s.student_id, s.name, c.course_name, sc.score,
       sc.score - AVG(sc.score) OVER (PARTITION BY c.course_id) AS score_diff
FROM student s
         JOIN score sc ON s.student_id = sc.student_id
         JOIN course c ON sc.course_id = c.course_id;
```

