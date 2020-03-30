---
title: Podpora azure table storage v Azure Cosmos DB
description: Zjistěte, jak azure cosmos DB table api a tabulky úložiště Azure spolupracují sdílením stejného modelu dat tabulky operace
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 12/02/2019
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 82397e49e473b8660dfada54a0e05cafe0da4120
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76770671"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Vývoj pomocí rozhraní Table API služby Azure Cosmos DB a úložištěm tabulek Azure

Rozhraní Table API služby Azure Cosmos DB a úložiště tabulek Azure sdílejí stejný tabulkový datový model a zpřístupňují přes svoje sady SDK stejné vytvářecí, odstraňovací, aktualizační a dotazovací operace.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Vývoj pomocí rozhraní Table API služby Azure Cosmos DB

[Rozhraní Table API služby Azure Cosmos DB](table-introduction.md) má v současnosti pro vývoj dostupné čtyři sady SDK: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Tato knihovna cílí na standard .NET a má stejné třídy a podpisy metod jako veřejná [sada Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), ale má také možnost připojení k účtům Azure Cosmos DB pomocí rozhraní Table API. Uživatelům knihovny rozhraní .NET Framework [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) se doporučuje upgradovat na [Microsoft.Azure.Cosmos.Table,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) protože je v režimu údržby a brzy se zastaralá.

* [Python SDK](table-sdk-python.md): Nová sada Azure Cosmos DB Python SDK je jedinou sadou SDK, která podporuje úložiště Azure Table v Pythonu. Tato sada SDK umožňuje připojení k úložišti tabulek Azure i k rozhraní Table API služby Azure Cosmos DB.

* [Java SDK](table-sdk-java.md): Tato sada Azure Storage SDK má možnost připojení k účtům Azure Cosmos DB pomocí rozhraní TABLE API.

* [Node.js SDK](table-sdk-nodejs.md): Tato sada Azure Storage SDK má možnost připojení k účtům Azure Cosmos DB pomocí rozhraní TABLE API.


Další informace o práci s rozhraním Table API jsou dostupné v článku s [častými dotazy o vývoji pomocí rozhraní Table API](faq.md#table).

## <a name="developing-with-azure-table-storage"></a>Vývoj pomocí úložiště tabulek Azure

Úložiště tabulek Azure má pro vývoj dostupné tyto sady SDK:

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/). Tato knihovna umožňuje pracovat se službou tabulky úložiště.
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python). Sada Azure Cosmos DB Table SDK pro Python podporuje službu Table Storage (protože azure table storage a rozhraní Table API Cosmos DB sdílejí stejné funkce a funkce a ve snaze zohlednit naše úsilí o vývoj sady SDK doporučujeme použít tuto sadu SDK).
- [Azure Storage SDK pro Jazyk Java](https://github.com/azure/azure-storage-java). Tato sada Azure Storage SDK poskytuje klientskou knihovnu v Javě pro využití úložiště tabulek Azure.
- [Sada Node.js SDK](https://github.com/Azure/azure-storage-node). Tato sada SDK nabízí balíček Node.js a javascriptovou klientskou knihovnu kompatibilní s prohlížečem pro využití služby tabulky úložiště.
- [Modul PowerShellu AzureRmStorageTable](https://www.powershellgallery.com/packages/AzureRmStorageTable). Tento modul PowerShellu obsahuje rutiny pro práci s tabulkami úložiště.
- [Klientská knihovna Azure Storage pro jazyk C++](https://github.com/Azure/azure-storage-cpp/). Tato knihovna umožňuje vývoj aplikací pro Azure Storage.
- [Klientská knihovna tabulky úložiště Azure pro jazyk Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Tento projekt poskytuje balíček Ruby, který usnadňuje přístup ke službám tabulky úložiště Azure.
- [Klientská knihovna PHP tabulky úložiště Azure](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Tento projekt poskytuje klientskou knihovnu PHP, která usnadňuje přístup ke službám tabulky úložiště Azure.


   





