# APEX Training OS — Supabase + Vercel

## Setup en 5 pasos

### 1. Supabase — Crear proyecto
1. Ve a [supabase.com](https://supabase.com) → New Project
2. Anota tu **Project URL** y **anon public key** (Settings → API)

### 2. Supabase — Crear tabla
Ve a SQL Editor y ejecuta:

```sql
create table apex_user_data (
  user_id text not null,
  key text not null,
  value jsonb,
  updated_at timestamptz default now(),
  primary key (user_id, key)
);

-- Row Level Security
alter table apex_user_data enable row level security;

create policy "Users can only access their own data"
  on apex_user_data
  for all
  using (auth.uid()::text = user_id)
  with check (auth.uid()::text = user_id);
```

### 3. Supabase — Activar Google Auth (opcional)
- Authentication → Providers → Google → Enable
- Necesitas credenciales OAuth de [console.cloud.google.com](https://console.cloud.google.com)
- En Supabase: añade tu URL de Vercel a "Redirect URLs"

### 4. Poner tus credenciales en index.html
Abre `index.html` y busca estas líneas (~línea 10 del JS):

```js
var SUPABASE_URL = 'YOUR_SUPABASE_URL';
var SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY';
```

Reemplázalas con tus valores de Supabase.

### 5. Deploy en Vercel
```bash
npm i -g vercel
vercel --prod
```
O arrastra la carpeta a [vercel.com/new](https://vercel.com/new)

## Variables de entorno (alternativa más segura)
Si prefieres no hardcodear las keys en el HTML, puedes usar un build step o simplemente dejarlas en el HTML ya que la `anon key` de Supabase es pública por diseño (la seguridad la gestiona Row Level Security).

## Strava callback
En tu app de Strava, el Authorization Callback Domain debe ser tu dominio de Vercel:
```
tu-app.vercel.app
```
