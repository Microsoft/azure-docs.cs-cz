---
title: Snadné kopírování dat pomocí Průvodce kopírováním – Azure
description: Přečtěte si, jak pomocí Průvodce kopírováním Data Factory kopírovat data z podporovaných zdrojů dat do jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 28169e43d0e6949a16cc56c7e7d5d91d6db1ef57
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636914"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Snadné kopírování a přesouvání dat pomocí Průvodce kopírováním Azure Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, podívejte se na [kurz o aktivitě kopírování](../quickstart-create-data-factory-dot-net.md). 


Průvodce kopírováním Azure Data Factory slouží k usnadnění procesu ingestování dat, což je obvykle první krok v uceleném scénáři integrace dat. Když projdete průvodcem kopírováním Azure Data Factory, nemusíte pochopit žádné definice JSON pro propojené služby, datové sady a kanály. Po dokončení všech kroků v průvodci ale průvodce automaticky vytvoří kanál pro kopírování dat z vybraného zdroje dat do vybraného cíle. Kromě toho vám Průvodce kopírováním pomůže ověřit data, která se ingestují v době vytváření, což šetří spoustu času, zejména při první ingestování dat ze zdroje dat. Průvodce kopírováním spustíte tak, že kliknete na dlaždici **Kopírovat data** na domovské stránce vaší datové továrny.

![Průvodce kopírováním](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Intuitivní průvodce kopírováním dat
Tento průvodce vám umožní snadno přesunout data z nejrůznějších zdrojů do cílových míst během několika minut. Po absolvování průvodce se automaticky vytvoří kanál s aktivitou kopírování, a to spolu se závislými Data Factory entitami (propojené služby a datové sady). K vytvoření kanálu nejsou nutné žádné další kroky.   

![Výběr zdroje dat](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Podrobné pokyny k vytvoření ukázkového kanálu pro kopírování dat z objektu blob Azure do Azure SQL Database tabulky najdete v článku [Průvodce kopírováním Průvodce](data-factory-copy-data-wizard-tutorial.md) . 
> 
> 

Průvodce je navržený s velkými objemy dat od začátku. Vytváření Data Factory kanálů, které přesouvají stovky složek, souborů nebo tabulek pomocí Průvodce Kopírování dat, je jednoduché a efektivní. Průvodce podporuje následující tři funkce: Automatické zobrazení dat, zachytávání schématu a mapování a filtrování dat. 

## <a name="automatic-data-preview"></a>Náhled automatických dat
Průvodce kopírováním umožňuje zkontrolovat část dat z vybraného zdroje dat, abyste ověřili, jestli se jedná o správná data, která chcete zkopírovat. Kromě toho, pokud jsou zdrojová data v textovém souboru, Průvodce kopírováním analyzuje textový soubor, aby se dozvěděl oddělovače řádků a sloupců, a schéma se automaticky. 

![Nastavení formátu souboru](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Zachytávání a mapování schématu
Schéma vstupních dat nemusí v některých případech odpovídat schématu výstupních dat. V tomto scénáři je nutné mapovat sloupce ze zdrojového schématu na sloupce z cílového schématu. 

Průvodce kopírováním automaticky mapuje sloupce ve zdrojovém schématu na sloupce v cílovém schématu. Mapování můžete přepsat pomocí rozevíracích seznamů (nebo) určit, zda je při kopírování dat nutné přeskočit sloupec.   

![Mapování schématu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrování dat
Průvodce umožňuje filtrovat zdrojová data a vybrat pouze data, která je třeba zkopírovat do cílového úložiště dat nebo úložiště dat jímky. Filtrování omezuje objem dat, která mají být zkopírována do úložiště dat jímky, a tím zvyšuje propustnost operace kopírování. Poskytuje flexibilní způsob, jak filtrovat data v relační databázi pomocí souborů dotazovacího jazyka SQL (nebo) ve složce objektů BLOB v Azure pomocí [Data Factory funkcí a proměnných](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrování dat v databázi
V příkladu používá dotaz SQL `Text.Format` funkci a `WindowStart` proměnnou. 

![Ověřit výrazy](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrování dat ve složce Azure Blob
Proměnné v cestě ke složce můžete použít ke kopírování dat ze složky, která je určena za běhu na základě [systémových proměnných](data-factory-functions-variables.md#data-factory-system-variables). Podporované proměnné jsou: **{year}** , **{month}** , **{Day}** , **{Hour}** , **{minute}** a **{Custom}** . Příklad: inputfolder/{Year}/{Month}/{Day}.

Předpokládejme, že máte vstupní složky v následujícím formátu:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klikněte na tlačítko **Procházet** pro **soubor nebo složku** , vyhledejte jednu z těchto složek (například 2016->03->01->02) a klikněte na **zvolit** . Mělo by se zobrazit `2016/03/01/02` v textovém poli. Nyní nahraďte **2016** řetězcem **{year}** , **03** a **{month}** , **01** s **{Day}** a **02** s **{Hour}** a stiskněte klávesu TAB. Měli byste vidět rozevírací seznamy a vybrat formát pro tyto čtyři proměnné:

![Použití systémových proměnných](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak je znázorněno na následujícím snímku obrazovky, můžete také použít **vlastní** proměnnou a všechny [podporované formátovací řetězce](/dotnet/standard/base-types/custom-date-and-time-format-strings). Chcete-li vybrat složku s touto strukturou, použijte nejprve tlačítko **Procházet** . Potom hodnotu nahraďte hodnotou **{Custom}** a stisknutím klávesy TAB zobrazte textové pole, ve kterém můžete zadat řetězec formátu.     

![Použití vlastní proměnné](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Podpora různých typů dat a objektů
Pomocí Průvodce kopírováním můžete efektivně přesunout stovky složek, souborů nebo tabulek.

![Vybrat tabulky, ze kterých se mají kopírovat data](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Možnosti plánování
Operaci kopírování můžete spustit jednou nebo podle plánu (každou hodinu, každý den atd.). Obě tyto možnosti se dají použít pro šířku konektorů napříč místními, cloudem a místními kopiemi na ploše.

Jednorázová operace kopírování umožňuje přesun dat ze zdroje do cíle pouze jednou. Platí pro data libovolné velikosti a veškerého podporovaného formátu. Naplánované kopírování umožňuje kopírovat data po předepsaném opakování. Ke konfiguraci naplánované kopie můžete použít bohatá nastavení (například opakování, časový limit a upozornění).

![Vlastnosti plánování](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Další kroky
Rychlý návod, jak pomocí Průvodce kopírováním Data Factory vytvořit kanál s aktivitou kopírování, najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).