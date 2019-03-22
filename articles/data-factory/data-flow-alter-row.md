---
title: Azure Data Factory mapování transformace Alter řádek toku dat
description: Postup aktualizace cílové databáze pomocí Azure Data Factory mapování toku Alter řádek transformace dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: d842898ca700490ae99b46140be6609622a144df
ms.sourcegitcommit: 5f41e855d415cfa741d8f710792ea486480df5cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58133157"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure Data Factory Alter řádek transformace

Pomocí transformace Alter řádek můžete nastavit zásady vložit, odstranit, aktualizace a upsert řádků. Můžete přidat podmínky 1 n jako výrazy. Každá z těchto podmínek může vést k řádku (nebo řádky) vložit, aktualizovat, odstranit nebo upsert. Příkaz ALTER řádek může vytvořit akce DDL a jazyk DML na vaší databázi.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Změnit nastavení řádku](media/data-flow/alter-row1.png "měnit nastavení řádku")

> [!NOTE]
> Příkaz ALTER řádek transformace bude pracovat pouze s database jímky ve svém toku dat. Akce, které přiřadíte k řádkům (insert, update, delete, upsertovat) nedojde během relace ladění. Musíte přidat úkol spuštění toku dat pro kanál a přijmout zásady řádek alter na databázových tabulek pomocí kanálu ladění nebo aktivační události.

## <a name="view-policies"></a>Zobrazit zásady

Přepněte na režim datový tok ladění a pak zobrazte výsledky zásad alter řádků v podokně dat ve verzi Preview. Provádění na alter řádek v režimu datového toku ladit akce DDL nebo DML s vaší cílovou nevytvoří. Pokud chcete mít tyto akce dojde k provedení toku dat uvnitř spuštění toku dat aktivitu v kanálu.

![Příkaz ALTER řádek zásady](media/data-flow/alter-row3.png "změnit zásady řádek")

To vám umožní ověřit a zobrazit stav jednotlivých řádků na základě vašich podmínek. Jsou ikona představuje pro každý insert, update, delete a upsert akce, která bude vytvářena v toku dat, která akci, která se provede při spuštění toku dat v kanálu.

## <a name="sink-settings"></a>Nastavení jímky

Musí mít databázi jímky typu pro řádek Alter pro práci. V nastavení jímky je nutné nastavit každou akci povolit.

![Příkaz ALTER řádek jímky](media/data-flow/alter-row2.png "Alter řádek jímky")

Výchozí chování v toku dat ADF jímky databáze je pro vložení řádků. Pokud chcete povolit aktualizace, upsertuje a také odstraní, musí taky zaškrtnout těchto polí v jímce umožňující akce.

> [!NOTE]
> Pokud operace vložení, aktualizace nebo upsertuje upravit schéma cílové tabulky v jímce, váš tok dat se nezdaří. Aby bylo možné upravit na cílové schéma v databázi, musíte zvolit možnost "Vytvořte tabulku" v jímce. To se vyřadit a znovu vytvoří tabulky s novou definici schématu.

## <a name="next-steps"></a>Další postup

Po transformaci řádek Alter, možná budete chtít [do cílového úložiště dat jímky dat](data-flow-sink.md).
