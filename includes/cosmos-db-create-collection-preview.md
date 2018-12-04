---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/19/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: c3cbfda674abaeea1adf35c3ee0d2b5ddf6b2f84
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52853036"
---
Teď můžete pomocí nástroje Průzkumník dat na webu Azure Portal vytvořit databázi a kolekci. 

1. Klikněte na **Průzkumník dat** > **Nová kolekce**. 
    
    Úplně vpravo se zobrazí oblast **Přidat kolekci**. Pokud ji nevidíte, možná se budete muset posunout doprava.

    ![Průzkumník dat na webu Azure Portal – okno Přidat kolekci](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. Na stránce **Přidat kolekci** zadejte nastavení pro novou kolekci.

    Nastavení|Navrhovaná hodnota|Popis
    ---|---|---
    ID databáze|*Úlohy*|Jako název nové databáze zadejte *Tasks*. Názvy databází musí mít délku 1 až 255 znaků a nesmí obsahovat znaky /, \\, #, ? ani koncové mezery.
    ID kolekce|*Položky*|Jako název nové kolekce zadejte *Items*. ID kolekcí mají stejné požadavky na znaky jako názvy databází.
    Zřízení propustnosti databáze|Ponechte prázdné|Azure Cosmos DB může zřídit propustnost na úrovni databáze (všechny kolekce v databázi sdílet stejnou propustnost) nebo na úrovni kolekce. Ponechte prázdné zřizování propustnosti na úrovni kolekce pro tuto konkrétní kolekci.
    Kapacita úložiště|*Unlimited*|Vyberte úložnou kapacitu **Unlimited**. 
    Klíč oddílu|*/ category*|Zadejte "/ category" jako klíč oddílu. Nastavení klíče oddílu umožňuje službě Azure Cosmos DB škálovat vaši kolekci pro potřeby úložiště a propustnost vaší aplikace. Obecně je dobrou volbou klíč oddílu, pokud má širokou škálu různých hodnot a výsledky v rovnoměrná distribuce objemu úložiště a žádost o celé zatížení. [Další informace o dělení.](../articles/cosmos-db/partitioning-overview.md)
    Propustnost|*400 RU/s*|Změňte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit. 
    
    Kromě předchozích nastavení můžete pro kolekci volitelně přidat **Jedinečné klíče**. V tomto příkladu ponecháme toto pole prázdné. Jedinečné klíče umožňují vývojářům přidat do databáze vrstvu integrity dat. Vytvořením zásady jedinečného klíče při vytváření kolekce zajistíte jedinečnost jedné nebo více hodnot pro každý klíč oddílu. Další informace najdete v článku [Jedinečné klíče ve službě Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Klikněte na **OK**.

    Průzkumník dat zobrazí novou databázi a kolekci.

    ![Průzkumník dat na webu Azure Portal zobrazující novou databázi a kolekci](./media/cosmos-db-create-collection/azure-cosmos-db-data-explorer-preview.png)