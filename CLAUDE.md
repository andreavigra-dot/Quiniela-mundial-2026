# Instrucciones del proyecto: Quiniela Mundial 2026

## Contexto general
Este proyecto administra una quiniela familiar para el Mundial 2026. Hay dos archivos principales que se mantienen sincronizados:

1. **Quiniela_Mundial_2026.xlsx** — Excel con las hojas: 📋 Instrucciones, 📝 Respuestas del Form, 📊 Resultados, 🏆 Puntos, 🌟 Campeón, 📈 Posiciones.
2. **Quiniela_Dashboard.html** — Dashboard visual (artifact HTML) con datos embebidos en una variable `DATA` dentro del `<script>`, que se publica en una URL fija (GitHub Pages) para que la familia lo consulte.

Cada vez que se actualiza información (resultados reales, nuevos participantes, pronósticos), **ambos archivos deben actualizarse juntos** y mantenerse consistentes entre sí.

## Sistema de puntos
- **2 puntos**: el marcador pronosticado coincide exactamente con el resultado real (ej. pronóstico "2-1", real "2-1").
- **1 punto**: el pronóstico acierta el resultado (quién gana o empate) pero no el marcador exacto.
- **0 puntos**: el pronóstico falla tanto el resultado como el marcador.
- **+5 puntos extra**: si el pronóstico del campeón del torneo coincide con el campeón real (solo se otorga al finalizar el Mundial).
- El resultado (G1/Empate/G2) se deriva siempre del marcador, comparando los dos números separados por "-". Nunca se pide el resultado por separado.

## Formato de datos
- Todo marcador se escribe como `X-Y` (goles equipo 1 - goles equipo 2), por ejemplo `2-1` o `0-0`.
- El "equipo 1" es siempre el primero que aparece en el nombre del partido (ej. en "México vs Sudáfrica", México es equipo 1).
- Si un pronóstico llega con texto adicional (nombres de equipos, palabras sueltas), se limpia para dejar solo el formato `X-Y` antes de integrarlo.
- Errores evidentes de captura (ej. "3-12") se le preguntan al usuario antes de corregir, nunca se asumen.

## Reglas de actualización del Excel
- **Hoja 📝 Respuestas del Form**: una fila por participante, columnas = Marca temporal, Nombre, 72 marcadores (uno por partido, en orden cronológico J1→J2→J3), y la pregunta del campeón al final.
- **Hoja 📊 Resultados**: una fila por partido (72 filas), con grupo, fecha, equipo 1, equipo 2, y el marcador real (se llena conforme se juegan los partidos).
- **Hoja 🏆 Puntos**: fórmulas automáticas que comparan pronóstico vs resultado real y calculan los puntos por partido y el total por participante. No se edita a mano.
- **Hoja 🌟 Campeón**: pronóstico del campeón por participante + campeón real (celda D3) + cálculo de los 5 puntos extra.
- **Hoja 📈 Posiciones**: ranking final, suma de puntos de partidos + campeón.
- Al agregar un nuevo participante: agregar su fila en "Respuestas del Form" y extender las fórmulas correspondientes en "Puntos", "Campeón" y "Posiciones" (copiando el patrón de fórmulas de las filas existentes, ajustando las referencias de fila).
- Después de cualquier cambio en el Excel, ejecutar el recalculo de fórmulas con el script de la skill de xlsx y verificar 0 errores antes de entregar el archivo.

## Reglas de actualización del Dashboard (HTML)
- Los datos viven en la variable `DATA` (JSON embebido) dentro del `<script>` del HTML: `participantes` (array con nombre, array de 72 pronósticos, campeón) y `partidos` (array de 72 partidos con grupo, fecha, equipos y resultado real).
- Para agregar un resultado real: actualizar el campo `"real"` del partido correspondiente en `DATA.partidos`.
- Para agregar un nuevo participante: agregar su objeto al array `DATA.participantes`, con su array de 72 pronósticos (usar `null` para partidos sin pronóstico).
- Para corregir un pronóstico: editar directamente el valor en el array `pron` del participante correspondiente.
- Cualquier cambio en el Excel (resultados, nuevos participantes, correcciones) debe reflejarse también en el HTML, y viceversa.

## Criterio de desempate en la tabla de posiciones
Cuando dos o más participantes tienen el mismo puntaje total, comparten la misma posición numérica en el ranking. El orden interno (no visible como posición distinta) se determina en cascada:
1. Mayor número de marcadores exactos (2 pts).
2. Si sigue empate, quién acertó el pronóstico del campeón.
3. Si persiste el empate, se marca como "empatado" en la tabla — es un empate real.

## Búsqueda de resultados reales
- Cuando el usuario pida actualizar resultados sin darlos directamente, buscar en internet el marcador final de cada partido.
- Verificar que el partido ya haya **terminado** (no usar resultados de partidos en curso o aún no jugados). Si un partido sigue en vivo o no ha comenzado, informarlo y no agregar un resultado parcial o de pronóstico.
- Confirmar el marcador con al menos una fuente que indique explícitamente que el partido finalizó.

## Flujo de trabajo típico
1. El usuario manda nuevos pronósticos (PDF de respuestas de Google Forms) o resultados reales de partidos.
2. Se actualiza el Excel (Respuestas del Form y/o Resultados), se recalculan fórmulas y se verifica que no haya errores.
3. Se actualiza el Dashboard HTML con los mismos datos (DATA.participantes y/o DATA.partidos).
4. Se entregan ambos archivos actualizados con `present_files`.
5. El usuario sube el HTML actualizado a GitHub Pages (reemplazando `index.html`) para que la familia vea la versión más reciente en la misma URL.

## Estilo del Dashboard
- Tema visual: "tablero de estadio" — fondo verde césped, tipografía deportiva (Oswald para títulos, Roboto Mono para números/marcadores), acentos dorados.
- Tres vistas con tabs: 🏆 Tabla (posiciones), ⚽ Partidos (calendario con filtro por jornada), 📋 Pronósticos (tabla matriz: partidos x participantes, con resultado real visible y celdas coloreadas según puntos obtenidos).
- Nombres completos de los participantes siempre visibles (no abreviar a solo el primer nombre) para evitar confusión entre personas con el mismo nombre.

## Privacidad
- Los nombres de los participantes son nombres reales de familiares (incluye apodos como "Abuela", "Abuelo"). Mantener esta información solo dentro del contexto de este proyecto.
