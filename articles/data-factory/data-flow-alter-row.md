---
title: Azure Data Factory mapování toku dat při změně řádku
description: Postup aktualizace cíle databáze pomocí Azure Data Factory mapování datového toku změna transformace řádku
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fff08b3e046161fbedefdc55f4e6a39a7f965f80
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72387270"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure Data Factory ALTER Row transformovat

Použijte transformaci ALTER Row k nastavení zásad vložení, odstranění, aktualizace a Upsert na řádcích. Jako výrazy můžete přidat podmínky 1: n. Tyto podmínky by měly být zadány v pořadí podle priority, protože každý řádek bude označen zásadou odpovídající výrazu, který odpovídá prvnímu. Každá z těchto podmínek může mít za následek vložení, aktualizaci, odstranění nebo upserted řádku (nebo řádků). Příkaz ALTER Row může pro vaši databázi vyvolat i akci DDL & DML.



![Změnit nastavení řádku](media/data-flow/alter-row1.png "Změnit nastavení řádku")

> [!NOTE]
> Příkaz ALTER Row transformes bude fungovat jenom u jímky databáze v toku dat. Akce, které přiřadíte do řádků (INSERT, Update, DELETE, Upsert), nebudou během relace ladění provedeny. Do kanálu musíte přidat úlohu spustit tok dat a pomocí ladění kanálu nebo triggerů můžete v databázových tabulkách přijmout zásady ALTER Row.

## <a name="indicate-a-default-row-policy"></a>Označení výchozích zásad řádků

Vytvořte transformaci ALTER Row a zadejte zásadu řádku s podmínkou `true()`. Každý řádek, který nesplňuje žádný z dříve definovaných výrazů, bude označen pro zadané zásady řádků. Ve výchozím nastavení budou všechny řádky, které nesplňují žádný podmíněný výraz, označeny pro `Insert`.

![Změnit řádek jedna zásada](media/data-flow/alter-row4.png "Změnit řádek jedna zásada")

> [!NOTE]
> Pokud chcete označit všechny řádky jednou zásadou, můžete pro tuto zásadu vytvořit podmínku a určit podmínku jako `true()`.

## <a name="view-policies"></a>Zobrazit zásady

Zapnout režim ladění toku dat, aby se zobrazily výsledky zásad ALTER Row v podokně náhledu dat. Provedení příkazu ALTER Row v režimu ladění toku dat nevygeneruje knihovny DDL ani DML v rámci vašeho cíle. Aby tyto akce probíhaly, spusťte tok dat v rámci aktivity toku dat spuštění v rámci kanálu.

![Změna zásad řádků](media/data-flow/alter-row3.png "Změna zásad řádků")

To vám umožní ověřit a zobrazit stav každého řádku na základě vašich podmínek. K dispozici je ikona pro každou akci vložení, aktualizace, odstranění a Upsert, ke které dojde v toku dat, což znamená, která akce bude probíhat při spuštění toku dat uvnitř kanálu.

## <a name="sink-settings"></a>Nastavení jímky

Chcete-li změnit řádek na práci, je nutné mít typ jímky databáze. V nastavení jímky byste měli nastavit všechny akce, které odpovídají vašim podmínkám pro změnu řádku, které mají být povoleny.

![Změnit jímku řádků](media/data-flow/alter-row2.png "Změnit jímku řádků")

Výchozím chováním při toku dat ADF pomocí jímky databáze je vložení řádků. Pokud chcete, aby byly povoleny aktualizace, upsertuje a DELETE, je také nutné zaškrtnout políčka v jímky, aby byly akce povoleny.

> [!NOTE]
> Pokud vaše vložení, aktualizace nebo upsertuje upraví schéma cílové tabulky v jímky, tok dat se nezdaří. Chcete-li změnit cílové schéma v databázi, je nutné zvolit možnost znovu vytvořit tabulku v jímky. Tím se vynechá a znovu vytvoří tabulka s novou definicí schématu.

## <a name="next-steps"></a>Další kroky

Po transformaci ALTER Row můžete chtít [data zajímky do cílového úložiště dat](data-flow-sink.md).
