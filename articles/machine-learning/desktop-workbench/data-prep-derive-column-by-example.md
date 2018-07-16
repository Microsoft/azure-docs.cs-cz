---
title: Odvození sloupce podle příkladu transformaci pomocí Azure Machine Learning Workbench
description: Referenční dokument pro transformaci "odvodit sloupec podle příkladu.
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 026ffed925606e2fdf31461035c9a0d73ad609e9
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060569"
---
# <a name="derive-column-by-example-transformation"></a>Odvození sloupce podle příkladu transformace

**Odvodit sloupec podle příkladu** transformace umožňuje uživatelům vytvářet odvozených děl na základě jednoho nebo více existujících sloupců pomocí poskytnutého uživatelem příklady odvozené výsledku. Odvozených děl na základě může obsahovat libovolnou kombinaci podporovaných řetězců, datum a transformace čísel. 

Následující čísla, řetězce a data jsou podporované transformace:

**Transformace řetězců:** 

Podřetězec včetně inteligentní extrakce čísla a kalendářní data, zřetězení, zpracování případu mapování konstantní hodnoty.

**Transformace data:** 

Formát data změní, extrahování části datum, mapování čas na čas přihrádky.

Transformace data jsou poměrně obecné s několika významná omezení:
* Timezones se nepodporují.
* Některé běžné formáty, které nejsou podporovány:
    * ISO 8601-týden formátu rok (například "2009-W53-7") 
    * UNIX unixovém čase.
* Všechny formáty jsou malá a velká písmena (zejména "4: 00" nebyl rozpoznán jako čas, i když je "4: 00").

**Transformace čísel:** 

Round, Floor, Ceiling Binning, odsazení s nuly nebo místa, divize nebo násobení hodnotou sílu 1000.

**Kompozitní transformace:** 

Libovolnou kombinaci řetězec, číslo nebo datum transformace.

## <a name="how-to-use-this-transformation"></a>Jak používat tuto transformaci

Pokud chcete provést tuto transformaci, postupujte takto:
1. Vyberte jeden nebo více sloupců, které chcete k odvození hodnoty z. 
2. Vyberte **odvodit sloupec podle příkladu** z **transformuje** nabídky. Nebo klikněte pravým tlačítkem na záhlaví libovolného vybraného sloupce a vyberte **odvodit sloupec podle příkladu** v místní nabídce. Otevře se Editor transformovat a vedle správné nejvíce vybraný sloupec je přidán nový sloupec. Vybrané sloupce lze identifikovat pomocí zaškrtávacích políček v záhlaví sloupců. Přidávání a odebírání sloupců z výběru můžete provést pomocí zaškrtávacích políček v záhlaví sloupců.
3. Zadejte příkladem *výstup* proti řádku a stiskněte klávesu enter. V tomto okamžiku v aplikaci Workbench analyzuje vstupní sloupec, jakož i dostupný výstup tak, aby odpovídaly program, který můžete transformovat daný vstupy do výstupu. Pro všechny řádky v datové mřížce provádí syntetizovaný program za dokončený. Nejednoznačný a složité případech může být potřeba více příkladů. V závislosti na tom, jestli jste v režimu Basic nebo Advanced lze zadat několik příkladů různých způsobů.
4. Zkontrolujte výstup a klikněte na tlačítko **OK** transformaci přijměte.

### <a name="transform-editor-basic-mode"></a>Transformace editor: základní režim

Základní režim poskytuje vložené možnosti v datové mřížce úprav. Přejděte na buňku, která vás zajímá, a zadání hodnoty můžete zadat příklady výstupu. 

Aplikace workbench analyzuje data a pokusí se identifikovat hraniční případy, které byste měli zkontrolovat uživatelem. Zatímco data se právě analyzuje, **analýza dat** se zobrazí v záhlaví editoru transformace. Analýza dokončení jednoho, buď **ne návrhy** , nebo **další navrhované řádku ke kontrole** je zobrazený v záhlaví. Hraniční případy můžete procházet kliknutím na **další navrhované řádku ke kontrole**. V případě, že hodnota je nesprávný pro řádek, by měl jako další příklady klíč ve správné hodnoty. 

### <a name="transform-editor-advanced-mode"></a>Transformace editor: rozšířeném režimu

Rozšířený režim poskytuje pohodlnější a pestřejší prostředí pro odvození sloupce podle příkladu. Všechny příklady jsou uvedeny na jednom místě. Můžete také zkontrolovat všechny hraniční případy na jednom místě po kliknutí na **zobrazení navrhovaných příkladů**. 

V rozšířeném režimu můžete přidat libovolný řádek jako na příklad řádek poklikáním na řádek v mřížce. Jeden řádek je zkopírován jako na příklad řádek, můžete také upravit data ve zdrojové sloupce provádět syntetické příklad. Díky tomu můžete přidat případů, které se aktuálně nenachází v ukázková data.

Uživatele můžete přepínat mezi **základním režimu** a **rozšířený režim** kliknutím na odkazy v editoru transformace.

### <a name="transform-editor-send-feedback"></a>Transformace editor: váš názor

Kliknutím na **poslat svůj názor** odkaz otevře **zpětnou vazbu** dialogové okno s pole komentáře naplněna příklady uživatel poskytl. Uživatel by měl zkontrolovat obsah pole komentáře a poskytují další podrobnosti o nám pomůže zjistit potíže. Pokud uživatel nechce sdílet data s Microsoftem, uživatel by měl odstranit předem vyplněná ukázková data před kliknutím na tlačítko **odeslat zpětnou vazbu** tlačítko. 

### <a name="editing-existing-transformation"></a>Úprava existující transformace

Uživatel může upravovat existující **odvodit sloupec podle příkladu** transformovat tak, že vyberete **upravit** kroku transformace. Kliknutím na **upravit** otevře Editor transformace v **rozšířený režim**, a jsou uvedeny všechny příklady, které byly poskytnuty při vytváření transformace.

## <a name="examples-of-string-transformations-by-example"></a>Příklady Transformace řetězců podle příkladu


>[!NOTE] 
>Hodnoty v **tučné** představují příklady, které byly k dispozici k dokončení transformací v zobrazených datové sady.


### <a name="s1-extracting-file-names-from-file-paths"></a>S1. Extrahování názvů souborů z cesty k souborům

Řadu příkladů, které byly zapotřebí pro tento případ: 2

|Vstup|Výstup|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.py|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|rgb.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.py|2to3.PY|
|C:\Python35\Tools\Scripts\analyze_dxp.py|**analyze_dxp.py**|
|C:\Python35\Tools\Scripts\byext.py|byext.py|
|C:\Python35\Tools\Scripts\byteyears.py|byteyears.py|
|C:\Python35\Tools\Scripts\checkappend.py|checkappend.PY|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. Případu manipulace při extrakci řetězec

Řadu příkladů, které byly zapotřebí pro tento případ: 3

|Vstup|Výstup|
|:-----|:-----|
|PŘEJEŠ CT & DEAD END;  NOVÉ HANNOVER; Stanice 332; 2015-12-10 \@ 17:10:52;|**Nové Hannover**|
|Cesta BRIAR & WHITEMARSH LN;  HATFIELD TOWNSHIP; Stanice 345; 2015-12-10 \@ 17:29:21;|Hatfield Township|
|ULOŽIT HAWS; NORRISTOWN; 2015-12-10 \@ 14:39:21-stanice: STA27;|**Norristown**|
|AIRY ST & ST řepky;  NORRISTOWN; Stanice 308A; 2015-12-10 \@ 16:47:36;|**Norristown**|
|TŘEŠEŇ CT & DEAD END;  NIŽŠÍ POTTSGROVE; Stanice 329; 2015-12-10 \@ 16:56:52;|Nižší Pottsgrove|
|BYT Uložit & ST 9 W;  LANSDALE; Stanice 345; 2015-12-10 \@ 15:39:04;|Lansdale|
|Uložit VAVŘÍN u & LOŽIT OAKDALE;  HORSHAM; Stanice 352; 2015-12-10 \@ 16:46:48;|Horsham|
|COLLEGEVILLE VP a LYWISKI RD;  SKIPPACK; Stanice 336; 2015-12-10 \@ 16:17:05|Skippack|
|MAIN ST & PIKE 's SUMNEYTOWN staré;  NIŽŠÍ SALFORD; Stanice 344; 2015-12-10 \@ 16:51:42;|Nižší Salford|
|BLUEROUTE &AMP; NB I476 VSTUPNÍ BRÁNA K CHEMICKÝCH VP; PLYMOUTH; 2015-12-10 \@ 17:35:41;|Plymouth|
|RT202 PKWY &AMP; KNAPP VZDÁLENÉ PLOŠE; MONTGOMERY; 2015-12-10 \@ 17:33:50;|Montgomery|
|HUSTOTA VÝSKYTU DRUHU VP &AMP; COLWELL LN; PLYMOUTH; 2015-12-10 \@ 16:32:10;|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. Manipulace s formát data během extrakce řetězec

Řadu příkladů, které byly zapotřebí pro tento případ: 1

|Vstup|Výstup|
|:-----|:-----|
|Uložit MONTGOMERY & WOODSIDE vzdálené ploše;  NIŽŠÍ MERION; Stanice 313; 2015-12-11 \@ 04:11:35|**Listopad 2015 12 4 AM**|
|DREYCOTT LN u & LOŽIT UNIVERZITĚ W;  NIŽŠÍ MERION; Stanice 313; 2015-12-11 \@ 01:29:52;|Listopad 2015 12 1 AM|
|VP MILL LEVERING E &AMP; CONSHOHOCKEN STAV VZDÁLENÉ PLOŠE; NIŽŠÍ MERION; 2015-12-11 \@ 07:29:58;|Listopad 2015 12 7 AM|
|DISKUTUJÍ VALLEY VP a MANOR RD;  NIŽŠÍ MERION; Stanice 313; 2015-12-10 \@ 20:53:30.|12. října 2015 20: 00|
|ULOŽIT BELMONT &AMP; OVERHILL VZDÁLENÉ PLOŠE; NIŽŠÍ MERION; 2015-12-10 \@ 23:02:27;|12. října 2015 23: 00|
|ULOŽIT MONTGOMERY W &AMP; PENNSWOOD VZDÁLENÉ PLOŠE; NIŽŠÍ MERION; 2015-12-10 \@ 19:25:22;|12. října 2015 19: 00|
|Uložit ROSEMONT & DEAD END;  NIŽŠÍ MERION; Stanice 313; 2015-12-10 \@ 18:43:07;|12. října 2015 18: 00|
|Zotavení po Havárii AVIGNON & DEAD END; NIŽŠÍ MERION; 2015-12-10 \@ 20:01:29 – stanice: STA24;|12. října 2015 20: 00|

### <a name="s4-concatenating-strings"></a>S4. Zřetězení řetězců

Řadu příkladů, které byly zapotřebí pro tento případ: 1

>[!NOTE] 
>V tomto příkladu speciální znak. představuje mezery ve výstupním sloupci.

|Jméno|Iniciály prostředního|Příjmení|Výstup|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda··Lohmann|
|Claudio|A|Chew|**Claudio·A·Chew**|
|Sarah Jana|S|Smith|Sarah-Jane·S·Smith|
|Brandi||Blumenthal|Brandi·· Blumenthal|
|Jesusita|R|Cesty|Jesusita· R· Cesty|
|Hermina||Hults|Hermina·· Hults|
|Anne Marie|W|Jones|Anne Marie· W· Jones|
|Rico||Ropp|Rico·· Ropp|
|Lauren dne||Fullmer|Lauren May·· Fullmer|
|Marc|T|Maine|Marc· T· Maine|
|Angie||Adelman|Angie·· Adelman|
|Jan Paul||Smith|Jan Paul·· Smith|
|Skladby|W|Staller|Song· W· Staller|
|Jill||Jefferies|Jill·· Jefferies|
|Ruby odkladu|M|Simmonse|Ruby Grace· M· Simmonse|

### <a name="s5-generating-initials"></a>S5. Generování iniciály

Řadu příkladů, které byly zapotřebí pro tento případ: 2

|Celý název|Výstup|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Claudio Chew|C.C.|
|Sarah Jan Macek|S.S.|
|Brandi Blumenthal|B.B.|
|Jesusita cesty|J.J.|
|Hermina Hults|H.H.|
|Anne Marie Jones|A.J.|
|Rico Ropp|R.R.|
|Fullmer Lauren dne|L.F.|
|Marc Maine|M. M.|
|Angie Adelman|A.A.|
|Jan Paul Smith|**J.S.**|
|Skladby Staller|S.S.|
|Jill Jefferies|J.J.|
|Simmonse odkladu Ruby|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. Mapování hodnot konstant

Řadu příkladů, které byly zapotřebí pro tento případ: 3

|Pro správu Gender|Výstup|
|:-----|:-----:|
|Muž|**0**|
|Žena|**1**|
|Neznámé|**2**|
|Žena|1|
|Žena|1|
|Muž|0|
|Neznámé|2|
|Muž|0|
|Žena|1|

## <a name="examples-of-number-transformations-by-example"></a>Příklady transformace čísel podle příkladu

>[!NOTE] 
>Hodnoty v **tučné** představují příklady, které byly k dispozici k dokončení transformací v zobrazených datové sady.


### <a name="n1-rounding-to-nearest-10"></a>N1. Zaokrouhlení na nejbližší 10

Řadu příkladů, které byly zapotřebí pro tento případ: 1

|Vstup|Výstup|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. Zaokrouhlení dolů na nejbližší 10

Řadu příkladů, které byly zapotřebí pro tento případ: 2

|Vstup|Výstup|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. Zaokrouhlení na nejbližší 0,05

Řadu příkladů, které byly zapotřebí pro tento případ: 2

|Vstup|Výstup|
|-----:|-----:|
|-75.5812935|**-75.60**|
|-75.2646799|-75.25|
|-75.3519752|-75.35|
|-75.343513|**-75.35**|
|-75.6033497|-75.60|
|-75.283245|-75.30|

### <a name="n4-binning"></a>N4. Binning

Řadu příkladů, které byly zapotřebí pro tento případ: 1

|Vstup|Výstup|
|-----:|:-----:|
|20.16|**20-25**|
|14.32|10-15|
|5.44|5-10|
|3.84|0-5|
|3.73|0-5|
|7.36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. Škálování hodnotou 1000

Řadu příkladů, které byly zapotřebí pro tento případ: 1

|Vstup|Výstup|
|-----:|-----:|
|-243|**-243000**|
|-12.5|-12500|
|-2345.23292|-2345232.92|
|-1202.3433|-1202343.3|
|1202.3433|1202343.3|

### <a name="n6-padding"></a>N6. Odsazení

Řadu příkladů, které byly zapotřebí pro tento případ: 1

|Kód|Výstup|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>Příklady transformace data podle příkladu

>[!NOTE] 
>Hodnoty v **tučné** představují příklady, které byly k dispozici k dokončení transformací v zobrazených datové sady.


### <a name="d1-extracting-date-parts"></a>D1. Extrahování částí data

Tyto části datum byly extrahovány pomocí jiný – například transformace na stejné datové sady. Tučné řetězce představují příklady, které byly zadány v jejich příslušných transformaci.

|DateTime|Den v týdnu|Datum|Měsíc|Rok|Hodina|Minuta|1 sekunda|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|Do 31. ledna 2031 05:54:18|**Pracovní dny**|**31**|**Jan**|**2031**|**5**|**54**|**18**|
|17. ledna 1990 13:32:01|St|17|Led|1990|13|32|01|
|. 2034 14 února 05:36:07|Út|14|Úno|2034|5|36|07|
|14. března 2002 13:16:16|Čt|14|Bře|2002|13|16|16|
|21. ledna 1985 05:44:43|Po|21|Led|1985|5|44|**43**|
|16. srpna 1985 01:11:56|Pá|16|Srp|1985|1|11|56|
|Roku 2033 20 Dec 18:36:29|Út|20|Pro|2033|18|36|29|
|16. července 1984 10:21:59|Po|16|Čec|1984|10|21|59|
|13. ledna 2038 10:59:36|St|13|Led|2038|10|59|36|
|14. srpna 1982 15:13:54|So|14|Srp|1982|15|13|54|
|22. listopadu 2030 08:18:08|Pá|22|Lis|2030|8|18|08|
|21-Oct-1997 08:42:58|Út|21|Říj|1997|8|42|58|
|28. listopadu 2006 14:19:15|Út|28|Lis|2006|14|19|15|
|29. dubna 2031 04:59:45.|Út|29|Dub|2031|4|59|45|
|. 2032 29 Jan 02:38:36|Čt|29|Led|2032|2|38|36|
|11. května 2028 15:31:52|Čt|11|května|2028|15|31|52|
|15. července 1977 12:45:39|Pá|15|Čec|1977|12|45|39|
|27. ledna 2029 05:55:41|So|27|Led|2029|5|55|41|
|03. března 2024 10:17:49|Ne|3|Bře|2024|10|17|49|
|14. dubna 2010 00:23:13|St|14|Dub|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. Formátování kalendářních dat

Jste dokončili tyto formattings data pomocí různých podle příkladu transformace na stejné datové sady. Tučné řetězce představují příklady, které byly zadány v jejich příslušných transformaci.

|DateTime|Format1|Format2|Format3|Format4|Format5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|Do 31. ledna 2031 05:54:18|**1/31/2031**|**Pátek 31. ledna 2031**|**01312031 5:54**|**DO 31/1/2031 5:54:00**|**Q1 2031**|
|17. ledna 1990 13:32:01|1/17/1990|Středa 17. ledna 1990|01171990 13:32|17/1/1990 1:32 PM|Q1 1990|
|. 2034 14 února 05:36:07|2/14/2034|Úterý 14. února 2034|02142034 5:36|14/2 /. 2034 5:36:00|1. 2034
|14. března 2002 13:16:16|3/14/2002|Čtvrtek 14. března 2002|03142002 13:16|3/14/2002 1:16 HODIN|Q1 2002
|21. ledna 1985 05:44:43|1/21/1985|Pondělí 21. ledna 1985|01211985 5:44|21/1/1985 5:44:00|Q1 1985
|16. srpna 1985 01:11:56|8/16/1985|Pátek 16. srpna 1985|08161985 1:11|8/16/1985 1:11:00|3. ČTVRTLETÍ 1985
|Roku 2033 20 Dec 18:36:29|12/20/2033|Úterý, 20 dne, roku 2033|12202033 18:36|20/12/ROKU 2033 18:36:00|ROKU 2033. AŽ 4.
|16. července 1984 10:21:59|7/16/1984|Pondělí 16 dne 1984|07161984 10:21|16/7/1984 10:21:00|3. ČTVRTLETÍ 1984
|13. ledna 2038 10:59:36|1/13/2038|Ve středu 13. ledna 2038|01132038 10:59|13/1/2038 10:59:00|Q1 2038
|14. srpna 1982 15:13:54|8/14/1982|Sobota 14. srpna 1982|08141982 15:13|8/14/1982 15:13:00|3. ČTVRTLETÍ 1982
|22. listopadu 2030 08:18:08|11/22/2030|Pátek 22. listopadu 2030|11222030 8:18|22/11/2030 8:18:00|4 2030
|21-Oct-1997 08:42:58|10/21/1997|Úterý 21. října 1997|10211997 8:42|21/10/1997 8:42:00|4. 1997
|28. listopadu 2006 14:19:15|11/28/2006|Úterý 28. listopadu 2006|11282006 14:19|28/11/2006 14:19:00|2006. AŽ 4.
|29. dubna 2031 04:59:45.|4/29/2031|Úterý 29. dubna 2031|04292031 4:59|29/4/2031 4:59:00|2. ČTVRTLETÍ 2031
|. 2032 29 Jan 02:38:36|1/29/2032|Čtvrtek 29. ledna 2032|01292032 2:38|29/1 /. 2032 2:38:00|1. 2032
|11. května 2028 15:31:52|5/11/2028|Čtvrtek 11 května 2028|05112028 15:31|11/5/2028 ČASU 15:31|2028 2. ČTVRTLETÍ
|15. července 1977 12:45:39|7/15/1977|Pátek 15. července 1977|07151977 12:45|15/7/1977 12:45 PM|3. ČTVRTLETÍ 1977
|27. ledna 2029 05:55:41|1/27/2029|Sobota 27. ledna 2029|01272029 5:55|27/1/2029 5:55:00|Q1 2029
|03. března 2024 10:17:49|3/3/2024|Neděle 3. března 2024|03032024 10:17|3/3/2024 10:17:00|Q1 2024
|14. dubna 2010 00:23:13|4/14/2010|Středa 14 Duben 2010|04142010 0:23|14/4/2010 00:23:00|2. ČTVRTLETÍ 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. Mapování čas pro období

Tato data a času na období mapování se provádí pomocí různých podle příkladu transformace na stejné datové sady. Tučné řetězce představují příklady, které byly zadány v jejich příslušných transformaci.

|DateTime|Period(seconds)|Period(minutes)|Období (dvě hodiny)|Období (30 minut)|
|-----:|-----:|-----:|-----:|-----:|
|Do 31. ledna 2031 05:54:18|**0-20**|**45-60**|**5AM-7AM**|**5:30-6:00**|
|17. ledna 1990 13:32:01|**0-20**|30-45|1PM-3PM|13:30-14:00|
|. 2034 14 února 05:36:07|0-20|30-45|5: 00 - 7: 00|5:30-6:00|
|14. března 2002 13:16:16|0-20|15-30|1PM-3PM|13:00-13:30|
|21. ledna 1985 05:44:43|40-60|30-45|5: 00 - 7: 00|5:30-6:00|
|16. srpna 1985 01:11:56|40-60|0-15|1: 00 - 3: 00|1:00-1:30|
|Roku 2033 20 Dec 18:36:29|20-40|30-45|5PM-7PM|18:30-19:00|
|16. července 1984 10:21:59|40-60|15-30|9: 00 - 11 AM|10:00-10:30|
|13. ledna 2038 10:59:36|20-40|45-60|9: 00 - 11 AM|10:30-11:00|
|14. srpna 1982 15:13:54|40-60|0-15|3PM-5PM|15:00-15:30|
|22. listopadu 2030 08:18:08|0-20|15-30|7: 00 - 9: 00|8:00-8:30|
|21-Oct-1997 08:42:58|40-60|30-45|7: 00 - 9: 00|8:30-9:00|
|28. listopadu 2006 14:19:15|0-20|15-30|1PM-3PM|14:00-14:30|
|29. dubna 2031 04:59:45.|40-60|45-60|3: 00 - 5: 00|4:30-5:00|
|. 2032 29 Jan 02:38:36|20-40|30-45|1: 00 - 3: 00|2:30-3:00|
|11. května 2028 15:31:52|40-60|30-45|3PM-5PM|15:30-16:00|
|15. července 1977 12:45:39|20-40|45-60|11 – 1 HOD|12:30-13:00|
|27. ledna 2029 05:55:41|40-60|45-60|5: 00 - 7: 00|5:30-6:00|
|03. března 2024 10:17:49|40-60|15-30|9: 00 - 11 AM|10:00-10:30|
|14. dubna 2010 00:23:13|0-20|15-30|23: 00 - 1: 00|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>Příklady kompozitní transformace podle příkladu

|tripduration|StartTime|Start station id|Start station latitude|Start station longitude|usertype|Sloupec|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42.3625|-71.08822|Odběratel|**Odběratel vyberou kolo z stanice 107, lat/dlouho (42.363,-71.088), 08 ledna 2016 v asi 16: 00. Doba trvání jízdy je 61 minut**|
|61|2016-01-17 09:28:10|74|42.373268|-71.118579|Zákazník|Zákazník vybere kolo ze stanice 74, lat/dlouho (42.373,-71.119), 17. ledna 2016 v přibližně 9: 00. Doba trvání jízdy je 61 minut|
|62|2016-01-25 08:10:26|176|42.386748020450561|-71.119018793106079|Odběratel|Odběratel vyberou kolo z stanice 176, lat/dlouho (42.387,-71.119), 25 ledna 2016 v přibližně 8: 00. Doba trvání jízdy je 62 minut|
|63|2016-01-08 10:10:29|107|42.3625|-71.08822|Odběratel|Odběratel vyberou kolo z stanice 107, lat/dlouho (42.363,-71.088), 08 ledna 2016 v přibližně 10: 00. Doba trvání jízdy je 63 minut|
|64|2016-01-15 19:42:08|68|42.36507|-71.1031|Odběratel|Odběratel vyberou kolo z stanice 68 lat/dlouho (42.365,-71.103), 15 ledna 2016 v přibližně 19: 00. Doba trvání jízdy je 64 minut|
|64|2016-01-22 18:16:13|115|42.387995|-71.119084|Odběratel|Odběratel vyberou kolo z stanice 115, lat/dlouho (42.388,-71.119), 22. ledna 2016 na přibližně 18: 00. Doba trvání jízdy je 64 minut|
|68|2016-01-18 09:51:52|178|42.359573201090441|-71.101294755935669|Odběratel|Odběratel vyberou kolo z stanice 178, lat/dlouho (42.360,-71.101), na 18. ledna 2016 v přibližně 9: 00. Doba trvání jízdy je 68 minut|
|69|2016-01-14 08:57:55|176|42.386748020450561|-71.119018793106079|Odběratel|Odběratel vyberou kolo z stanice 176, lat/dlouho (42.387,-71.119), 14. ledna 2016 v přibližně 8: 00. Doba trvání jízdy je 69 minut|
|69|2016-01-13 22:12:55|141|42.363560158429884|-71.08216792345047|Odběratel|Odběratel vyberou kolo z stanice 141, lat/dlouho (42.364,-71.082), na 13. ledna 2016 ve asi 22: 00. Doba trvání jízdy je 69 minut|
|69|2016-01-15 08:13:09|176|42.386748020450561|-71.119018793106079|Odběratel|Odběratel vyberou kolo z stanice 176, lat/dlouho (42.387,-71.119), 15 ledna 2016 v přibližně 8: 00. Doba trvání jízdy je 69 minut|


## <a name="technical-notes"></a>Technické poznámky

### <a name="conditional-transformations"></a>Podmíněné transformace
V některých případech nelze nalézt jeden transformace, která by splnila dané příklady. V takových případech pokusí odvodit sloupec podle příkladu transformace Seskupit podle některých vzor vstupy a další samostatné transformace pro každou skupinu. To označujeme **podmíněné transformace**. **Podmíněné transformace** dojde k pokusu o pouze pro transformace s jedním sloupcem vstupu. 

### <a name="reference"></a>Referenční informace
Další informace o řetězec transformaci podle příkladu technologie najdete v [této publikace](https://www.microsoft.com/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/).
