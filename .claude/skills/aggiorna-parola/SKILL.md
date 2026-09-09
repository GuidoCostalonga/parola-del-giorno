---
name: aggiorna-parola
description: Aggiorna index.html con i contenuti liturgici di una data (per impostazione predefinita oggi, fuso orario di Roma), verificandoli sulla CEI e su Vatican News. Usala ogni volta che viene chiesto di aggiornare, preparare o rigenerare la Parola del Giorno.
allowed-tools: Read, Edit, Bash(git *), WebFetch
---

# Aggiorna la Parola del Giorno

## Passaggi
1. Determina la data: quella indicata dall'utente, altrimenti oggi nel fuso
   orario Europe/Rome. Ricava `AAAAMMGG` e `AAAA/MM/GG`.
2. Leggi la liturgia del giorno sulla CEI (Conferenza Episcopale Italiana):
   https://www.chiesacattolica.it/liturgia-del-giorno/?data-liturgia=AAAAMMGG
   Ricava: tempo liturgico, anno, letture con riferimento, Vangelo integrale,
   santo del giorno con grado della celebrazione.
3. Confronta con Vatican News:
   https://www.vaticannews.va/it/vangelo-del-giorno-e-parola-del-giorno/AAAA/MM/GG.html
   Se i riferimenti non coincidono, fermati e segnala la discordanza.
4. Scrivi i testi redazionali, rispettando `.claude/rules/`:
   - titolo `h1`: una frase del Vangelo tra caporali, al massimo dieci parole;
   - una riga di sintesi per ciascuna lettura (al massimo dodici parole);
   - sintesi del Vangelo in tre o quattro paragrafi brevi;
   - `blockquote` con il versetto centrale, presente nel brano;
   - meditazione: titolo in due righe (la seconda in corsivo), due paragrafi,
     una domanda finale;
   - preghiera in quattro versi più "Amen.";
   - scheda del santo: nome, qualifica e date, una riga di presentazione.
5. Modifica `index.html` solo nel testo delle sezioni esistenti. Aggiorna i due
   collegamenti alla CEI e quello a Vatican News con la nuova data.
6. Fai rileggere il risultato al subagente `revisore` e correggi ciò che segnala.
7. Verifica che il file sia ancora valido: apri la pagina in locale o controlla
   che i tag di apertura e chiusura siano bilanciati.
8. Esegui il commit: `Aggiorna la pagina al GG mese AAAA`.

## Vincoli
- Nessun dato inventato. In caso di dubbio non pubblicare.
- Non toccare `<style>` e `<script>`.
- Non cambiare la struttura delle sezioni né i loro `id`.
