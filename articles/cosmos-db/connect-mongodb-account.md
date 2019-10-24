---
title: Připojení aplikace MongoDB k Azure Cosmos DB
description: Naučte se, jak připojit aplikaci MongoDB k Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 05/21/2019
ms.reviewer: sngun
ms.openlocfilehash: 30b37b86bbe0e91887932423fa1884d8bd49333c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757008"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Připojení aplikace MongoDB k Azure Cosmos DB
Naučte se připojit aplikaci MongoDB k Azure Cosmos DB pomocí připojovacího řetězce MongoDB. Pak můžete použít databázi Azure Cosmos jako úložiště dat vaší aplikace MongoDB. 

V tomto kurzu najdete dva způsoby, jak načíst informace o připojovacím řetězci:

- [Metoda rychlý Start](#QuickstartConnection)pro použití s ovladači .NET, Node. js, MongoDB Shell, Java a Python
- [Metoda vlastního připojovacího řetězce](#GetCustomConnection)pro použití s jinými ovladači

## <a name="prerequisites"></a>Předpoklady

- Účet Azure. Pokud nemáte účet Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) hned teď. 
- Účet Cosmos. Pokyny najdete v tématu [Vytvoření webové aplikace pomocí rozhraní API Azure Cosmos DB pro MongoDB a .NET SDK](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Získání připojovacího řetězce MongoDB pomocí rychlého startu
1. V internetovém prohlížeči se přihlaste k [Azure Portal](https://portal.azure.com).
2. V okně **Azure Cosmos DB** vyberte rozhraní API. 
3. V levém podokně okna účtu klikněte na **rychlý Start**. 
4. Vyberte svou platformu ( **.NET**, **Node. js**, **MongoDB Shell**, **Java**, **Python**). Pokud nevidíte váš ovladač nebo nástroj, nedělejte si starosti – budeme průběžně dokumentovat více fragmentů kódu pro připojení. Níže prosím zadejte komentář na to, co byste chtěli vidět. Pokud se chcete dozvědět, jak vytvořit vlastní připojení, přečtěte si téma [získání informací o připojovacím řetězci účtu](#GetCustomConnection).
5. Zkopírujte a vložte fragment kódu do aplikace MongoDB.

    ![Okno rychlý Start](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a>Získání připojovacího řetězce MongoDB k přizpůsobení
1. V internetovém prohlížeči se přihlaste k [Azure Portal](https://portal.azure.com).
2. V okně **Azure Cosmos DB** vyberte rozhraní API. 
3. V levém podokně okna účtu klikněte na **připojovací řetězec**. 
4. Otevře se okno **připojovací řetězec** . Obsahuje všechny informace potřebné pro připojení k účtu pomocí ovladače pro MongoDB, včetně předkonstruovaného připojovacího řetězce.

    ![Okno Připojovací řetězec](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Požadavky na připojovací řetězec
> [!Important]
> Azure Cosmos DB má přísné požadavky na zabezpečení a standardy. Účty Azure Cosmos DB vyžadují ověřování a zabezpečenou komunikaci prostřednictvím *protokolu SSL*. 
>
>

Azure Cosmos DB podporuje standardní formát identifikátoru URI připojovacího řetězce MongoDB s několika konkrétními požadavky: účty Azure Cosmos DB vyžadují ověřování a zabezpečenou komunikaci přes SSL. Formát připojovacího řetězce je tedy následující:

    mongodb://username:password@host:port/[database]?ssl=true

Hodnoty tohoto řetězce jsou k dispozici v okně **připojovací řetězec** zobrazeném výše:

* Uživatelské jméno (povinné): Cosmos název účtu.
* Heslo (povinné): Cosmos heslo účtu.
* Hostitel (povinné): plně kvalifikovaný název domény účtu Cosmos.
* Port (povinné): 10255.
* Databáze (volitelné): databáze, kterou připojení používá. Pokud není k dispozici žádná databáze, výchozí databáze je "test".
* SSL = true (povinné)

Představte si třeba účet zobrazený v okně **připojovací řetězec** . Platný připojovací řetězec:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.
