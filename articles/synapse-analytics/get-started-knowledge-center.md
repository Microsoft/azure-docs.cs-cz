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
ms.date: 04/04/2021
ms.openlocfilehash: a26f46da7b392bd3b4a49aacb360a4c6147f8d2c
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382636"
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

> [!div class="nextstepaction"]
> [Přidat správce](get-started-add-admin.md)

