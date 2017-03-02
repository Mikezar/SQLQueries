# SQLQueries

<h2>Задача </h2>
<hr/>

Пусть существует таблица, хранящая сотрудников организации. Столбцы таблицы следующие: Id, DepartmentId, Name, Salary. Требуется:

<ol>
<li> Описать схему таблицы; </li>

<li> Написать запросы: </li>
<ul><li>Количество сотрудников по каждому подразделению организации;

<li>Имена сотрудников и их зарплата, при этом зарплата таких сотрудников должна быть выше средней зарплаты по всей организации; </li>

<li>Все подразделения и первое наиболее часто встречающееся имя сотрудника в каждом подразделении, количество сотрудников с таким именем в подразделении. </li>
</ul></ol>

Схему можно описать, например, как инструкцию CREATE TABLE (T-SQL).

Запросы могут быть написаны на T-SQL или LINQ.
<hr>

<h2>Решение</h2>

<b>Схема:</b>
<pre>
CREATE TABLE [dbo].Employees (
[Id] INT NOT NULL IDENTITY (1,1),
[DepartmentId] INT NOT NULL,
[Name] NCHAR(25) NOT NULL,
[Salary] INT NOT NULL
CONSTRAINT primarykey_Id PRIMARY KEY (ID),
);

GO

INSERT INTO Employees (DepartmentId, Name, Salary)VALUES 
('1', 'Alfreds Futterkiste', '35000'), ('2', 'Maria Anders', '35000'),
('3', 'Antonio Moreno', '15000'),('1', 'Thomas Hardy', '48000'),
('2', 'Christina Berglund', '67000'),('1', 'John Gragham', '35000'),
('1', 'Mila Valkos', '15000'),('3', 'Andriana Benich', '25000'),
('1', 'Mike Vazovskiy', '25000'),('2', 'John Karter', '25000'),
('3', 'Sarah Connor', '70000'), ('1', 'Alfreds Futterkiste', '45000'),
('3', 'Antonio Moreno', '25000'), ('2', 'Christina Berglund', '57000')
</pre>

1.
<code>
USE Organisation;

SELECT DepartmentId As 'Номер отдела', COUNT(Name) As 'Количество сотрудников' FROM dbo.Employees
GROUP BY DepartmentId;
</code>

2.
<code>
SELECT DepartmentId, Name, COUNT(Name) FROM Employees
WHERE Name IN (SELECT TOP (1) Name FROM Employees Group By Name Having COUNT(*) > 0 order by COUNT(Name) desc)
Group By DepartmentId, Name
Order by DepartmentId, Name desc
</code>

3. 
<code>
USE Organisation;
SELECT DepartmentId, Name, COUNT(Name)
FROM Employees em
GROUP BY DepartmentId, Name
HAVING Count(Name) = (SELECT TOP (1) MAX(cnt) FROM 
       (SELECT DepartmentId, Name, COUNT(*) as cnt
        FROM Employees 
        GROUP BY DepartmentId, Name) as e where e.DepartmentId = em.DepartmentId)
ORDER BY 1,2
</code>
