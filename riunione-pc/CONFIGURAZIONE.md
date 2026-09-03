# Configurazione della pagina «Riunione mensile PC Roveredo»

La pagina è un unico file, `index.html`, e funziona su GitHub Pages senza alcun
programma da installare. Per salvare i dati condivisi si appoggia a **Supabase**,
un servizio con piano gratuito. Servono quindici minuti, una volta sola.

## 1. Crea il progetto Supabase

1. Vai su https://supabase.com e crea un account gratuito.
2. Premi **New project**. Dai un nome, per esempio `pc-roveredo`, scegli la
   regione **Central EU (Frankfurt)** e imposta una password del database
   (serve solo a Supabase, non ai volontari): conservala.
3. Attendi qualche minuto la creazione del progetto.

## 2. Crea la tabella

Apri **SQL Editor** nel menu a sinistra, incolla tutto il blocco seguente e premi **Run**.

```sql
-- tabella unica: ogni riga è un documento (una riunione oppure un punto)
create table if not exists public.documenti (
  percorso   text primary key,
  raccolta   text not null,
  dati       jsonb not null default '{}'::jsonb,
  aggiornato timestamptz not null default now()
);
create index if not exists documenti_raccolta on public.documenti (raccolta);

-- aggiornamento parziale di un documento, senza sovrascrivere gli altri campi
create or replace function public.unisci(p_percorso text, p_raccolta text, p_dati jsonb)
returns void
language sql
as $$
  insert into public.documenti (percorso, raccolta, dati, aggiornato)
  values (p_percorso, p_raccolta, p_dati, now())
  on conflict (percorso) do update
    set dati = documenti.dati || excluded.dati,
        aggiornato = now();
$$;

-- permessi per la chiave pubblica usata dalla pagina
alter table public.documenti enable row level security;

drop policy if exists "lettura"      on public.documenti;
drop policy if exists "inserimento"  on public.documenti;
drop policy if exists "modifica"     on public.documenti;
drop policy if exists "cancellazione" on public.documenti;

create policy "lettura"       on public.documenti for select to anon using (true);
create policy "inserimento"   on public.documenti for insert to anon with check (true);
create policy "modifica"      on public.documenti for update to anon using (true) with check (true);
create policy "cancellazione" on public.documenti for delete to anon using (true);

grant usage on schema public to anon;
grant select, insert, update, delete on public.documenti to anon;
grant execute on function public.unisci(text, text, jsonb) to anon;
```

## 3. Copia i due valori nella pagina

In Supabase apri **Project Settings**, poi **API**, e copia:

| Valore in Supabase | Dove va nella pagina |
|---|---|
| **Project URL** (per esempio `https://abcdefghijklm.supabase.co`) | `var SUPABASE_URL = "";` |
| **Project API keys → anon public** | `var SUPABASE_CHIAVE = "";` |

I due valori si trovano in `index.html`, poco dopo il commento
`configurazione della base dati`. Riempili fra le virgolette e salva.

## 4. Pubblica

Il file viene servito da GitHub Pages all'indirizzo

    https://guidocostalonga.github.io/parola-del-giorno/riunione-pc/

Da lì la pagina è raggiungibile da qualunque telefono o computer, senza account
e senza applicazioni: basta il collegamento e la password.

## Password

La password di accesso è **Roveredo2026** e si cambia direttamente dalla pagina,
in fondo, con «Cambia la password»: la nuova vale per tutti.

## Che cosa protegge la password, e che cosa no

La password tiene fuori chi capita sulla pagina per caso ed è più che sufficiente
per un ordine del giorno di squadra. Non è però una cassaforte: la chiave pubblica
di Supabase è visibile nel codice della pagina, come previsto dal servizio, quindi
una persona esperta che possieda il collegamento potrebbe leggere i dati anche
senza password. Per questo la pagina non contiene dati personali sensibili, non
viene indicizzata dai motori di ricerca e il collegamento va diffuso solo dentro
il gruppo.

## Manutenzione

- **Nessun costo**: il piano gratuito di Supabase copre ampiamente l'uso di una
  squadra comunale. I progetti gratuiti vengono sospesi dopo una settimana senza
  alcun accesso e si riattivano da soli con un clic dal pannello Supabase.
- **Copia di sicurezza**: dalla pagina, il pulsante «Esporta in Excel» salva
  l'ordine del giorno completo della riunione aperta.
