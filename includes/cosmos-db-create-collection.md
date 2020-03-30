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
ms.openlocfilehash: 9bc5be37f3892186233fac197c08066dbfacb43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77780456"
---
Teď můžete použít nástroj Průzkumník dat na webu Azure Portal k vytvoření databáze a kontejneru. 

1. Vyberte nový kontejner **průzkumníka** > **dat**. 
    
    Oblast **Přidat kontejner** se zobrazí zcela vpravo, možná budete muset posunout doprava, abyste ji viděli.

    ![Průzkumník dat na portálu Azure Portal – podokno Přidat kontejner](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. Na stránce **Přidat kontejner** zadejte nastavení nového kontejneru.

    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|Úlohy|Jako název nové databáze zadejte *Tasks*. Názvy databází musí obsahovat 1 až 255 znaků a nemohou obsahovat `/, \\, #, ?`nebo koncové mezery. Zkontrolujte možnost **Propustnost zřizování databáze,** umožňuje sdílet propustnost zřízenou do databáze ve všech kontejnerech v databázi. Tato možnost také pomáhá s úsporami nákladů. |
    |**Propustnost**|400|Ponechte propustnost na 400 jednotek požadavku za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.| 
    |**ID kontejneru**|Items|Jako název nového kontejneru zadejte *položky.* Pro ID kontejnerů platí stejné požadavky týkající se použitých znaků jako pro názvy databází.|
    |**Klíč oddílu**| /kategorie| Ukázka popsaná v tomto článku používá */category* jako klíč oddílu.|
    
    Kromě předchozínastavení můžete volitelně přidat **jedinečné klíče** pro kontejner. V tomto příkladu ponecháme toto pole prázdné. Jedinečné klíče umožňují vývojářům přidat do databáze vrstvu integrity dat. Vytvořením zásady jedinečného klíče při vytváření kontejneru zajistíte jedinečnost jedné nebo více hodnot na klíč oddílu. Další informace najdete v článku [Jedinečné klíče ve službě Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner.