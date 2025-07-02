-- 1. Buat tabel Transactions
create table "Transactions" (
  id uuid primary key default gen_random_uuid(),
    user_id bigint,
      username text,
        full_name text,
          order_id text not null unique,
            transaction_id text,
              payment_type text,
                gross_amount numeric,
                wallet text,
                  currency text,
                    transaction_status text,
                      transaction_time timestamp,
                        settlement_time timestamp,
                          status_message text,
                            fraud_status text,
                              signature_key text,
                                merchant_id text,
                                  created_at timestamp default timezone('utc'::text, now())
                                  );

                                  -- 2. Aktifkan RLS
                                  alter table "Transactions" enable row level security;

                                  -- 3. Izinkan SELECT
                                  create policy "Allow SELECT for all"
                                  on "Transactions"
                                  for select
                                  using (true);

                                  -- 4. Izinkan INSERT
                                  create policy "Allow INSERT for all"
                                  on "Transactions"
                                  for insert
                                  with check (true);

                                  -- 5. Izinkan UPDATE
                                  create policy "Allow UPDATE for all"
                                  on "Transactions"
                                  for update
                                  using (true);

                                  -- 6. Izinkan DELETE
                                  create policy "Allow DELETE for all"
                                  on "Transactions"
                                  for delete
                                  using (true);
                                  
                                  
                                  
-- Buat tabel Users
create table if not exists "Users" (
  id uuid primary key default gen_random_uuid(),
    user_id bigint,
      username text,
        full_name text,
          is_vip boolean default false,
            vip_since timestamp,
              created_at timestamp with time zone default timezone('utc'::text, now())
              );

              -- Aktifkan RLS
              alter table "Users" enable row level security;

              -- Policy: Allow SELECT to everyone
              create policy "Allow select for all"
              on "Users"
              for select
              using (true);

              -- Policy: Allow INSERT to everyone
              create policy "Allow insert for all"
              on "Users"
              for insert
              with check (true);

              -- Policy: Allow UPDATE to everyone
              create policy "Allow update for all"
              on "Users"
              for update
              using (true)
              with check (true);

              -- Policy: Allow DELETE to everyone
              create policy "Allow delete for all"
              on "Users"
              for delete
              using (true);
              
              
              
              
              
              
create table public.Settings (
    key text primary key,
      value text,
        updated_at timestamp with time zone default now()
        );

        -- Izinkan service_role akses penuh (digunakan oleh Supabase client via server)
-- Aktifkan RLS
alter table public."Settings" enable row level security;

-- Allow SELECT untuk semua role (termasuk anon)
create policy "Allow select for all"
on public."Settings"
as permissive
for select
to public
using (true);

-- Allow INSERT untuk semua role
create policy "Allow insert for all"
on public."Settings"
as permissive
for insert
to public
with check (true);

-- Allow UPDATE untuk semua role
create policy "Allow update for all"
on public."Settings"
as permissive
for update
to public
using (true)
with check (true);

-- Allow DELETE untuk semua role
create policy "Allow delete for all"
on public."Settings"
as permissive
for delete
to public
using (true);



insert into "Settings" (key, value) values ('vip_price', '350000');
