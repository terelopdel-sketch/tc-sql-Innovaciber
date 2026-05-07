# SQL Murder Mystery — Resolución del Caso

---

## Contexto del Caso

| Dato | Detalle |
|---|---|
| **Tipo de crimen** | Asesinato |
| **Fecha** | 15 de enero de 2018 |
| **Lugar** | SQL City |

---

## Paso 1 — Reconocimiento de la Base de Datos

Primero exploramos las tablas disponibles:

```sql
SELECT name 
FROM sqlite_master
WHERE type = 'table'
```

Luego inspeccionamos la estructura de la tabla principal:

```sql
SELECT sql 
FROM sqlite_master
WHERE name = 'crime_scene_report'
```

---

## Paso 2 — Buscar el Informe del Crimen

```sql
SELECT *
FROM crime_scene_report
WHERE date = 20180115
```

> **Resultado:** Entre múltiples registros, identificamos visualmente el único asesinato ocurrido ese día en SQL City.

### Pista obtenida

> *"Las grabaciones de seguridad muestran que había 2 testigos. El primer testigo vive en la última casa de **Northwestern Dr**. El segundo testigo, llamado **Annabel**, vive en algún lugar de **Franklin Ave**."*

---

## Paso 3 — Identificar a los Testigos

### Testigo 1 — Último residente de Northwestern Dr

```sql
SELECT * 
FROM person
WHERE address_street_name = 'Northwestern Dr'
ORDER BY address_number
```

Nos fijamos en la **última fila** de la tabla (mayor número de calle):

| Campo | Valor |
|---|---|
| **ID** | 14887 |
| **Nombre** | Morty Schapiro |
| **License ID** | 118009 |
| **Nº Calle** | 4919 |
| **SSN** | 111564949 |

---

### Testigo 2 — Annabel en Franklin Ave

```sql
SELECT * 
FROM person
WHERE address_street_name = 'Franklin Ave' 
AND name LIKE '%Annabel%'
```

| Campo | Valor |
|---|---|
| **ID** | 16371 |
| **Nombre** | Annabel Miller |
| **License ID** | 490173 |
| **Nº Calle** | 103 |
| **SSN** | 318771143 |

---

## Paso 4 — Consultar las Entrevistas

### Declaración de Annabel Miller

```sql
SELECT *
FROM interview
WHERE person_id = 16371
```

> *"Vio el asesinato y reconoció al asesino porque fue a su gimnasio la semana pasada, el **9 de enero**."*

También verificamos su membresía:

```sql
SELECT *
FROM get_fit_now_member
WHERE name = 'Annabel Miller'
```

| Dato | Valor |
|---|---|
| **Inicio membresía** | 20160208 |
| **Estado** | Gold |

---

### Declaración de Morty Schapiro

```sql
SELECT *
FROM interview
WHERE person_id = 14887
```

> *"Oyó un disparo y vio pasar a un hombre corriendo con una bolsa que ponía **'Get Fit Now Gym'**. El número de socio empezaba por **`48Z`** — solo los miembros **Gold** tienen esas bolsas. Logró ver al hombre subirse a un coche con matrícula que incluía **`H42W`**."*

---

## Paso 5 — Identificar al Asesino

Con todas las pistas reunidas, lanzamos la consulta final:

```sql
SELECT
    p.name,
    p.id AS person_id,
    m.id AS member_id,
    m.membership_status,
    d.plate_number
FROM get_fit_now_member AS m
JOIN person AS p
    ON m.person_id = p.id
JOIN drivers_license AS d
    ON p.license_id = d.id
JOIN get_fit_now_check_in AS c
    ON m.id = c.membership_id
WHERE m.membership_status = 'gold'
AND m.id LIKE '48Z%'
AND d.plate_number LIKE '%H42W%'
AND c.check_in_date = 20180109
```

---

## Resultado Final

| Campo | Valor |
|---|---|
| **Nombre** | Jeremy Bowers |
| **Person ID** | 67318 |
| **Member ID** | 48Z55 |
| **Matrícula** | 0H42W2 |

> ## Caso Resuelto
> El asesino es **Jeremy Bowers**, miembro Gold del gimnasio Get Fit Now, identificado gracias a su número de socio y la matrícula de su vehículo.