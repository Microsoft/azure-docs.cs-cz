---
title: Rozdělit sloupec podle příkladu transformaci pomocí Azure Machine Learning Workbench
description: Referenční dokument pro transformaci 'rozdělit sloupec podle příkladu.
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
ROBOTS: NOINDEX
ms.openlocfilehash: 3edf49484e5bc05a297b8d8969632fb902aa1714
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953731"
---
# <a name="split-column-by-example-transformation"></a>Rozdělit sloupec podle příkladu transformace

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Tato transformace predictively rozdělí obsah sloupce na smysluplný hranice bez zásahu uživatele. Algoritmus rozdělit vybere hranice po analýze obsahu sloupce. Může být definované tyto hranice
* Oprava oddělovač
* Více, libovolného oddělovače povolí, zejména kontextech, nebo,
* Data, vzory nebo určité typy entit

Uživatelé můžou také řídit rozdělení chování v rozšířeném režimu, kde můžete určit oddělovače nebo poskytnout příklady požadované rozdělení.

Teoreticky vzato rozdělení operace lze provést také v aplikaci Workbench pomocí řady *odvodit sloupec podle příkladu* transformace. Ale pokud existuje několik sloupců, každý z nich jednotlivě i použití přístupu pomocí příkladu odvozování může být časově velmi náročné. Prediktivní rozdělení umožňuje snadné rozdělení bez nutnosti nechat příklady pro jednotlivé sloupce uživatele. 

## <a name="how-to-perform-this-transformation"></a>Jak provést tuto transformaci

1. Vyberte sloupec, který chcete rozdělit. 
2. Vyberte **rozdělit sloupec podle příkladu** z **transformuje** nabídky. Nebo klikněte pravým tlačítkem na záhlaví vybraného sloupce a vyberte **rozdělit sloupec podle příkladu** v místní nabídce. Otevře se Editor transformovat a jsou přidány sloupce vedle ve vybraném sloupci. V tomto okamžiku v aplikaci Workbench analyzuje vstupní sloupec, určuje rozdělení hranice a syntetizuje program na rozdělení sloupce pro zobrazení v mřížce. Syntetizovaný program za dokončený se provede na všech řádků ve sloupci. Oddělovače, pokud existují, jsou vyloučeny z konečný výsledek.
3. Můžete kliknout na **rozšířený režim** pro lepší kontrolu nad transformace rozdělení. 
4. Zkontrolujte výstup a klikněte na tlačítko **OK** transformaci přijměte. 

Transformací, která se má vytvořit stejný počet výsledných sloupců pro všechny řádky. Pokud libovolný řádek nelze rozdělit na určené hranice, vytvoří *null* pro všechny sloupce ve výchozím nastavení. Toto chování lze změnit v **rozšířený režim**.

### <a name="transform-editor-advanced-mode"></a>Transformace editor: rozšířeném režimu
**Rozšířený režim** poskytuje pohodlnější a pestřejší prostředí pro rozdělení sloupce. 

Výběr **zachovat oddělovač sloupců** obsahuje oddělovače v konečný výsledek. Ve výchozím nastavení jsou vyloučeny oddělovače.

Určení **oddělovače** přepíše logiku výběru automatické oddělovač. Více oddělovačů, jeden v každém řádku, můžete zadat hodnotu **oddělovače**. Všechny tyto znaky se používají jako oddělovače k rozdělení sloupce.

V některých případech rozdělení hodnotu na určené hranice vytváří jiný počet sloupců, než většinu ostatních. V takových případech **vyplnit směr** se používá k určení pořadí, ve kterém by se mělo vyplnit sloupce.

Kliknutím na **zobrazení navrhovaných příkladů** zobrazí reprezentativní řádky, pro které uživatel by měl uveďte příklad rozdělení. Uživatel může klepnout na **nahoru** šipku napravo od navrhovaný řádek ke zvýšení úrovně řádků jako příklad. 

Uživatel může **odstranit sloupec** nebo **vložit nové sloupce** kliknutím pravým tlačítkem na záhlaví **příklady tabulky**.

Uživatel může kopírovat a vložit hodnoty z jedné buňky do jiného negace příklad rozdělení.

Uživatele můžete přepínat mezi **základním režimu** a **rozšířený režim** kliknutím na odkazy v editoru transformace.

### <a name="transform-editor-send-feedback"></a>Transformace editor: váš názor

Kliknutím na **poslat svůj názor** odkaz otevře **zpětnou vazbu** dialogové okno s pole komentáře naplněna výběry parametrů a příklady uživatel poskytl. Uživatel by měl zkontrolovat obsah pole komentáře a poskytují další podrobnosti o nám pomůže zjistit potíže. Pokud uživatel nechce sdílet data s Microsoftem, uživatel by měl odstranit předem vyplněná ukázková data před kliknutím na tlačítko **odeslat zpětnou vazbu** tlačítko. 


### <a name="editing-an-existing-transformation"></a>Úprava existující transformace

Uživatel může upravovat existující **rozdělit sloupec podle příkladu** transformovat tak, že vyberete **upravit** kroku transformace. Kliknutím na **upravit** otevře Editor transformace v **rozšířený režim**, a jsou uvedeny všechny příklady, které byly poskytnuty při vytváření transformace.

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>Příklady rozdělení na pevné, jedním znakem oddělovače

Je běžné, že datová pole pro oddělené bránou jeden dlouhodobý oddělovač například čárkami ve formátu CSV. Rozdělení transformací, která se pokusí odvodit tyto oddělovače automaticky. Například v následujícím scénáři je automaticky odvodí, "." jako oddělovač.

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

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>Příklady rozdělení na více oddělovačů v rámci konkrétní kontexty

Data uživatele mohou obsahovat mnoho různých oddělovače oddělení různých polí. Pouze některé výskytů oddělovače řetězce může být navíc oddělovač, ale ne pro všechny. V následujícím případě sada oddělovačů vyžaduje je například "-",","a":" požadovaný výstup. Ale ne každý výskyt ":" by měl být bod rozdělení, protože není chceme rozdělit času, ale zachovat v jednom sloupci. Rozdělení transformací, která odvodí oddělovače v kontextech, ve kterých k nim dojde v vstupní data a ne všechny možné výskyt oddělovače. Transformace je také přehled o běžné typy dat, jako je například data a časy.   

### <a name="splitting-store-opening-timings"></a>Rozdělení časování otevírání úložiště

Hodnoty v následujícím *časování* sloupec získat predictively rozdělit na devět sloupce v tabulce pod ním.

|Časování|
|:-----|
|Pondělí - pátek: 7:00:00 – 18:00 hodin, neděle: 9:00:00 – 17:00:00, neděle: uzavřeno|
|Pondělí - pátek: 9:00:00 – 18:00 hodin, neděle: 4:00 – 4:00 hodin, neděle: uzavřeno|
|Pondělí - pátek: 8:30:00 – 19:00:00, sobota: 3:00:00 – 2:30 odp., neděle: uzavřeno|
|Pondělí - pátek: 8:00:00 – 18:00 hodin, neděle: 2:00 - 3:00 hodin, neděle: uzavřeno|
|Pondělí - pátek: 4:00:00 – 19:00 hodin, neděle: 9:00:00 – 4:00 pm, neděle: uzavřeno|
|Pondělí - pátek: 8:30:00 – 4:30 odp., neděle: 9:00:00 – 17:00:00, neděle: uzavřeno|
|Pondělí - pátek: 5:30:00 – 18:30:00, sobota: 5:00:00 – 4:00 pm, neděle: uzavřeno|
|Pondělí - pátek: 8:30:00 - 8:30 odp., neděle: 6:00:00 – 17:00:00, neděle: uzavřeno|
|Pondělí - pátek: 8:00:00 – 21:00:00, neděle: 9:00:00 – 20:00:00, neděle: uzavřeno|
|Pondělí - pátek: 10:00:00 – 21:30:00, sobota: 9:30:00 - 3:00 pm, neděle: uzavřeno|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|Pondělí|Pátek|7:00:00|18:00:00|Sobota|9:00|17:00:00|Neděle|Zavřeno|
|Pondělí|Pátek|9:00|18:00:00|Sobota|4:00:00|16:00:00|Neděle|Zavřeno|
|Pondělí|Pátek|8:30:00|19:00:00|Sobota|3:00:00|2:30 odp.|Neděle|Zavřeno|
|Pondělí|Pátek|8:00:00|18:00:00|Sobota|2:00:00|3:00 pm|Neděle|Zavřeno|
|Pondělí|Pátek|4:00:00|19:00:00|Sobota|9:00|16:00:00|Neděle|Zavřeno|
|Pondělí|Pátek|8:30:00|4:30 odp.|Sobota|9:00|17:00:00|Neděle|Zavřeno|
|Pondělí|Pátek|5:30:00|18:30:00|Sobota|5:00:00|16:00:00|Neděle|Zavřeno|
|Pondělí|Pátek|8:30:00|8:30 odp.|Sobota|6:00:00|17:00:00|Neděle|Zavřeno|
|Pondělí|Pátek|8:00:00|21:00:00|Sobota|9:00|20:00:00|Neděle|Zavřeno|
|Pondělí|Pátek|10:00:00|9:30 odp.|Sobota|9:30:00|3:00 pm|Neděle|Zavřeno|

### <a name="splitting-iis-log"></a>Rozdělení protokol IIS

Tady je další příklad libovolného více oddělovačů. Tento příklad také obsahuje kontextové oddělovač "/", které nesmí být rozdělit do adresy URL nebo cesty k souborům. Je únavné provádět důsledkem tohoto rozdělení pomocí řady *odvodit sloupec podle příkladu* transformace a poskytuje příklady pro každé pole. Pomocí modulu transform rozdělení jsme můžete provádět, prediktivní rozdělení bez ztráty veškeré ukázky.

|logtext|
|:-----|
|192.128.138.20--[16. října/2016 16:22:33-0200] "/images/picture.gif GET protokolu HTTP/1.1" 234 343 www.yahoo.com "http://www.example.com/" "Mozilla/4.0 (kompatibilní; MSIE 4)""-"|
|10.128.72.213--[17. října/2016 12:43:12 +0300] "/news/stuff.html GET protokolu HTTP/1.1" 200 6233 www.aol.com "http://www.sample.com/" "Mozilla/5.0 (MSIE)" "-"|
|192.165.71.165--[12. listopadu/2016 14:22:44 -0500] "/sample.ico GET protokolu HTTP/1.1" 342 7342 www.facebook.com "-" "Mozilla/5.0 (Windows; U; Windows NT 5.1; Rv:1.7.3) ""-"|
|10.166.64.165--[23. listopadu/2016 01:52:45 -0800] "/style.css GET protokolu HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.167.1.193--[16. ledna/2017 22:34:56 + 0200] "/js/ads.js GET protokolu HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.147.76.193--[28. ledna/2017 26:36:16 +0800] "GET /search.php HTTP/1.1" 400 1777 www.bing.com "-" "Mozilla/4.0 (kompatibilní; MSIE 6.0; Windows NT 5.1)""-"|
|192.166.64.165--[23. března/2017 01:55:25 -0800] "/style.css GET protokolu HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|11.167.1.193--[16. dubna/2017 11:34:36 + 0200] "/js/ads.js GET protokolu HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|

Získá rozdělit:

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|16. října/2016|16:22:33|-0200|GET|Images/Picture.GIF|HTTP|1.1|234|343|www.yahoo.com|http://www.example.com/|Mozilla|4.0|kompatibilní; MSIE 4|
|10.128.72.213|17. října/2016|12:43:12|+0300|GET|news/stuff.html|HTTP|1.1|200|6233|www.aol.com|http://www.sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|12. listopadu/2016|14:22:44|-0500|GET|sample.ico|HTTP|1.1|342|7342|www.facebook.com|-|Mozilla|5.0|Windows; U; Windows NT 5.1; Rv:1.7.3|
|10.166.64.165|23. listopadu/2016|01:52:45|-0800|GET|Style.CSS|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|192.167.1.193|16. ledna/2017|22:34:56|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|
|192.147.76.193|28. ledna/2017|26:36:16|+0800|GET|search.php|HTTP|1.1|400|1777|www.bing.com|-|Mozilla|4.0|kompatibilní; MSIE 6.0; Windows NT 5.1|
|192.166.64.165|23. března/2017|01:55:25|-0800|GET|Style.CSS|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|11.167.1.193|16. dubna/2017|11:34:36|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>Příklady rozdělení bez oddělovačů
V některých případech neexistují žádné skutečné oddělovače a datová pole může dojít k souvisle vedle sebe. V takovém případě rozdělení transformace automaticky rozpozná vzory v datech k odvození rozdělí pravděpodobně. Například v následujícím scénáři chceme oddělit od typ Měna. velikost a rozdělení automaticky odvodí hranici mezi jiné než číselné a číselné dat jako bod rozdělení.

### <a name="splitting-amount-with-currency-symbol"></a>Rozdělení částka se symbolem měny

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

V následujícím příkladu rádi bychom oddělit hodnot váhu z měrné jednotky. Znovu rozdělit odvození automaticky rozpozná smysluplné hranice a upřednostňuje se přes jiné možné oddělovače, jako "." znaku. 

### <a name="splitting-weights-with-units"></a>Rozdělení váhy s jednotkami

|Hmotnost|Weight_1|Weight_2|
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
|1.5KGA|1,5|KGA|

## <a name="technical-notes"></a>Technické poznámky

Funkce Split transformace je založena na **prediktivní syntéza programů** technika. Při použití této techniky jsou zjistili programů pro transformaci dat automaticky v závislosti na vstupní data. Programy jsou syntetizovat do jazyka specifického pro doménu. DSL je na základě oddělovače a pole, ke kterým dochází především kontexty regulární výraz. Další informace o této technologii najdete v [poslední publikace k tomuto tématu](https://www.microsoft.com/research/publication/automated-data-extraction-using-predictive-program-synthesis/). 
