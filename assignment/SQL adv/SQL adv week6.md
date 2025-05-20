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
