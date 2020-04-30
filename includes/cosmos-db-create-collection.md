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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "77780456"
---
Nyní můžete použít nástroj Průzkumník dat v Azure Portal k vytvoření databáze a kontejneru. 

1. Vyberte **Průzkumník dat** > **Nový kontejner**. 
    
    Úplně vpravo se zobrazí oblast **Přidat kontejner** , možná se budete muset posunout doprava, aby se zobrazila.

    ![Průzkumník dat na portálu Azure Portal – podokno Přidat kontejner](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. Na stránce **Přidat kontejner** zadejte nastavení pro nový kontejner.

    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|Úlohy|Jako název nové databáze zadejte *Tasks*. Názvy databází musí obsahovat 1 až 255 znaků a nesmí obsahovat `/, \\, #, ?`ani mezeru. Ověřte možnost **zřízení propustnosti databáze** , která umožňuje sdílení propustnosti zřízené do databáze napříč všemi kontejnery v rámci databáze. Tato možnost také pomáhá při úsporách nákladů. |
    |**Propustnost**|400|Nechte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.| 
    |**ID kontejneru**|Items|Jako název nového kontejneru zadejte *položky* . Pro ID kontejnerů platí stejné požadavky týkající se použitých znaků jako pro názvy databází.|
    |**Klíč oddílu**| /kategorie| Ukázka popsaná v tomto článku používá jako klíč oddílu */Category* .|
    
    Kromě předchozích nastavení můžete volitelně přidat **jedinečné klíče** pro kontejner. V tomto příkladu ponecháme toto pole prázdné. Jedinečné klíče umožňují vývojářům přidat do databáze vrstvu integrity dat. Vytvořením jedinečné zásady klíčů při vytváření kontejneru zajistíte jedinečnost jedné nebo více hodnot na klíč oddílu. Další informace najdete v článku [Jedinečné klíče ve službě Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner.