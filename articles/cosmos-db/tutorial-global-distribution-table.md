---
title: Kurz pro globální distribuci Azure Cosmos DB pro rozhraní API pro tabulky
description: Přečtěte si, jak globální distribuce funguje v Azure Cosmos DB rozhraní API pro tabulky účty a jak nakonfigurovat preferovaný seznam oblastí.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: f0d62ae3909bc886fa6a56ba7ed32d55d27302dd
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568653"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Nastavení globální distribuce služby Azure Cosmos DB pomocí rozhraní Table API

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní [Table API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Připojení k preferované oblasti pomocí rozhraní Table API

Aby bylo možné využít [globální distribuci](distribute-data-globally.md), měly by klientské aplikace určovat aktuální umístění, ve kterém je aplikace spuštěná. To se provádí nastavením `CosmosExecutorConfiguration.CurrentRegion` Vlastnosti. `CurrentRegion`Vlastnost by měla obsahovat jedno umístění. Každá instance klienta může určit svou vlastní oblast pro čtení s nízkou latencí. Oblast musí být pojmenována pomocí [zobrazovaných názvů](https://msdn.microsoft.com/library/azure/gg441293.aspx) , jako je například "západní USA". 

Sada Azure Cosmos DB rozhraní API pro tabulky SDK automaticky vybere nejlepší koncový bod ke komunikaci s nástrojem na základě konfigurace účtu a aktuální regionální dostupnosti. Přidělí prioritu nejbližší oblasti, aby poskytovala lepší latenci klientům. Po nastavení aktuální `CurrentRegion` vlastnosti jsou požadavky na čtení a zápis směrovány takto:

* **Žádosti o čtení:** Všechny požadavky na čtení se odesílají do nakonfigurovaného `CurrentRegion` . Na základě blízkosti se sada SDK automaticky vybere jako záložní geograficky replikovaný region pro zajištění vysoké dostupnosti.

* **Požadavky na zápis:** Sada SDK automaticky pošle všechny požadavky na zápis do aktuální oblasti pro zápis. V rámci účtu s zápisy ve více oblastech bude aktuální oblast obsluhovat i požadavky na zápis. Na základě blízkosti se sada SDK automaticky vybere jako záložní geograficky replikovaný region pro zajištění vysoké dostupnosti.

Pokud vlastnost nezadáte `CurrentRegion` , použije sada SDK aktuální oblast zápisu pro všechny operace.

Například pokud je účet Azure Cosmos v oblastech "Západní USA" a "Východní USA". Pokud je "Západní USA" oblast pro zápis a aplikace je k dispozici v "Východní USA". Pokud vlastnost CurrentRegion není nakonfigurovaná, všechny požadavky na čtení a zápis se vždycky přesměrují do oblasti "Západní USA". Pokud je nakonfigurována vlastnost CurrentRegion, jsou všechny požadavky na čtení obsluhovány z oblasti "Východní USA".

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní Table API služby Azure Cosmos DB

