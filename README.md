# Решение Ильи Майорова задач по SQL в Тинькофф Старт

--Таблица 1: Пилоты называется pilots; Таблица 2: самолеты называется planes;
--Таблица 3: Рейсы называется flights.

--Задача 1. Напишите запрос, который выведет пилотов, которые в качестве
--второго пилота в августе этого года трижды ездили в аэропорт Шереметьево.

SELECT pilots.pilot_id, pilots.name FROM pilots
INNER JOIN flights ON flights.second_pilot_id = pilots.pilot_id
WHERE DATEPART(MONTH, flight_dt) = 8 AND DATEPART(YEAR, flight_dt) = DATEPART(YEAR, GETDATE()) AND flights.destination = 'Шереметьево'
GROUP BY pilots.pilot_id, pilots.name
HAVING COUNT(flights.flight_id) = 3;

--Задача 2. Выведите пилотов старше 45 лет, совершали полеты на
--самолетах с количеством пассажиров больше 30.

SELECT pilots.pilot_id, pilots.name FROM pilots
INNER JOIN flights ON flights.first_pilot_id = pilots.pilot_id OR
flights.second_pilot_id = pilots.pilot_id
INNER JOIN planes ON planes.plane_id = flights.plane_id AND planes.cargo_flg = 0
WHERE pilots.age > 45 AND flights.quantity > 30
GROUP BY pilots.pilot_id, pilots.name

--Задача 3. Выведите ТОП 10 пилотов-капитанов (first_pilot_id), которые
--совершили наибольшее число грузовых перелетов в этом году.

SELECT TOP 10 pilots.pilot_id, pilots.name FROM pilots
INNER JOIN flights ON flights.first_pilot_id = pilots.pilot_id
INNER JOIN planes ON planes.plane_id = flights.plane_id AND planes.cargo_flg = 1
WHERE DATEPART(YEAR, flights.flight_dt) = DATEPART(YEAR, GETDATE())
GROUP BY pilots.pilot_id, pilots.name
ORDER BY COUNT(flights.flight_id) DESC;
