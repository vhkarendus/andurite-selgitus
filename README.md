# Andurid – interaktiivsed selgitused

Interaktiivsed ühe-failiga õppematerjalid, mis selgitavad, kuidas andureid Arduinoga ühendada. Iga leht keskendub ühele küsimusele: **kuidas teha anduri muutusest pinge, mida Arduino saab mõõta**, ja kuidas valida selleks õige takisti.

Vaata otse: **[GitHub Pages](https://vhkarendus.github.io/Andurite-selgitus/)**

Sarja esimene osa on [Potentsiomeetri selgitus](https://vhkarendus.github.io/Potentsiomeetri-selgitus/).

---

## Lehed

| Leht | Kaust | Seis |
|---|---|---|
| Avaleht: mis on andur, kolm andurite peret | `index.html` | valmis |
| Valgusandur (fototransistor) | `valgusandur/` | valmis, fotod puudu |
| Temperatuuriandur (NTC termistor) | `temperatuur/` | valmis, fotod puudu |

### Avaleht
- **Pingejagur** (interaktiivne): R₁/R₂ liugurid, pingeriba, vool ja analogRead; potentsiomeetri režiim (R₁ + R₂ = 10 kΩ); seos potentsiomeetri, termistori ja fototransistoriga
- Ahel *füüsikaline suurus → andur → takisti → ADC → programm*
- Kolm andurite peret: **takistus muutub** (pingejagur), **vool muutub** (takisti GND-sse), **annab ise pinge** (otse pinile)
- Näidete tabel

### Valgusandur
**Põhiosa (lihtne):**
1. **Mis on fototransistor?** Valguse liugur, sümbol voolu animatsiooniga, voolu näit.
2. **Miks on takisti vaja?** Skeem Arduinoga, allatõmbetakisti lüliti (hõljuv sisend), Serial Monitor ja lihtne kood ainult `analogRead`-iga.
3. **Takisti suurus = tundlikkus.** Graafik `analogRead` vs valgustatus eri takistitega, küllastus ≈ 960, lihtne reegel (näit kogu aeg ~960 → väiksem takisti, kogu aeg ~0 → suurem).
4. **Valgustase `map()`-iga:** „pime“ ja „ere“ salvestamine, `map()` + `constrain()` → 0–100 %, öölamp (< 20 %), kood genereeritakse salvestatud väärtustest.
5. **Päriselus** (fotod lisanduvad) ja lihtsad mõtlemisküsimused.

**Süvenemiseks** (avatav plokk): andmeleht ja mudel (HW5P-1, k ≈ 6 µA/lx, miks küllastus on 960), takisti arvutamine R = 4,5 V / I_max + kalkulaator, katse k mõõtmiseks luksmeetriga, võrdlustabel termistoriga (link temperatuurilehele), küsimused edasijõudnutele.

### Temperatuuriandur
**Põhiosa (lihtne):**
1. **Mis on termistor?** NTC, termomeeter ja takistuse graafik (ilma valemita).
2. **Pingejagur:** Arduino skeem, takisti lüliti (ilma takistita A0 ≈ 5 V), Serial Monitor ja lihtne kood ainult `analogRead`-iga.
3. **Takisti mõju:** üks graafik `analogRead` vs temperatuur eri takistitega; reegel „takisti ≈ termistori takistus“.
4. **Kalibreerimine `map()`-iga:** kaks mõõtmist (jäävesi + toas), graafik map-i sirgest vs tegelikust kõverast, viga reaalajas, kood genereeritakse valitud punktidest (Arduino täisarvuline `map`).
5. **Päriselus** (fotod lisanduvad) ja lihtsad mõtlemisküsimused.

**Süvenemiseks** (avatav plokk lehe lõpus): termistori valik (10 kΩ B 3950 / 22 kΩ B 3740 / 3,3 kΩ B 3977 + 5,1 kΩ), B-võrrand ja log-skaala, tundlikkuse graafik (sammu/°C), takisti valimine R = √(R_NTC(T_min) · R_NTC(T_max)) + kalkulaator, isekuumenemine (δ = 2 mW/°C), analogRead → °C samm-sammult ja kood B-võrrandiga, küsimused edasijõudnutele.

---

## Mudel ja andmed (valgusandur)

Komponent: CTC GO komplekti fototransistor. Andmed võetud Arduino komplektides levinud fototransistori **HW5P-1** andmelehelt:

| Parameeter | Väärtus |
|---|---|
| Kollektori fotovool | 50–70 µA @ 10 lx (V = 5 V, R = 1 kΩ) |
| Pimevool | 100 nA |
| U<sub>CE(sat)</sub> | 0,3 V |
| Jalad | pikk = kollektor (+), lühike = emitter (−) |

```
I = I_pime + k · E          k ≈ 6 µA/lx, I_pime = 0,1 µA
                            (väga eredas valguses pehme piir ~20 mA)
U = I · R, piiratud U_max = 5 − 0,3 = 4,7 V (pehme küllastus)
analogRead = round(U / 5 · 1023)

Takisti valik: R = 4,5 V / (k · E_max) → lähim väiksem E12 väärtus
Kasulik vahemik: valgus, mille juures 0,25 V < U < 4,4 V
```

Lineaarne mudel on lihtsustus. Tegelik vool sõltub valguse spektrist, eksemplarist ja nurgast, seega on lehel ka katse, kus õpilased mõõdavad k ise.

---

## Mudel ja andmed (temperatuuriandur)

```
R_NTC = R0 · exp(B · (1/T − 1/T0))      T kelvinites, T0 = 298,15 K
U_A0  = 5 · R / (R + R_NTC)             5V → termistor → A0 → R → GND
Takisti valik: R = √(R_NTC(T_min) · R_NTC(T_max)) → lähim E12 väärtus
Isekuumenemine: P = I² · R_NTC, ΔT = P / δ, δ = 2 mW/°C
```

Hajumistegur δ ≥ 2 mW/°C ja ajakonstant ≤ 7 s on võetud MF52-seeria NTC andmelehelt (seisvas õhus).

## Failide struktuur

```
index.html              # avaleht
valgusandur/index.html  # valgusanduri leht (HTML + SVG + CSS + JS, sõltuvusi pole)
temperatuur/index.html  # temperatuurianduri leht
_config.yml             # GitHub Pages (theme: null)
```

Pildid lisatakse iga lehe kausta alamkausta `pildid/`.

## Kasutamine

Ava `index.html` otse brauseris (serverit pole vaja) või vaata GitHub Pages linki. Mobiilis saab paneele kerida horisontaalselt.

## Tehtud valikud

- **Stiil** järgib Potentsiomeetri selgitust (kaardid, kõrvuti paneelid, „Näita koodi“), et lehed näeksid välja ühe sarjana.
- **„pin“** Arduino pinide kohta, transistori jalgu nimetatakse kollektoriks ja emitteriks.
- **Koodis ainult ASCII muutujanimed** (`lugem`, mitte `väärtus`), sest Arduino AVR kompilaator ei luba täpitähti muutujanimedes.
- **Üks valguse liugur** on kõigis paneelides sünkroonis; Arduino skeem kasutab alati tunnis kasutatud 10 kΩ takistit, graafikul saab takistit vahetada.
