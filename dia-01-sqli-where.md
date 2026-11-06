# Día 01 — SQL injection en cláusula WHERE (datos ocultos)

**Fecha:** 2026-11-05
**Plataforma:** PortSwigger Web Security Academy
**Categoría:** Web
**Tiempo:** XX min
**Dificultad:** ⭐☆☆
**Link:** https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data

---

## 🎯 Objetivo
Hacer que la tienda muestre productos no publicados (`released = 0`) manipulando el filtro de categoría.

## 🛠️ Herramientas usadas
- Navegador (modificar el parámetro `category` en la URL)
- Burp Suite Community *(opcional, para ver y editar la petición)*

## 🔍 Qué hice (paso a paso)
1. Abrí una categoría y vi la URL: `/filter?category=Gifts`
2. Deduje la consulta probable:
   `SELECT * FROM products WHERE category = 'Gifts' AND released = 1`
3. Cambié el parámetro a: `Gifts' OR 1=1--`
4. La consulta quedó: `... WHERE category = 'Gifts' OR 1=1--' AND released = 1`
5. El `--` comentó el resto y `OR 1=1` volvió verdadera la condición → aparecieron todos los productos.

## 💡 Por qué funcionó
La aplicación concatena la entrada del usuario directamente en la consulta SQL. La comilla `'` cierra el string y lo que sigue se interpreta como código SQL, no como dato.

## 🛡️ Cómo se previene / detecta
- **Prevención:** consultas parametrizadas / prepared statements; nunca concatenar input.
  ```python
  # ❌ Vulnerable
  cursor.execute(f"SELECT * FROM products WHERE category = '{cat}' AND released = 1")
  # ✅ Seguro
  cursor.execute("SELECT * FROM products WHERE category = %s AND released = 1", (cat,))
  ```
- **Detección:** WAF o reglas en logs web que alerten por `'`, `OR 1=1`, `--`, `UNION SELECT` en parámetros.
- **Referencia:** OWASP Top 10 — A03: Injection · CWE-89

## 🧠 Lo que aprendí
- 
- 

## ❓ Dudas pendientes
- 

---
**Tags:** #sqli #web #owasp #portswigger
