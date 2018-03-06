---
title: "Rozdělit sloupec podle příkladu transformace pomocí Azure Machine Learning Workbench"
description: "V referenčním dokumentu pro transformaci 'rozdělit sloupec podle příkladu."
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
ms.openlocfilehash: 497c1725fc4554792add11c0ec069d1628a89fbd
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="split-column-by-example-transformation"></a>Rozdělit sloupec podle příkladu transformace
Tato transformace predictively rozdělí obsah sloupce na smysluplný hranicích bez zásahu uživatele. Algoritmus rozdělení vybere hranice po analýze obsahu sloupce. Může být definovaná tyto hranice
* Opravené oddělovač,
* Víc libovolný oddělovače zobrazovaných na konkrétní kontexty, nebo,
* Vzorky dat nebo určitých typů entit

Uživatele můžete upravovat také rozdělení chování v rozšířeném režimu, kde můžete určit oddělovače nebo podle nabízí příklady požadované rozdělení.

Teoreticky rozdělení, lze operace také provádět v Workbench pomocí řady *odvozena sloupec podle příkladu* transformace. Ale pokud je několik sloupců, odvozování každý z nich jednotlivě i pomocí přístup podle příkladu může být časově velmi náročná. Prediktivní rozdělení umožňuje snadno rozdělení bez uživatele museli příklady pro jednotlivé sloupce. 

## <a name="how-to-perform-this-transformation"></a>Jak provádět Tato transformace

1. Vyberte sloupec, který chcete rozdělit. 
2. Vyberte **rozdělit sloupec podle příkladu** z **transformuje** nabídky. Nebo, klikněte pravým tlačítkem na záhlaví vybraného sloupce a vyberte **rozdělit sloupec podle příkladu** v místní nabídce. Otevře se Editor transformovat a jsou přidány sloupce vedle ve vybraném sloupci. V tomto okamžiku nástroje Workbench analyzuje vstupní sloupec, určuje rozdělení hranice a syntetizuje program rozdělit sloupec podle zobrazení v mřížce. Syntetizovaná program je spustit pro všechny řádky ve sloupci. Oddělovače, pokud existuje, jsou vyloučeny z konečný výsledek.
3. Kliknutím na **pokročilého režimu** lepší kontrolu nad transformace rozdělení. 
4. Zkontrolujte výstup a klikněte na tlačítko **OK** tak, aby přijímal pro transformaci. 

Transformovat cílem je vytvořit stejný počet výsledné sloupce pro všechny řádky. Pokud žádné řádek nelze rozdělit na určené hranice, vyvolá *null* pro všechny sloupce ve výchozím nastavení. Toto chování lze změnit v **rozšířeném režimu**.

### <a name="transform-editor-advanced-mode"></a>Transformace editor: pokročilého režimu
**Rozšířený režim** poskytuje bohatší možnosti pro rozdělení sloupce. 

Výběr **zachovat sloupce oddělovač** zahrnuje oddělovače v konečný výsledek. Ve výchozím nastavení jsou vyloučeny oddělovače.

Určení **oddělovače** přepsání logiku výběr automatické oddělovač. Více oddělovačů, jeden v každém řádku lze zadat jako **oddělovače**. Všechny tyto znaky se používají jako oddělovače k rozdělení sloupce.

V některých případech rozdělení hodnoty na určené hranice vytváří jiný počet sloupců, než většinu ostatních. V takových případech **vyplnění směr** se používá k určení pořadí, ve kterém by mělo být zadáno sloupce.

Kliknutím na **zobrazit navrhovaných příkladů** zobrazí reprezentativní řádky, pro které uživatele by měl poskytovat příklad rozdělení. Uživatel může klepnout na **až** šipku vpravo navrhované řádku ke zvýšení úrovně řádek jako příklad. 

Uživatel může **odstranit sloupce** nebo **nové sloupce k vložení** kliknutím pravým tlačítkem na záhlaví **příklady tabulky**.

Uživatele můžete zkopírovat a vložit hodnoty z jedné buňky do jiného aby uveďte příklad rozdělení.

Uživatele můžete přepínat mezi **základní režimu** a **rozšířeném režimu** kliknutím na odkazy v editoru transformace.

### <a name="transform-editor-send-feedback"></a>Transformace editor: váš názor

Kliknutím na **odeslat názor** odkaz otevře **zpětné vazby** poskytl dialogové okno s pole komentáře naplněna výběry parametrů a příklady uživatele. Uživatel by měl zkontrolovat obsah pole komentáře a obsahují další podrobnosti, abychom tento problém pochopit. Pokud uživatel nechce sdílení dat s Microsoft, uživatel by měl odstranit data předem vyplněnou příklad před kliknutím na tlačítko **odeslat zpětnou vazbu** tlačítko. 


### <a name="editing-an-existing-transformation"></a>Úprava existující transformace

Uživatele můžete upravit stávající **rozdělit sloupec podle příkladu** transformace výběrem **upravit** možnost kroku transformace. Kliknutím na **upravit** otevře v editoru transformace **rozšířeném režimu**, a jsou zobrazeny všechny příklady, které byly poskytnuty během vytváření transformace.

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>Příklady rozdělení na pevnou, jeden znak oddělovač

Je běžné pro datová pole oddělit jeden pevný oddělovač například čárkami ve formátu CSV. Transformace rozdělení se pokusí automaticky odvození tyto oddělovače. Například v následujícím scénáři je automaticky odvodí, že "." jako oddělovač.

### <a name="splitting-ip-addresses"></a>Rozdělení IP adresy

Hodnoty z prvního sloupce jsou predictively rozdělit na čtyři sloupce.

|IP adresa|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>Příklady rozdělení na více oddělovače v rámci konkrétní kontexty

Data uživatele mohou obsahovat mnoho různých oddělovače oddělení různých polí. Kromě toho jenom některé výskyty rozdělujících řetězec může být oddělovač, ale ne všechny. Například v případě, že následující sada oddělovače požadované je "-",","a":" k vytvoření požadované výstup. Ale ne všechny výskyty řetězce ":" musí být bod rozdělení, protože jsme nechcete rozdělí dobu, ale ponechat v jeden sloupec. Transformace rozdělení odvodí oddělovače v kontextu, ve kterých se vyskytují v vstupních dat, nikoli všech možných výskytu oddělovače. Transformovat má také informace o běžné typy dat, například data a časy.   

### <a name="splitting-store-opening-timings"></a>Rozdělení časování otevírání úložiště

Hodnoty v následujícím *časování* sloupec získat predictively rozdělit na devět sloupců v tabulce v něm.

|Časování|
|:-----|
|Pondělí – pátek: 7:00:00 – 18:00:00, sobota: 9:00:00 – 17:00:00, neděle: uzavřený|
|Pondělí – pátek: 9:00:00 – 18:00:00, sobota: 4:00:00 - 4:00 pm, neděle: uzavřený|
|Pondělí – pátek: 8:30:00 – 19:00:00, sobota: 3:00 am - 2:30 pm, neděle: uzavřený|
|Pondělí – pátek: 8:00:00 – 18:00:00, sobota: 2:00:00 - 3:00 pm, neděle: uzavřený|
|Pondělí – pátek: 4:00:00 – 19:00:00, sobota: 9:00:00 - 4:00 pm, neděle: uzavřený|
|Pondělí – pátek: 8:30 am - 4:30 pm, sobota: 9:00:00 – 17:00:00, neděle: uzavřený|
|Pondělí – pátek: 5:30:00 – 18:30:00, sobota: 5:00 - 4:00 hodin, neděle: uzavřený|
|Pondělí – pátek: 8:30:00 - 8:30 pm, sobota: 6:00:00 – 17:00:00, neděle: uzavřený|
|Pondělí – pátek: 8:00 am – 21:00:00, sobota: 9:00:00 - 8:00 pm, neděle: uzavřený|
|Pondělí – pátek: 10:00 am – 9:30 pm, sobota: 9:30:00 - 3:00 pm, neděle: uzavřený|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|Pondělí|Pátek|7:00:00|18:00:00|Sobota|9:00|17:00:00|Neděle|Uzavřeno|
|Pondělí|Pátek|9:00|18:00:00|Sobota|4:00:00|4:00 pm|Neděle|Uzavřeno|
|Pondělí|Pátek|8:30:00|19:00:00|Sobota|3:00:00|14:30:00|Neděle|Uzavřeno|
|Pondělí|Pátek|8:00:00|18:00:00|Sobota|2:00:00|3:00 pm|Neděle|Uzavřeno|
|Pondělí|Pátek|4:00:00|19:00:00|Sobota|9:00|4:00 pm|Neděle|Uzavřeno|
|Pondělí|Pátek|8:30:00|4:30 pm|Sobota|9:00|17:00:00|Neděle|Uzavřeno|
|Pondělí|Pátek|5:30:00|18:30:00|Sobota|5:00:00|4:00 pm|Neděle|Uzavřeno|
|Pondělí|Pátek|8:30:00|20:30:00|Sobota|6:00:00|17:00:00|Neděle|Uzavřeno|
|Pondělí|Pátek|8:00:00|21:00:00|Sobota|9:00|8:00 pm|Neděle|Uzavřeno|
|Pondělí|Pátek|10:00 am|9:30 pm|Sobota|9:30:00|3:00 pm|Neděle|Uzavřeno|

### <a name="splitting-iis-log"></a>Rozdělení protokolu služby IIS

Zde je další příklad více libovolný oddělovače. Tento příklad také obsahuje kontextové oddělovač "a", který nesmí být rozdělení uvnitř adresy URL nebo cesty k souborům. Je zdlouhavé k provedení této rozdělení pomocí mnoho *odvozena sloupec podle příkladu* transformací a pro každé pole, která poskytuje příklady. Pomocí rozdělení transformace jsme proveďte, prediktivní rozdělení bez nutnosti poskytnutí všechny příklady.

|logtext|
|:-----|
|192.128.138.20 – [16/Oct/2016 16:22:33-0200] "GET /images/picture.gif HTTP/1.1" 234 343 www.yahoo.com "http://www.example.com/" "Mozilla/4.0 (kompatibilní; MSIE 4)""-"|
|10.128.72.213 – [Oct/17/2016 12:43:12 +0300] "GET /news/stuff.html HTTP/1.1" 200 6233 www.aol.com "http://www.sample.com/" "Mozilla/5.0 (MSIE)" "-"|
|192.165.71.165 – [12. listopadu/2016 14:22:44 -0500] "GET /sample.ico HTTP/1.1" 342 7342 www.facebook.com "-" "Mozilla/5.0 (Windows; U; Systém Windows NT 5.1; Rv:1.7.3) ""-"|
|10.166.64.165 – [23. listopadu/2016 01:52:45 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.167.1.193 – [16/ledna/2017 22:34:56 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.147.76.193 – [ledna/28/2017 26:36:16 +0800] "GET /search.php HTTP/1.1" 400 1777 www.bing.com "-" "Mozilla/4.0 (kompatibilní; MSIE 6.0; Systém Windows NT 5.1)""-"|
|192.166.64.165 – [23/března/2017 01:55:25 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|11.167.1.193 – [16/dubna/2017 11:34:36 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|

Získá rozdělit na:

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|Oct 16. 2016|16:22:33|-0200|GET|Images/Picture.GIF|HTTP|1.1|234|343|www.yahoo.com|http://www.example.com/|Mozilla|4.0|kompatibilní; MSIE 4|
|10.128.72.213|Oct/17/2016|12:43:12|+0300|GET|news/stuff.html|HTTP|1.1|200|6233|www.aol.com|http://www.sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|12. listopadu/2016|14:22:44|-0500|GET|sample.ico|HTTP|1.1|342|7342|www.facebook.com|-|Mozilla|5.0|Windows; U; Windows NT 5.1; rv:1.7.3|
|10.166.64.165|Listopadu 23. 2016|01:52:45|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|192.167.1.193|Jan/16/2017|22:34:56|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|
|192.147.76.193|Jan/28/2017|26:36:16|+0800|GET|search.php|HTTP|1.1|400|1777|www.bing.com|-|Mozilla|4.0|kompatibilní; MSIE 6.0; Systém Windows NT 5.1|
|192.166.64.165|23/března/2017|01:55:25|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|11.167.1.193|16/dubna/2017|11:34:36|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>Příklady rozdělení bez oddělovačů
V některých případech neexistují žádné skutečné oddělovače a datová pole můžou vznikat souvisle vedle sebe. V takovém případě transformace rozdělení automaticky rozpozná vzorů v datech odvodit pravděpodobně body rozdělení. Například v následujícím scénáři chceme velikost nezávislá na typ Měna a rozdělení automaticky odvodí hranice mezi daty jiné než číselné a číselné jako bod rozdělení.

### <a name="splitting-amount-with-currency-symbol"></a>Rozdělení velikost pomocí symbolu měny

|Částka|Amount_1|Amount_2|
|:-----|:-----|:-----|
|\$14|$|14|
|£9|£|9|
|\$34|$|34|
|€ 18|€ |18|
|\$42|$|42|
|\$7|$|7|
|£42|£|42|
|\$16|$|16|
|€ 16|€ |16|
|\$15|$|15|
|\$16|$|16|
|€ 64|€ |64|

V následujícím příkladu jsme chtěli oddělte hodnoty váhy z měrné jednotky. Znovu odvození rozdělení automaticky rozpozná smysluplný hranic a upřednostní ho přes jiné možné oddělovače, jako "." znak. 

### <a name="splitting-weights-with-units"></a>Rozdělení váhu u jednotek

|Váha|Weight_1|Weight_2|
|:-----|:-----|:-----|
|2.27KG|2.27|KG|
|1L|1|L|
|2.5 KG|2.5|KG|
|2KG|2|KG|
|1.7KGA|1.7|KGA|
|3KG|3|KG|
|2KG|2|KG|
|125G|125|G|
|500G|500|G|
|1.5KGA|1.5|KGA|

## <a name="technical-notes"></a>Technické poznámky

Funkce transformace rozdělení je založena na **prediktivní souhrnnou Program** techniku. V této technice jsou zjistili programy transformaci dat automaticky v závislosti na vstupní data. Programy jsou syntetizován v jazyce specifické pro doménu. DSL je založena na oddělovače a polí, ke kterým došlo na konkrétní kontexty regulární výraz. Další informace o této technologii najdete v [poslední publikace v tomto tématu](https://www.microsoft.com/en-us/research/publication/automated-data-extraction-using-predictive-program-synthesis/). 
