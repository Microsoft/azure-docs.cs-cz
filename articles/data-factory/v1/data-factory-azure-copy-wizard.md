---
title: Průvodce kopírováním Azure | Microsoft Docs
description: Další informace o tom, jak pomocí Průvodce kopírováním Azure Data Factory ke zkopírování dat z podporovaných zdrojů dat do jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ebd8abf8c34be1fa6fcd282c9bfbc63d81a56239
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621128"
---
# <a name="azure-data-factory-copy-wizard"></a>Průvodce kopírováním služby Azure Data Factory
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). 

Průvodce kopírováním Azure Data Factory usnadňuje proces příjem dat, což je většinou první krok v případě integrace pomocí dat začátku do konce. Při průchodu přes Průvodce kopírováním Azure Data Factory, není potřeba pochopit žádné definice JSON propojené služby, datové sady a kanály. Průvodce automaticky vytvoří kanál ke zkopírování dat z vybraného zdroje dat do vybraného cílového umístění. Kromě toho Průvodce kopírováním vám pomůže ověřit data se požity v době vytváření obsahu. Tím ušetříte čas, zvláště když je jsou příjem dat první ze zdroje dat. Chcete-li spustit Průvodce kopírováním, klikněte na tlačítko **kopírování dat** na domovské stránce objektu pro vytváření dat dlaždici.

![Průvodce kopírováním](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Určená pro velké objemy dat
Tento průvodce umožňuje snadno přesunout data z různých zdrojů a cílů v minutách. Po projít průvodce kanál s aktivitou kopírování automaticky vytvoří za vás, společně s závislé entity služby Data Factory (propojené služby a datové sady). Žádné další kroky jsou nutné k vytvoření kanálu.   

![Vyberte zdroj dat](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Podrobné pokyny k vytvoření ukázkový kanál ke zkopírování dat z Azure blob do tabulky Azure SQL Database, najdete v článku [Průvodce kopírováním kurzu](data-factory-copy-data-wizard-tutorial.md).
>
>

Průvodce je určený s velkým objemem dat v paměti od začátku, s podporou pro různé data a typy objektů. Můžete vytvořit kanály pro vytváření dat, které přesunout stovky složky, soubory nebo tabulky. Průvodce podporuje automatické datový ve verzi preview, schéma zachycení a mapování a filtrování dat.

## <a name="automatic-data-preview"></a>Náhled automatické dat
Náhled část dat z vybraného zdroje dat si můžete zobrazit k ověření, zda chcete zkopírovat data. Kromě toho pokud je zdrojová data do textového souboru, Průvodce kopírováním analyzuje textový soubor další oddělovače řádků a sloupců a schéma automaticky.

![Nastavení formátu souboru](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schéma zachycení a mapování
Schéma vstupní data nemusí odpovídat schématu výstupních dat, v některých případech. V tomto scénáři budete muset mapování sloupců ze schématu zdroje na sloupce ze schématu cílové.

> [!TIP]
> Při kopírování dat z SQL serveru nebo databáze SQL Azure do Azure SQL Data Warehouse, pokud tabulka neexistuje v cílové úložiště, Data Factory podporují automatické vytvoření tabulky pomocí schématu zdroje. Další informace z [přesun dat do a z Azure SQL Data Warehouse pomocí Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Pomocí rozevíracího seznamu vyberte sloupec ze schématu zdroje k mapování na sloupec v cílové schéma. Průvodce kopírováním se pokusí zjistit vaše vzor pro mapování sloupců. Používá stejného vzoru se zbytek sloupce, takže není nutné vybrat jednotlivé sloupce jednotlivě k dokončení schéma mapování. Pokud dáváte přednost, můžete přepsat tato mapování pomocí rozevíracích seznamů k mapování sloupců po jednom. Vzor stane přesnější jako mapovat více sloupců. Průvodce kopírováním neustále aktualizuje vzoru a nakonec dosáhne správné vzor pro mapování sloupců, které chcete dosáhnout.     

![Schéma mapování](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrování dat
Můžete filtrovat zdrojová data a vyberte pouze data, která je možné zkopírovat do úložiště dat jímky. Filtrování snižuje objem dat, který se má zkopírovat do úložiště dat podřízený a proto zvyšuje propustnost kopírování. Poskytuje flexibilní způsob, jak filtrování dat v relační databázi pomocí dotazu jazyka SQL, nebo soubory ve složce aplikace objektů blob v Azure pomocí [Data Factory funkce a proměnné](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrování dat v databázi
Následující snímek obrazovky ukazuje dotaz SQL pomocí `Text.Format` funkce a `WindowStart` proměnné.

![Ověření výrazy](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrování dat v Azure blob složce
Proměnné v cestě ke složce můžete použít ke zkopírování dat z složku, která je určena za běhu na základě [systémové proměnné](data-factory-functions-variables.md#data-factory-system-variables). Podporované proměnné jsou: **{year}**, **{month}**, **{day}**, **{hodinu}**, **{minutu}**, a **{vlastní}**. Příklad: inputfolder / {year} / {month} / {day}.

Předpokládejme, že máte vstupní složky v následujícím formátu:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klikněte na tlačítko **Procházet** tlačítko pro **souboru nebo složky**, přejděte do jednoho z těchto složek (například 2016 -> 03 -> 01 -> 02) a klikněte na tlačítko **zvolte**. Měli byste vidět `2016/03/01/02` v textovém poli. Nyní, nahraďte **2016** s **{year}**, **03** s **{month}**, **01** s **{day}**, a **02** s **{hodinu}** a stiskněte klávesu **kartě** klíč. Měli byste vidět rozevíracích seznamech vyberte formát pro tyto čtyři proměnné:

![Pomocí systémové proměnné](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak je znázorněno na následujícím snímku obrazovky, můžete použít také **vlastní** proměnné a všechny [podporované řetězce formátu](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Chcete-li vybrat jinou složku s této struktury, použijte **Procházet** nejprve tlačítko. Potom můžete nahradit hodnotu s **{vlastní}** a stiskněte klávesu **kartě** klíč najdete v části textového pole, můžete zadat řetězec formátu.     

![Použití vlastní proměnné](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Možnosti plánování
Operace kopírování můžete spustit jednou nebo podle plánu (každou hodinu, denně, a tak dále). Obě tyto možnosti lze použít pro šířky konektory prostředích, včetně místní, cloud a místní kopie plochy.

Operace kopírování jednorázové umožňuje přesun dat ze zdroje do cíle pouze jednou. Platí pro data libovolné velikosti a jakýkoli podporovaný formát. Naplánované kopie umožňuje kopírovat data na předepsané opakování. Bohaté nastavení (např. Zkuste to znovu, vypršení časového limitu a upozornění) můžete použít ke konfiguraci naplánované kopírování.

![Plánování vlastnosti](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Další postup
Rychlé návod k vytvoření kanálu s aktivitou kopírování pomocí Průvodce kopírováním Data Factory najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).
