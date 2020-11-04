---
title: 'Kurz: Začínáme prozkoumat centrum znalostí pro synapse'
description: V tomto kurzu se naučíte používat synapse Knowledge Center.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 2857989fb09555ecd808bfdba95bcf58e4444594
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93304904"
---
# <a name="explore-the-synapse-knowledge-center"></a>Prozkoumejte centrum synapse Knowledge Center

V tomto kurzu se naučíte používat centrum znalostí synapse Studio.

## <a name="getting-to-the-knowledge-center"></a>Získání centra znalostí

Existují dva způsoby, jak najít centrum znalostí v synapse studiu:

  1. V centru domů v části užitečné odkazy klikněte na první odkaz s názvem **Centrum znalostí**.
  2. V řádku nabídek nahoře klikněte na **?** a pak  **Centrum znalostí**.

Vyberte jednu z metod a otevřete **Centrum znalostí**.

## <a name="overview"></a>Přehled

**Centrum znalostí** vám umožňuje provádět tři věci:
* **Používejte vzorky hned**. Tato možnost je optimalizovaná pro zobrazení analýz v akci co nejrychlejší. Pokud potřebujete rychlý příklad, jak synapse funguje, vyberte tuto možnost.
* **Ukázka dostupná v prohlížeči** Tato možnost umožňuje propojit ukázkové sady dat a přidat vzorový kód ve formě skriptů SQL, poznámkových bloků a kanálů.
* **Projděte si synapse Studio**. Tato možnost vás provede krátkou prohlídku základních částí synapse studia. To je užitečné, pokud jste ještě nikdy nepoužili synapse Studio.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Prozkoumávání služby Blob Storage s neserverovým fondem SQL

1. **Centrum znalostí** , klikněte na **použít ukázky hned**
1. Vybrat **data dotazu pomocí SQL** 
1. Kliknout na **použít ukázky hned**
1. Vytvoří se nový skript SQL.
1. Posuňte se na první dotaz (řádky 28 až 32) a vyberte text dotazu.
1. Klikněte na Spustit. Spustí se vybraný text.

## <a name="loading-more-nyc-taxi-data"></a>Načítají se další data taxislužby NYC.
1. **Centrum znalostí** klikněte na **Procházet dostupné ukázky** . 
1. Vyberte kartu **skripty SQL** v horní části.
1. Vyberte **načíst datovou sadu taxislužby města New York** .
1. V části **vstupy** zvolte **Vybrat existující fond** a pak vyberte **SQLDB1** .
1. Klikněte na **otevřít skript** .
1. Zobrazí se nový skript SQL.
1. Klikněte na **Spustit** .
1. Tím se vytvoří několik tabulek pro všechna data NYC taxislužby a načtou se pomocí příkazu T-SQL COPY.

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure synapse Analytics](get-started.md)
* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použít fond SQL bez serveru](quickstart-sql-on-demand.md)
