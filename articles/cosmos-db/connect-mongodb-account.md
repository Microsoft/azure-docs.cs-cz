---
title: Připojovací řetězec MongoDB pro účet služby Azure Cosmos DB | Dokumentace Microsoftu
description: Zjistěte, jak připojit vaši aplikaci MongoDB k účtu služby Azure Cosmos DB pomocí připojovacího řetězce MongoDB.
keywords: připojovací řetězec mongodb
services: cosmos-db
author: slyons
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 12/19/2017
ms.author: sclyon
ms.openlocfilehash: ad8d6fe36c289c4c9e37689e1c7d755dc3bf9048
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42061290"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Připojení aplikace MongoDB ke službě Azure Cosmos DB
Zjistěte, jak připojit vaši aplikaci MongoDB k účtu služby Azure Cosmos DB pomocí připojovacího řetězce MongoDB. Potom můžete databázi Azure Cosmos DB jako data store pro vaši aplikaci MongoDB. 

Tento kurz obsahuje dva způsoby, jak načíst informace o připojovacím řetězci:

- [Rychlý start – metoda](#QuickstartConnection), pomocí ovladače .NET, Node.js, MongoDB Shell, Javě a Pythonu
- [Metoda vlastní připojovací řetězec](#GetCustomConnection), pro použití s další ovladače

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Pokud ještě nemáte účet Azure, vytvořte [bezplatný účet Azure](https://azure.microsoft.com/free/) nyní. 
- Účet služby Azure Cosmos DB. Pokyny najdete v tématu [vytvoření webové aplikace MongoDB API s využitím .NET a webu Azure portal](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Získání připojovacího řetězce MongoDB pomocí rychlý start
1. V internetovém prohlížeči, přihlaste se k [webu Azure portal](https://portal.azure.com).
2. V **služby Azure Cosmos DB** okně vyberte rozhraní API pro účet MongoDB. 
3. V levém podokně okna účtu klikněte na tlačítko **úvodní**. 
4. Zvolte vaši platformu (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Pokud se váš ovladač nebo nástroj v seznamu, Nedělejte si starosti – průběžně dokumentujeme další fragmenty kódu pro připojení. Zadejte komentář dole na co chcete zobrazit. Zjistěte, jak vytvořit vlastní připojení, přečtěte si téma [získat informace o připojovacím řetězci účtu](#GetCustomConnection).
5. Zkopírujte a vložte fragment kódu do své aplikace MongoDB.

    ![Okna rychlý start](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Získání připojovacího řetězce MongoDB pro přizpůsobení
1. V internetovém prohlížeči, přihlaste se k [webu Azure portal](https://portal.azure.com).
2. V **služby Azure Cosmos DB** okně vyberte rozhraní API pro účet MongoDB. 
3. V levém podokně okna účtu klikněte na tlačítko **připojovací řetězec**. 
4. **Připojovací řetězec** se otevře okno. Obsahuje všechny informace potřebné pro připojení k účtu pomocí ovladače pro MongoDB, včetně preconstructed připojovací řetězec.

    ![Okno Připojovací řetězec](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Požadavky na připojovací řetězec
> [!Important]
> Azure Cosmos DB má striktní bezpečnostní požadavky a standardy. Účty Azure Cosmos DB vyžadují ověření a zabezpečenou komunikaci přes *SSL*. 
>
>

Azure Cosmos DB podporuje standardní MongoDB formát připojovacího řetězce identifikátoru URI, pomocí několika konkrétním požadavkům: účty služby Azure Cosmos DB vyžadují ověření a zabezpečenou komunikaci přes protokol SSL. Ano formát připojovacího řetězce je:

    mongodb://username:password@host:port/[database]?ssl=true

Hodnoty tohoto řetězce jsou k dispozici v **připojovací řetězec** okno je uvedeno výše:

* Uživatelské jméno (povinné): název účtu služby Azure Cosmos DB.
* Heslo (povinné): heslo k účtu Azure Cosmos DB.
* Hostitel (povinné): plně kvalifikovaný název domény účtu služby Azure Cosmos DB.
* Port (povinné): 10255.
* Databáze (nepovinné): databáze, která používá připojení. Pokud je k dispozici žádné databáze, výchozí databáze je "test".
* SSL = true (povinné)

Představte si třeba účet, který ukazuje **připojovací řetězec** okno. Je platný připojovací řetězec:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [použití studia 3T (MongoChef)](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro účet MongoDB.
* Prozkoumejte rozhraní API Azure Cosmos DB pro MongoDB zobrazením [ukázky](mongodb-samples.md).
