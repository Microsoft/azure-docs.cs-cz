---
title: Průvodce kopírováním Azure v datové továrně dat
description: Přečtěte si, jak pomocí Průvodce kopírováním Azure v datové továrně pomocí zkopírovat data z podporovaných zdrojů dat do propadů.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 0974eb40-db98-4149-a50d-48db46817076
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fef9059700e2bd94029c40bb819870a7174e0812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930117"
---
# <a name="azure-data-factory-copy-wizard"></a>Průvodce kopírováním datové továrny Azure
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

Průvodce kopírováním azure data factory usnadňuje proces ingestování dat, což je obvykle prvním krokem ve scénáři integrace dat od konce. Když procházíte Průvodce kopírováním datové továrny Azure, nemusíte rozumět žádné definice JSON pro propojené služby, datové sady a kanály. Průvodce automaticky vytvoří kanál pro kopírování dat z vybraného zdroje dat do vybraného cíle. Průvodce kopírováním vám navíc pomůže ověřit, že data jsou při vytváření ingestována. To šetří čas, zejména při ingestování dat poprvé ze zdroje dat. Chcete-li spustit Průvodce kopírováním, klikněte na domovské stránce datové továrny na dlaždici **Kopírovat data.**

![Průvodce kopírováním](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Navrženo pro velké objemy dat
Tento průvodce umožňuje snadno přesunout data z široké škály zdrojů do cílů během několika minut. Po procházení průvodcem se automaticky vytvoří kanál s aktivitou kopírování spolu se závislými entitami Data Factory (propojené služby a sady dat). K vytvoření kanálu nejsou nutné žádné další kroky.   

![Výběr zdroje dat](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Podrobné pokyny k vytvoření ukázkového kanálu pro kopírování dat z objektu blob Azure do tabulky Azure SQL Database najdete v [kurzu Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).
>
>

Průvodce je navržen s ohledem na velké objemy dat od začátku s podporou různých typů dat a objektů. Můžete vytvářet kanály Data Factory, které přesouvají stovky složek, souborů nebo tabulek. Průvodce podporuje automatický náhled dat, sběr a mapování schématu a filtrování dat.

## <a name="automatic-data-preview"></a>Automatický náhled dat
Můžete zobrazit náhled části dat z vybraného zdroje dat, abyste ověřili, zda jsou data to, co chcete zkopírovat. Kromě toho, pokud jsou zdrojová data v textovém souboru, Průvodce kopírováním analyzuje textový soubor, aby se automaticky naučil oddělovače řádků a sloupců a schéma.

![Nastavení formátu souboru](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Zachycení a mapování schématu
Schéma vstupních dat nemusí odpovídat schématu výstupních dat v některých případech. V tomto scénáři je třeba mapovat sloupce ze zdrojového schématu do sloupců z cílového schématu.

> [!TIP]
> Při kopírování dat z SQL Serveru nebo Azure SQL Database do Azure SQL Data Warehouse, pokud tabulka neexistuje v cílovém úložišti, Data Factory podporují automatické vytváření tabulek pomocí schématu zdroje. Další informace najdete v článku [Přesun dat do a z Azure SQL Data Warehouse pomocí Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Pomocí rozevíracího seznamu vyberte sloupec ze zdrojového schématu, který chcete namapovat na sloupec v cílovém schématu. Průvodce kopírováním se pokusí porozumět vzoru pro mapování sloupců. Použije stejný vzorek pro zbytek sloupců, takže není nutné vybrat každý sloupec jednotlivě k dokončení mapování schématu. Pokud chcete, můžete přepsat tato mapování pomocí rozevíracích seznamů mapovat sloupce jeden po druhém. Vzorek se při mapování více sloupců stává přesnějším. Průvodce kopírováním vzorek neustále aktualizuje a nakonec dosáhne správného vzoru pro mapování sloupců, kterého chcete dosáhnout.     

![Mapování schématu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrování dat
Můžete filtrovat zdrojová data a vybrat pouze data, která je třeba zkopírovat do úložiště dat jímky. Filtrování snižuje objem dat, která mají být zkopírována do úložiště dat jímky a proto zvyšuje propustnost operace kopírování. Poskytuje flexibilní způsob filtrování dat v relační databázi pomocí dotazovacího jazyka SQL nebo souborů ve složce objektů blob Azure pomocí [funkcí a proměnných data factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrování dat v databázi
Následující snímek obrazovky ukazuje `Text.Format` dotaz `WindowStart` SQL pomocí funkce a proměnné.

![Ověřit výrazy](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrování dat ve složce objektů blob Azure
Proměnné v cestě ke složce můžete použít ke kopírování dat ze složky, která je určena za běhu na základě [systémových proměnných](data-factory-functions-variables.md#data-factory-system-variables). Podporované proměnné jsou: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** a **{custom}**. Příklad: inputfolder/{year}/{month}/{day}.

Předpokládejme, že máte vstupní složky v následujícím formátu:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klepněte na tlačítko **Procházet** **pro soubor nebo složku**, vyhledejte jednu z těchto složek (například 2016->03->01->02) a klepněte na tlačítko **Vybrat**. Měli byste `2016/03/01/02` vidět v textovém poli. Nyní nahraďte **rok 2016** **položkou {year}**, **03** s **{month}**, **01** s **{day}** a **02** s **{hour}** a stiskněte klávesu **Tab.** Měli byste vidět rozevírací seznamy pro výběr formátu pro tyto čtyři proměnné:

![Použití systémových proměnných](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak je znázorněno na následujícím snímku obrazovky, můžete také použít **vlastní** proměnnou a všechny [podporované formátovací řetězce](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Chcete-li vybrat složku s tou strukturou, použijte nejprve tlačítko **Procházet.** Potom nahraďte hodnotu **hodnotou {custom}** a stisknutím **klávesy Tab** zobekďte textové pole, do kterého můžete zadat formátovací řetězec.     

![Použití vlastní proměnné](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Možnosti plánování
Operaci kopírování můžete spustit jednou nebo podle plánu (každou hodinu, denně a tak dále). Obě tyto možnosti lze použít pro šíři konektorů napříč prostředími, včetně místní, cloudové a místní kopie plochy.

Jednorázová operace kopírování umožňuje přesun dat ze zdroje do cíle pouze jednou. Vztahuje se na data libovolné velikosti a podporovaného formátu. Naplánovaná kopie umožňuje kopírovat data při předepsaném opakování. K konfiguraci naplánované kopie můžete použít rozšířené nastavení (například opakování, časový čas a výstrahy).

![Vlastnosti plánování](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Další kroky
Rychlý návod k vytvoření kanálu s aktivitou kopírování pomocí Průvodce kopírováním v datové továrně najdete v [tématu Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).
