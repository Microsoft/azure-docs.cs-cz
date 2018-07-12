---
title: Kurz globální distribuce služby Azure Cosmos DB pro rozhraní Table API | Microsoft Docs
description: Zjistěte, jak nastavit globální distribuci služby Azure Cosmos DB pomocí rozhraní Table API.
services: cosmos-db
keywords: global distribution, Table
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 7baa1e5b4b50d1f420b514854fa4d4c9ef786c99
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38582131"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Nastavení globální distribuce služby Azure Cosmos DB pomocí rozhraní Table API

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní [Table API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Připojení k preferované oblasti pomocí rozhraní Table API

Aby mohly využívat [globální distribuci](distribute-data-globally.md), můžou mít klientské aplikace určený seřazený seznam preferovaných oblastí, které se mají použít k provádění operací s dokumenty. Můžete to provést nastavením vlastnosti [TableConnectionPolicy.PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.table.tableconnectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_CosmosDB_Table_TableConnectionPolicy_PreferredLocations). Sada SDK rozhraní Table API služby Azure Cosmos DB vybere nejlepší koncový bod pro komunikaci na základě konfigurace účtu, aktuální dostupnosti oblastí a zadaného seznamu preferencí.

Seznam PreferredLocations by měl obsahovat čárkami oddělený seznam upřednostňovaných (vícenásobné navádění) umístění pro čtení. Každá instance klienta může určit podmnožinu těchto oblastí v upřednostňovaném pořadí pro zajištění nízké latence čtení. Oblasti musí být pojmenované pomocí jejich [zobrazovaného názvu](https://msdn.microsoft.com/library/azure/gg441293.aspx), například `West US`.

Všechny operace čtení se odesílají do první dostupné oblasti v seznamu PreferredLocations. Pokud požadavek selže, klient přejde k další oblasti v seznamu atd.

Sada SDK se pokouší číst z oblastí uvedených v seznamu PreferredLocations. Takže pokud je například účet databáze dostupný ve třech oblastech, ale v seznamu PreferredLocations klienta jsou uvedené pouze dvě oblasti jen pro čtení, z oblasti pro zápis se číst nebude, a to ani v případě převzetí služeb při selhání.

Sada SDK automaticky odesílá všechny operace zápisu do aktuální oblasti pro zápis.

Pokud vlastnost PreferredLocations není nastavená, všechny požadavky se budou obsluhovat z aktuální oblasti pro zápis.

To je vše, tento kurz je u konce. Informace o správě konzistence vašeho globálně replikovaného účtu najdete v tématu [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md). Další informace o fungování globální replikace databází ve službě Azure Cosmos DB najdete v tématu [Globální distribuce dat pomocí služby Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní Table API služby Azure Cosmos DB

