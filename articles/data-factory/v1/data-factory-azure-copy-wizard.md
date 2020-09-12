---
title: Průvodce kopírováním Data Factory Azure
description: Přečtěte si, jak pomocí Průvodce kopírováním Azure Data Factory zkopírovat data z podporovaných zdrojů dat do jímky.
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
ms.openlocfilehash: 2dfb5876922fd53c372afe82ecdfa843179fb135
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89439006"
---
# <a name="azure-data-factory-copy-wizard"></a>Průvodce kopírováním Azure Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

Průvodce kopírováním Azure Data Factory usnadňuje proces ingestování dat, což je obvykle první krok v uceleném scénáři integrace dat. Když projdete průvodcem kopírováním Azure Data Factory, nemusíte pochopit žádné definice JSON pro propojené služby, datové sady a kanály. Průvodce automaticky vytvoří kanál pro kopírování dat z vybraného zdroje dat do vybraného cíle. Kromě toho vám Průvodce kopírováním pomůže ověřit data, která se ingestují v době vytváření. Tím ušetříte čas, zejména při první ingestování dat ze zdroje dat. Průvodce kopírováním spustíte tak, že kliknete na dlaždici **Kopírovat data** na domovské stránce vaší datové továrny.

![Průvodce kopírováním](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Navrženo pro velké objemy dat
Tento průvodce vám umožní snadno přesunout data z nejrůznějších zdrojů do cílových míst během několika minut. Po absolvování průvodce se automaticky vytvoří kanál s aktivitou kopírování, spolu se závislými Data Factory entitami (propojené služby a datové sady). K vytvoření kanálu nejsou nutné žádné další kroky.   

![Výběr zdroje dat](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Podrobné pokyny k vytvoření ukázkového kanálu pro kopírování dat z objektu blob Azure do tabulky Azure SQL Database najdete v [kurzu Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).
>
>

Průvodce je navržen s velkými objemy dat od začátku, s podporou pro různé typy dat a objektů. Můžete vytvářet Data Factory kanály, které přesouvají stovky složek, souborů nebo tabulek. Průvodce podporuje automatickou náhled dat, zachytávání schématu a mapování a filtrování dat.

## <a name="automatic-data-preview"></a>Náhled automatických dat
Můžete zobrazit náhled části dat z vybraného zdroje dat a ověřit tak, zda jsou data co chcete kopírovat. Kromě toho, pokud jsou zdrojová data v textovém souboru, Průvodce kopírováním analyzuje textový soubor, aby se dozvěděl oddělovače řádků a sloupců a schéma automaticky.

![Nastavení formátu souboru](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Zachytávání a mapování schématu
Schéma vstupních dat nemusí v některých případech odpovídat schématu výstupních dat. V tomto scénáři je nutné mapovat sloupce ze zdrojového schématu na sloupce z cílového schématu.

> [!TIP]
> Při kopírování dat z SQL Server nebo Azure SQL Database do služby Azure synapse Analytics (dříve SQL Data Warehouse), pokud tabulka v cílovém úložišti neexistuje, Data Factory podpora automatického vytváření tabulek pomocí schématu zdroje. Přečtěte si další informace z [přesunu dat do a z Azure synapse Analytics pomocí Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).
>

Pomocí rozevíracího seznamu vyberte sloupec ze zdrojového schématu, který chcete namapovat na sloupec v cílovém schématu. Průvodce kopírováním se pokusí pochopit vzor pro mapování sloupců. Aplikuje stejný vzor na zbytek sloupců, takže nemusíte jednotlivě vybírat jednotlivé sloupce pro dokončení mapování schématu. Pokud chcete, můžete tato mapování přepsat pomocí rozevíracích seznamů a namapovat sloupce jeden po jedné. Vzor je přesnější při mapování více sloupců. Průvodce kopírováním průběžně aktualizuje vzor a nakonec dosáhne správného vzoru mapování sloupců, které chcete dosáhnout.     

![Mapování schématu](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtrování dat
Můžete filtrovat zdrojová data a vybrat pouze data, která je třeba zkopírovat do úložiště dat jímky. Filtrování omezuje objem dat, která mají být zkopírována do úložiště dat jímky, a tím zvyšuje propustnost operace kopírování. Poskytuje flexibilní způsob, jak filtrovat data v relační databázi pomocí dotazovacího jazyka SQL nebo souborů ve složce objektů BLOB v Azure pomocí [Data Factory funkcí a proměnných](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filtrování dat v databázi
Následující snímek obrazovky ukazuje dotaz SQL pomocí `Text.Format` funkce a `WindowStart` proměnné.

![Ověřit výrazy](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtrování dat ve složce Azure Blob
Proměnné v cestě ke složce můžete použít ke kopírování dat ze složky, která je určena za běhu na základě [systémových proměnných](data-factory-functions-variables.md#data-factory-system-variables). Podporované proměnné jsou: **{year}**, **{month}**, **{Day}**, **{Hour}**, **{minute}** a **{Custom}**. Příklad: inputfolder/{Year}/{Month}/{Day}.

Předpokládejme, že máte vstupní složky v následujícím formátu:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klikněte na tlačítko **Procházet** pro **soubor nebo složku**, vyhledejte jednu z těchto složek (například 2016->03->01->02) a klikněte na **zvolit**. Mělo by se zobrazit `2016/03/01/02` v textovém poli. Nyní nahraďte **2016** **řetězcem** **{year}**, **03** a **{month}**, **01** s **{Day}** a **02** a stiskněte klávesu **TAB** . Měli byste vidět rozevírací seznamy a vybrat formát pro tyto čtyři proměnné:

![Použití systémových proměnných](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Jak je znázorněno na následujícím snímku obrazovky, můžete také použít **vlastní** proměnnou a všechny [podporované formátovací řetězce](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Chcete-li vybrat složku s touto strukturou, použijte nejprve tlačítko **Procházet** . Potom hodnotu nahraďte hodnotou **{Custom}** a stisknutím klávesy **TAB** zobrazte textové pole, ve kterém můžete zadat řetězec formátu.     

![Použití vlastní proměnné](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Možnosti plánování
Operaci kopírování můžete spustit jednou nebo podle plánu (každou hodinu, každý den atd.). Obě tyto možnosti lze použít pro šířku konektorů napříč prostředími, včetně místních, cloudových a místních kopií.

Jednorázová operace kopírování umožňuje přesun dat ze zdroje do cíle pouze jednou. Platí pro data libovolné velikosti a veškerého podporovaného formátu. Naplánované kopírování umožňuje kopírovat data po předepsaném opakování. Ke konfiguraci naplánované kopie můžete použít bohatá nastavení (například opakování, časový limit a upozornění).

![Vlastnosti plánování](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Další kroky
Rychlý návod, jak pomocí Průvodce kopírováním Data Factory vytvořit kanál s aktivitou kopírování, najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).
