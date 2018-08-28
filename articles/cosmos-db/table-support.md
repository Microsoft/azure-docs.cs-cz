---
title: Podpora úložiště tabulek Azure ve službě Azure Cosmos DB | Microsoft Docs
description: Zjistěte, jak rozhraní Table API služby Azure Cosmos DB spolupracuje s tabulkami úložišť v Azure.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: a6ac954caa8341d6d949811f0bb9d7e68c0b5aac
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42023131"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Vývoj pomocí rozhraní Table API služby Azure Cosmos DB a úložištěm tabulek Azure

Rozhraní Table API služby Azure Cosmos DB a úložiště tabulek Azure sdílejí stejný tabulkový datový model a zpřístupňují přes svoje sady SDK stejné vytvářecí, odstraňovací, aktualizační a dotazovací operace. 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Vývoj pomocí rozhraní Table API služby Azure Cosmos DB

[Rozhraní Table API služby Azure Cosmos DB](table-introduction.md) má v současnosti pro vývoj dostupné čtyři sady SDK: 
- [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget) .NET SDK. Tato knihovna má nejen stejné podpisy tříd a metod jako veřejná sada [Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), ale také možnost připojení k účtům Azure Cosmos DB pomocí rozhraní Table API. Poznámka: Knihovna `Microsoft.Azure.CosmosDB.Table` je aktuálně dostupná pouze pro .NET Standard, pro .NET Core ještě dostupná není.
- [Python SDK](table-sdk-python.md). Nová sada Azure Cosmos DB Python SDK je jediná sada SDK, která podporuje úložiště tabulek Azure v jazyce Python. Tato sada SDK umožňuje připojení k úložišti tabulek Azure i k rozhraní Table API služby Azure Cosmos DB.
- [Java SDK](table-sdk-java.md). Tato sada Azure Storage SDK umožňuje připojení k účtům služby Azure Cosmos DB pomocí rozhraní Table API.
- [Node.js SDK](table-sdk-nodejs.md). Tato sada Azure Storage SDK umožňuje připojení k účtům služby Azure Cosmos DB pomocí rozhraní Table API.

Další informace o práci s rozhraním Table API jsou dostupné v článku s [častými dotazy o vývoji pomocí rozhraní Table API](faq.md#develop-with-the-table-api).

## <a name="developing-with-azure-table-storage"></a>Vývoj pomocí úložiště tabulek Azure

Úložiště tabulek Azure má pro vývoj dostupné tyto sady SDK:

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/). Tato knihovna umožňuje pracovat se službou tabulky úložiště.
- [Python SDK](table-sdk-python.md). Sada Azure Cosmos DB Table SDK pro Python také podporuje službu tabulky úložiště.
- [Azure Storage SDK pro jazyk Java](https://github.com/azure/azure-storage-java). Tato sada Azure Storage SDK poskytuje klientskou knihovnu v Javě pro využití úložiště tabulek Azure.
- [Node.js SDK](table-sdk-nodejs.md). Tato sada SDK nabízí balíček Node.js a javascriptovou klientskou knihovnu kompatibilní s prohlížečem pro využití služby tabulky úložiště.
- [Modul PowerShellu AzureRmStorageTable](https://www.powershellgallery.com/packages/AzureRmStorageTable/1.0.0.7). Tento modul PowerShellu obsahuje rutiny pro práci s tabulkami úložiště.
- [Klientská knihovna Azure Storage pro jazyk C++](https://github.com/Azure/azure-storage-cpp/). Tato knihovna umožňuje vývoj aplikací pro Azure Storage.
- [Klientská knihovna tabulky úložiště Azure pro jazyk Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Tento projekt poskytuje balíček Ruby, který usnadňuje přístup ke službám tabulky úložiště Azure.
- [Klientská knihovna PHP tabulky úložiště Azure](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Tento projekt poskytuje klientskou knihovnu PHP, která usnadňuje přístup ke službám tabulky úložiště Azure.


   





