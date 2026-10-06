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
| Temperatuuriandur (termistor) | `temperatuur/` | tulekul |

### Avaleht
- Ahel *füüsikaline suurus → andur → takisti → ADC → programm*
- Kolm andurite peret: **takistus muutub** (pingejagur), **vool muutub** (takisti GND-sse), **annab ise pinge** (otse pinile)
- Näidete tabel

### Valgusandur
1. **Mis on fototransistor?** Valguse liugur, sümbol voolu animatsiooniga, voolu näit.
2. **Miks on takisti vaja?** Skeem Arduinoga; lülitiga saab allatõmbetakisti lahti ühendada ja näha hõljuvat sisendit Serial Monitori simulatsioonis. Näitekood nupu taga.
3. **Takisti suurus = tundlikkus.** Graafik `analogRead` vs valgustatus (log-skaala) takistitele 1 kΩ … 100 kΩ, kasulik vahemik ja küllastus.
4. **Kuidas valida takistit?** Retsept + kalkulaator (E12-rida), tulemust saab graafikul proovida.
5. **Katse:** oma anduri tundlikkuse mõõtmine.
6. **Päriselus:** kollektor/emitter (fotod lisanduvad).
7. **Teine näide: termistor** (NTC 10 kΩ, B 3950, nagu VHK_Telemeetria tutorial 03): pingejagur, tundlikkus sammudes kraadi kohta, reegel R = √(R_NTC(T_min) · R_NTC(T_max)), kalkulaator, kood ja võrdlustabel fototransistoriga.
8. Mõtlemisküsimused ja sõnastik.

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

## Failide struktuur

```
index.html              # avaleht
valgusandur/index.html  # valgusanduri leht (HTML + SVG + CSS + JS, sõltuvusi pole)
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
