---
title: MongoDB připojovací řetězec pro účet Azure Cosmos DB | Microsoft Docs
description: Zjistěte, jak připojit aplikace MongoDB k účtu Azure Cosmos DB pomocí připojovacího řetězce MongoDB.
keywords: mongodb připojovací řetězec
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 12/19/2017
ms.author: sngun
ms.openlocfilehash: 441f6725b96dc7a099304e06db6d46a65e7f9c81
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795954"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Připojení aplikace MongoDB pro Azure Cosmos DB
Zjistěte, jak připojit aplikace MongoDB k účtu Azure Cosmos DB pomocí připojovacího řetězce MongoDB. Potom můžete databázi Azure Cosmos DB jako data store pro vaši aplikaci MongoDB. 

V tomto kurzu nabízí dva způsoby, jak načíst informace o připojovacím řetězci:

- [Rychlý start – metoda](#QuickstartConnection), pro použití s .NET, Node.js, prostředí MongoDB Shell, Java a Python ovladače
- [Metodu vlastní připojovací řetězec](#GetCustomConnection), pro použití s další ovladače

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Pokud nemáte účet Azure, vytvořte [bezplatný účet Azure](https://azure.microsoft.com/free/) nyní. 
- Účet služby Azure Cosmos DB. Pokyny najdete v tématu [sestavení webové aplikace MongoDB API pomocí rozhraní .NET a portálu Azure](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Získat připojovací řetězec MongoDB pomocí rychlý start
1. V internetovém prohlížeči, přihlaste se k [portál Azure](https://portal.azure.com).
2. V **Azure Cosmos DB** okně vybrat rozhraní API pro účet MongoDB. 
3. V levém podokně okna účet, klikněte na tlačítko **úvodní**. 
4. Vyberte svou platformu (**.NET**, **Node.js**, **MongoDB prostředí**, **Java**, **Python**). Pokud nevidíte ovladače nebo nástroj v seznamu uvedena, nemusíte si dělat starosti – jsme nepřetržitě dokumentů další fragmenty kódu připojení. Zadejte komentář níže na co se má zobrazit. Chcete-li zjistit, jak vytvořit vlastní připojení, přečtěte si [získat informace o připojovacím řetězci účtu](#GetCustomConnection).
5. Zkopírujte a vložte fragment kódu do vaší aplikace pro MongoDB.

    ![Okna rychlý start](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Získat MongoDB připojovací řetězec pro přizpůsobení
1. V internetovém prohlížeči, přihlaste se k [portál Azure](https://portal.azure.com).
2. V **Azure Cosmos DB** okně vybrat rozhraní API pro účet MongoDB. 
3. V levém podokně okna účet, klikněte na tlačítko **připojovací řetězec**. 
4. **Připojovací řetězec** otevře se okno. Obsahuje všechny informace potřebné k připojení k účtu pomocí ovladač pro MongoDB, včetně preconstructed připojovací řetězec.

    ![Okno řetězec připojení](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Požadavky na řetězec připojení
> [!Important]
> Azure Cosmos DB má vysokými nároky na zabezpečení a standardy. Účty Azure Cosmos DB vyžadují ověřování a zabezpečenou komunikaci prostřednictvím *SSL*. 
>
>

Azure Cosmos DB podporuje standardní MongoDB připojovací řetězec formátu URI, pomocí několika specifické požadavky: účty Azure Cosmos DB vyžadují ověřování a zabezpečenou komunikaci prostřednictvím protokolu SSL. Ano je formátu řetězce připojení:

    mongodb://username:password@host:port/[database]?ssl=true

Hodnoty tohoto řetězce jsou k dispozici v **připojovací řetězec** okno uvedena výše:

* Uživatelské jméno (povinné): název účtu Azure Cosmos DB.
* Heslo (povinné): heslo k účtu Azure Cosmos DB.
* Hostitel (povinné): plně kvalifikovaný název domény účtu Azure Cosmos DB.
* Port (povinné): 10255.
* Databáze (volitelné): databáze, který používá připojení. Pokud je k dispozici žádná databáze, výchozí databáze je "test".
* SSL = true (povinné)

Představte si třeba účet, který ukazuje **připojovací řetězec** okno. Je platný připojovací řetězec:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [použít Studio 3T (MongoChef)](mongodb-mongochef.md) rozhraním API DB Cosmos Azure pro účet MongoDB.
* Prozkoumejte rozhraní API služby Azure Cosmos DB pro MongoDB zobrazením [ukázky](mongodb-samples.md).
