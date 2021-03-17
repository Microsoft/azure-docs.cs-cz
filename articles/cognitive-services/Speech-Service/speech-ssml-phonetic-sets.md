---
title: Řeč, foneticky – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Naučte se, jak je fonetická abeceda pro rozpoznávání řeči namapována na mezinárodní fonetickou abecedu (IPA) a kdy použít tuto sadu.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 580de0112c99eef4ba22ba946f6a2b81bc326001
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432039"
---
# <a name="speech-service-phonetic-sets"></a>Fonetické sady pro hlasové služby

Služba Speech definuje fonetické abecedy ("telefonní sady" pro krátké), které se skládají z sedmi jazyků; `en-US`, `fr-FR` , `de-DE` , `es-ES` , `ja-JP` , a `zh-CN` `zh-TW` . Telefonní sady pro hlasové služby se obvykle mapují na <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">mezinárodní foneticky abecedy (IPA) </a>. Telefonní sady pro hlasové služby se používají ve spojení s [jazykem SSML (Speech syntézy)](speech-synthesis-markup.md)jako součást nabídky služeb převodu textu na řeč. V tomto článku se dozvíte, jak jsou tyto telefonní sady namapované a kdy použít telefonní sadu.

# <a name="en-us"></a>[EN-US](#tab/en-US)

### <a name="english-suprasegmentals"></a>Suprasegmentals v angličtině

| Příklad 1 (Úvod k souhláskě, slovo počáteční pro samohlásku) | Příklad 2 (pro souhlásky, wordové nucleusy pro samohlásku) | Příklad 3 (Coda pro souhlásku, slovo konečný pro samohlásku) | Komentáře |
|--|--|--|--|
| Burger/b ER **1** r-g AX r/ | Falafel/f AX-l AA **1** -f AX l/ | baskytaru/g IH-t AA **1** r/ | Telefonická sada hlasových služeb – telefon po samohláskě s napětím |
| nevhodné/IH **2** – n AA-p AX r-t UW 1 n/ | podobnost/d/d IH-s IH **2**-m AX-l eh 1-r AX-t iy/ | pracovní síly/w ER 1 r k-f Ao **2** r s/ | Telefonická sada hlasových služeb: po samohláskě slabiky se nastaví zátěž. |

### <a name="english-vowels"></a>Anglické samohlásky

| `sapi` | `ipa` | Příklad 1     | Příklad 2 | Příklad 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| iy     | `i`   | **EA** t       | f **EE** l  | Vall **enalezen**                  |
| ih     | `ɪ`   | **i** f        | f **vše**  |                             |
| enalezen     | `eɪ`  | **te**       | g **a** te  | d **Ay**                     |
| Uh     | `ɛ`   | **e**     | p **e** t   | m **eh** (výjimečné slovo finally) |
| ä     | `æ`   | **ktivní**    | c **a** t   | n **Ah** (výjimečné slovo finally) |
| aa     | `ɑ`   | **o** bstinate | p **o** PPY | r **Ah** (výjimečné slovo finally) |
| po     | `ɔ`   | Rozsah **o**    | c **au** se | UT **Ah**                    |
| Uh     | `ʊ`   | b **Nún** k      |           |                             |
| Jak     | `oʊ`  | **o** ld       | CL **o** ne | g **o**                      |
| UW     | `u`   | **U** ber      | b **ó** St | t **ó**                     |
| protokol     | `ʌ`   | **u** ncle     | c **u** t   |                             |
| ay     | `aɪ`  | **i** CE       | b **i** te  | FL **y**                     |
| AW     | `aʊ`  | **organizační jednotka** t       | s **ou** tou | navýšení **c**                     |
| Oy     | `ɔɪ`  | **Oi** l       | j **Oi** n  | t **Oy**                     |
| UW y   | `ju`  | **Yu** mA      | h **u** člověka | f **ový**                     |
| AX     | `ə`   | **a** přejít       | WOM **a** n | jsou                    |

### <a name="english-r-colored-vowels"></a>Angličtina R – barevné samohlásky

| `sapi` | `ipa` | Příklad 1    | Příklad 2      | Příklad 3  |
|--------|-------|--------------|----------------|------------|
| IH r   | `ɪɹ`  | **ušní** prvky     | t **IR** AMISU   | n **ušní**   |
| Eh r   | `ɛɹ`  | **vzduchová** rovina | aplikace **ar** ently | SC **ar** e  |
| Uh r   | `ʊɹ`  |              |                | c **vaše** e   |
| Ay r   | `aɪɹ` | **Vyžadovat** půda  | ahradit f **IR**  | ch **OIR**  |
| AW r   | `aʊɹ` | **hodina** s    | p **malá** Vytvářejte   | s **naším**   |
| Ao r   | `ɔɹ`  | **nebo** AD   | m **nebo** Al      | s **OAR**   |
| AA r   | `ɑɹ`  | tist **ar**   | St **ar** t      | c **ar**    |
| ER r   | `ɝ`   | **ušní** th    | b **IR** d       | f    |
| AX r   | `ɚ`   |              | všechny Gy **ER**    | Po **ER** |

### <a name="english-semivowels"></a>Semivowels v angličtině

| `sapi` | `ipa` | Příklad 1           | Příklad 2  | Příklad 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w t**, s **UE** de | Al **w** AYS |           |
| y      | `j`   | **y** ARD, f **e** w   | **zapnuto**  |           |

### <a name="english-aspirated-oral-stops"></a>Ústní zastavení s latinkou vysáváním

| `sapi` | `ipa` | Příklad 1 | Příklad 2   | Příklad 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p** UT   | ha **PP**– cs  | FLA **p**   |
| b      | `b`   | IG **b**   | číslo **b** ER  | CRA **b**   |
| t      | `t`   | **t** ALK  | CAPI **t** Al | sough **t** |
| d      | `d`   | **d** IG   | spuštěný **d** OM  | ro **d**    |
| k      | `k`   | **c** UT   | SLA **CK** ER | Ira **q**   |
| g      | `g`   | **g** o    | a **g** o     | DRA **g**   |

### <a name="english-nasal-stops"></a>Nasaly anglické verze

| `sapi` | `ipa` | Příklad 1        | Příklad 2  | Příklad 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m** na, Smash   | období **certifikační** autority | Roo **m**    |
| n      | `n`   | **n** o, s **n** oddálení | te **n** t   | kuřata **n** |
| plyn     | `ŋ`   |                  | li **n** k   | s    |

### <a name="english-fricatives"></a>Fricatives v angličtině

| `sapi` | `ipa` | Příklad 1   | Příklad 2        | Příklad 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | síť **f**    | Le **f** t         | vrstva HAL **f**   |
| v      | `v`   | **v** alue   | Elektronický **v**        | Lo **v** e   |
| Kolik     | `θ`   | **tou** v    | EMPA **y**      | Mon **th**  |
| algoritm     | `ð`   | **th** EN    | mo **th**       | smoo **th** |
| s      | `s`   | **s** IT     | rezervované instance **s**         | fakt  |
| z      | `z`   | bod **z**     | bu **s** y         | děti **s**   |
| SH     | `ʃ`   | **SH** e    | abbrevia **ČŘ** | ru **SH**   |
| ZH     | `ʒ`   | **J** acques | Plea **s** urovat     | Gara **g** e |
| h      | `h`   | **h** ELP    | průvo EN **h**      | a-**h** a!  |

### <a name="english-affricates"></a>Affricates v angličtině

| `sapi` | `ipa` | Příklad 1 | Příklad 2    | Příklad 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch** v  | Fu **t** urovat   | Atta **ch** |
| jh     | `dʒ`  | **j** Oy   | Ori **g** inal | Oran **g** e |

### <a name="english-approximants"></a>Approximants v angličtině

| `sapi` | `ipa` | Příklad 1          | Příklad 2  | Příklad 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l** ID, g **l** AD  | PA –**t**– ACE | chí **vše** |
| r      | `ɹ`   | **r** Ed, b **r** z | Bo **RR** | **r** ta   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Francouzština suprasegmentals

Telefonická sada pro hlasové služby přináší zátěž po samohláskě zatížené slabiky, ale `fr-FR` telefonní sada hlasové služby nepodporuje IPA ˌ. Pokud je požadováno IPA, měli byste použít IPA přímo.

### <a name="french-vowels"></a>Francouzské samohlásky

| `sapi` | `ipa` | Příklad 1     | Příklad 2       | Příklad 3 |
|--------|-------|---------------|-----------------|-----------|
| pro      | `a`   | **rbre**     | p **a** TTE       | IR **a**   |
| aa     | `ɑ`   |               | p **–**– te        | p **a** s   |
| AA ~   | `ɑ̃`  | **EN** fant    | ENF **EN** t      | t **em** PS |
| AX     | `ə`   |               | p **e** Tite      | l **e**    |
| Uh     | `ɛ`   | **e** lle      | p **e** RDU       | ét **AI** t |
| Balijšitna     | `ø`   | **œu** FS      | CR **–** ta – ser     | qu **EU**  |
| enalezen     | `e`   | ému           | crétin          | ôté       |
| Eh   | `ɛ̃`  | port **im** | p **Ein** telů    | rohože **v** |
| iy     | `i`   | **dée**      | PET **i** te      | jsem **já**   |
| OE     | `œ`   | **œu** f       | p **EU** r        |           |
| Oh     | `ɔ`   | **o** bstacle  | c **o** RPS       |           |
| Ne. ~   | `ɔ̃`  | **na ze systému**      | r **v** deur     | b **zapnuto**   |
| Jak     | `o`   | **au** diteur  | b **Eau** Coup    | p **ô**    |
| OE ~   | `œ̃ ` | **instalován**        | l **un** di       | BR **un**  |
| UW     | `u`   | trage **organizační jednotky**   | Vable **organizační jednotky** intr | **jednotky**    |
| uy     | `y`   | **u** ne       | p **u** Jmenuji Nir a       | él **u**   |

### <a name="french-consonants"></a>Francouzské souhlásky

| `sapi` | `ipa` | Příklad 1   | Příklad 2     | Příklad 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | ête **b**    | ha **b** Ille   | ro **b** e                         |
| d      | `d`   | **d** vyžadovat    | Ron **d** EUR   | Chau **d** e                       |
| f      | `f`   | Emme **f**   | Su **FF** IXE   | Bo **f**                          |
| g      | `g`   | auche **g**  | é **g** rahy     | ba –**Gu** e                        |
| plyn     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)**parky** |
| hy     | `ɥ`   | h **u**   | n **u** vyžadovat     |                                  |
| k      | `k`   | Arte **jazyka c**   | é **c** aille   | být **c**                          |
| l      | `l`   | **l** Ong    | é **l** vyžadovat     | ba **l**                          |
| m      | `m`   | **m** Adam  | AI **m** ER     | Po **mm** e                        |
| n      | `n`   | **n** OJ    | te **n** IR     | Bo **NN** e                        |
| Newarku     | `ɲ`   |             |               | Pei **GN** e                       |
| p      | `p`   | **p** Atte   | znovu **p** jako     | CA **p**                          |
| r      | `ʁ`   | **r** v     | Ča **r** IoT   | odesílá se **r**                       |
| s      | `s`   | **s** ourir  | **SS** EZ     | PA **SS** e                        |
| SH     | `ʃ`   | **ch**– ante | Ma **ch** DEK   | Po **ch** e                        |
| t      | `t`   | **t** ête    | ô **t** ER      | Ne **t**                          |
| v      | `v`   | **v-v**    | v **ENTER v** zadejte  | rê **v** e                         |
| w      | `w`   | **organizační jednotka** i     | dek **organizační jednotky**    |                                  |
| y      | `j`   | **a** od     | étiner p **i**  | Mars **Ille**                    |
| z      | `z`   | * * z * * éro   | Rai **s** onner | ro **s** e                         |
| ZH     | `ʒ`   | **j** ardin  | muž **g** ER    | piè **g** e                        |
|        | `n‿`  |             |               | u **n** Arbre                     |
|        | `t‿`  |             |               | Quan **d**                        |
|        | `z‿`  |             |               | di **x**                          |

<a id="fr-1"></a>
**1** *pouze pro některá cizí slova.*

> [!TIP]
> `fr-FR`Telefonní sada hlasové služby nepodporuje následující francouzské liasions, `n‿` , `t‿` a `z‿` . Pokud jsou potřeba, měli byste zvážit přímé použití IPA.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Německé suprasegmentals

| Příklad 1 (Úvod k souhláskě, slovo počáteční pro samohlásku) | Příklad 2 (pro souhlásky, wordové nucleusy pro samohlásku) | Příklad 3 (Coda pro souhlásku, slovo konečný pro samohlásku) | Komentáře |
|--|--|--|--|
| Anders/a **1** n-d AX r s/ | Multiplikationszeichen/m uh l-t iy-p l iy-k a-TS y navýšení na **1** n s-TS Ay-c n/ | Biologie/b iy – přečtení z-l v-g iy **1**/ | Telefonická sada hlasových služeb – telefon po samohláskě s napětím |
| Allgemeinwissen/a **2** l-g AX-m Ay 1 n-v IH-s n/ | Abfallentsorgungsfirma/a 1 p-f a l-^ eh n t-z Oh **2** AX r-g uh ng s-f IH AX r-m a/ | Computertomographie/k Oh m-p y UW 1-t AX r-t – Čt-m-g r a-f iy **2**/ | Telefonická sada hlasových služeb: po samohláskě slabiky se nastaví zátěž. |

### <a name="german-vowels"></a>Německé samohlásky

| `sapi` | `ipa`     | Příklad 1                             | Příklad 2     | Příklad 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| určitého     | `aː`      | **Ber**                              | Maßst **a** b   | Schem **a**                         |
| pro      | `a`       | **Bfall**                            | B **a** ch      | Agath **a**                         |
| Oh     | `ɔ`       | **O** sten                             | PF **o** sten   |                                    |
| Uh    | `ɛː`      | **Ä** hnlichkeit                       | B **ä** r       | [<sup>1</sup>](#de-v-1) Fasci **AE** |
| Uh     | `ɛ`       | **ä** ndern                            | ProZ **e** NT   | Amygdal **AE**                      |
| AX     | `ə`       | [<sup>2</sup>](#de-v-2)' v **e** rstauen | Aach **e** n    | Frag **e**                          |
| iy     | `iː`      | Spustil (a) **jsem**                              | ABB **IE** gt   | Relativitätstheor **IE**            |
| ih     | `ɪ`       | **Nnung**                            | s **–** Ngen    | Dřevo **y**                          |
| Balijšitna     | `øː`      | **Ö**                              | ABL **ö** sten  | MALM **ö**                          |
| Jak     | `o`, `oː` | **o** hne                              | Balk **o** n    | Trept                        |
| OE     | `œ`       | **Ö** ffnung                           | BEF **ö** rdern |                                    |
| enalezen     | `e`, `eː` | **E** berhard                          | ABF **e** gt    | b                                  |
| UW     | `uː`      | **U**                               | H **u** t       | AKK **u**                           |
| Uh     | `ʊ`       | **U** nterschiedes                     | b **u** NT      |                                    |
| UE     | `yː`      | **Ü** bermut                           | PFL **ü** gt    | Muži **ü**                           |
| uy     | `ʏ`       | **ü** PPIG                             | Kmen S **y**    |                                    |

<a id="de-v-1"></a>
**1** *pouze ve slovech cizího původu, například: fasci **AE**.*<br>
<a id="de-v-2"></a>
**2** *Word – intially jenom ve slovech cizího původu, jako **je** třeba ppointment. Slabiky na začátku v: ' v **e** rstauen.*

### <a name="german-diphthong"></a>Německé diphthong

| `sapi` | `ipa`       | Příklad 1    | Příklad 2          | Příklad 3 |
|--------|-------------|--------------|--------------------|-----------|
| ay     | `ai`        | **EI** nsam   | Unabhängigk **EI** t | ABT **EI** |
| AW     | `au`        | **au** ßen    | ABB **au** St        | St **au**  |
| Oy     | `ɔy`, `ɔʏ̯` | Phorie **EU** | TR **äu** MT         | SCH **EU** |

### <a name="german-semivowels"></a>Německé semivowels

| `sapi` | `ipa` | Příklad 1 | Příklad 2    | Příklad 3  |
|--------|-------|-----------|--------------|------------|
| AX r   | `ɐ`   |           | abänd **ER** n | zámek **ER** |

### <a name="german-consonants"></a>Německé souhlásky

| `sapi` | `ipa` | Příklad 1 | Příklad 2 | Příklad 3 |
|--|--|--|--|--|
| b | `b` | ANK **B** |  | [<sup>1</sup>](#de-c-1) PU **b** |  |
| c | `ç` | **Ch** Emie | mögli **ch** St | [<sup>2</sup>](#de-c-2)i **ch** |
| d | `d` | **d** anken | [<sup>3</sup>](#de-c-3) . Len **d** l | [<sup>4</sup>](#de-c-4) . Clau **d** e |  |
| jh | `ʤ` | **J** EFF | gemana **g** t | [<sup>5</sup>](#de-c-5) . Kanál **g** e |
| f | `f` | Ahrtdauer **F** | angri **FF** slustig | abbruchrei **f** |  |
| g | `g` | **g** UT |  | [<sup>6</sup>](#de-c-6) GRE **g** |  |
| h | `h` | **H** ausanbau |  |  |  |
| y | `j` | **J** od | Reakt **i** on | hu **i** |  |
| k | `k` | **K** OMA | Aspe **k** t | Flec **k** |  |
| l | `l` | **l** au | ähne **l** n | zuvie **l** |  |
| m | `m` | **M** UT | A **m** t | Leh **m** |  |
| n | `n` | **n** un | u **n** d | Huh **n** |  |
| plyn | `ŋ` | [<sup>7</sup>](#de-c-7)**NG** Uyen | Šva **NK** | R **z** |  |
| p | `p` | **P** Artner | abru **p** t | Ča **p** |  |
| stránkovací | `pf` | Záchranná disketa **PF** | přehradit **PF** t | Do **BF** |  |
| r | `ʀ`, `r`, `ʁ` | Eise jazyka **R** | KNU **RR** t | Há **r** |  |
| s | `s` | taccato [<sup>8</sup>](#de-c-8)**S** | BI **s** t | Mie **s** |  |
| SH | `ʃ` | **SCH** ravidlo | mi **SCH** t | Lappi **SCH** |  |
| t | `t` | **T** Raum | S **t** raße | Mu **t** |  |
| licence | `ts` | **Z** g | Ar **z** t | WIT **z** |  |
| ch | `tʃ` | **Tsch** echien | aufgepu **tsch** t | bundesdeu **tsch** |  |
| v | `v` | **w** inken | Q **u** Alle | [<sup>9</sup>](#de-c-9) GR **ó** ve |  |
| x | [<sup>10</sup>](#de-c-10) `x` ,[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12) . Ba **ch** erach | Ma **ch** t mögli **ch** St | Schma **ch** ' i **ch** ' |
| z | `z` | **s** uper |  |  |  |
| ZH | `ʒ` | Enre **G** | B **znovu** ezinski | EDVI **g** e |

<a id="de-c-1"></a>
**1** *pouze ve slovech cizího původu, například: PU **b**.*<br>
<a id="de-c-2"></a>
**2** *měkké "ch" po "e" a "i"*<br>
<a id="de-c-3"></a>
**3** *jenom ve slovech cizího původu, například: len **d** l.*<br>
<a id="de-c-4"></a>
**4** *pouze ve slovech cizího původu, například: Clau **d** e.*<br>
<a id="de-c-5"></a>
**5** *pouze ve slovech cizího původu, například: kanál **g** e.*<br>
<a id="de-c-6"></a>
**6** *slov – jenom v případě cizího původu, jako je GRE **g***<br>
<a id="de-c-7"></a>
**7** *pouze ve slovech cizího původu, jako je: **NG** Uyen.*<br>
<a id="de-c-8"></a>
**8** *pouze ve slovech cizího původu, jako je například: **S** taccato.*<br>
<a id="de-c-9"></a>
**9** *pouze v slovech cizího původu, například: gr **ó** v.*<br>
<a id="de-c-10"></a>
**10** *IPA `x` je pevně "ch" po všech nepředních samohláskách (a, AA, Oh, uh, uh, UW a diphthong AW).*<br>
<a id="de-c-11"></a>
**11** *IPA `ç` je měkký "ch" za předními samohláskami (IH, IY, eh, AE, UY, UE, OE, EU také v diphthongs Ay, Oy) a souhlásky*<br>
<a id="de-c-12"></a>
**12** *slov – zpočátku jenom v slovech cizího původu, například: **J** močovina Slabiky – zpočátku také ve slově jako: ba **ch** erach.*<br>

### <a name="german-oral-consonants"></a>Německé orální souhlásky

| `sapi` | `ipa` | Příklad 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich/b AX – ^ a 1 x t-l IH c/ |

> [!NOTE]
> Musíme přidat [GS \] telefon mezi dvě odlišná samohlásky, s výjimkou těchto dvou samohlásek je pravý diphthong. Tato orální souhláska je ráz, další informace najdete v části <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank"> ráz a stop <span class="docon docon-navigate-external x-hidden-focus"> </a> </a> .

# <a name="es-es"></a>[ES-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Španělština – samohlásky

| `sapi` | `ipa` | Příklad 1    | Příklad 2     | Příklad 3    |
|--------|-------|--------------|---------------|--------------|
| pro      | `a`   | **LTO**     | c **a** ntar    | CAS **a**     |
| Mohu      | `i`   | **bérica**  | AV **i** Spa    | daň **i**     |
| e      | `e`   | **e** LeFante | v **e** nChcete-li    | Elefant **e** |
| o      | `o`   | **o** caso    | enc **o** ntrar | ocasenc **o** |
| u      | `u`   | **u** STED    | p **u** NTA     | Juanl **u**   |

### <a name="spanish-consonants"></a>Španělské souhlásky

| `sapi` | `ipa`      | Příklad 1  | Příklad 2      | Příklad 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | aobab **b** |                | am **b**        |
|        | `β`        |            | Bao **b** AB     | Baoba **b**     |
| ch     | `tʃ`       | **ch** eque | Co **ch** e      | Marraque **ch** |
| d      | `d`        | **d** Edo   |                | portLAN **d**   |
|        | `ð`        |            | de **d** o       | Verda **d**     |
| f      | `f`        | ácil **f**  | ele **f** ante   | PU **f**        |
| g      | `g`        | anga **g**  |                | dópin **g**     |
|        | `ɣ`        |            | **g** UA       | tuare **g**     |
| j      | `j`        | **Odo**   | seznam **CAL**   | znovu **y**        |
| JJ     | `j.j` `jj` |            | VI **vše** a      |                |
| k      | `k`        | Oche **jazyka c**  | Bo **c** a       | Titáni **c**    |
| l      | `l`        | **l** ápiz  | a        | šňůra **l**     |
| vše     | `ʎ`        | **šechny** Ave  | desarro **vše** o |                |
| m      | `m`        | pořadí **m** | a **m** ar       | álbu **m**      |
| n      | `n`        | **n** Ada   | CE **n** a       | rató **n**      |
| Newarku     | `ɲ`        | **ñ** aña   | Ara **ñ** azo    |                |
| p      | `p`        | **p** OCA   | do **p** o       | ši **p**       |
| r      | `ɾ`        |            | CA **r** a       | Abri **r**      |
| názv     | `r`        | ADIO jazyka **r**  | Co **RR** e      | PU **RR**       |
| s      | `s`        | **s** Accountable Care Organization   | VA **s**– o       | pelo **s**      |
| t      | `t`        | **t** oldo  | a **t** ar       | Disque **t**    |
| Kolik     | `θ`        | **z** Ebra  | **z z** ul       | lápi **z**      |
| w      | `w`        | h **u** eso  | AG **u** a       | Gua **u**       |
| x      | `x`        | **j** OTA   | a **j** o        | relo **j**      |

> [!TIP]
> `es-ES`Telefonní sada hlasové služby nepodporuje následující španělský IPA, `β` , a `ð` `ɣ` . Pokud jsou potřeba, měli byste zvážit přímé použití IPA.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

Telefonická sada pro hlasové služby `zh-CN` je založena na nativní sadě <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">pchin-jin </a> pro telefony.

### <a name="tone"></a>Styl podání

| Tón pchin-jin | `sapi` | Příklad znaku |
|-------------|--------|-------------------|
| mā          | ma 1  | 妈                 |
| má          | ma 2  | 麻                 |
| mǎ          | ma 3  | 马                 |
| mà          | mA 4  | 骂                 |
| Vy          | ma 5  | 嘛                 |

#### <a name="example"></a>Příklad

| Znak | Služba Speech                |
|-----------|-------------------------------|
| 组织关系      | zu 3 – Zhi 1 – Guan 1 – XI 5 |
| 累进        | Lei 3 – Jin 4                 |
| 西宅巷       | XI 1 – Zhai 2 – Xiang 4      |

# <a name="zh-tw"></a>[zh – TW](#tab/zh-TW)

Telefonická sada pro hlasové služby `zh-TW` je založená na nativním <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">začu </a> s telefonní sadou.

### <a name="tone"></a>Styl podání

| Tón služby Speech | Tón Ču-jin | Příklad (Word) | Telefony hlasové služby | Vai | Pchin-jin (拼音) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | empty         | 偵              | ㄓㄣˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚˊ                   | ㄔㄚˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚˇ                   | ㄉㄚˇ      | dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥˇ ㄗ˙               | 一ㄥˇ ㄗ˙  | yǐng Zi    |

#### <a name="example"></a>Příklad

| Znak | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡˇ      |
| 然后        | ㄖㄢˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢˇㄉㄧㄠˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

Telefonická sada pro hlasové služby `ja-JP` je založená na nativním nastavení telefonu <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana </a> .

### <a name="stress"></a>Zdůrazňuje

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ` mainstress |
| `+`    | `ˌ` zátěžový  |

#### <a name="example"></a>Příklad

| Znak | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | Přejít na ˈ wɯseji   |
| 所有者       | ショュ'ウ?ャ | ɕjojɯˈwɯɕja |
| 最適化       | サィテキカ +  | sajitecikaˌ |

***