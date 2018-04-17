---
title: Azure Table Storage podpory v Azure Cosmos DB | Microsoft Docs
description: Zjistěte, jak spolupracují rozhraní API služby Azure Cosmos DB tabulky a úložiště tabulek Azure.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 2560c2ee34a83ce86db043e17fb41192c31de398
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Vývoj s Azure Cosmos DB tabulky rozhraní API a Azure Table storage

Rozhraní API tabulky DB Cosmos Azure a Azure Table storage sdílet stejné tabulky datového modelu a vystavit stejné vytvoření, odstranění, aktualizace a operace dotazů prostřednictvím jejich sady SDK. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Vývoj s Azure Cosmos DB tabulky API

V tomto okamžiku [rozhraní API služby Azure Cosmos DB tabulky](table-introduction.md) má čtyři sady SDK k dispozici pro vývoj: 
- [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget) sady .NET SDK. V této knihovně bylo stejné třídy a metody podpisy jako veřejnosti [sada SDK úložiště Windows Azure](https://www.nuget.org/packages/WindowsAzure.Storage), ale také obsahuje schopnost připojit k účtům Azure Cosmos DB pomocí rozhraní API tabulky. 
- [Python SDK](table-sdk-python.md). Nové DB Python SDK Cosmos Azure je pouze SDK, který podporuje Azure Table storage v Pythonu. Tato sada SDK se připojí pomocí Azure Table storage a rozhraní API služby Azure Cosmos DB tabulky.
- [Java SDK](table-sdk-java.md). Tato sada SDK úložiště Azure má schopnost připojit k účtům Azure Cosmos DB pomocí rozhraní API tabulky.
- [Node.js SDK](table-sdk-nodejs.md). Tato sada SDK úložiště Azure má schopnost připojit k účtům Azure Cosmos DB pomocí rozhraní API tabulky.

Další informace o práci s rozhraním API pro tabulku jsou dostupné v [– nejčastější dotazy: vyvíjet s rozhraním API pro tabulku](faq.md#develop-with-the-table-api) článku.

## <a name="developing-with-the-azure-table-storage"></a>Vývoj s Azure Table storage

[Azure Table storage](table-storage-overview.md) má mnoho sady SDK k dispozici a kurzy k dispozici, které jsou k dispozici [Azure Table storage](table-storage-overview.md) části. Tyto články aktualizované jako vzájemná funkční spolupráce mezi Azure Table storage sady SDK a rozhraní API Správce Azure Cosmos DB tabulka bude k dispozici.  





