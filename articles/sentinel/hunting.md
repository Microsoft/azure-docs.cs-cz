---
title: Možnosti lovu v Azure Sentinelu| Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak používat možnosti hledání Azure Sentinelu.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.date: 09/10/2019
ms.author: yelevin
ms.openlocfilehash: 54ddf6818b95a4037188ab222501ddfa69b28149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587894"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Hledání hrozeb pomocí Azure Sentinelu

Pokud jste vyšetřovatel, který chce být proaktivní při hledání bezpečnostních hrozeb, Azure Sentinel výkonné nástroje pro vyhledávání a vyhledávání dotazů pro vyhledávání bezpečnostních hrozeb ve zdrojích dat vaší organizace. Ale vaše systémy a bezpečnostní zařízení generovat hory dat, které může být obtížné analyzovat a filtrovat do smysluplné události. Aby se analytici zabezpečení mohli aktivně podívat na nové anomálie, které vaše aplikace zabezpečení nezjistily, integrované vyhledávací dotazy Azure Sentinelu vás navedou k tomu, abyste položili správné otázky k nalezení problémů v datech, která už máte v síti. 

Například jeden vestavěný dotaz poskytuje data o nejvíce neobvyklých procesech spuštěných ve vaší infrastruktuře - nechcete, aby se výstraha o každém spuštění, mohou být zcela nevinní, ale můžete se na dotaz občas podívat, abyste zjistili, zda Je tu něco neobvyklého. 



S Azure Sentinel lov, můžete využít následující funkce:

- Integrované dotazy: Úvodní stránka obsahuje předinstalované příklady dotazů, které vám pomohou začít a seznámit se s tabulkami a jazykem dotazu. Tyto vestavěné vyhledávací dotazy jsou vyvíjeny výzkumníky zabezpečení společnosti Microsoft průběžně, přidávají nové dotazy a dolaďují stávající dotazy, aby vám poskytly vstupní bod, aby hledaly nové detekce a zjistily, kde začít lovit nových útoků. 

- Výkonný dotazovací jazyk s technologiemi IntelliSense: Je postaven na dotazovacím jazyce, který vám poskytuje flexibilitu, kterou potřebujete k tomu, abyste lov i další úroveň.

- Vytvořte si vlastní záložky: Během procesu lovu můžete narazit na shody nebo nálezy, řídicí panely nebo aktivity, které vypadají neobvykle nebo podezřele. Chcete-li tyto položky označit, abyste se k nim mohli v budoucnu vrátit, použijte funkci záložky. Záložky umožňují uložit položky na později, které mají být použity k vytvoření incidentu pro vyšetřování. Další informace o záložkách najdete v tématu [Použití záložek při lovu](hunting.md).
- Používejte poznámkové bloky k automatizaci vyšetřování: Poznámkové bloky jsou jako podrobné playbooky, které můžete sestavit, abyste mohli projít kroky vyšetřování a lovu.  Poznámkové bloky zapouzdřují všechny lovecké kroky do opakovaně použitelného playbooku, který lze sdílet s ostatními uživateli ve vaší organizaci. 
- Dotaz na uložená data: Data jsou přístupná v tabulkách, na které se můžete dotazovat. Můžete například dotazovat na vytváření procesů, události DNS a mnoho dalších typů událostí.

- Odkazy na komunitu: Využijte sílu větší komunity k vyhledání dalších dotazů a zdrojů dat.
 
## <a name="get-started-hunting"></a>Začínáme lovit

1. Na portálu Azure Sentinel klikněte na **Lov**.
  ![Azure Sentinel začíná lovit](media/tutorial-hunting/hunting-start.png)

2. Když otevřete stránku **Lov,** všechny lovecké dotazy jsou zobrazeny v jedné tabulce. V tabulce jsou uvedeny všechny dotazy napsané týmem analytiků zabezpečení společnosti Microsoft a také všechny další dotazy, které jste vytvořili nebo upravili. Každý dotaz obsahuje popis toho, co loví a jaký druh dat, na kterých běží. Tyto šablony jsou seskupeny podle jejich různých taktik - ikony na pravé straně kategorizují typ hrozby, jako je počáteční přístup, vytrvalost a exfiltrace. Tyto šablony loveckých dotazů můžete filtrovat pomocí libovolného pole. Libovolný dotaz můžete uložit do oblíbených položek. Uložením dotazu do oblíbených položek se dotaz automaticky spustí při každém přístupu na stránku **Lov.** Můžete vytvořit vlastní lovecký dotaz nebo klonovat a přizpůsobit existující šablonu loveckého dotazu. 
 
2. Kliknutím na **Spustit dotaz** na stránce podrobností loveckého dotazu spusťte libovolný dotaz, aniž byste opustili stránku lovu.  Počet shod se zobrazí v tabulce. Prohlédněte si seznam loveckých dotazů a jejich shod. Podívejte se, ke které fázi v řetězci zabití je shoda přidružena.

3. Proveďte rychlou kontrolu podkladového dotazu v podokně podrobností dotazu nebo kliknutím na **zobrazit výsledek dotazu** otevřete dotaz v Log Analytics. V dolní části zkontrolujte shody pro dotaz.

4.  Klikněte na řádek a vyberte **Přidat záložku** pro přidání řádků, které mají být zkoumány - můžete to udělat pro cokoliv, co vypadá podezřele. 

5. Poté se vraťte na hlavní stránku **Hunting** a kliknutím na **záložky** zobekte všechny podezřelé aktivity. 

6. Vyberte záložku a kliknutím na **prozkoumat** otevřete prostředí šetření. Záložky můžete filtrovat. Pokud například zkoumáte kampaň, můžete pro kampaň vytvořit značku a potom filtrovat všechny záložky na základě kampaně.

1. Poté, co jste zjistili, který lovecký dotaz poskytuje vysoce hodnotné přehledy o možných útocích, můžete také vytvořit vlastní pravidla zjišťování na základě dotazu a zobrazit tyto přehledy jako výstrahy pro vaše respondenty incidentů zabezpečení.

 

## <a name="query-language"></a>Dotazovací jazyk 

Lov v Azure Sentinelu je založený na dotazovém jazyce Kusto. Další informace o dotazovacím jazyce a podporovaných operátorech naleznete v [tématu Reference pro dotaz .](https://docs.loganalytics.io/docs/Language-Reference/)

## <a name="public-hunting-query-github-repository"></a>Veřejné lovecký dotaz GitHub úložiště

Podívejte se na [úložiště dotazů Hunting](https://github.com/Azure/Orion). Přispívat a používat ukázkové dotazy sdílené našimi zákazníky.

 

## <a name="sample-query"></a>Ukázkový dotaz

Typický dotaz začíná názvem tabulky následovaným řadou \|operátorů oddělených .

Ve výše uvedeném příkladu začněte s názvem tabulky SecurityEvent a podle potřeby přidejte prvky s potrubím.

1. Definujte časový filtr pro kontrolu pouze záznamů z předchozích sedmi dnů.

2. Přidejte do dotazu filtr, který zobrazí pouze ID události 4688.

3. Přidejte do dotazu na příkazové čáře filtr, který bude obsahovat pouze instance souboru cscript.exe.

4. Promítejte jenom sloupce, které vás zajímají, a omezte výsledky na 1000 a klikněte na **Spustit dotaz**.
5. Klepněte na zelený trojúhelník a spusťte dotaz. Můžete otestovat dotaz a spustit jej hledat neobvyklé chování.

## <a name="useful-operators"></a>Užitečné operátory

Dotazovací jazyk je výkonný a má mnoho dostupných operátorů, některé užitečné operátory jsou uvedeny zde:

**kde** - Filtrujte tabulku do podmnožiny řádků, které splňují predikát.

**souhrn** - Vytvoří tabulku, která agreguje obsah vstupní tabulky.

**spojení** - Sloučit řádky dvou tabulek a vytvořit novou tabulku porovnáním hodnot zadaného sloupce (sloupců) z každé tabulky.

**count** - Vrátí počet záznamů ve vstupní sadě záznamů.

**nahoře** – Vrátí první N záznamy seřazené podle zadaných sloupců.

**limit** - Návrat až do zadaného počtu řádků.

**project** - Vyberte sloupce, které chcete zahrnout, přejmenovat nebo přetažení a vložit nové vypočítané sloupce.

**rozšířit** - Vytvořit počítané sloupce a připojit je k sadě výsledků.

**makeset** - Vrátí dynamické (JSON) pole sady odlišných hodnot, které Expr bere ve skupině

**najít** – najít řádky, které odpovídají predikátu přes sadu tabulek.

## <a name="save-a-query"></a>Uložení dotazu

Můžete vytvořit nebo upravit dotaz a uložit jej jako vlastní dotaz nebo jej sdílet s uživateli, kteří jsou ve stejném tenantovi.

   ![Uložit dotaz](./media/tutorial-hunting/save-query.png)

Vytvořte nový lovecký dotaz:

1. Klepněte na **nový dotaz** a vyberte **uložit**.
2. Vyplňte všechna prázdná pole a vyberte **Uložit**.

   ![Nový dotaz](./media/tutorial-hunting/new-query.png)

Klonovat a upravit existující lovecký dotaz:

1. Vyberte lovecký dotaz v tabulce, kterou chcete upravit.
2. Vyberte tři tečky (...) v řádku dotazu, který chcete upravit, a vyberte **dotaz Klonování**.

   ![dotaz klonování](./media/tutorial-hunting/clone-query.png)
 

3. Upravte dotaz a vyberte **Vytvořit**.

   ![vlastní dotaz](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Další kroky
V tomto článku jste se naučili, jak spustit hledání šetření s Azure Sentinel. Další informace o Azure Sentinelu najdete v následujících článcích:


- [Používání automatických loveckých kampaní pomocí poznámkových bloků](notebooks.md)
- [Použití záložek pro ukládání zajímavých informací při lovu](bookmarks.md)
