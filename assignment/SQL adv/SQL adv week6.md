# 1번
```SQL
select
  a.name
from
  athletes as a
  join records as r on a.id = r.athlete_id
  join teams as t on r.team_id = t.id
  join events as e on r.event_id = e.id
  join games as g on r.game_id = g.id
WHERE
  g.year >= 2000
  and r.medal is not null
group by
  a.id,
  a.name
HAVING
  COUNT(DISTINCT t.id) >= 2
order by
  a.name asc
```

# 2번
```SQL
SELECT 
    SUBSTR(o.order_date, 1, 7) AS order_month,
    SUM(CASE WHEN o.order_id NOT LIKE 'C%' THEN oi.price * oi.quantity ELSE 0 END) AS ordered_amount,
    SUM(CASE WHEN o.order_id LIKE 'C%' THEN oi.price * oi.quantity ELSE 0 END) AS canceled_amount,
    SUM(oi.price * oi.quantity) AS total_amount
FROM order_items oi
JOIN orders o ON o.order_id = oi.order_id
GROUP BY order_month
ORDER BY order_month;
```

# 3번
```SQL
SELECT
    e1.user_a_id as user_a_id,
    e1.user_b_id as user_b_id,
    e2.user_b_id as user_c_id
FROM edges e1
JOIN edges e2 
    ON e1.user_a_id = e2.user_a_id 
    AND e1.user_b_id < e2.user_b_id
JOIN edges e3 
    ON e3.user_a_id = e1.user_b_id 
    AND e3.user_b_id = e2.user_b_id
WHERE 3820 IN (e1.user_a_id, e1.user_b_id, e2.user_b_id)
    AND e1.user_a_id < e1.user_b_id
    AND e1.user_b_id < e2.user_b_id
ORDER BY user_a_id, user_b_id, user_c_id;
```