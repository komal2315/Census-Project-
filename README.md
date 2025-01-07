# Census-Project
``` sql
select * from Census.dbo.dataset1;

Select * from Census.dbo.dataset2;

---Number of rows

Select count(*) from dataset1;
Select count(*) from dataset2;

--Collect data from jharkhand and bihar

Select * from dataset1 
where State in ('Jharkhand', 'Bihar')


-- Total population of india 

Select Sum(Population) as total from Dataset2


----Average Growth of india 

Select Avg(growth) * 100 as Average_growth from Dataset1;

---- Select Avg growth by state 

Select State, Round(avg(growth),4) * 100 as avg_growth  from Dataset1
group by State


--- Avg Sex_ratio
Select State, Round(avg(Sex_ratio),2) as Avg_Sexratio  from Dataset1
group by State
order by 2 Desc


---Average Literacy Rate
Select State, Round(avg(Literacy),0) as Avg_literacy  from Dataset1
group by State
having Round(avg(Literacy),0) > 90
order by 2 desc

--- Top 3 State avg_growth
Select top 3 State, Round(avg(growth),4) * 100 as avg_growth  from Dataset1
group by State
order by 2 Desc

--- bottom 3 state Sex_ratio
Select top 3 State, Round(avg(Sex_ratio),0) as Avg_Sexratio  from Dataset1
group by State
order by 2 Asc

--- Top and bottom 3 in literacy rate
Drop Table if exists #Topstates

Create Table #Topstates
(State Varchar(255),
Literacy_rate Float)

Insert into  #Topstates
Select State, Round(avg(Literacy),0) as Avg_literacy  from Dataset1
group by State
order by 2 desc;

Select top 3 * from  #Topstates
order by #Topstates.Literacy_rate Desc

----Bottom 3 Literacy Rate
Drop table if exists #Bottomstate

Create table #Bottomstate
(State Varchar(255),
Literact_rate Float)

Insert Into #Bottomstate
Select State, Round(avg(Literacy),0) as Avg_literacy  from Dataset1
group by State
order by 2 desc;

Select top 3 * from #Bottomstate
order by #Bottomstate.Literact_rate asc


-----Union Operator
Select * from 
(Select top 3 * from  #Topstates
order by #Topstates.Literacy_rate Desc)as a
union 
Select * from (Select top 3 * from #Bottomstate
order by #Bottomstate.Literact_rate asc) b;

---States starting with letter A

Select DISTINCT(STATE) from Dataset1
where state Like 'a%' OR State like 'b%';

Select DISTINCT(STATE) from Dataset1
where state Like 'a%' and State like '%m';

---Joining table
Select a.District,a.State, a.Sex_Ratio,b.Population from Census.dbo.dataset1 as a
join Census.dbo.dataset2 as b
on a.District = b.District;

---Total number of males and females
Select State, sum(females), sum(males) from (
Select c.District,c.State, round((c.population/(c.Sex_Ratio + 1)),0) as females, round((c.population - c.population/(c.Sex_Ratio + 1)),0) as males, c.population from
(Select a.District,a.State, a.Sex_Ratio/1000 as sex_ratio,b.Population from Census.dbo.dataset1 as a
join Census.dbo.dataset2 as b
on a.District = b.District) as c) d
group by State;

---Total Literacy rate
Select State, SUM(illiterate)illiterate,sum(literate) literate from(
Select c.District,c.State, round(literacy_ratio * Population,0) as illiterate, round(((1 - literacy_ratio) * population)
,0) as literate,c.population
from
(Select a.District,a.State, a.Literacy/100 as literacy_ratio,b.Population from Census.dbo.dataset1 as a
join Census.dbo.dataset2 as b
on a.District = b.District)c)d
group by state;

----Population in previous cencus

Select e.District,e.previous,e.current_pop,  (e.current_pop-e.previous ) as diff from
(Select d.District, round(sum(Previous_census),0) as previous, round(sum(current_population),0) as current_pop from
(Select c.District,c.State, (c.Population/(1 + c.Growth_rate)) as Previous_census , Population as current_population from
(Select a.District,a.State, a.Growth as Growth_rate ,b.Population from Census.dbo.dataset1 as a
join Census.dbo.dataset2 as b on a.District = b.District) as c)as d
group by d.District) as e
where e.District = 'Thane'


Select d.district,d.state,round(population/(1+growth),0) as previous_census,d.population from
(Select a.district, a.state,a.growth as growth, b.population from Census.dbo.dataset1 as a inner join Census.dbo.dataset2 as b on a.District = b.District) d 
where d.district = 'Thane'

Select a.district, a.state,round(a.growth,4) as growth, b.population from Census.dbo.dataset1 as a inner join Census.dbo.dataset2 as b on a.District = b.District


----population vs area
Select total_area/previous as previous_Area_pp,total_area/current_pop as Current_Area_pp from
(Select round(sum(Previous_census),0) as previous, round(sum(current_population),0) as current_pop, Sum(Area) as total_area from
(Select c.District,c.State, (c.Population/(1 + c.Growth_rate)) as Previous_census , Population as current_population, c.Area_km2 as Area from
(Select a.District,a.State, a.Growth as Growth_rate ,b.Population,b.Area_km2 from Census.dbo.dataset1 as a
join Census.dbo.dataset2 as b on a.District = b.District) as c)as d)as e;

----Window Functions
----Output top 3 district from each state with highest literacy rate

Select * from (
Select State, District, literacy, rank() over (partition by State order by literacy Desc) as Rank_ from Census.dbo.dataset1) a
where Rank_ in (1,2,3);
```


