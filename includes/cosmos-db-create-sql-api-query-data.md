---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: e6e70da1f939547cdb589ae7bcb6ed1f6148f22e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38733761"
---
Teď můžete k načítání a filtrování dat používat dotazy v Průzkumníku dat.

1. Všimněte si, že ve výchozím nastavení je dotaz nastavený na `SELECT * FROM c`. Tento výchozí dotaz načte a zobrazí všechny dokumenty v kolekci. 

    ![Výchozí dotaz v Průzkumníku dat je „SELECT * FROM c“](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)

2. Zůstaňte na kartě **Dokumenty** a změňte dotaz tak, že kliknete na tlačítko **Upravit filtr**, do pole predikátu dotazu přidáte `ORDER BY c._ts DESC` a kliknete na **Použít filtr**.

    ![Změna výchozího dotazu přidáním „ORDER BY c._ts DESC“ a kliknutím na Použít filtr](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

Tento upravený dotaz vypíše dokumenty v sestupném pořadí na základě jejich časového razítka, takže teď je jako první uvedený váš druhý dokument. Pokud jste obeznámeni se syntaxí jazyka SQL, můžete do tohoto pole zadat jakýkoli z podporovaných [příkazů jazyka SQL](../articles/cosmos-db/sql-api-sql-query.md). 

Tím končí naše práce v Průzkumníku dat. Než se přesuneme k práci s kódem, upozorňujeme, že Průzkumník dat můžete použít také k vytváření uložených procedur, funkcí UDF a triggerů pro provádění obchodní logiky na straně serveru a také škálování propustnosti. Průzkumník dat zpřístupní všechna integrovaná programová data v rozhraních API, ale zajistí jednoduchý přístup k vašim datům na portálu Azure Portal.