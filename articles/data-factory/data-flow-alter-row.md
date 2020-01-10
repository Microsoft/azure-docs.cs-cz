---
title: Změna transformace řádku v toku dat mapování
description: Postup aktualizace cíle databáze pomocí transformace ALTER Row v toku dat při mapování
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 0798a3f9ab45ce68086681e7aea96deeb9639f94
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834543"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Změna transformace řádku v toku dat mapování

Použijte transformaci ALTER Row k nastavení zásad vložení, odstranění, aktualizace a Upsert na řádcích. Jako výrazy můžete přidat podmínky 1: n. Tyto podmínky by měly být zadány v pořadí podle priority, protože každý řádek bude označen zásadou odpovídající výrazu, který odpovídá prvnímu. Každá z těchto podmínek může mít za následek vložení, aktualizaci, odstranění nebo upserted řádku (nebo řádků). Příkaz ALTER Row může pro vaši databázi vyvolat i akci DDL & DML.

![Změnit nastavení řádku](media/data-flow/alter-row1.png "Změnit nastavení řádku")

Příkaz ALTER Row transformes bude pracovat jenom s databázemi nebo CosmosDB jímkami v toku dat. Akce, které přiřadíte k řádkům (vložení, aktualizace, odstranění, Upsert), nebudou během relace ladění provedeny. Spuštěním aktivity spustit tok dat v kanálu můžete v databázových tabulkách přijmout zásady ALTER Row.

## <a name="specify-a-default-row-policy"></a>Zadání výchozích zásad řádků

Vytvořte transformaci ALTER Row a zadejte zásadu řádku s podmínkou `true()`. Každý řádek, který neodpovídá žádnému z dříve definovaných výrazů, bude označen pro zadané zásady řádků. Ve výchozím nastavení budou všechny řádky, které neodpovídají žádnému podmíněnému výrazu, označeny pro `Insert`.

![Změnit zásady řádků](media/data-flow/alter-row4.png "Změnit zásady řádků")

> [!NOTE]
> Pokud chcete označit všechny řádky jednou zásadou, můžete pro tuto zásadu vytvořit podmínku a určit podmínku jako `true()`.

## <a name="view-policies-in-data-preview"></a>Zobrazit zásady v náhledu dat

Použijte [režim ladění](concepts-data-flow-debug-mode.md) k zobrazení výsledků zásad ALTER Row v podokně Náhled dat. Data ve verzi Preview transformace ALTER Row nepřinesou v cíli akce DDL ani DML.

![Změna zásad řádků](media/data-flow/alter-row3.png "Změna zásad řádků")

Jednotlivé zásady pro změnu řádků jsou reprezentované ikonou, která indikuje, jestli se má provést akce INSERT, Update, Upsert nebo Deleted. V horním záhlaví se zobrazuje počet řádků ovlivněných každou zásadou ve verzi Preview.

## <a name="allow-alter-row-policies-in-sink"></a>Povoluje změnu zásad řádků v jímky.

Aby fungovaly změny zásad řádků, musí datový proud zapisovat do databáze nebo Cosmos jímky. Na kartě **Nastavení** v jímky povolte, které změny zásad řádků jsou pro tuto jímku povoleny.

![Změnit jímku řádků](media/data-flow/alter-row2.png "Změnit jímku řádků")

 Výchozím chováním je povolení vkládání pouze. Pokud chcete povolení aktualizací, upsertuje nebo odstranění, zaškrtněte políčko v jímky odpovídající této podmínce. Pokud jsou povoleny aktualizace, upsertuje nebo, je třeba určit, které klíčové sloupce se mají v jímky shodovat.

> [!NOTE]
> Pokud vaše vložení, aktualizace nebo upsertuje upraví schéma cílové tabulky v jímky, tok dat se nezdaří. Chcete-li upravit cílové schéma v databázi, vyberte možnost **znovu vytvořit tabulku** jako akci tabulky. Tím se vynechá a znovu vytvoří tabulka s novou definicí schématu.

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

### <a name="example"></a>Příklad:

Níže uvedený příklad je změna transformace řádku s názvem `CleanData`, která přebírá příchozí datový proud `SpecifyUpsertConditions` a vytváří tři stavy ALTER Row. V předchozí transformaci je vypočítán sloupec s názvem `alterRowCondition`, který určuje, zda je řádek vložen, aktualizován nebo odstraněn v databázi. Pokud má hodnota sloupce hodnotu řetězce, která odpovídá pravidlu ALTER Row, přiřadí se tato zásada.

V uživatelském prostředí Data Factory Tato transformace vypadá jako na následujícím obrázku:

![Příklad ALTER Row](media/data-flow/alter-row4.png "Příklad ALTER Row")

Skript toku dat pro tuto transformaci je v následujícím fragmentu kódu:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Další kroky

Po transformaci ALTER Row můžete chtít [data zajímky do cílového úložiště dat](data-flow-sink.md).
