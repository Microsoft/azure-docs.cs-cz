---
title: Kurz globální distribuce Azure Cosmos DB pro rozhraní TABLE API
description: Zjistěte, jak funguje globální distribuce v účtech rozhraní API Azure Cosmos DB Table API a jak nakonfigurovat upřednostňovaný seznam oblastí
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 627086bdb13acdd29821af399f90fee8deaae432
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76900184"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Nastavení globální distribuce služby Azure Cosmos DB pomocí rozhraní Table API

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní [Table API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Připojení k preferované oblasti pomocí rozhraní Table API

Aby bylo možné využít [globální distribuce](distribute-data-globally.md), klientské aplikace by měly určit aktuální umístění, kde je spuštěna jejich aplikace. To se provádí `CosmosExecutorConfiguration.CurrentRegion` nastavením vlastnosti. Vlastnost `CurrentRegion` by měla obsahovat jedno umístění. Každá instance klienta můžete zadat vlastní oblast pro čtení s nízkou latencí. Oblast musí být pojmenována pomocí jejich [zobrazované názvy,](https://msdn.microsoft.com/library/azure/gg441293.aspx) například "Západní USA". 

Sada Azure Cosmos DB Table API Api automaticky vybere nejlepší koncový bod pro komunikaci s na základě konfigurace účtu a aktuální místní dostupnosti. Upřednostňuje nejbližší oblast, aby klientům poskytovala lepší latenci. Po nastavení aktuální `CurrentRegion` vlastnosti jsou požadavky na čtení a zápis směrovány následujícím způsobem:

* **Čtení požadavků:** Všechny požadavky na čtení jsou `CurrentRegion`odesílány do nakonfigurovaného . Na základě blízkosti sada SDK automaticky vybere záložní geograficky replikovanou oblast pro vysokou dostupnost.

* **Požadavky na zápis:** Sada SDK automaticky odešle všechny požadavky na zápis do aktuální oblasti zápisu. Ve více hlavních účtech bude aktuální oblast sloužit také požadavkům na zápisy. Na základě blízkosti sada SDK automaticky vybere záložní geograficky replikovanou oblast pro vysokou dostupnost.

Pokud nezadáte `CurrentRegion` vlastnost, sada SDK používá aktuální oblast zápisu pro všechny operace.

Například pokud účet Azure Cosmos je v oblasti "Západní USA" a "Východní USA". Pokud "Západ USA" je oblast zápisu a aplikace je k dispozici v "Východní USA". Pokud není nakonfigurována vlastnost CurrentRegion, všechny požadavky na čtení a zápis jsou vždy směrovány do oblasti "Západní USA". Pokud je nakonfigurována vlastnost CurrentRegion, všechny požadavky na čtení jsou obsluhovány z oblasti "Východní USA".

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Konfigurace globální distribuce pomocí webu Azure Portal
> * Konfigurace globální distribuce pomocí rozhraní Table API služby Azure Cosmos DB

