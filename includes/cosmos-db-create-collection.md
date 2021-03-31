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
ms.openlocfilehash: 076afcfc8abf4e90d3ccedbb26653656032e59be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85115379"
---
Nyní můžete použít nástroj Průzkumník dat v Azure Portal k vytvoření databáze a kontejneru. 

1. Vyberte **Průzkumník dat**  >  **Nový kontejner**. 
    
    Úplně vpravo se zobrazí oblast **Přidat kontejner** , možná se budete muset posunout doprava, aby se zobrazila.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png" alt-text="Průzkumník dat na webu Azure Portal s podoknem Přidat kontejner":::

2. Na stránce **Přidat kontejner** zadejte nastavení pro nový kontejner.

    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|Úlohy|Jako název nové databáze zadejte *Tasks*. Názvy databází musí obsahovat 1 až 255 znaků a nesmí obsahovat ani mezeru `/, \\, #, ?` . Ověřte možnost **zřízení propustnosti databáze** , která umožňuje sdílení propustnosti zřízené do databáze napříč všemi kontejnery v rámci databáze. Tato možnost také pomáhá při úsporách nákladů. |
    |**Propustnost**|400|Nechte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.| 
    |**ID kontejneru**|Položky|Jako název nového kontejneru zadejte *položky* . Pro ID kontejnerů platí stejné požadavky týkající se použitých znaků jako pro názvy databází.|
    |**Klíč oddílu**| /kategorie| Ukázka popsaná v tomto článku používá jako klíč oddílu */Category* .|
    
    Kromě předchozích nastavení můžete volitelně přidat **jedinečné klíče** pro kontejner. V tomto příkladu ponecháme toto pole prázdné. Jedinečné klíče umožňují vývojářům přidat do databáze vrstvu integrity dat. Vytvořením jedinečné zásady klíčů při vytváření kontejneru zajistíte jedinečnost jedné nebo více hodnot na klíč oddílu. Další informace najdete v článku [Jedinečné klíče ve službě Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner.