---
title: Snadné kopírování dat pomocí Průvodce kopírováním – Azure
description: Přečtěte si, jak pomocí Průvodce kopírováním datové továrny kopírovat ke kopírování dat z podporovaných zdrojů dat do propadů.
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
ms.openlocfilehash: 8b872cc30ae66e83274f189138dad6d609e3f536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927054"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Snadné kopírování nebo přesouvání dat pomocí Průvodce kopírováním dat azure
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, podívejte se na [kurz o aktivitě kopírování](../quickstart-create-data-factory-dot-net.md). 


Průvodce kopírováním azure data factory je usnadnit proces ingestování dat, což je obvykle prvním krokem ve scénáři integrace dat od konce do konce. Při procházení Průvodce kopírováním datové továrny Azure nemusíte rozumět žádným definicím JSON pro propojené služby, datové sady a kanály. Po dokončení všech kroků v průvodci však průvodce automaticky vytvoří kanál pro kopírování dat z vybraného zdroje dat do vybraného cíle. Průvodce kopírováním vám navíc pomůže ověřit, že data jsou ingestována v době vytváření, což šetří většinu času, zejména při prvním ingestování dat ze zdroje dat. Chcete-li spustit Průvodce kopírováním, klikněte na domovské stránce datové továrny na dlaždici **Kopírovat data.**

![Průvodce kopírováním](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Intuitivní průvodce kopírováním dat
Tento průvodce umožňuje snadno přesunout data z široké škály zdrojů do cílů během několika minut. Po procházení průvodcem se automaticky vytvoří kanál s aktivitou kopírování spolu se závislými entitami Data Factory (propojené služby a datové sady). K vytvoření kanálu nejsou nutné žádné další kroky.   

![Výběr zdroje dat](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Podrobný článek [průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) k vytvoření ukázkového kanálu pro kopírování dat z objektu blob Azure do tabulky Databáze Azure SQL. 
> 
> 

Průvodce je navržen s ohledem na velká data od začátku. Je jednoduché a efektivní vytvářet kanály Data Factory, které přesouvají stovky složek, souborů nebo tabulek pomocí Průvodce kopírováním dat. Průvodce podporuje následující tři funkce: Automatický náhled dat, sběr a mapování schématu a filtrování dat. 

## <a name="automatic-data-preview"></a>Automatický náhled dat
Průvodce kopírováním umožňuje zkontrolovat část dat z vybraného zdroje dat a ověřit, zda se jedná o správná data, která chcete zkopírovat. Kromě toho, pokud jsou zdrojová data v textovém souboru, průvodce kopírováním analyzuje textový soubor, aby se naučil oddělovače řádků a sloupců, a schéma automaticky. 

![Nastavení formátu souboru](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Zachycení a mapování schématu
Schéma vstupních dat nemusí odpovídat schématu výstupních dat v některých případech. V tomto scénáři je třeba mapovat sloupce ze zdrojového schématu do sloupců z cílového schématu. 

Průvodce kopírováním automaticky mapuje sloupce ve zdrojovém schématu na sloupce v cílovém schématu. Mapování můžete přepsat pomocí rozevíracích seznamů (nebo) určit, zda má být sloupec při kopírování dat přeskočen.   

![Mapování schématu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrování dat
Průvodce umožňuje filtrovat zdrojová data a vybrat pouze data, která je třeba zkopírovat do úložiště dat cílového/jímky. Filtrování snižuje objem dat, která mají být zkopírována do úložiště dat jímky a proto zvyšuje propustnost operace kopírování. Poskytuje flexibilní způsob filtrování dat v relační databázi pomocí dotazovacího jazyka SQL (nebo) souborů ve složce objektů blob Azure pomocí [funkcí a proměnných Data Factory](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrování dat v databázi
V příkladu dotaz SQL `Text.Format` používá `WindowStart` funkci a proměnnou. 

![Ověřit výrazy](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrování dat ve složce objektů blob Azure
Proměnné v cestě ke složce můžete použít ke kopírování dat ze složky, která je určena za běhu na základě [systémových proměnných](data-factory-functions-variables.md#data-factory-system-variables). Podporované proměnné jsou: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** a **{custom}**. Příklad: inputfolder/{year}/{month}/{day}.

Předpokládejme, že máte vstupní složky v následujícím formátu:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Klepněte na tlačítko **Procházet** **pro soubor nebo složku**, vyhledejte jednu z těchto složek (například 2016->03->01->02) a klepněte na tlačítko **Vybrat**. Měli byste `2016/03/01/02` vidět v textovém poli. Nyní nahraďte **rok 2016** **položkou {year}**, **03** s **{month}**, **01** s **{day}** a **02** s **{hour}** a stiskněte klávesu Tab. Měli byste vidět rozevírací seznamy pro výběr formátu pro tyto čtyři proměnné:

![Použití systémových proměnných](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak je znázorněno na následujícím snímku obrazovky, můžete také použít **vlastní** proměnnou a všechny [podporované formátovací řetězce](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Chcete-li vybrat složku s tou strukturou, použijte nejprve tlačítko **Procházet.** Potom nahraďte hodnotu **hodnotou {custom}** a stisknutím klávesy Tab zodvaáše textové pole, do kterého můžete zadat formátovací řetězec.     

![Použití vlastní proměnné](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Podpora různých typů dat a objektů
Pomocí Průvodce kopírováním můžete efektivně přesunout stovky složek, souborů nebo tabulek.

![Výběr tabulek, ze kterých chcete kopírovat data](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Možnosti plánování
Operaci kopírování můžete spustit jednou nebo podle plánu (každou hodinu, denně a tak dále). Obě tyto možnosti lze použít pro šíři konektorů napříč místní, cloudovou a místní kopií plochy.

Jednorázová operace kopírování umožňuje přesun dat ze zdroje do cíle pouze jednou. Vztahuje se na data libovolné velikosti a podporovaného formátu. Naplánovaná kopie umožňuje kopírovat data při předepsaném opakování. K konfiguraci naplánované kopie můžete použít rozšířené nastavení (například opakování, časový čas a výstrahy).

![Vlastnosti plánování](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Další kroky
Rychlý návod k vytvoření kanálu s aktivitou kopírování pomocí Průvodce kopírováním v datové továrně najdete v [tématu Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

