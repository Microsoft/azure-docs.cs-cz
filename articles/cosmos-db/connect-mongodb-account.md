---
title: Připojení aplikace MongoDB ke službě Azure Cosmos DB
description: Naučte se, jak připojit aplikaci MongoDB k Azure Cosmos DB získáním připojovacího řetězce z Azure Portal
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2020
ms.reviewer: sngun
adobe-target: true
adobe-target-activity: DocsExp-A/B-384740-MongoDB-2.8.2021
adobe-target-experience: Experience B
adobe-target-content: ./connect-mongodb-account-experimental
ms.openlocfilehash: 702c2d896da2316a2e75c4dbbe4f16249537832b
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093416"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Připojení aplikace MongoDB ke službě Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Zjistěte, jak připojit aplikaci MongoDB ke službě Azure Cosmos DB pomocí připojovacího řetězce MongoDB. Pak můžete jako úložiště dat pro aplikaci MongoDB použít databázi Azure Cosmos DB.

Tento kurz nabízí dva způsoby, jak načíst informace o připojovacím řetězci:

- [Metoda rychlý Start](#get-the-mongodb-connection-string-by-using-the-quick-start)pro použití s ovladači .net, Node.js, MongoDB Shell, Java a Python
- [Metoda vlastního připojovacího řetězce](#get-the-mongodb-connection-string-to-customize)pro použití s jinými ovladači

## <a name="prerequisites"></a>Požadavky

- Účet Azure: Pokud nemáte účet Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) hned teď.
- Účet Cosmos. Pokyny najdete v tématu [Vytvoření webové aplikace pomocí rozhraní API Azure Cosmos DB pro MongoDB a .NET SDK](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Získání připojovacího řetězce MongoDB pomocí rychlého startu

1. V internetovém prohlížeči se přihlaste k [Azure Portal](https://portal.azure.com).
2. V okně **Azure Cosmos DB** vyberte rozhraní API.
3. V levém podokně okna účtu klikněte na **rychlý Start**.
4. Vyberte platformu (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Pokud nevidíte váš ovladač nebo nástroj, nedělejte si starosti – budeme průběžně dokumentovat více fragmentů kódu pro připojení. Níže prosím zadejte komentář na to, co byste chtěli vidět. Pokud se chcete dozvědět, jak vytvořit vlastní připojení, přečtěte si téma [získání informací o připojovacím řetězci účtu](#get-the-mongodb-connection-string-to-customize).
5. Zkopírujte a vložte fragment kódu do aplikace MongoDB.

    :::image type="content" source="./media/connect-mongodb-account/QuickStartBlade.png" alt-text="Okno rychlý Start":::

## <a name="get-the-mongodb-connection-string-to-customize"></a>Získání připojovacího řetězce MongoDB k přizpůsobení

1. V internetovém prohlížeči se přihlaste k [Azure Portal](https://portal.azure.com).
2. V okně **Azure Cosmos DB** vyberte rozhraní API.
3. V levém podokně okna účtu klikněte na **připojovací řetězec**.
4. Otevře se okno **připojovací řetězec** . Obsahuje všechny informace potřebné pro připojení k účtu pomocí ovladače pro MongoDB, včetně předkonstruovaného připojovacího řetězce.

   :::image type="content" source="./media/connect-mongodb-account/ConnectionStringBlade.png" alt-text="Okno Připojovací řetězec" lightbox= "./media/connect-mongodb-account/ConnectionStringBlade.png" :::

## <a name="connection-string-requirements"></a>Požadavky na připojovací řetězec

> [!Important]
> Azure Cosmos DB má přísné požadavky na zabezpečení a standardy. Účty Azure Cosmos DB vyžadují ověřování a zabezpečenou komunikaci pomocí protokolu *TLS*.

Azure Cosmos DB podporuje standardní formát identifikátoru URI připojovacího řetězce MongoDB s několika konkrétními požadavky: účty Azure Cosmos DB vyžadují ověřování a zabezpečenou komunikaci pomocí protokolu TLS. Formát připojovacího řetězce je tedy následující:

`mongodb://username:password@host:port/[database]?ssl=true`

Hodnoty tohoto řetězce jsou k dispozici v okně **připojovací řetězec** zobrazeném výše:

* Uživatelské jméno (povinné): Cosmos název účtu.
* Heslo (povinné): Cosmos heslo účtu.
* Hostitel (povinné): plně kvalifikovaný název domény účtu Cosmos.
* Port (povinné): 10255.
* Databáze (volitelné): databáze, kterou připojení používá. Pokud není k dispozici žádná databáze, výchozí databáze je "test".
* SSL = true (povinné)

Představte si třeba účet zobrazený v okně **připojovací řetězec** . Platný připojovací řetězec:

`mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true`

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.
