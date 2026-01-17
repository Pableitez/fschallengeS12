# SQL Murder Mystery - Mi Solución Paso a Paso

Presento la solución al **SQL Murder Mystery**, explicando cómo resolví cada parte del ejercicio con las consultas SQL que utilicé.

---

## 1. Exploro las tablas de la base de datos

Primero quiero ver qué tablas existen para saber dónde buscar información:

```sql
SELECT name 
FROM sqlite_master
WHERE type = 'table';
```

**Resultado:** `crime_scene_report`, `drivers_license`, `facebook_event_checkin`, `interview`, `get_fit_now_member`, `get_fit_now_check_in`, `solution`, `income`, `person`.

---

## 2. Reviso los incidentes del 15 de enero de 2018 en SQL City

```sql
SELECT *
FROM crime_scene_report
WHERE date = 20180115
  AND city = 'SQL City';
```

Veo que hubo dos asaltos y un asesinato. El asesinato menciona dos testigos: uno en "Northwestern Dr" y otro, Annabel, en "Franklin Ave".

---

## 3. Identifico a los testigos

Busco a las personas por dirección:

```sql
-- Primer testigo
SELECT *
FROM person
WHERE address_street_name = 'Northwestern Dr';

-- Segundo testigo
SELECT *
FROM person
WHERE address_street_name = 'Franklin Ave';
```

**Resultados:**

* Morty Schapiro: 4919 Northwestern Dr
* Annabel Miller: 103 Franklin Ave

---

## 4. Reviso las entrevistas de los testigos

```sql
SELECT * FROM interview;
```

Testimonios importantes:

* Morty: vio al sospechoso con **bolsa de "Get Fit Now Gym" Gold** y matrícula parcial 'H42W'.
* Annabel: reconoce al asesino del gimnasio el 9 de enero de 2018.

---

## 5. Reviso miembros del gimnasio

Primero reviso todos los miembros:

```sql
SELECT *
FROM get_fit_now_member;
```

Luego filtro los que estuvieron el 9 de enero:

```sql
SELECT name, membership_status, check_in_date
FROM get_fit_now_member
JOIN get_fit_now_check_in
ON get_fit_now_member.membership_id = get_fit_now_check_in.membership_id
WHERE check_in_date = 20180109;
```

**Resultados relevantes:**

* Joe Germuska
* Jeremy Bowers ✅
* Annabel Miller
  (Otros miembros también estaban pero no son sospechosos según las pistas)

---

## 6. Determino el asesino

Cruzando los datos del testigo y los check-ins, el único que coincide con la bolsa Gold y la fecha es:

```sql
INSERT INTO solution VALUES (1, 'Jeremy Bowers');
SELECT value FROM solution;
```

**Conclusión:** Jeremy Bowers es el asesino.

---

## 7. Investigo el verdadero villano

Reviso asistencia a eventos de Facebook:

```sql
SELECT person_id, attendances
FROM facebook_event_checkin
WHERE event_name = 'SQL Symphony Concert';
```

Candidatos con 3 asistencias:

* Bryan Pardo
* Miranda Priestly ✅

```sql
SELECT *
FROM person
WHERE id IN (24556, 99716);
```

Entonces marco al verdadero villano:

```sql
INSERT INTO solution VALUES (1, 'Miranda Priestly');
SELECT value FROM solution;
```

**Conclusión:** Miranda Priestly es el verdadero villano.

---

## 8. Diagrama de flujo de la investigación

```
[Explorar tablas]
        |
        v
[Revisar crime_scene_report] --> Detecta asesinato en SQL City
        |
        v
[Filtrar testigos por dirección] --> Morty Schapiro, Annabel Miller
        |
        v
[Revisar entrevistas] --> Sospechoso con bolsa Gold y matrícula parcial
        |
        v
[Verificar miembros del gimnasio] --> Chequear check-ins 9 enero
        |
        v
[Sospechosos Gold activos] --> Jeremy Bowers, Joe Germuska, Annabel Miller
        |
        v
[Determinar asesino] --> Jeremy Bowers
        |
        v
[Revisar asistencia a eventos] --> Bryan Pardo, Miranda Priestly
        |
        v
[Determinar verdadero villano] --> Miranda Priestly
```

---

**Resumen final:**

* **Asesino:** Jeremy Bowers
* **Verdadero villano:** Miranda Priestly

Cada consulta SQL y cruce de datos nos permitió llegar a esta conclusión paso a paso.
