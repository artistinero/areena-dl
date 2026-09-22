# areena-dl

Komentorivityökalut YLE Areena -sisällön lataamiseen Jellyfin-mediapalvelimelle.

## Skriptit

### `areena-dl`

Lataa yksittäisen jakson tai kokonaisen sarjan. Tunnistaa automaattisesti onko sisältö video vai audio, ja käyttää sopivaa lataustyökalua.

```bash
areena-dl https://areena.yle.fi/1-64771970
areena-dl https://areena.yle.fi/1-4172550   # lataa kaikki sarjan jaksot
```

Jokaisen jakson oheen syntyy:
- `.nfo` — Jellyfinille sopiva jakson metadata (otsikko, kuvaus, päivämäärä)
- `.jpg` — pikkukuva

Sarjan kansioon syntyy:
- `tvshow.nfo` — sarjan nimi ja kuvailuteksti (haetaan Areena-sivulta)
- `poster.jpg` — sarjan pääkuva

Tiedostonimet muodossa `S01E05_Jakson nimi.mp3` tai `2023-02-05_Jakson nimi.mp3`.

### `areena-dl-watch`

Tarkistaa seurattavat sarjat ja lataa uudet jaksot automaattisesti. Ajettavaksi cronilla.

- Tarkistaa TeraStation-mountin ennen latauksia, yrittää remounttia tarvittaessa
- Lähettää ntfy-ilmoituksen virheistä ja uusista latauksista
- Lokit: `~/.local/log/areena-dl-watch/`
- Config: `~/.config/areena-dl-watch.json`

```bash
areena-dl-watch
```

Cron joka kolmas päivä klo 7:
```
0 7 */3 * * PATH="$HOME/.local/bin:$PATH" /usr/local/bin/areena-dl-watch
```

### `areena-dl-watch-add`

Lisää sarjan automaattiseen seurantaan. Aja kohdehakemistossa.

```bash
cd /mnt/terastation/dlna/other-audio/music-pods/Jazzklubi
areena-dl-watch-add https://areena.yle.fi/1-2120710
```

Lisää nykyisen hakemiston ja URL:n `~/.config/areena-dl-watch.json`-tiedostoon.
Kopioi `areena-dl-watch.json.example` pohjaksi tai anna skriptin luoda config automaattisesti.

### `areena-dl-backfill`

Luo puuttuvat NFO- ja thumb-tiedostot jo aiemmin ladatuille jaksoille.

```bash
cd /mnt/Shared/Yle/SarjanKansio
areena-dl-backfill https://areena.yle.fi/1-4172550
```

## Asennus

```bash
sudo cp areena-dl areena-dl-watch areena-dl-watch-add /usr/local/bin/
sudo chmod +x /usr/local/bin/areena-dl /usr/local/bin/areena-dl-watch /usr/local/bin/areena-dl-watch-add
```

## Riippuvuudet

- [yt-dlp](https://github.com/yt-dlp/yt-dlp) — videolataukset
- [yle-dl](https://github.com/aajanki/yle-dl) — audio- ja podcast-lataukset (`pipx install yle-dl`)
- `jq` — JSON-parsinta (`apt install jq`)
- `curl`, `ffmpeg`

## Konfiguraatio

`yle-dl` lukee asetukset tiedostosta `~/.yledl.conf`:

```
sublang = all
destdir = /mnt/Shared/Yle
```
