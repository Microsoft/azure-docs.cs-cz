---
title: Kopírování dat snadno pomocí Průvodce kopírováním - Azure | Microsoft Docs
description: Další informace o tom, jak pomocí Průvodce kopírováním Data Factory ke zkopírování dat z podporovaných zdrojů dat do jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b74a431664faa95e8be9c9ff90970fd6e7c0ec7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048716"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Zkopírovat nebo přesunout data snadno pomocí Průvodce kopírováním objekt pro vytváření dat Azure
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [kurzu aktivity kopírování](../quickstart-create-data-factory-dot-net.md). 


Průvodce kopírováním Azure Data Factory je k usnadnění procesu příjem dat, který je obvykle prvním krokem v případě integrace pomocí dat začátku do konce. Při průchodu přes Průvodce kopírováním Azure Data Factory, není potřeba pochopit žádné definice JSON propojené služby, datové sady a kanály. Ale po dokončení všech kroků v průvodci, průvodce automaticky vytvoří kanál ke zkopírování dat z vybraného zdroje dat do vybraného cílového umístění. Kromě toho Průvodce kopírováním umožňuje ověřit data se požity v době vytváření obsahu, které ukládá většinu doby, zvláště když je jsou příjem dat první ze zdroje dat. Chcete-li spustit Průvodce kopírováním, klikněte na tlačítko **kopírování dat** na domovské stránce objektu pro vytváření dat dlaždici.

![Průvodce kopírováním](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Intuitivní Průvodce pro kopírování dat
Tento průvodce umožňuje snadno přesunout data z různých zdrojů a cílů v minutách. Poté, co projde průvodce, kanál s aktivitou kopírování se automaticky vytvoří za vás společně s závislé entity služby Data Factory (propojené služby a datové sady). Žádné další kroky jsou nutné k vytvoření kanálu.   

![Výběr zdroje dat](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> V tématu [Průvodce kopírováním kurzu](data-factory-copy-data-wizard-tutorial.md) článku podrobné pokyny k vytvoření ukázkový kanál kopírování dat z Azure blob do tabulky Azure SQL Database. 
> 
> 

Průvodce je určený s velkým objemem dat v paměti od začátku. Je jednoduchý a efektivně vytvářet kanály pro vytváření dat, které přesunout stovky složky, soubory nebo pomocí Průvodce kopírování dat tabulky. Průvodce podporuje následujících tří funkcí: náhled dat, schématu zachycení a mapování a filtrování dat. 

## <a name="automatic-data-preview"></a>Náhled automatické dat
Průvodce kopírováním umožňuje zkontrolovat část dat ze zdroje dat vybraných pro vás k ověření toho, jestli data je správná data, kterou chcete zkopírovat. Kromě toho pokud je zdrojová data do textového souboru, Průvodce kopírováním analyzuje textový soubor automaticky další řádků a sloupců oddělovače a schéma. 

![Nastavení formátu souboru](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schéma zachycení a mapování
Schéma vstupní data nemusí odpovídat schématu výstupních dat, v některých případech. V tomto scénáři budete muset mapování sloupců ze schématu zdroje na sloupce ze schématu cílové. 

Průvodce kopírováním automaticky mapuje sloupců ve schématu zdroje na sloupce ve schématu cílové. Můžete přepsat mapování pomocí rozevíracích seznamů (nebo) určete, zda sloupec je nutné při kopírování dat.   

![Schéma mapování](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrování dat
Průvodce vám umožní filtrovat zdroje dat a vyberte pouze data, která je možné zkopírovat do úložiště dat cílové nebo podřízený. Filtrování snižuje objem dat, který se má zkopírovat do úložiště dat podřízený a proto zvyšuje propustnost kopírování. Poskytuje flexibilní způsob, jak filtrování dat v relační databázi pomocí SQL dotazu jazyka (nebo) soubory ve složce aplikace objektů blob v Azure pomocí [Data Factory funkce a proměnné](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrování dat v databázi
V příkladu se používá příkaz jazyka SQL `Text.Format` funkce a `WindowStart` proměnné. 

![Ověření výrazy](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrování dat v Azure blob složce
Proměnné v cestě ke složce můžete použít ke zkopírování dat z složku, která je určena za běhu na základě [systémové proměnné](data-factory-functions-variables.md#data-factory-system-variables). Podporované proměnné jsou: **{year}**, **{month}**, **{day}**, **{hodinu}**, **{minutu}** a **{{vlastní}**. Příklad: inputfolder / {year} / {month} / {day}.

Předpokládejme, že máte vstupní složky v následujícím formátu:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klikněte na tlačítko **Procházet** tlačítko pro **souboru nebo složky**, přejděte do jednoho z těchto složek (například 2016 -> 03 -> 01 -> 02) a klikněte na tlačítko **zvolte**. Měli byste vidět `2016/03/01/02` v textovém poli. Nyní, nahraďte **2016** s **{year}**, **03** s **{month}**, **01** s **{day}** , a **02** s **{hodinu}**, a stiskněte klávesu Tab. Měli byste vidět rozevíracích seznamech vyberte formát pro tyto čtyři proměnné:

![Pomocí systémové proměnné](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak je znázorněno na následujícím snímku obrazovky, můžete použít také **vlastní** proměnné a všechny [podporované řetězce formátu](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Chcete-li vybrat jinou složku s této struktury, použijte **Procházet** nejprve tlačítko. Potom můžete nahradit hodnotu s **{vlastní}**, a stiskněte klávesu Tab zobrazíte textového pole, můžete zadat řetězec formátu.     

![Použití vlastní proměnné](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Podpora různých dat a typy objektů
Pomocí Průvodce kopírováním můžete efektivně přesouvají stovky složky, soubory nebo tabulky.

![Vyberte tabulky, ze kterého chcete zkopírovat data](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Možnosti plánování
Operace kopírování můžete spustit jednou nebo podle plánu (každou hodinu, denně, a tak dále). Obě tyto možnosti lze použít pro šířky konektory napříč místními, cloud a místní kopie plochy.

Operace kopírování jednorázové umožňuje přesun dat ze zdroje do cíle pouze jednou. Platí pro data libovolné velikosti a jakýkoli podporovaný formát. Naplánované kopie umožňuje kopírovat data na předepsané opakování. Bohaté nastavení (např. Zkuste to znovu, vypršení časového limitu a upozornění) můžete použít ke konfiguraci naplánované kopírování.

![Plánování vlastnosti](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Další postup
Rychlé návod k vytvoření kanálu s aktivitou kopírování pomocí Průvodce kopírováním Data Factory najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

