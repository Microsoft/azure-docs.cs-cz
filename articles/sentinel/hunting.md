---
title: Možnosti lovu v Azure Sentinel Preview | Microsoft Docs
description: Tento článek popisuje, jak používat možnosti lovu do Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 5e6ad3c0b415722349dc584434add1031b7c3cb1
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780463"
---
# <a name="hunt-for-threats-with-in-azure-sentinel-preview"></a>Prohledat hrozby ve službě Azure Sentinel Preview

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud jste zkoušející, který chce být aktivní pro hledání bezpečnostních hrozeb, výkonné nástroje pro vyhledávání a dotazování v souvislosti s technologií Azure pro zajištění bezpečnostních hrozeb v rámci zdrojů dat vaší organizace. Ale vaše systémy a bezpečnostní zařízení generují Mountains dat, která se můžou obtížně analyzovat a filtrovat na smysluplné události. Aby se analytikům zabezpečení dokázali aktivně podívat na nové anomálie, které nezjistily vaše aplikace zabezpečení, v rámci integrovaných dotazů Azure Sentinele se podíváte na tyto otázky, které vám pomůžou najít problémy v datech, která už máte ve vaší síti. 

Například jeden integrovaný dotaz poskytuje data o většině neobvyklých procesů spuštěných v infrastruktuře – nechcete, aby výstraha pokaždé, když je spuštěná, mohla být zcela innocentá, ale můžete se podívat na dotaz, jestli je th. ere je něco neobvyklého. 



S ohledem na dodržování ověřovacích funkcí Azure můžete využít výhod následujících možností:

- Integrované dotazy: Úvodní stránka vám poskytne předem načtené příklady dotazů, které vám pomohou začít a seznámit s tabulkami a dotazovacím jazykem. Tyto integrované lovecké dotazy jsou vyvíjené výzkumnými pracovníky Microsoftu, a to průběžně, přidáváním nových dotazů a vyladěním existujících dotazů, které vám poskytnou vstupní bod, který bude hledat nové detekce a zjistit, kde začít v lovu začátek nových útoků. 

- Výkonný dotazovací jazyk pomocí technologie IntelliSense: Postaven na základě dotazovacího jazyka, který vám poskytne flexibilitu, kterou je třeba vzít v úvahu pro další úroveň.

- Vytvořte si vlastní záložky: Během loveckého procesu se můžete setkat se všemi shodami nebo nálezy, řídicími panely nebo aktivitami, které vypadají neobvyklé nebo podezřelé. Aby bylo možné označit tyto položky, abyste se do nich mohli vrátit v budoucnu, použijte funkci záložky. Záložky umožňují uložit položky pro pozdější použití k vytvoření incidentu pro účely šetření. Další informace o záložkách naleznete v tématu Use [záložky v lovu].

- K automatizaci šetření používejte poznámkové bloky: Poznámkové bloky se podobají podrobným playbookyům, které můžete sestavit, abyste provedli kroky vyšetřování a průzkumu.  Poznámkové bloky zapouzdřují všechny kroky pro lovecké v opakovaně použitelných PlayBook, které je možné sdílet s ostatními uživateli ve vaší organizaci. 
- Dotaz na uložená data: Data jsou v tabulkách k dispozici pro dotazování. Můžete například zadat dotaz na vytváření procesů, události DNS a mnoho dalších typů událostí.

- Odkazy na komunitu: Využijte sílu větší komunity k nalezení dalších dotazů a zdrojů dat.
 
## <a name="get-started-hunting"></a>Začít s loveckím

1. Na portálu Sentinel Azure klikněte na možnost **rozlovu**.
  ![Azure Sentinel začíná v lovu](media/tutorial-hunting/hunting-start.png)

2. Po otevření stránky pro **lov** se všechny lovecké dotazy zobrazí v jedné tabulce. V tabulce jsou uvedeny všechny dotazy napsané týmem analytiků zabezpečení společnosti Microsoft a všechny další dotazy, které jste vytvořili nebo upravili. Každý dotaz poskytuje popis toho, co je v nástroji k dispozici, a druh dat, na kterých se spouští. Tyto šablony jsou seskupené podle jejich různých taktiku – ikony na pravé straně typ hrozby, například počáteční přístup, trvalost a exfiltrace. Tyto šablony pro lovecké dotazy můžete filtrovat pomocí kteréhokoli z těchto polí. Libovolný dotaz můžete uložit do oblíbených položek. Když uložíte dotaz do oblíbených položek, dotaz se automaticky spustí při každém otevření stránky pro **lov** . Můžete vytvořit vlastní lovecký dotaz nebo klonovat a přizpůsobit existující šablonu pro lovecké dotazy. 
 
2. Kliknutím na **Spustit dotaz** na stránce s podrobnostmi o loveckém dotazu spusťte libovolný dotaz, aniž byste museli opustit stránku pro lov.  Počet shod se zobrazí v tabulce. Prohlédněte si seznam loveckých dotazů a jejich shody. Podívejte se, ke které fázi v dezaktivačním řetězci je přidružena shoda.

3. Proveďte rychlou kontrolu nad podkladovým dotazem v podokně podrobností dotazu nebo kliknutím na **Zobrazit výsledek dotazu** otevřete dotaz v Log Analytics. V dolní části Zkontrolujte shody dotazu.

4.  Kliknutím na řádek a vybráním **Přidat záložku** přidáte řádky, které se mají prozkoumat – můžete to provést pro cokoli, co vypadá podezřelě. 

5. Pak se vraťte na hlavní stránku s **loveckým** prostředím a kliknutím na kartu **záložky** zobrazíte všechny podezřelé aktivity. 

6. Vyberte záložku a potom kliknutím na tlačítko **prozkoumat** otevřete prostředí pro šetření. Záložky můžete filtrovat. Pokud například provádíte šetření kampaně, můžete vytvořit značku pro kampaň a potom filtrovat všechny záložky na základě kampaně.

1. Až zjistíte, který lovecký dotaz poskytuje v případě možných útokům vysoké hodnoty, můžete také vytvořit vlastní pravidla zjišťování založená na dotazu a obcházet tyto poznatky jako upozornění na vaše reakce na incidenty zabezpečení.

 

## <a name="query-language"></a>Dotazovací jazyk 

Lov v Azure Sentinel vychází z dotazovacího jazyka Azure Log Analytics. Další informace o dotazovacím jazyku a podporovaných operátorech naleznete v tématu [Referenční dokumentace jazyka dotazů](https://docs.loganalytics.io/docs/Language-Reference/).

## <a name="public-hunting-query-github-repository"></a>Úložiště GitHub pro veřejné lovecké dotazy

Podívejte se na [úložiště pro lovecké dotazy](https://github.com/Azure/Orion). Přispívat a používejte příklady dotazů, které jsou sdíleny našimi zákazníky.

 

## <a name="sample-query"></a>Vzorový dotaz

Typický dotaz začíná názvem tabulky následovaný řadou operátorů, které \|jsou odděleny.

V předchozím příkladu začněte s názvem tabulky SecurityEvent a podle potřeby přidejte do kanálu prvky.

1. Definujte časový filtr, který bude kontrolovat jenom záznamy za posledních sedm dnů.

2. Přidejte do dotazu filtr, aby se zobrazila pouze událost s ID 4688.

3. Do dotazu v příkazovém řádku přidejte filtr, který bude obsahovat jenom instance nástroje cscript. exe.

4. Prozkoumejte pouze sloupce, které vás zajímají, a omezte výsledky na 1000 a klikněte na **Spustit dotaz**.
5. Klikněte na zelený trojúhelník a spusťte dotaz. Můžete otestovat dotaz a spustit ho pro hledání neobvyklé chování.

## <a name="useful-operators"></a>Užitečné operátory

Dotazovací jazyk je výkonný a má mnoho dostupných operátorů. tady jsou uvedené některé užitečné operátory:

**WHERE** – filtruje tabulku na podmnožinu řádků, které odpovídají predikátu.

**Shrnutí** – vytvoří tabulku, která agreguje obsah vstupní tabulky.

**spojit** – sloučí řádky dvou tabulek a vytvoří novou tabulku tak, že bude odpovídat hodnotám zadaných sloupců z každé tabulky.

**Count** – vrátí počet záznamů ve vstupní sadě záznamů.

**Top** – vrátí prvních N záznamů seřazených podle zadaných sloupců.

**limit** – vrátí zadaný počet řádků.

**projekt** – vyberte sloupce, které chcete zahrnout, přejmenujte nebo přetáhněte a vložte nové počítané sloupce.

**Extended** – vytvořte počítané sloupce a přidejte je do sady výsledků.

**makeset** – vrátí dynamické pole (JSON) sady různých hodnot, které výraz expr převezme ve skupině.

**find** – najde řádky, které odpovídají predikátu napříč sadou tabulek.

## <a name="save-a-query"></a>Uložit dotaz

Dotaz můžete vytvořit nebo upravit a uložit jako vlastní dotaz nebo ho sdílet s uživateli, kteří jsou ve stejném tenantovi.

   ![Uložit dotaz](./media/tutorial-hunting/save-query.png)

Vytvořit nový lovecký dotaz:

1. Klikněte na **Nový dotaz** a vyberte **Uložit**.
2. Vyplňte všechna prázdná pole a vyberte **Uložit**.

   ![Nový dotaz](./media/tutorial-hunting/new-query.png)

Klonovat a upravit existující lovecký dotaz:

1. V tabulce, kterou chcete upravit, vyberte dotaz pro lov.
2. Na řádku dotazu, který chcete upravit, vyberte tři tečky (...) a vyberte **klonovat dotaz**.

   ![klonovat dotaz](./media/tutorial-hunting/clone-query.png)
 

3. Upravte dotaz a vyberte **vytvořit**.

   ![vlastní dotaz](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak spustit lovecké šetření s použitím funkce Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:


- [Použití poznámkových bloků ke spouštění automatizovaných loveckých kampaní](notebooks.md)
- [Použití záložek k ukládání zajímavých informací při lovu](bookmarks.md)
