---
title: "Odvození sloupec podle příkladu transformace pomocí Azure Machine Learning Workbench"
description: "V referenčním dokumentu pro transformaci 'odvozena sloupec podle příkladu."
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 3eaf42f8d0bdad274ec92f5790fe79878500a1b2
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="derive-column-by-example-transformation"></a>Odvození sloupec podle příkladu transformace

**Odvozena sloupec podle příkladu** transformace umožňuje uživatelům vytvářet odvozený jeden nebo více existujících sloupců pomocí uživatel zadal příklady odvozené výsledku. Odvozených může být libovolnou kombinací podporovaných řetězců, datum a číslo transformace. 

Následující řetězec, datum a počet transformace podporují:

**Transformace řetězců:** 

Substring – včetně inteligentního extrakce čísla a kalendářní data, zřetězení, manipulaci případ mapování konstantní hodnoty.

**Transformace kalendářních dat:** 

Formát data změnit, extrahování částí datum, čas mapování do přihrádek čas.

Transformace kalendářních dat jsou poměrně obecné s významné několik omezení:
* Timezones nejsou podporovány.
* Některé běžné formáty, které nejsou podporovány:
    * ISO 8601-týden roku formátu (například "2009-W53-7") 
    * UNIX epoch čas.
* Všechny formáty jsou malá a velká písmena (zejména "4: 00" nebyl rozpoznán jako čas, i když je "4: 00").

**Číslo transformace:** 

Round, Floor, Ceiling, Přihrádkování, odsazení s nuly nebo místa, oddělení nebo násobení energie 1000.

**Kompozitní transformace:** 

Libovolnou kombinaci řetězec, číslo nebo datum transformace.

## <a name="how-to-use-this-transformation"></a>Jak používat Tato transformace

K provedení této transformace, postupujte takto:
1. Vyberte jeden nebo více sloupců, které chcete získat hodnotu z. 
2. Vyberte **odvozena sloupec podle příkladu** z **transformuje** nabídky. Nebo klikněte pravým tlačítkem na záhlaví některého z vybraného sloupce a vyberte **odvozena sloupec podle příkladu** v místní nabídce. Otevře se Editor transformace, nový sloupec je přidat vedle správné nejvíce vybraném sloupci. Vybrané sloupce lze identifikovat podle políček v záhlaví sloupců. Přidávání a odebírání sloupců z výběru lze provést pomocí zaškrtávacích políček v záhlaví sloupců.
3. Zadejte příkladem *výstup* proti řádku a stiskněte klávesu enter. V tomto okamžiku nástroje Workbench analyzuje vstupním sloupci a také dostupný výstup syntetizovat program, který můžete převést danou vstupy do výstupu. Syntetizovaná program je spustit pro všechny řádky v mřížce data. Nejednoznačný a komplikované případech může být potřeba více příkladů. V závislosti na tom, jestli jste v režimu Basic nebo Advanced lze zadat více příkladů různými způsoby.
4. Zkontrolujte výstup a klikněte na tlačítko **OK** tak, aby přijímal pro transformaci.

### <a name="transform-editor-basic-mode"></a>Transformace editor: základní režimu

Základní režim poskytuje o vložený editační rozhraní v datové mřížce. Příklady výstupu můžete zadat tak, že přejdete do buňky týkající se a zadáte hodnotu. 

Nástroje workbench analýzy dat a pokusí k identifikaci případů edge, které je potřeba zkontrolovat uživatelem. Když je analyzován data, **analyzování dat** se zobrazí v záhlaví editoru transformace. Analýza dokončení jednoho, buď **ne návrhy** nebo **zkontrolujte další návrhy řádek** se zobrazí v záhlaví. Můžete přejít přes hraniční případy kliknutím na **zkontrolujte další návrhy řádek**. V případě, že hodnota není v pořádku pro řádek, by měl jako další příklad klíče v správnou hodnotu. 

### <a name="transform-editor-advanced-mode"></a>Transformace editor: pokročilého režimu

Rozšířený režim poskytuje bohatší možnosti pro odvození sloupce podle příkladu. Všechny příklady jsou uvedeny na jednom místě. Můžete také zkontrolovat všechny případy edge na jednom místě kliknutím na **zobrazit navrhovaných příkladů**. 

V rozšířeném režimu můžete přidat libovolný řádek jako řádek příklad dvojím kliknutím na řádek v mřížce. Jeden řádek zkopírován jako příklad řádek, můžete také upravit data ve zdrojové sloupce aby syntetické příklad. Díky tomu můžete přidat případů, které nejsou aktuálně nachází v ukázková data.

Uživatele můžete přepínat mezi **základní režimu** a **rozšířeném režimu** kliknutím na odkazy v editoru transformace.

### <a name="transform-editor-send-feedback"></a>Transformace editor: váš názor

Kliknutím na **odeslat názor** odkaz otevře **zpětné vazby** poskytl dialogové okno s pole komentáře naplněna příklady uživatele. Uživatel by měl zkontrolovat obsah pole komentáře a obsahují další podrobnosti, abychom tento problém pochopit. Pokud uživatel nechce sdílení dat s Microsoft, uživatel by měl odstranit data předem vyplněnou příklad před kliknutím na tlačítko **odeslat zpětnou vazbu** tlačítko. 

### <a name="editing-existing-transformation"></a>Úprava existující transformace

Uživatele můžete upravit stávající **odvozena sloupec podle příkladu** transformace výběrem **upravit** možnost kroku transformace. Kliknutím na **upravit** otevře v editoru transformace **rozšířeném režimu**, a jsou zobrazeny všechny příklady, které byly poskytnuty během vytváření transformace.

## <a name="examples-of-string-transformations-by-example"></a>Příklady Transformace řetězců příklad


>[!NOTE] 
>Hodnoty v **tučné** představují příklady, které byly poskytnuty dokončení transformace uvedené datové sady.


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. Extrahování názvy souborů z cesty k souborům

Počet příklady, které byly požadované pro tento případ: 2

|Vstup|Výstup|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.py|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|rgb.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.py|2to3.py|
|C:\Python35\Tools\Scripts\analyze_dxp.py|**analyze_dxp.py**|
|C:\Python35\Tools\Scripts\byext.py|byext.py|
|C:\Python35\Tools\Scripts\byteyears.py|byteyears.py|
|C:\Python35\Tools\Scripts\checkappend.py|checkappend.py|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. Case manipulace při extrakci řetězec

Počet příklady, které byly požadované pro tento případ: 3

|Vstup|Výstup|
|:-----|:-----|
|PŘEJEŠ Berte & NEZDAŘENÁ akce;  NOVÉ HANNOVER; Stanice 332; 2015-12-10 @ 17:10:52;|**Nové Hannover**|
|Cesta BRIAR & WHITEMARSH LN;  HATFIELD TOWNSHIP; Stanice 345; 2015-12-10 @ 17:29:21;|Hatfield Township|
|PRŮMĚR HAWS; NORRISTOWN; 2015-12-10 @ 14:39:21-stanice: STA27;|**Norristown**|
|AIRY ST & ST řepky;  NORRISTOWN; Stanice 308A; 2015-12-10 @ 16:47:36;|**Norristown**|
|Berte TŘEŠEŇ & NEZDAŘENÁ akce;  NIŽŠÍ POTTSGROVE; Stanice 329; 2015-12-10 @ 16:56:52;|Nižší Pottsgrove|
|PRŮMĚR byt & ST 9. W;  LANSDALE; Stanice 345; 2015-12-10 @ 15:39:04;|Lansdale|
|PRŮMĚR VAVŘÍN & OAKDALE průměr;  HORSHAM; Stanice 352; 2015-12-10 @ 16:46:48;|Horsham|
|COLLEGEVILLE VP & LYWISKI VP;  SKIPPACK; Stanice 336; 2015-12-10 @ 16:17:05;|Skippack|
|HLAVNÍ ST & PIKE SUMNEYTOWN STARÝ;  NIŽŠÍ SALFORD; Stanice 344; 2015-12-10 @ 16:51:42;|Nižší Salford|
|BLUEROUTE &AMP; DOBĚHU I476 NB K CHEMICKÉ VP; PLYMOUTH; 2015-12-10 @ 17:35:41;|Plymouth|
|RT202 PKWY &AMP; KNAPP VP; MONTGOMERY; 2015-12-10 @ 17:33:50;|Montgomery|
|HUSTOTA VÝSKYTU DRUHU VP &AMP; COLWELL LN; PLYMOUTH; 2015-12-10 @ 16:32:10;|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. Formát data manipulace při extrakci řetězec

Počet příklady, které byly požadované pro tento případ: 1

|Vstup|Výstup|
|:-----|:-----|
|PRŮMĚR MONTGOMERY & WOODSIDE VP;  NIŽŠÍ MERION; Stanice 313; 2015-12-11 @ 04:11:35;|**12 listopadu 2015 4: 00**|
|DREYCOTT LN & volit LANCASTERSKÉHO průměr;  NIŽŠÍ MERION; Stanice 313; 2015-12-11 @ 01:29:52;|12 listopadu 2015 1: 00|
|VP MILL LEVERING E &AMP; CONSHOHOCKEN STAVU VP; NIŽŠÍ MERION; 2015-12-11 @ 07:29:58;|12 listopadu 2015 7: 00|
|Členem této VALLEY VP & MANOR VP;  NIŽŠÍ MERION; Stanice 313; 2015-12-10 @ 20:53:30;|12 října 2015 20: 00|
|PRŮMĚR BELMONT &AMP; OVERHILL VP; NIŽŠÍ MERION; 2015-12-10 @ 23:02:27;|12 října 2015 23: 00|
|PRŮMĚR MONTGOMERY W &AMP; PENNSWOOD VP; NIŽŠÍ MERION; 2015-12-10 @ 19:25:22;|12 října 2015 19: 00|
|PRŮMĚR ROSEMONT & NEZDAŘENÁ akce;  NIŽŠÍ MERION; Stanice 313; 2015-12-10 @ 18:43:07;|12 října 2015 18: 00|
|Zotavení po Havárii AVIGNON & NEZDAŘENÁ akce; NIŽŠÍ MERION; 2015-12-10 @ 20:01:29-stanice: STA24;|12 října 2015 20: 00|

### <a name="s4-concatenating-strings"></a>S4. Zřetězení řetězců

Počet příklady, které byly požadované pro tento případ: 1

>[!NOTE] 
>V tomto příkladu · speciální znak představuje prostory v výstupního sloupce.

|Jméno|Střední počáteční|Příjmení|Výstup|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda··Lohmann|
|Claudio|A|Chew|**Claudio·A·Chew**|
|Sarah-Jane|S|Smith|Sarah-Jane·S·Smith|
|Brandi||Blumenthal|Brandi··Blumenthal|
|Jesusita|R|Cesty|Jesusita·R·Journey|
|Hermina||Hults|Hermina·· Hults|
|Anne-Marie|W|Petr|Anne-Marie·W·Jones|
|Portoriku||Ropp|Rico··Ropp|
|Může Lauren||Fullmer|Lauren-May··Fullmer|
|Matolin|út|Maine|Marc· T· Maine|
|Angie||Adelman|Angie·· Adelman|
|John-Paul||Smith|John-Paul··Smith|
|Skladbu|W|Staller|Song·W·Staller|
|Jill||Jefferies|Jill·· Jefferies|
|Poskytnutá Ruby|M|Simmons|Ruby-Grace·M·Simmons|

### <a name="s5-generating-initials"></a>S5. Generování iniciály

Počet příklady, které byly požadované pro tento případ: 2

|Celý název|Výstup|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Claudio Chew|C.C.|
|Sarah-Jane Smith|S.S.|
|Brandi Blumenthal|B.B.|
|Jesusita cesty|J.J.|
|Hermina Hults|H.H.|
|Dana Marie Petr|A.J.|
|Rico Ropp|R.R.|
|Může Lauren Fullmer|L.F.|
|Matolin Maine|M.M.|
|Angie Adelman|A.A.|
|John Paul Smith|**J.S.**|
|Skladbu Staller|S.S.|
|Jill Jefferies|J.J.|
|Ruby-Grace Simmons|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. Mapování konstantní hodnoty

Počet příklady, které byly požadované pro tento případ: 3

|Správce pohlaví|Výstup|
|:-----|:-----:|
|Muž|**0**|
|Žena|**1**|
|Neznámý|**2**|
|Žena|1|
|Žena|1|
|Muž|0|
|Neznámý|2|
|Muž|0|
|Žena|1|

## <a name="examples-of-number-transformations-by-example"></a>Příklady číslo transformací příklad

>[!NOTE] 
>Hodnoty v **tučné** představují příklady, které byly poskytnuty dokončení transformace uvedené datové sady.


### <a name="n1-rounding-to-nearest-10"></a>N1. Zaokrouhlení na nejbližší 10

Počet příklady, které byly požadované pro tento případ: 1

|Vstup|Výstup|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. Zaokrouhlení dolů na nejbližší 10

Počet příklady, které byly požadované pro tento případ: 2

|Vstup|Výstup|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. Zaokrouhlení na nejbližší hodnotu 0,05

Počet příklady, které byly požadované pro tento případ: 2

|Vstup|Výstup|
|-----:|-----:|
|-75.5812935|**-75.60**|
|-75.2646799|-75.25|
|-75.3519752|-75.35|
|-75.343513|**-75.35**|
|-75.6033497|-75.60|
|-75.283245|-75.30|

### <a name="n4-binning"></a>N4. Přihrádkování

Počet příklady, které byly požadované pro tento případ: 1

|Vstup|Výstup|
|-----:|:-----:|
|20.16|**20-25**|
|14.32|10-15|
|5.44|5-10|
|3.84|0-5|
|3.73|0-5|
|7.36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. Škálování podle 1000

Počet příklady, které byly požadované pro tento případ: 1

|Vstup|Výstup|
|-----:|-----:|
|-243|**-243000**|
|-12.5|-12500|
|-2345.23292|-2345232.92|
|-1202.3433|-1202343.3|
|1202.3433|1202343.3|

### <a name="n6-padding"></a>N6. Odsazení

Počet příklady, které byly požadované pro tento případ: 1

|Kód|Výstup|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>Příklady datum transformací příklad

>[!NOTE] 
>Hodnoty v **tučné** představují příklady, které byly poskytnuty dokončení transformace uvedené datové sady.


### <a name="d1-extracting-date-parts"></a>D1. Extrahování částí data

Tyto části datum byly extrahovat pomocí různých podle příkladu transformace na stejné datové sady. Tučné řetězce představují příklady, které byly zadány v jejich odpovídajících transformaci.

|DateTime|den v týdnu|Datum|Měsíc|Rok|Hodina|Minuta|1 sekunda|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**Fri**|**31**|**Jan**|**2031**|**5**|**54**|**18**|
|17. ledna 1990 13:32:01|St|17|Led|1990|13|32|01|
|. 2034 14 února 05:36:07|Út|14|Úno|2034|5|36|07|
|14. března 2002 13:16:16|Čt|14|Bře|2002|13|16|16|
|21-Jan-1985 05:44:43|Po|21|Led|1985|5|44|**43**|
|16. srpna 1985 01:11:56|Pá|16|Srp|1985|1|11|56|
|20. prosince 2033 18:36:29|Út|20|Pro|2033|18|36|29|
|16. července 1984 10:21:59|Po|16|Čec|1984|10|21|59|
|13. ledna 2038 10:59:36|St|13|Led|2038|10|59|36|
|14. srpna 1982 15:13:54|So|14|Srp|1982|15|13|54|
|22. listopadu 2030 08:18:08|Pá|22|Lis|2030|8|18|08|
|21-Oct-1997 08:42:58|Út|21|Říj|1997|8|42|58|
|28. listopadu 2006 14:19:15|Út|28|Lis|2006|14|19|15|
|29-Apr-2031 04:59:45|Út|29|Dub|2031|4|59|45|
|. 2032 29 ledna 02:38:36|Čt|29|Led|2032|2|38|36|
|11. pravděpodobně 2028 15:31:52|Čt|11|Květen|2028|15|31|52|
|15. července 1977 12:45:39|Pá|15|Čec|1977|12|45|39|
|27-Jan-2029 05:55:41|So|27|Led|2029|5|55|41|
|03. března 2024 10:17:49|Ne|3|Bře|2024|10|17|49|
|14. dubna 2010 00:23:13|St|14|Dub|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. Formátování kalendářních dat

Tato data formattings měla provést pomocí různých podle příkladu transformace na stejné datové sady. Tučné řetězce představují příklady, které byly zadány v jejich odpovídajících transformaci.

|DateTime|Format1|Format2|Format3|Format4|Format5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**1/31/2031**|**Pátek 31. ledna 2031**|**01312031 5:54**|**31/1/2031 5:54:00**|**Q1 2031**|
|17. ledna 1990 13:32:01|1/17/1990|Středa 17. ledna 1990|01171990 13:32|17 1 1990:32 13: 00|OTÁZKA Č. 1 1990|
|. 2034 14 února 05:36:07|2/14/2034|Úterý 14. února 2034|02142034 5:36|14/2 /. 2034 5:36:00|Q1 2034
|14. března 2002 13:16:16|3/14/2002|Čtvrtek 14. března 2002|03142002 13:16|14/3/2002:16 13: 00|Q1 2002
|21-Jan-1985 05:44:43|1/21/1985|Pondělí 21. ledna 1985|01211985 5:44|21/1/1985 5:44:00|OTÁZKA Č. 1 1985
|16. srpna 1985 01:11:56|8/16/1985|Pátek 16 srpen 1985|08161985 1:11|16/8/1985 1:11:00|3. ČTVRTLETÍ 1985
|20. prosince 2033 18:36:29|12/20/2033|Úterý 20 prosince 2033|12202033 18:36|20/12/2033 18:36:00|Q4 2033
|16. července 1984 10:21:59|7/16/1984|Pondělí 16. července 1984|07161984 10:21|16/7/1984 10:21 AM|Q3 1984
|13. ledna 2038 10:59:36|1/13/2038|Středa 13. ledna 2038|01132038 10:59|13/1/2038 10:59 AM|Q1 2038
|14. srpna 1982 15:13:54|8/14/1982|Sobota 14 srpen 1982|08141982 15:13|8/14/1982 15:13:00|3. ČTVRTLETÍ 1982
|22. listopadu 2030 08:18:08|11/22/2030|Pátek 22 listopadu 2030|11222030 8:18|22/11/2030 8:18:00|Q4 2030
|21-Oct-1997 08:42:58|10/21/1997|Úterý 21 říjen 1997|10211997 8:42|21/10/1997 8:42:00|Q4 1997
|28. listopadu 2006 14:19:15|11/28/2006|Úterý 28. listopadu 2006|11282006 14:19|28/11/2006 14:19:00|Q4 2006
|29-Apr-2031 04:59:45|4/29/2031|Úterý 29. dubna 2031|04292031 4:59|29/4 NEBO 2031 4:59:00|Q2 2031
|. 2032 29 ledna 02:38:36|1/29/2032|Čtvrtek 29. ledna 2032|01292032 2:38|29/1 /. 2032 2:38:00|Q1 2032
|11. pravděpodobně 2028 15:31:52|5/11/2028|Čtvrtek 11 může 2028|05112028 15:31|11/5/2028 31 ČASU 15:|Q2 2028
|15. července 1977 12:45:39|7/15/1977|Pátek 15. července 1977|07151977 12:45|15/7/1977 12:45 ODP.|3. ČTVRTLETÍ 1977
|27-Jan-2029 05:55:41|1/27/2029|Sobota 27 leden 2029|01272029 5:55|27/1/2029 5:55:00|Q1 2029
|03. března 2024 10:17:49|3/3/2024|Neděle 3. března 2024|03032024 10:17|3, 3 NEBO 2024 10:17 AM|Q1 2024
|14. dubna 2010 00:23:13|4/14/2010|Středa 14 Duben 2010|04142010 0:23|4/14/2010 12:23:00|Q2 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. Mapování času na dobu

Tato data a času na období mapování se provádí pomocí různých podle příkladu transformace na stejné datové sady. Tučné řetězce představují příklady, které byly zadány v jejich odpovídajících transformaci.

|DateTime|Period(seconds)|Period(minutes)|Období (dvě hodiny)|Období (30 minut)|
|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**0-20**|**45-60**|**5AM-7AM**|**5:30-6:00**|
|17. ledna 1990 13:32:01|**0-20**|30-45|1PM-3PM|13:30-14:00|
|. 2034 14 února 05:36:07|0-20|30-45|5AM-7AM|5:30-6:00|
|14. března 2002 13:16:16|0-20|15-30|1PM-3PM|13:00-13:30|
|21-Jan-1985 05:44:43|40-60|30-45|5AM-7AM|5:30-6:00|
|16. srpna 1985 01:11:56|40-60|0-15|1AM-3AM|1:00-1:30|
|20. prosince 2033 18:36:29|20-40|30-45|5PM-7PM|18:30-19:00|
|16. července 1984 10:21:59|40-60|15-30|9AM-11AM|10:00-10:30|
|13. ledna 2038 10:59:36|20-40|45-60|9AM-11AM|10:30-11:00|
|14. srpna 1982 15:13:54|40-60|0-15|3PM-5PM|15:00-15:30|
|22. listopadu 2030 08:18:08|0-20|15-30|7AM-9AM|8:00-8:30|
|21-Oct-1997 08:42:58|40-60|30-45|7AM-9AM|8:30-9:00|
|28. listopadu 2006 14:19:15|0-20|15-30|1PM-3PM|14:00-14:30|
|29-Apr-2031 04:59:45|40-60|45-60|3: 00 - 5: 00|4:30-5:00|
|. 2032 29 ledna 02:38:36|20-40|30-45|1AM-3AM|2:30-3:00|
|11. pravděpodobně 2028 15:31:52|40-60|30-45|3PM-5PM|15:30-16:00|
|15. července 1977 12:45:39|20-40|45-60|11AM-1PM|12:30-13:00|
|27-Jan-2029 05:55:41|40-60|45-60|5AM-7AM|5:30-6:00|
|03. března 2024 10:17:49|40-60|15-30|9AM-11AM|10:00-10:30|
|14. dubna 2010 00:23:13|0-20|15-30|11PM-1AM|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>Příklady kompozitní transformace příklad

|tripduration|čas spuštění|spustit s id stanice|Spustit zeměpisnou šířku stanice|Spustit zeměpisnou délku stanice|usertype|Sloupec|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42.3625|-71.08822|Odběratel|**Odběratel zachyceny kolo z stanice 107, lat/dlouhé (42.363,-71.088), 08 ledna 2016 na zhruba 16: 00. Doba trvání cesty byl 61 minut**|
|61|2016-01-17 09:28:10|74|42.373268|-71.118579|Zákazník|Zákazník zachyceny kolo z stanice 74, lat/dlouhé (42.373,-71.119), 17. ledna 2016 na přibližně 9: 00. Doba trvání cesty byl 61 minut|
|62|2016-01-25 08:10:26|176|42.386748020450561|-71.119018793106079|Odběratel|Odběratel zachyceny kolo z stanice 176, lat/dlouhé (42.387,-71.119), 25 ledna 2016 na přibližně 8: 00. Doba trvání cesty byl 62 minut|
|63|2016-01-08 10:10:29|107|42.3625|-71.08822|Odběratel|Odběratel zachyceny kolo z stanice 107, lat/dlouhé (42.363,-71.088), 08 ledna 2016 na přibližně 10 AM. Doba trvání cesty byl 63 minut|
|64|2016-01-15 19:42:08|68|42.36507|-71.1031|Odběratel|Odběratel zachyceny kolo z stanice 68, lat/dlouhé (42.365,-71.103), 15 ledna 2016 na přibližně 19: 00. Doba trvání cesty byl 64 minut|
|64|2016-01-22 18:16:13|115|42.387995|-71.119084|Odběratel|Odběratel zachyceny kolo z stanice 115, lat/dlouhé (42.388,-71.119), 22. ledna 2016 na přibližně 18: 00. Doba trvání cesty byl 64 minut|
|68|2016-01-18 09:51:52|178|42.359573201090441|-71.101294755935669|Odběratel|Odběratel zachyceny kolo z stanice 178, lat/dlouhé (42.360,-71.101), na 18. ledna 2016 na přibližně 9: 00. Doba trvání cesty byl 68 minut|
|69|2016-01-14 08:57:55|176|42.386748020450561|-71.119018793106079|Odběratel|Odběratel zachyceny kolo z stanice 176, lat/dlouhé (42.387,-71.119), 14 ledna 2016 na přibližně 8: 00. Doba trvání cesty byl 69 minut|
|69|2016-01-13 22:12:55|141|42.363560158429884|-71.08216792345047|Odběratel|Odběratel zachyceny kolo z stanice 141, lat/dlouhé (42.364,-71.082), 13. ledna 2016 na přibližně 22: 00. Doba trvání cesty byl 69 minut|
|69|2016-01-15 08:13:09|176|42.386748020450561|-71.119018793106079|Odběratel|Odběratel zachyceny kolo z stanice 176, lat/dlouhé (42.387,-71.119), 15 ledna 2016 na přibližně 8: 00. Doba trvání cesty byl 69 minut|


## <a name="technical-notes"></a>Technické poznámky

### <a name="conditional-transformations"></a>Podmíněné transformace
V některých případech nelze nalézt jeden transformaci, která by splnila dané příklady. V takových případech odvozena sloupec podle příkladu transformace pokusí vstupy na základě vzoru pro některé skupiny a další samostatné transformace pro každou skupinu. Říkáme to **podmíněného transformace**. **Podmíněné transformace** dojde k pokusu o pouze pro transformace s jedním sloupcem vstupní. 

### <a name="reference"></a>Referenční informace
Další informace o transformaci řetězec technologie příklad naleznete v [této publikace](https://www.microsoft.com/en-us/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/).
