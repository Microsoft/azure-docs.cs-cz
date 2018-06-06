---
title: Použít Studio 3T (MongoChef) s Azure Cosmos DB | Microsoft Docs
description: Další informace o použití Studio 3T s účtem Azure Cosmos DB MongoDB rozhraní API
keywords: mongochef, studio 3T
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2018
ms.author: sngun
ms.openlocfilehash: 9d00f260252e31ab9186c8aab8f07db848d1af29
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794766"
---
# <a name="azure-cosmos-db-use-studio-3t-with-a-mongodb-api-account"></a>Azure Cosmos DB: Pomocí Studio 3T s účtem MongoDB rozhraní API

Chcete-li připojit k účtu Azure Cosmos DB MongoDB rozhraní API, postupujte takto:

* Stáhněte a nainstalujte [Studio 3T](https://studio3t.com/) (dříve označované jako MongoChef)
* Mít vaše Azure DB Cosmos [připojovací řetězec](connect-mongodb-account.md) informací pro váš účet MongoDB

## <a name="create-the-connection-in-studio-3t"></a>Vytvoření připojení v Studio 3T
Pokud chcete přidat do Správce připojení 3T Studio účtu Azure Cosmos DB, proveďte následující kroky:

1. Načíst informace o připojení Azure Cosmos databázi MongoDB API účtu pomocí pokynů v [připojit MongoDB aplikace pro Azure Cosmos DB](connect-mongodb-account.md) článku.

    ![Snímek obrazovky stránky řetězec připojení](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Klikněte na tlačítko **připojit** a otevřít Správce připojení, potom klikněte na **nové připojení**

    ![Snímek obrazovky Správce Studio 3T připojení](./media/mongodb-mongochef/ConnectionManager.png)
3. V **nové připojení** okno na **Server** zadejte hostitele (FQDN) účtu Azure Cosmos DB a PORT.

    ![Snímek obrazovky karty Studio 3T připojení správce serveru](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. V **nové připojení** okno na **ověřování** , zvolte režim ověřování **Basic (MONGODB CR nebo SCARM-SHA-1)** a zadejte uživatelské jméno a heslo.  Přijměte výchozí ověřování databáze (správce) nebo zadejte vlastní hodnotu.

    ![Snímek obrazovky kartě Studio 3T připojení Správce ověřování](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. V **nové připojení** okno na **SSL** zkontrolujte **protokol SSL používá pro připojení** zaškrtávací políčko a **přijmout SSL certifikáty podepsané svým držitelem serveru**  přepínač.

    ![Snímek obrazovky s kartou SSL na připojení manager Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Klikněte **Test připojení** a ověřte informace o připojení, klikněte na tlačítko **OK** vraťte do okna nové připojení, a pak klikněte na **Uložit**.

    ![Snímek obrazovky okna Studio 3T test připojení](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Použít Studio 3T k vytvoření databáze, kolekce a dokumentů
K vytvoření databáze, kolekce a dokumenty pomocí Studio 3T, proveďte následující kroky:

1. V **Správce připojení**, zvýrazněte připojení a klikněte na **Connect**.

    ![Snímek obrazovky Správce Studio 3T připojení](./media/mongodb-mongochef/ConnectToAccount.png)
2. Klikněte pravým tlačítkem na hostitele a zvolte **přidat databázi**.  Zadejte název databáze a klikněte na tlačítko **OK**.

    ![Snímek obrazovky možnost přidat databáze 3T Studio](./media/mongodb-mongochef/AddDatabase1.png)
3. Klikněte pravým tlačítkem na databázi a zvolte **přidat kolekce**.  Zadejte název kolekce a klikněte na **vytvořit**.

    ![Snímek obrazovky možnost přidat kolekce 3T Studio](./media/mongodb-mongochef/AddCollection.png)
4. Klikněte na tlačítko **kolekce** nabídky položky, klepněte na tlačítko **přidat dokument**.

    ![Snímek obrazovky položky nabídky přidat dokument 3T Studio](./media/mongodb-mongochef/AddDocument1.png)
5. V dialogovém okně Přidat dokument vložte následující a potom klikněte na **přidat dokument**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Přidání jiného dokumentu, tentokrát s následujícím obsahem:

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Spustit ukázkový dotaz. Například vyhledejte rodiny s příjmení 'rodinu a vrátí nadřazené položky a pole stavu.

    ![Snímek obrazovky Mongo Chef výsledky dotazu](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Další postup
* Prozkoumejte rozhraní API služby Azure Cosmos DB MongoDB [ukázky](mongodb-samples.md).
