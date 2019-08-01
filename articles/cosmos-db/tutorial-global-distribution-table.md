---
title: Kurz pro globální distribuci Azure Cosmos DB pro rozhraní API pro tabulky
description: Zjistěte, jak nastavit globální distribuci služby Azure Cosmos DB pomocí rozhraní Table API.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/13/2018
ms.reviewer: sngun
ms.openlocfilehash: 8562d37d81ce02e150e6ad1cc2a440cf7bb1e5e3
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693358"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Nastavení globální distribuce služby Azure Cosmos DB pomocí rozhraní Table API

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní [Table API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Připojení k preferované oblasti pomocí rozhraní Table API

Aby mohly využívat [globální distribuci](distribute-data-globally.md), můžou mít klientské aplikace určený seřazený seznam preferovaných oblastí, které se mají použít k provádění operací s dokumenty. Můžete to provést nastavením vlastnosti [TableConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet). Sada SDK rozhraní Table API služby Azure Cosmos DB vybere nejlepší koncový bod pro komunikaci na základě konfigurace účtu, aktuální dostupnosti oblastí a zadaného seznamu preferencí.

Seznam PreferredLocations by měl obsahovat čárkami oddělený seznam upřednostňovaných (vícenásobné navádění) umístění pro čtení. Každá instance klienta může určit podmnožinu těchto oblastí v upřednostňovaném pořadí pro zajištění nízké latence čtení. Oblasti musí být pojmenované pomocí jejich [zobrazovaného názvu](https://msdn.microsoft.com/library/azure/gg441293.aspx), například `West US`.

Všechny operace čtení se odesílají do první dostupné oblasti v seznamu PreferredLocations. Pokud požadavek selže, klient přejde k další oblasti v seznamu atd.

Sada SDK se pokouší číst z oblastí uvedených v seznamu PreferredLocations. Takže pokud je například účet databáze dostupný ve třech oblastech, ale v seznamu PreferredLocations klienta jsou uvedené pouze dvě oblasti jen pro čtení, z oblasti pro zápis se číst nebude, a to ani v případě převzetí služeb při selhání.

Sada SDK automaticky odesílá všechny operace zápisu do aktuální oblasti pro zápis.

Pokud vlastnost PreferredLocations není nastavená, všechny požadavky se budou obsluhovat z aktuální oblasti pro zápis.

To je vše, tento kurz je u konce. Informace o správě konzistence vašeho globálně replikovaného účtu najdete v tématu [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md). Další informace o fungování globální replikace databází ve službě Azure Cosmos DB najdete v tématu [Globální distribuce dat pomocí služby Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní Table API služby Azure Cosmos DB

