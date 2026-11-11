# Examen teórico Clase B (Chile)

App de estudio no oficial, basada en el Libro del Nuevo Conductor (CONASET). Es un solo archivo (`index.html`), sin dependencias.
Incluye 330 preguntas por capítulo, repetición espaciada, test de diagnóstico, aprendizaje inteligente, tarjetas, casos y verdadero o falso.

## Publicar en GitHub Pages (gratis)
1. Crea un repositorio nuevo en GitHub y sube `index.html` y este `README.md`.
2. En el repositorio: **Settings → Pages → Build and deployment**, elige *Deploy from a branch*, rama `main`, carpeta `/ (root)`.
3. En un par de minutos tendrás la dirección `https://TU-USUARIO.github.io/NOMBRE-DEL-REPO/`.

Sin más pasos, la app funciona **sin cuentas**: cada persona guarda su avance en su propio dispositivo.

## Activar cuentas (nombre, correo y contraseña) con Supabase gratis
GitHub Pages no tiene servidor, así que las cuentas se guardan en Supabase. Las contraseñas las maneja Supabase (nunca se guardan en tu página).

1. Crea una cuenta y un proyecto en https://supabase.com (plan gratuito).
2. En **SQL Editor**, ejecuta esto:

```sql
create table public.progreso (
  user_id uuid primary key references auth.users(id) on delete cascade,
  nombre text, email text,
  avance int default 0, respondidas int default 0, aciertos int default 0,
  datos jsonb, actualizado timestamptz default now()
);
alter table public.progreso enable row level security;
create policy "ver el propio"        on public.progreso for select using (auth.uid() = user_id);
create policy "crear el propio"      on public.progreso for insert with check (auth.uid() = user_id);
create policy "actualizar el propio" on public.progreso for update using (auth.uid() = user_id) with check (auth.uid() = user_id);
```

3. En **Authentication → Providers → Email**: si quieres que se registren sin confirmar el correo, desactiva "Confirm email". Si lo dejas activo, cada persona debe confirmar desde su correo antes de entrar.
4. En **Project Settings → API** copia la *Project URL* y la clave pública (*anon* o *publishable*).
5. Abre `index.html` y pega esos dos valores en `SUPA_URL` y `SUPA_KEY` (busca "CUENTAS (opcional)").
6. Sube el cambio a GitHub.

**Registro de estudiantes:** en Supabase, **Table Editor → progreso**, verás una fila por estudiante con nombre, correo, % de avance, respondidas, % de aciertos y última actividad. Solo tú entras ahí, con tu cuenta de Supabase.

### Seguridad
- La clave *anon* es pública por diseño. Lo que protege los datos son las políticas RLS del paso 2: cada estudiante solo puede leer y escribir su propia fila.
- **Nunca** pegues la clave *service_role* en `index.html`.
- Es un registro de avance de estudio. No guardes datos sensibles de las personas más allá de nombre y correo.
- El plan gratuito puede pausar el proyecto si no hay actividad por un tiempo; se reactiva desde el panel.

## Agregar preguntas
En `index.html`, dentro de `NEW`, agrega una línea al final con este formato (la correcta va primero; la app mezcla las opciones):
`[capítulo 1-9, "Pregunta", "Correcta", "Falsa 1", "Falsa 2", "Falsa 3", "Justificación"]`
