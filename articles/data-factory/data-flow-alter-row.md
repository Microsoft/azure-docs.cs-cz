---
title: Změna transformace řádků v toku dat mapování
description: Jak aktualizovat cíl databáze pomocí transformace změnit řádek v toku dat mapování
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 2923e087426ee04c74da629f4e2d2d49a06eb1ef
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416536"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Změna transformace řádků v toku dat mapování

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pomocí transformace Alter Row nastavte na řádcích zásady vložení, odstranění, aktualizace a upsert. Jako výrazy můžete přidat podmínky 1:N. Tyto podmínky by měly být specifikovány v pořadí podle priority, protože každý řádek bude označen zásadou odpovídající výrazu první hospo- odpovídající. Každá z těchto podmínek může mít za následek řádek (nebo řádky) vkládá, aktualizuje, odstraní nebo upserted. Alter Row může vytvořit obě akce DDL & DML proti databázi.

![Změnit nastavení řádků](media/data-flow/alter-row1.png "Změnit nastavení řádků")

Alter Row transformace bude fungovat pouze na databázi nebo CosmosDB jímky v toku dat. Akce, které přiřadíte řádkům (vložení, aktualizace, odstranění, upsert) nedojde během ladění relací. Spusťte aktivitu Spustit tok dat v kanálu a uzákoněte zásady změny řádků v databázových tabulkách.

## <a name="specify-a-default-row-policy"></a>Určení výchozí zásady řádku

Vytvořte transformaci Alter Row a zadejte `true()`zásadu řádku s podmínkou . Každý řádek, který neodpovídá žádnému z dříve definovaných výrazů, bude označen pro zadanou zásadu řádku. Ve výchozím nastavení bude každý řádek, který neodpovídá `Insert`žádnému podmíněnému výrazu, označen pro .

![Změnit zásadu řádku](media/data-flow/alter-row4.png "Změnit zásadu řádku")

> [!NOTE]
> Chcete-li označit všechny řádky jednou zásadou, můžete vytvořit podmínku pro tuto zásadu a zadat podmínku jako `true()`.

## <a name="view-policies-in-data-preview"></a>Zobrazení zásad ve verzi Preview dat

Režim [ladění](concepts-data-flow-debug-mode.md) slouží k zobrazení výsledků zásad změn řádků v podokně náhledu dat. Náhled dat transformace změnit řádek nebude vyrábět DDL nebo DML akce proti cíli.

![Změnit zásady řádků](media/data-flow/alter-row3.png "Změnit zásady řádků")

Každá zásada změnit řádek je reprezentována ikonou, která označuje, zda dojde k akci vložení, aktualizace, upsert nebo odstraněné. Horní záhlaví ukazuje, kolik řádků jsou ovlivněny jednotlivých zásad v náhledu.

## <a name="allow-alter-row-policies-in-sink"></a>Povolit změnu zásad řádků v jímce

Pro zásady změnit řádek pracovat datový proud musí zapisovat do databáze nebo Cosmos jímky. Na kartě **Nastavení** v umyvadle povolte, které zásady změny řádků jsou povoleny pro tento jímky.

![Změnit propad řádku](media/data-flow/alter-row2.png "Změnit propad řádku")

 Výchozí chování je povolit pouze vloží. Chcete-li povolit aktualizace, upserts nebo odstraní, zaškrtněte políčko v jímce odpovídající této podmínce. Pokud aktualizace, upserts nebo odstranění jsou povoleny, je nutné zadat, které klíčové sloupce v jímce, aby odpovídaly na.

> [!NOTE]
> Pokud vloží, aktualizace nebo upserts upravit schéma cílové tabulky v jímce, tok dat se nezdaří. Chcete-li upravit cílové schéma v databázi, zvolte **Znovu vytvořit tabulku** jako akci tabulky. Tím se přetáhne a znovu vytvoříte tabulku s novou definicí schématu.

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Příklad

Níže uvedený příklad je změna `CleanData` řádku transformace s `SpecifyUpsertConditions` názvem, který trvá příchozí datový proud a vytvoří tři změnit podmínky řádku. V předchozí transformaci je `alterRowCondition` vypočítán sloupec s názvem, který určuje, zda je řádek vložen, aktualizován nebo odstraněn v databázi. Pokud hodnota sloupce má hodnotu řetězce, která odpovídá pravidlo změnit řádek, je přiřazena tato zásada.

V ux datové továrny tato transformace vypadá jako následující obrázek:

![Změnit příklad řádku](media/data-flow/alter-row4.png "Změnit příklad řádku")

Skript toku dat pro tuto transformaci je ve fragmentu níže:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Další kroky

Po transformaci Změnit řádek můžete chtít [uřadit data do cílového úložiště dat](data-flow-sink.md).
