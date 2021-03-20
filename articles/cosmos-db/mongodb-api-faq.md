---
title: Nejčastější dotazy týkající se rozhraní API Azure Cosmos DB pro MongoDB
description: Získejte odpovědi na nejčastější dotazy k rozhraní API Azure Cosmos DB pro MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 08899018d03209dab09f61d4dd74feceee03b246
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019003"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>Nejčastější dotazy týkající se rozhraní API Azure Cosmos DB pro MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Rozhraní API pro Azure Cosmos DB pro MongoDB je vrstva kompatibility protokolů, která umožňuje aplikacím snadno a transparentně komunikovat s nativním databázovým strojem Azure Cosmos pomocí stávajících sad SDK a ovladačů podporovaných komunitou pro MongoDB. Vývojáři teď můžou využívat stávající MongoDB sady nástrojů a dovednosti k sestavování aplikací, které využívají Azure Cosmos DB. Vývojáři mají výhodu jedinečných funkcí Azure Cosmos DB, které zahrnují globální distribuci s mezioblastcí replikaci pro zápis, automatické indexování, údržbu zálohování, finančně zálohovaných smluv o úrovni služeb (SLA) atd.

## <a name="how-do-i-connect-to-my-database"></a>Návody se připojit k databázi?

Nejrychlejší způsob, jak se připojit k databázi Cosmos s rozhraním API Azure Cosmos DB pro MongoDB, je přejít na [Azure Portal](https://portal.azure.com). Přejděte na svůj účet a potom v levé navigační nabídce klikněte na **rychlé zprovoznění**. Rychlý Start je nejlepší způsob, jak získat fragmenty kódu pro připojení k vaší databázi.

Azure Cosmos DB vynutil přísné požadavky na zabezpečení a standardy. Účty Azure Cosmos DB vyžadují ověřování a zabezpečenou komunikaci pomocí protokolu TLS, proto nezapomeňte používat TLSv 1.2.

Další informace najdete v tématu [připojení k databázi Cosmos s rozhraním API Azure Cosmos DB pro MongoDB](connect-mongodb-account.md).

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Kódy chyb při použití rozhraní API Azure Cosmos DB pro MongoDB?

Spolu s běžnými kódy chyb MongoDB má rozhraní API Azure Cosmos DB pro MongoDB vlastní konkrétní kódy chyb. Najdete je v [Průvodci odstraňováním potíží](mongodb-troubleshoot.md).

## <a name="supported-drivers"></a>Podporované ovladače

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Je ovladač Simba pro MongoDB podporovaný pro použití s rozhraním API Azure Cosmos DB pro MongoDB?

Ano, můžete použít ovladač ODBC Simba Mongo s rozhraním API Azure Cosmos DB pro MongoDB

## <a name="next-steps"></a>Další kroky

* [Sestavení webové aplikace .NET pomocí rozhraní API Azure Cosmos DB pro MongoDB](create-mongodb-dotnet.md)
* [Vytvořte konzolovou aplikaci pomocí Java a rozhraní MongoDB API v Azure Cosmos DB](create-mongodb-java.md)
