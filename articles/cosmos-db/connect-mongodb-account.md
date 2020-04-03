---
title: Připojení aplikace MongoDB ke službě Azure Cosmos DB
description: Zjistěte, jak připojit aplikaci MongoDB k Azure Cosmos DB získáním připojovacího řetězce z webu Azure Portal
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/19/2020
ms.reviewer: sngun
ms.openlocfilehash: cba16d79b6506f9809a76f0128938a68afd15c92
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617039"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Připojení aplikace MongoDB ke službě Azure Cosmos DB

Zjistěte, jak připojit aplikaci MongoDB ke službě Azure Cosmos DB pomocí připojovacího řetězce MongoDB. Pak můžete jako úložiště dat pro aplikaci MongoDB použít databázi Azure Cosmos DB.

Tento kurz nabízí dva způsoby, jak načíst informace o připojovacím řetězci:

- [Metoda rychlého startu](#get-the-mongodb-connection-string-by-using-the-quick-start)pro použití s ovladači .NET, Node.js, MongoDB Shell, Java a Python
- [Vlastní metoda připojovacího řetězce](#get-the-mongodb-connection-string-to-customize)pro použití s jinými ovladači

## <a name="prerequisites"></a>Požadavky

- Účet Azure. Pokud nemáte účet Azure, vytvořte si [bezplatný účet Azure.](https://azure.microsoft.com/free/)
- Účet Cosmos. Pokyny najdete [v tématu Vytvoření webové aplikace pomocí rozhraní API Azure Cosmos DB pro MongoDB a .NET SDK](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Získání připojovacího řetězce MongoDB pomocí rychlého startu

1. V internetovém prohlížeči se přihlaste na [portál Azure](https://portal.azure.com).
2. V okně **Azure Cosmos DB** vyberte rozhraní API.
3. V levém podokně okna účtu klepněte na tlačítko **Rychlý start**.
4. Vyberte si platformu (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Pokud ovladač nebo nástroj nevidíte v seznamu, nebojte se – průběžně dokumentujeme další fragmenty kódu připojení. Prosím, komentář níže na to, co byste chtěli vidět. Chcete-li se dozvědět, jak vytvořit vlastní připojení, přečtěte [si získat informace o připojovacím řetězci účtu](#get-the-mongodb-connection-string-to-customize).
5. Zkopírujte a vložte fragment kódu do aplikace MongoDB.

    ![Čepel rychlého startu](./media/connect-mongodb-account/QuickStartBlade.png)

## <a name="get-the-mongodb-connection-string-to-customize"></a>Získejte připojovací řetězec MongoDB pro přizpůsobení

1. V internetovém prohlížeči se přihlaste na [portál Azure](https://portal.azure.com).
2. V okně **Azure Cosmos DB** vyberte rozhraní API.
3. V levém podokně okna účtu klepněte na **položku Připojovací řetězec**.
4. Otevře se okno **připojovacího řetězce.** Obsahuje všechny informace potřebné pro připojení k účtu pomocí ovladače pro MongoDB, včetně předem sestaveného připojovacího řetězce.

   [![Připojovací řetězec](./media/connect-mongodb-account/ConnectionStringBlade.png)](./media/connect-mongodb-account/ConnectionStringBlade.png#lightbox)

## <a name="connection-string-requirements"></a>Požadavky na připojovací řetězec

> [!Important]
> Azure Cosmos DB má přísné požadavky na zabezpečení a standardy. Účty Azure Cosmos DB vyžadují ověřování a zabezpečenou komunikaci prostřednictvím *protokolu TLS*. 
>
>

Azure Cosmos DB podporuje standardní formát URI připojovacího řetězce MongoDB s několika specifickými požadavky: účty Azure Cosmos DB vyžadují ověřování a zabezpečenou komunikaci prostřednictvím TLS. Formát připojovacího řetězce je tedy následující:

    mongodb://username:password@host:port/[database]?ssl=true

Hodnoty tohoto řetězce jsou k dispozici v okně **připojovacířetězec,** které bylo zobrazeno dříve:

* Uživatelské jméno (povinné): Název účtu Cosmos.
* Heslo (povinné): Heslo účtu Cosmos.
* Hostitel (povinné): Hlavní název souboru Ke skutku sympan účtu Cosmos.
* Port (povinné): 10255.
* Databáze (volitelné): Databáze, která používá připojení. Pokud není k dispozici žádná databáze, výchozí databáze je "test".
* ssl=true (povinné)

Zvažte například účet zobrazený v okně **Připojovací řetězec.** Platný připojovací řetězec je:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [používat Studio 3T](mongodb-mongochef.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Zjistěte, jak [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MongoDB pomocí rozhraní API Azure Cosmos DB pro MongoDB.
