---
title: Kopírování dat snadno pomocí Průvodce kopírováním – Azure | Dokumentace Microsoftu
description: Další informace o tom, jak používat Průvodce kopírováním služby Data Factory pro kopírování dat z podporovaných zdrojů dat do jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 90f78428601d7b039d00d39c1ca8339ab3ace9ba
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020349"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Kopírování nebo přesun dat snadno pomocí Průvodce kopírováním služby Azure Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, podívejte se na [kurz o aktivitě kopírování](../quickstart-create-data-factory-dot-net.md). 


Průvodce kopírováním služby Azure Data Factory je usnadňují proces ingestuje data, která je obvykle prvním krokem při scénáři integrace data začátku do konce. Když přejdete pomocí Průvodce kopírováním služby Azure Data Factory, není nutné pochopit žádné definice JSON propojené služby, datové sady a kanály. Ale po dokončení všech kroků v průvodci, průvodce automaticky vytvoří kanál ke zkopírování dat z vybraného zdroje dat do vybraného cíle. Kromě toho Průvodce kopírováním umožňuje ověřit data ingestuje v době vytváření obsahu, který ukládá mnoho času, zejména když jste jsou ingestovat data poprvé ze zdroje dat. Spuštění Průvodce kopírováním, klikněte na tlačítko **kopírování dat** dlaždici na domovské stránce vaší datové továrně.

![Průvodce kopírováním](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Intuitivní Průvodce pro kopírování dat
Tento průvodce umožňuje snadno přesunout data z nejrůznějších zdrojů do cílů během několika minut. Po absolvování průvodce, kanál s aktivitou kopírování se automaticky vytvoří za vás spolu s závislé entity služby Data Factory (propojené služby a datové sady). Žádné další kroky jsou nutné k vytvoření kanálu.   

![Výběr zdroje dat](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Zobrazit [kurzu Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) najdete podrobné pokyny k vytvoření ukázkového kanálu pro kopírování dat z Azure blob do tabulky databáze SQL Azure. 
> 
> 

Průvodce je navržená s velkými objemy dat v paměti od začátku. Je to jednoduché a efektivní vytváření kanálů Data Factory, které přesunout stovky složek, souborů nebo pomocí Průvodce kopírování dat tabulky. Průvodce podporuje následujících tří funkcí: Automatické dat ve verzi preview, zachycení schématu a mapování a filtrování dat. 

## <a name="automatic-data-preview"></a>Automatické dat ve verzi preview
Průvodce kopírováním umožňuje zkontrolovat spolu s daty z vybraného zdroje dat. můžete se ověřit, zda data je ta správná data, které chcete kopírovat. Kromě toho pokud se zdrojová data jsou v textovém souboru, Průvodce kopírováním analyzuje textový soubor, automaticky další řádek a sloupec oddělovače a schéma. 

![Nastavení formátu souboru](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schéma zachycení a mapování
Schéma vstupních dat nemusí odpovídat schématu výstupní data v některých případech. V tomto scénáři budete muset namapovat sloupce ze schématu zdroje na sloupce ze schématu cíle. 

Průvodce kopírováním automaticky mapují na sloupce ve schématu cílové sloupců ve schématu zdroje. Můžete přepsat mapování pomocí rozevíracích seznamů (nebo) určete, zda sloupec musí být přeskočeny při kopírování dat.   

![mapování schématu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrování dat
Průvodce vám umožní filtrovat zdrojová data a vyberte pouze data, která se musí zkopírovat do úložiště dat cíl/jímka. Filtrování snižuje objem dat, které se mají zkopírovat do úložiště dat jímky a proto zvyšuje propustnost operace kopírování. Poskytuje flexibilní způsob, jak filtrovat data v relační databázi s použitím SQL query language (nebo) soubory do složky objektů blob v Azure s použitím [funkce Data Factory a proměnné](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrování dat v databázi
V tomto příkladu se používá dotaz SQL `Text.Format` funkce a `WindowStart` proměnné. 

![Ověření výrazy](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrování dat v složce objektů blob v Azure
Můžete použít proměnné v cesta ke složce pro kopírování dat ze složky, která je určena v době běhu na základě [systémové proměnné](data-factory-functions-variables.md#data-factory-system-variables). Podporované proměnné jsou: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** a **{vlastní}**. Příklad: inputfolder / {year} / {month} / {day}.

Předpokládejme, že mají vstupní složky v následujícím formátu:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klikněte na tlačítko **Procházet** tlačítko pro **souboru nebo složky**, přejděte do jednoho z těchto složek (třeba 2016 -> 03 -> 02-01 >) a klikněte na tlačítko **zvolit**. Měli byste vidět `2016/03/01/02` v textovém poli. Nyní nahraďte **2016** s **{year}**, **03** s **{month}**, **01** s **{day}** , a **02** s **{hour}**, a stiskněte klávesu Tab. Měli byste vidět rozevíracích seznamech vyberte formát pro tyto čtyři proměnné:

![Pomocí systémové proměnné](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak je znázorněno na následujícím snímku obrazovky, můžete použít také **vlastní** proměnné a jakékoli [podporované formátovací řetězce](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Chcete-li vybrat složku s danou strukturu, použijte **Procházet** nejprve tlačítko. Potom nahraďte hodnotu s **{vlastní}**, a stiskněte klávesu Tab zobrazíte textové pole, kam můžete zadat řetězec formátu.     

![Použití vlastní proměnné](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Podpora pro data rozdílné a typy objektů
Pomocí Průvodce kopírováním můžete efektivně přesunout stovky složky, soubory nebo tabulky.

![Vyberte tabulky, ze kterého chcete kopírovat data](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Možnosti plánování
Operace kopírování můžete spustit jednou nebo podle plánu (každou hodinu, každý den, a tak dále). Obě tyto možnosti je možné pro škálu konektorů místní, Cloudová a místní kopii klasické pracovní plochy.

Operace kopírování jednorázové umožňuje přesun dat ze zdroje do cíle pouze jednou. Platí pro data libovolné velikosti a jakýkoli podporovaný formát. Plánované kopírování umožňuje kopírovat data na předepsané opakování. Bohaté možnosti nastavení (např. Zkuste to znovu, vypršení časového limitu a upozornění) můžete použít ke konfiguraci plánované kopírování.

![Plánování vlastnosti](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Další postup
Rychlý postup k vytvoření kanálu s aktivitou kopírování pomocí Průvodce kopírováním služby Data Factory, najdete v části [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

