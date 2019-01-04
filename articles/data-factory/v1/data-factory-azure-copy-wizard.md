---
title: Průvodce kopírováním Azure | Dokumentace Microsoftu
description: Další informace o tom, jak používat Průvodce kopírováním služby Data Factory Azure ke zkopírování dat z podporovaných zdrojů dat do jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d13e304b0d10e8bd34d306426f1f9164bcc6be94
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014246"
---
# <a name="azure-data-factory-copy-wizard"></a>Průvodce kopírováním služby Azure Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

Průvodce kopírováním služby Data Factory Azure usnadňuje proces ingestuje data, která je obvykle prvním krokem při scénáři integrace data začátku do konce. Když přejdete pomocí Průvodce kopírováním služby Azure Data Factory, není nutné pochopit žádné definice JSON propojené služby, datové sady a kanály. Průvodce automaticky vytvoří kanál ke zkopírování dat z vybraného zdroje dat do vybraného cíle. Kromě toho Průvodce kopírováním vám pomůže ověřit data ingestuje v době vytváření obsahu. Tím ušetříte čas, zejména když jste jsou ingestovat data poprvé ze zdroje dat. Spuštění Průvodce kopírováním, klikněte na tlačítko **kopírování dat** dlaždici na domovské stránce vaší datové továrně.

![Průvodce kopírováním](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Navržené pro velké objemy dat
Tento průvodce umožňuje snadno přesunout data z nejrůznějších zdrojů do cílů během několika minut. Po projít průvodce kanál s aktivitou kopírování automaticky vytvoří za vás, spolu s závislé entity služby Data Factory (propojené služby a datové sady). Žádné další kroky jsou nutné k vytvoření kanálu.   

![Výběr zdroje dat](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Podrobné pokyny k vytvoření ukázkového kanálu pro kopírování dat z Azure blob do tabulky databáze SQL Azure, najdete v článku [Průvodce kopírováním kurzu](data-factory-copy-data-wizard-tutorial.md).
>
>

Průvodce je navržená s velkými objemy dat v paměti od začátku, díky podpoře různorodých a typy objektů. Můžete vytvářet kanály Data Factory, které přesunout stovky složky, soubory nebo tabulky. Průvodce podporuje automatické dat ve verzi preview, zachycení schématu a mapování a filtrování dat.

## <a name="automatic-data-preview"></a>Automatické dat ve verzi preview
Můžete zobrazit náhled spolu s daty z vybraného zdroje dat. Chcete-li ověřit, zda chcete zkopírovat data. Kromě toho pokud se zdrojová data jsou v textovém souboru, Průvodce kopírováním analyzuje textový soubor, automaticky další řádek a sloupec oddělovače a schéma.

![Nastavení formátu souboru](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schéma zachycení a mapování
Schéma vstupních dat nemusí odpovídat schématu výstupní data v některých případech. V tomto scénáři budete muset namapovat sloupce ze schématu zdroje na sloupce ze schématu cíle.

> [!TIP]
> Při kopírování dat z Azure SQL Database nebo SQL serveru do Azure SQL Data Warehouse, pokud tabulka neexistuje v cílovém úložišti, Data Factory podporují automatické vytvoření tabulky pomocí schématu zdroje. Další informace z [přesun dat do a z Azure SQL Data Warehouse pomocí Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Pomocí rozevíracího seznamu vyberte sloupce ze zdrojového schématu mapovat na sloupec v cílové schéma. Průvodce kopírováním pokusí k pochopení vašeho modelu pro mapování sloupců. Použije stejný vzor pro zbývající sloupce, takže není potřeba vybrat všechny sloupce jednotlivě k dokončení mapování schématu. Pokud dáváte přednost, můžete přepsat tato mapování pomocí rozevíracích seznamech pro mapování sloupců jeden po druhém. Vzor se změní na přesnější, jak mapovat více sloupců. Průvodce kopírováním neustále aktualizuje vzor a nakonec dosáhne správné vzor pro mapování sloupců, které chcete dosáhnout.     

![mapování schématu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrování dat
Můžete filtrovat zdrojová data a vyberte pouze data, která se musí zkopírovat do úložiště dat jímky. Filtrování snižuje objem dat, které se mají zkopírovat do úložiště dat jímky a proto zvyšuje propustnost operace kopírování. Poskytuje flexibilní způsob, jak filtrovat data do relační databáze pomocí dotazu jazyka SQL, nebo soubory ve složce blobů Azure s použitím [funkce Data Factory a proměnné](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrování dat v databázi
Následující snímek obrazovky ukazuje pomocí dotazu SQL `Text.Format` funkce a `WindowStart` proměnné.

![Ověření výrazy](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrování dat v složce objektů blob v Azure
Můžete použít proměnné v cesta ke složce pro kopírování dat ze složky, která je určena v době běhu na základě [systémové proměnné](data-factory-functions-variables.md#data-factory-system-variables). Podporované proměnné jsou: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** a **{vlastní}**. Příklad: inputfolder / {year} / {month} / {day}.

Předpokládejme, že mají vstupní složky v následujícím formátu:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klikněte na tlačítko **Procházet** tlačítko pro **souboru nebo složky**, přejděte do jednoho z těchto složek (třeba 2016 -> 03 -> 02-01 >) a klikněte na tlačítko **zvolit**. Měli byste vidět `2016/03/01/02` v textovém poli. Nyní nahraďte **2016** s **{year}**, **03** s **{month}**, **01** s **{day}** , a **02** s **{hour}** a stiskněte klávesu **kartu** klíč. Měli byste vidět rozevíracích seznamech vyberte formát pro tyto čtyři proměnné:

![Pomocí systémové proměnné](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak je znázorněno na následujícím snímku obrazovky, můžete použít také **vlastní** proměnné a jakékoli [podporované formátovací řetězce](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Chcete-li vybrat složku s danou strukturu, použijte **Procházet** nejprve tlačítko. Potom nahraďte hodnotu s **{vlastní}** a stiskněte klávesu **kartu** zobrazíte textové pole, kam můžete zadat řetězec formátu.     

![Použití vlastní proměnné](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Možnosti plánování
Operace kopírování můžete spustit jednou nebo podle plánu (každou hodinu, každý den, a tak dále). Obě tyto možnosti lze použít pro škálu konektorů v prostředích, včetně místní, Cloudová a místní kopii klasické pracovní plochy.

Operace kopírování jednorázové umožňuje přesun dat ze zdroje do cíle pouze jednou. Platí pro data libovolné velikosti a jakýkoli podporovaný formát. Plánované kopírování umožňuje kopírovat data na předepsané opakování. Bohaté možnosti nastavení (např. Zkuste to znovu, vypršení časového limitu a upozornění) můžete použít ke konfiguraci plánované kopírování.

![Plánování vlastnosti](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Další postup
Rychlý postup k vytvoření kanálu s aktivitou kopírování pomocí Průvodce kopírováním služby Data Factory, najdete v části [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).
