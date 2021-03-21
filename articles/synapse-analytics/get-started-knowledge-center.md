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
ms.date: 11/16/2020
ms.openlocfilehash: 59364a0a2c1d1e411f41dc35dee812c8e920b72d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98209198"
---
# <a name="explore-the-synapse-knowledge-center"></a>Prozkoumejte centrum synapse Knowledge Center

V tomto kurzu se naučíte používat centrum znalostí synapse Studio.

## <a name="getting-to-the-knowledge-center"></a>Získání centra znalostí

Existují dva způsoby, jak najít centrum znalostí v synapse studiu:

  1. V domovském centru klikněte v pravém horním rohu stránky na **Další informace**.
  2. V řádku nabídek nahoře klikněte na **?** a pak **Centrum znalostí**.

Vyberte jednu z metod a otevřete **Centrum znalostí**.

## <a name="overview"></a>Přehled

**Centrum znalostí** vám umožňuje provádět tři věci:
* **Používejte vzorky hned**. Pokud potřebujete rychlý příklad, jak synapse funguje, vyberte tuto možnost.
* **Procházet galerii**. Tato možnost umožňuje propojit ukázkové sady dat a přidat vzorový kód ve formě skriptů SQL, poznámkových bloků a kanálů.
* **Projděte si synapse Studio**. Tato možnost vás provede krátkou prohlídku základních částí synapse studia. To je užitečné, pokud jste ještě nikdy nepoužili synapse Studio.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Prozkoumávání služby Blob Storage s neserverovým fondem SQL

1. Přejděte do **centra znalostí** a ihned klikněte na možnost **použít vzorky**.
1. Vyberte **data dotazu pomocí SQL**.
1. Klikněte na **použít ukázku**.
1. Otevře se nový vzorový skript SQL.
1. Posuňte se na první dotaz (řádky 28 až 32) a vyberte text dotazu.
1. Klikněte na Spustit. Spustí se jenom kód, který jste vybrali.

## <a name="loading-more-nyc-taxi-data"></a>Načítají se další data taxislužby NYC.
1. Přejděte do **centra znalostí** a klikněte na **Procházet galerii**.
1. V horní části vyberte kartu **skripty SQL** .
1. Vyberte načíst ukázku příjmu dat datové **sady taxislužby města New York** a klikněte na **pokračovat**.
1. V části **fond SQL** zvolte **Vybrat existující fond** a vyberte **SQLPOOL1** a vyberte databázi **SQLPOOL1** , kterou jste vytvořili dříve.
1. Klikněte na **otevřít skript**.
1. Otevře se nový vzorový skript SQL.
1. Klikněte na **Spustit** .
1. Tím se vytvoří několik tabulek pro všechna data NYC taxislužby a načtou se pomocí příkazu T-SQL COPY. Pokud jste tyto tabulky vytvořili v předchozích krocích rychlého spuštění, vyberte a spusťte pouze kód pro vytvoření a ZKOPÍROVÁNí tabulek, které neexistují.

    > [!NOTE] 
    > Pokud používáte ukázkovou galerii pro skript SQL s vyhrazeným fondem SQL (dřív SQL DW), budete moct použít jenom existující vyhrazený fond SQL (dřív SQL DW).

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure Synapse Analytics](get-started.md)
* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použití bezserverového fondu SQL](quickstart-sql-on-demand.md)
