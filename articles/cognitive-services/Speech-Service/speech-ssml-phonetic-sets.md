---
title: Fotici řeči - Služba řeči
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak se foetická abeceda služby Speech mapuje na mezinárodní fonetickou abecedu (IPA) a kdy použít nastavenou sadu.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675331"
---
# <a name="speech-service-phonetic-sets"></a>Fonetické sady služby rozpoznávání řeči

Služba Řeč definuje fonetickou abecedu (zkráceně "telefonní sady", které se skládají ze sedmi jazyků; `en-US`, `fr-FR` `de-DE`, `es-ES` `ja-JP`, `zh-CN`, `zh-TW`, a . Telefonní sada služby Speech obvykle mapuje na <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">mezinárodní <span class="docon docon-navigate-external x-hidden-focus"> </span>fonetickou abecedu (IPA). </a> Sady telefonních služeb pro rozpoznávání řeči se používají ve spojení s [jazykem s označením syntézy řeči (SSML)](speech-synthesis-markup.md)jako součást nabídky služby převodu textu na řeč. V tomto článku se dozvíte, jak jsou tyto sady telefonů mapovány a kdy použít kterou sadu telefonů.

# <a name="en-us"></a>[en-US](#tab/en-US)

### <a name="english-suprasegmentals"></a>Anglické suprasegmenty

| Příklad 1 (Nástup souhly, počáteční slovo pro samohlásek) | Příklad 2 (Intervocalic pro souhle, slovo mediální jádro pro samohlásku) | Příklad 3 (Coda pro souhrádku, slovo konečné pro samohlásku) | Komentáře |
|--|--|--|--|
| burger /b er **1** r - g ax r/ | falafel /f sekera - l aa **1** - f ax l/ | kytara /g ih - t aa **1** r/ | Řeč služby telefonní sada dát stres po samohláze stresové slabiky |
| invahodná /ih **2** - n aa - p ax r - t uw 1 n/ | odlišnost /d ih - s ih **2**- m ax - l eh 1 - r ax - t iy/ | pracovní síla /w er 1 r k - f ao **2** r s/ | Řeč oválná telefonní sada kladla důraz po samoobsluze slabiky podnapětí |

### <a name="english-vowels"></a>Anglické samohlásky

| `sapi` | `ipa` | Příklad 1     | Příklad 2 | Příklad 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| Iy     | `i`   | **ea**t       | f**ee**l  | vall**ey**                  |
| Ih     | `ɪ`   | **i**f        | f**i**ll  |                             |
| Ey     | `eɪ`  | **a**te       | g**a**te  | d**ay**                     |
| Co     | `ɛ`   | **e**velmi     | p**e**t   | m**eh** (vzácné slovo konečně) |
| Ae     | `æ`   | **ctive**    | c**a**t   | n**ah** (vzácné slovo konečně) |
| aa     | `ɑ`   | **o**bstinate | p**o**ppy | r**ah** (vzácné slovo konečně) |
| Ao     | `ɔ`   | **o**rozsah    | c**au**se | Ut**ah**                    |
| Uh     | `ʊ`   | b**oo**k      |           |                             |
| Ow     | `oʊ`  | **o**ld       | cl**o**ne | g**o**                      |
| Uw     | `u`   | **U**ber      | b**oo**st | t**oo**                     |
| Ah     | `ʌ`   | **u**ncle     | c**u t**   |                             |
| Ay     | `aɪ`  | **i**ce       | b**i**te  | fl**y**                     |
| Aw     | `aʊ`  | **ou**t       | s**ou**th | c**ow**                     |
| Oy     | `ɔɪ`  | **oi**l       | j**oi**n  | t**oy**                     |
| y uw   | `ju`  | **Yu**ma      | h**u**muž | f**ew**                     |
| Ax     | `ə`   | **jít**       | wom**a**n | jsou**a**                    |

### <a name="english-r-colored-vowels"></a>Anglické r-barevné samowels

| `sapi` | `ipa` | Příklad 1    | Příklad 2      | Příklad 3  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **ucho**s     | t**ir**amisu   | n**ucho**   |
| eh r   | `ɛɹ`  | **vzduchová**rovina | aplikace**ar**ently | sc**ar**e  |
| uh r r   | `ʊɹ`  |              |                | c**vaše**e   |
| ay r   | `aɪɹ` | **Ire**země  | f**ir**místo  | ch**oir**  |
| aw r   | `aʊɹ` | **hodina**s    | p**ower**ful   | je**to naše**   |
| ao r   | `ɔɹ`  | **nebo**ange   | m**nebo**al      | s**veslo**   |
| aa r   | `ɑɹ`  | **ar**tist   | st**ar**t      | c**ar**    |
| er r   | `ɝ`   | **ucho**th    | b**ir**d       | f**vaše**    |
| sekera r   | `ɚ`   |              | všechny**er**gy    | supp**er** |

### <a name="english-semivowels"></a>Anglické semivowels

| `sapi` | `ipa` | Příklad 1           | Příklad 2  | Příklad 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**ith, s**ue**de | al**w**ays |           |
| y      | `j`   | **y**ard, f**e**w   | na**i**na  |           |

### <a name="english-aspirated-oral-stops"></a>Angličtina aspirované ústní zastávky

| `sapi` | `ipa` | Příklad 1 | Příklad 2   | Příklad 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**ut   | ha**pp**en  | fla**p**   |
| b      | `b`   | **b**ig   | num**b**er  | cra**b**   |
| t      | `t`   | **t**alk  | capi**t**al | sough**t** |
| d      | `d`   | **d**ig   | běžel**d**om  | ro**d**    |
| k      | `k`   | **c**ut   | sla**ck**er | Ira**q**   |
| g      | `g`   | **g**o    | a**g**o     | dra**g**   |

### <a name="english-nasal-stops"></a>Angličtina Nosní zastávky

| `sapi` | `ipa` | Příklad 1        | Příklad 2  | Příklad 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**na, rozbít   | **ca m**éra | roo**m**    |
| n      | `n`   | **n**o, s**n**ow | te**n**t   | chicke**n** |
| Ng     | `ŋ`   |                  | li**n**k   | s**ing**    |

### <a name="english-fricatives"></a>Anglické fricatives

| `sapi` | `ipa` | Příklad 1   | Příklad 2        | Příklad 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f**ork    | le**f**t         | hal**f**   |
| v      | `v`   | **v**alue   | e**v**ent        | lo**v**e   |
| Th     | `θ`   | **th**v    | empa**th y**      | po**čt**  |
| Dh     | `ð`   | **th**en    | **mo th er**       | smoo**th** |
| s      | `s`   | **je**to     | ri**s**k         | fakt**s**  |
| z      | `z`   | **z**ap     | bu**s**y         | dítě**s**   |
| Sh     | `ʃ`   | **sh** e    | zkráceně**ti**na | ru**sh**   |
| Zh     | `ʒ`   | **J**acques | důvod**s**ure     | gara**g**e |
| h      | `h`   | **h**elp    | en**h**ance      | a-**h**a!  |

### <a name="english-affricates"></a>Anglické affricates

| `sapi` | `ipa` | Příklad 1 | Příklad 2    | Příklad 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch**v  | fu**t**ure   | atta**ch** |
| Jh     | `dʒ`  | **j**oy   | ori**g**inální | oran**g**e |

### <a name="english-approximants"></a>Anglické aproximanty

| `sapi` | `ipa` | Příklad 1          | Příklad 2  | Příklad 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**id, g**l**ad  | pa**l**eso | chi**ll** |
| r      | `ɹ`   | **r**ed, b**r**ing | bo**rr**ow | ta**r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Francouzské suprasegmenty

Sada telefonních služeb Speech však klade důraz na samohlásek stresované slabiky; telefonní `fr-FR` sada služby Speech nepodporuje podpěru IPA "中". Pokud je podpíra NPP potřeba, měli byste jej použít přímo.

### <a name="french-vowels"></a>Francouzské samohlásky

| `sapi` | `ipa` | Příklad 1     | Příklad 2       | Příklad 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **a**rbre     | p**a**tte       | ir**a**   |
| aa     | `ɑ`   |               | p**â**te        | p**a**s   |
| aa ~   | `ɑ̃`  | **en**fant    | enf**en**t      | t**em**ps |
| Ax     | `ə`   |               | p**e**tite      | l**e**    |
| Co     | `ɛ`   | **e**lle      | p**e**rdu       | ét**ai**t |
| Eu     | `ø`   | **œu**fs      | cr**eu**ser     | qu**eu**  |
| Ey     | `e`   | ému           | crétin          | ôté       |
| eh ~   | `ɛ̃`  | **im**portant | p**ein**ture    | rohož**v** |
| Iy     | `i`   | **i**dée      | pet**i**te      | jsem**i**   |
| Oe     | `œ`   | **œu**f       | p**eu**r        |           |
| Oh     | `ɔ`   | **o**bstakl  | c**o**rps       |           |
| ach ~   | `ɔ̃`  | **na**ze      | r**na**deur     | b**na**   |
| Ow     | `o`   | **au**diteur  | b**eau**převrat    | p**ô**    |
| oe ~   | `œ̃ ` | **Osn**        | l**un**di       | br**un**  |
| Uw     | `u`   | **ou**trage   | ink**ou**vable | **Ou**    |
| Uy     | `y`   | **u**ne       | p**u**nir       | él**u**   |

### <a name="french-consonants"></a>Francouzské souhlásky

| `sapi` | `ipa` | Příklad 1   | Příklad 2     | Příklad 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**êe    | ha**b**ille   | ro**b**e                         |
| d      | `d`   | **d**ire    | ron**d**eur   | chau**d**e                       |
| f      | `f`   | **f**emme   | su**ff**ixe   | bo**f**                          |
| g      | `g`   | **g**auche  | é**g**ale     | ba**gu**e                        |
| Ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)**parking** |
| Hy     | `ɥ`   | h**u**ile   | n**u**ire     |                                  |
| k      | `k`   | **c**arte   | é**c**aille   | být**c**                          |
| l      | `l`   | **l**ong    | é**l**ire     | ba**l**                          |
| m      | `m`   | **m**adame  | ai**er**     | po**mm**e                        |
| n      | `n`   | **n**ous    | te**n**ir     | bo**nn**e                        |
| Nj     | `ɲ`   |             |               | pei**gn**e                       |
| p      | `p`   | **p**atte   | re**p**jako     | ca**p**                          |
| r      | `ʁ`   | **r**při     | cha**r**iot   | senti**r**                       |
| s      | `s`   | **s**ourir  | a**ss**ez     | pa**ss**e                        |
| Sh     | `ʃ`   | **ch**anter | ma**ch**ine   | po**ch**e                        |
| t      | `t`   | **t**ête    | ô**t**er      | ne**t**                          |
| v      | `v`   | **v**ent    | v**v zadejte**  | rê**v**e                         |
| w      | `w`   | **ou**i     | f**ou**ine    |                                  |
| y      | `j`   | **y**od     | p**i**étiner  | Marse**ille**                    |
| z      | `z`   | **z **éro   | rai**s**onner | ro**s**e                         |
| Zh     | `ʒ`   | **j**ardin  | muž**g**er    | piè**g**e                        |
|        | `n‿`  |             |               | u**n** arbre                     |
|        | `t‿`  |             |               | quan**d**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>
**1** *Pouze pro některá cizí slova*

> [!TIP]
> Telefonní `fr-FR` sada služby Speech nepodporuje následující francouzské `n‿`limuzíny `t‿`, `z‿`, a . Pokud jsou potřeba, měli byste zvážit použití IPA přímo.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Německé suprasegmenty

| Příklad 1 (Nástup souhly, počáteční slovo pro samohlásek) | Příklad 2 (Intervocalic pro souhle, slovo mediální jádro pro samohlásku) | Příklad 3 (Coda pro souhrádku, slovo konečné pro samohlásku) | Komentáře |
|--|--|--|--|
| anders /a **1** n - d ax r s/ | Multiplikationszeichen /m uh l - t iy - p l iy - k a - ts y ow **1** n s - ts ay - c n/ | Biologie /b iy - ow - l ow - g iy **1**/ | Řeč služby telefonní sada dát stres po samohláze stresové slabiky |
| Allgemeinwissen /a **2** l - g ax - m ay 1 n - v ih - s n/ | Abfallentsorgungsfirma /a 1 p - f a l - ^ eh n t - z oh **2** ax r - g uh ng s - f ih ax r - m a/ | Computertomographie /k oh m - p y uw 1 - t ax r - t ow - m ow - g r a - f iy **2**/ | Řeč oválná telefonní sada kladla důraz po samoobsluze slabiky podnapětí |

### <a name="german-vowels"></a>Německé samohlásky

| `sapi` | `ipa`     | Příklad 1                             | Příklad 2     | Příklad 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| A:     | `aː`      | **Ber**                              | Maßst**a**b   | Schéma**a**                         |
| a      | `a`       | **Bpád**                            | B**a**ch      | Agath**a**                         |
| Oh     | `ɔ`       | **O**sten                             | Pf**o**sten   |                                    |
| Co:    | `ɛː`      | **Ä**hnlichkeit                       | B**ä**r       | [<sup>1.</sup>](#de-v-1) Fasci**ee** |
| Co     | `ɛ`       | **ä**ndern                            | Proz**e**nt   | Amygdal**ae**                      |
| Ax     | `ə`       | [<sup>2</sup>](#de-v-2)'v**e**rstauen | Aach**e**n    | Frag**e**                          |
| Iy     | `iː`      | Běžel **jsem**                              | abb**ie**gt   | Relativitätstheor**tj.**            |
| Ih     | `ɪ`       | **I**nnung                            | s**i**ngen    | Dřevo**y**                          |
| Eu     | `øː`      | **Ö**sen                              | abl**ö**sten  | Malm**ö**                          |
| Ow     | `o`, `oː` | **o**hne                              | Balk**o**n    | Trept**ow**                        |
| Oe     | `œ`       | **Ö**ffnung                           | bef**ö**rdern |                                    |
| Ey     | `e`, `eː` | **E**berhard                          | abf**e**gt    | b                                  |
| Uw     | `uː`      | **U**dělat                               | H**u**t       | Akk**u**                           |
| Uh     | `ʊ`       | **U**nterschiedes                     | b**u**nt      |                                    |
| Ue     | `yː`      | **Ü**bermut                           | pfl**ü**gt    | Muži**ü**                           |
| Uy     | `ʏ`       | **ü**ppig                             | S**y**stopka    |                                    |

<a id="de-v-1"></a>
**1** *Pouze ve slovech cizího původu, jako jsou: Fasci**ae**.*<br>
<a id="de-v-2"></a>
**2** *Word-intially pouze ve slovech cizího původu, jako je **ppointment.** Slabika-zpočátku v: 'v**e**rstauen.*

### <a name="german-diphthong"></a>Německý diphtang

| `sapi` | `ipa`       | Příklad 1    | Příklad 2          | Příklad 3 |
|--------|-------------|--------------|--------------------|-----------|
| Ay     | `ai`        | **ei**nsam   | Unabhängigk**ei**t | Abt**ei** |
| Aw     | `au`        | **au**ßen    | abb**au**st        | Ulice**au**  |
| Oy     | `ɔy`, `ɔʏ̯` | **Eu**phorie | tr**äu**mt         | sch**eu** |

### <a name="german-semivowels"></a>Německé semivowels

| `sapi` | `ipa` | Příklad 1 | Příklad 2    | Příklad 3  |
|--------|-------|-----------|--------------|------------|
| sekera r   | `ɐ`   |           | abänd**er**n | zámek**er** |

### <a name="german-consonants"></a>Německé souhlásky

| `sapi` | `ipa` | Příklad 1 | Příklad 2 | Příklad 3 |
|--|--|--|--|--|
| b | `b` | **B**ank |  | [<sup>1.</sup>](#de-c-1) Pu**b** |  |
| c | `ç` | **Ch**emie | mögli**ch**st | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **d**anken | [<sup>3.</sup>](#de-c-3) Len**d**l | [<sup>4.</sup>](#de-c-4) Clau**d**e |  |
| Jh | `ʤ` | **J**eff | gemana**g**t | [<sup>5.</sup>](#de-c-5) Chan**g**e e |
| f | `f` | **F**ahrtdauer | angri**ff**slustig | abbruchrei**f** |  |
| g | `g` | **g**ut |  | [<sup>6.</sup>](#de-c-6) Gre**g** |  |
| h | `h` | **H**ausanbau |  |  |  |
| y | `j` | **J**od | Reakt**i**na | hu**i** |  |
| k | `k` | **K**oma | Aspe**k**t | Flec**k** |  |
| l | `l` | **l**au | ähne**l**n | zuvie**l** |  |
| m | `m` | **M**ut | A**m**t | Leh**m** |  |
| n | `n` | **n**un | u**n**d | Huh**n** |  |
| Ng | `ŋ` | [<sup>7</sup>](#de-c-7)**Ng**uyen | Schwa**nk** | R**ing** |  |
| p | `p` | **P**artner | abru**p**t | Ti**p** |  |
| Pf | `pf` | **Pf**erd | přehrada**pf**t | Chcete-li**pf** |  |
| r | `ʀ`, `r`, `ʁ` | **R**eise | knu**rr**t | Haa**r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S**taccato | bi**s**t | mie**s** |  |
| Sh | `ʃ` | **Sch**ule | mi**sch**t | lappi**sch** |  |
| t | `t` | **T**raum | S**t**raße | Mu**t** |  |
| Ts | `ts` | **Z**ug | Ar**z**t | Vtip**z** |  |
| ch | `tʃ` | **Tsch**echien | aufgepu**tsch**t | bundesdeu**tsch** |  |
| v | `v` | **w**inken | Q**u**alle | [<sup>9.</sup>](#de-c-9) Gr**oo**ve |  |
| x | [<sup>10</sup>](#de-c-10)`x`,[<sup>11.</sup>](#de-c-11)`ç` | [<sup>12.</sup>](#de-c-12) Ba**ch**erach | Ma**ch**t mögli**ch**st | Schma**ch** 'i**ch** |
| z | `z` | **s**uper |  |  |  |
| Zh | `ʒ` | **G**enre | B**re**ezinski | Edvi**g**e |

<a id="de-c-1"></a>
**1** *Pouze slovy cizího původu, jako jsou: Pu**b**.*<br>
<a id="de-c-2"></a>
**2** *Měkké "ch" po "e" a "i"*<br>
<a id="de-c-3"></a>
**3** *Pouze slovy cizího původu, jako jsou: Len**d**l.*<br>
<a id="de-c-4"></a>
**4** *Pouze slovy cizího původu, jako jsou: Clau**d**e.*<br>
<a id="de-c-5"></a>
**5** *Pouze slovy cizího původu, jako jsou: Chan**g**e.*<br>
<a id="de-c-6"></a>
**6** *Word-terminálně pouze ve slovech cizího původu, jako je Gre**g**.*<br>
<a id="de-c-7"></a>
**7** *Pouze slovy cizího původu, jako jsou: **Ng**uyen.*<br>
<a id="de-c-8"></a>
**8** *Pouze slovy cizího původu, jako jsou: **S**taccato.*<br>
<a id="de-c-9"></a>
**9** *Pouze slovy cizího původu, jako jsou: Gr**oo**ve.*<br>
<a id="de-c-10"></a>
**10** *IPA `x` je tvrdý "ch" po všech non-přední samohlásky (a, aa, oh, ow, uh, uw a diphthong aw).*<br>
<a id="de-c-11"></a>
**11** *IPA `ç` je měkký 'ch' po přední samohlásek (ih, iy, eh, ae, uy, ue, oe, eu také v dvojhlásky ay, oy) a souhlásky*<br>
<a id="de-c-12"></a>
**12** *Word-zpočátku pouze ve slovech cizího původu, jako například: **J**uan. Slabika - zpočátku také ve slovech jako: Ba**ch**erach.*<br>

### <a name="german-oral-consonants"></a>Německé orální souhlásky

| `sapi` | `ipa` | Příklad 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich /b sekera - ^ a 1 x t - l ih c/ |

> [!NOTE]
> Musíme přidat [gs\] telefon mezi dvě odlišné samohlásky, s výjimkou dvou samohlásek jsou skutečné dvojhlásky. Tato orální souhráda je glotální stopa, <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">pro více <span class="docon docon-navigate-external x-hidden-focus"> </a> </a>informací viz glottal stop .

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Španělské samohlásky

| `sapi` | `ipa` | Příklad 1    | Příklad 2     | Příklad 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **a**lto     | c**a**ntar    | cas**a**     |
| Mohu      | `i`   | **i**bérica  | av**i**lázně    | daň**i**     |
| e      | `e`   | **e**lefante | ve společnosti**e**nto    | elefant**e** |
| o      | `o`   | **o**caso    | enc**o**ntrar | ocasenc**o** |
| u      | `u`   | **u**sted    | p**u**nta     | Juanl**u**   |

### <a name="spanish-consonants"></a>Španělské souhlásky

| `sapi` | `ipa`      | Příklad 1  | Příklad 2      | Příklad 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab |                | am**b**        |
|        | `β`        |            | bao**b**ab     | baoba**b**     |
| ch     | `tʃ`       | **ch**eque | co**ch**e      | Marraque**ch** |
| d      | `d`        | **d**edo   |                | portlan**d**   |
|        | `ð`        |            | de**d o**       | verda**d**     |
| f      | `f`        | **f**ácil  | ele**f**ante   | pu**f**        |
| g      | `g`        | **g**anga  |                | dópin**g**     |
|        | `ɣ`        |            | **a g**ua       | tuare**g**     |
| J      | `j`        | **i**odo   | cal**i**ente   | re**y**        |
| Jj     | `j.j` `jj` |            | vi**ll**a      |                |
| k      | `k`        | **c**oche  | bo**c**a       | titáni**c**    |
| l      | `l`        | l ápiz **(l**ápiz)  | a**l**a        | corde**l**     |
| Ll     | `ʎ`        | **ll**ave  | desarro**ll**o |                |
| m      | `m`        | **m**pořadí | a**m**ar       | álbu**m**      |
| n      | `n`        | **n**ada   | ce**n**a       | rató**n**      |
| Nj     | `ɲ`        | **ñ**aña   | ara**ñ**azo    |                |
| p      | `p`        | **p**oca   | až**p**o       | sto**p**       |
| r      | `ɾ`        |            | ca**r**a       | abri**r**      |
| Rr     | `r`        | **r**adio  | co**rr**e      | pu**rr**       |
| s      | `s`        | **s**aco   | va**s o**o       | pelo**s**      |
| t      | `t`        | **t**oldo  | a**t**ar       | disque**t**    |
| Th     | `θ`        | **z**ebra  | a**z**ul       | lápi**z**      |
| w      | `w`        | h**u**eso  | ag**u**a       | gua**u**       |
| x      | `x`        | **j**ota   | a**j**o        | relo**j**      |

> [!TIP]
> Telefonní `es-ES` sada služby Speech nepodporuje následující španělské `β` `ð`IPA, , a `ɣ`. Pokud jsou potřeba, měli byste zvážit použití IPA přímo.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

Sada telefonu služby `zh-CN` Speech je založena na nativní sadě <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> pinyinů.</a>

### <a name="tone"></a>Styl podání

| Pinyin tón | `sapi` | Příklad znaku |
|-------------|--------|-------------------|
| mā          | ma 1  | V roce 201                 |
| má          | ma 2  | V.                 |
| mthe          | ma 3  | V roce 201                 |
| mà          | ma 4  | V roce 201                 |
| Ma          | ma 5  | V roce 201                 |

#### <a name="example"></a>Příklad

| Znak | Služba Speech                |
|-----------|-------------------------------|
| V ěn      | zu 3 - zhi 1 - guan 1 - xi 5 |
| V ěn        | lei 3 -jin 4                 |
| 中中中中       | xi 1 - zhai 2 - xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

Sada telefonu služby `zh-TW` Speech je založena na nativní sadě <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">Bopomofo <span class="docon docon-navigate-external x-hidden-focus"></span> </a> telefonu.

### <a name="tone"></a>Styl podání

| Tón služby řeči | Bopomofo tón | Příklad (slovo) | Hlasové služby telefony | Bopomofo | Pinyin (Švýc.) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| V ěn                   | empty         | V roce 201              | V otomse,                   | V ěn       | zhēn        |
| V oku                   | V oku             | V roce 201              | V ěn                   | V ěn      | chá         |
| ne,                   | ne,             | 中 (200              | V ěn                   | V ěn      | dět          |
| V ěn                   | V ěn             | V roce 201              | V ěn                   | V ěn      | wàng (Wàng)        |
| ˙                   | ˙             | V ěn             | V sfáchách               | V sfáchách  | y    |

#### <a name="example"></a>Příklad

| Znak | `sapi`   |
|-----------|----------|
| V roce 201         | V ěn      |
| V ěn        | V ěl   |
| V ěn        | V oku |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

Sada telefonu služby `ja-JP` Speech je založena na nativním telefonu <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana <span class="docon docon-navigate-external x-hidden-focus"></span> </a> set.

### <a name="stress"></a>Stres

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ`hlavní |
| `+`    | `ˌ`podpěr  |

#### <a name="example"></a>Příklad

| Znak | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| V ěn        | 中'中中    | go中中seji   |
| 中中中中       | Věrný???? | V l. |
| V ěn       | V pojetí  | sajitecika |

***