---
title: Připojení k rozhraní API Azure Cosmos DB pro MongoDB pomocí Studia 3T
description: Zjistěte, jak se připojit k rozhraní API Azure Cosmos DB pro MongoDB pomocí Studia 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 84b703cceeb130b177b8ab32281ef616b1ec632b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548823"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Připojení k účtu Azure Cosmos pomocí Studia 3T

Chcete-li se připojit k rozhraní API Služby Azure Cosmos DB pro MongoDB pomocí studia 3T, musíte:

* Stáhněte a nainstalujte [Studio 3T](https://studio3t.com/).
* Mít informace o [připojovacím řetězci](connect-mongodb-account.md) účtu Azure Cosmos.

## <a name="create-the-connection-in-studio-3t"></a>Vytvoření připojení v aplikaci Studio 3T

Pokud chcete přidat svůj účet Azure Cosmos do správce připojení Studio 3T, použijte následující kroky:

1. Načtěte informace o připojení pro rozhraní API azure cosmos DB pro účet MongoDB pomocí pokynů v [článku Připojit mongoDB k Azure Cosmos DB.](connect-mongodb-account.md)

    ![Snímek obrazovky stránky připojovacího řetězce](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Klepnutím na tlačítko **Připojit** otevřete Program Connection Manager a potom klepněte na **tlačítko Nové připojení.**

    ![Snímek obrazovky správce připojení Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. V okně **Nové připojení** na kartě **Server** zadejte HOSTITELE (FQDN) účtu Azure Cosmos a PORT.

    ![Snímek obrazovky s kartou Studio 3T connection manager server](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. V okně **Nové připojení** na kartě **Ověřování** zvolte Režim ověřování **Basic (MONGODB-CR nebo SCARM-SHA-1)** a zadejte uživatelské jméno a heslo.  Přijměte výchozí ověřovací db (admin) nebo zadejte vlastní hodnotu.

    ![Snímek obrazovky s kartou ověřování správce připojení Studia 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. V okně **Nové připojení** zaškrtněte na kartě **SSL** políčko **Použít protokol SSL pro připojení** a přepínací tlačítko **SSL certifikáty s podpisem s vlastním podpisem přijmout server.**

    ![Snímek obrazovky s kartou SSL správce připojení Studia 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Klepnutím na tlačítko **Testovat připojení** ověřte informace o připojení, klepnutím na **tlačítko OK** se vraťte do okna Nové připojení a potom klepněte na tlačítko **Uložit**.

    ![Snímek obrazovky okna testovacího připojení Studia 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Vytvoření databáze, kolekce a dokumentů pomocí aplikace Studio 3T
Chcete-li vytvořit databázi, kolekci a dokumenty pomocí aplikace Studio 3T, proveďte následující kroky:

1. V **programu Connection Manager**zvýrazněte připojení a klepněte na tlačítko **Připojit**.

    ![Snímek obrazovky správce připojení Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Klepněte pravým tlačítkem myši na hostitele a zvolte **Přidat databázi**.  Zadejte název databáze a klepněte na tlačítko **OK**.

    ![Snímek obrazovky s možností Přidat databázi ve studiu 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Klepněte pravým tlačítkem myši na databázi a zvolte **Přidat kolekci**.  Zadejte název kolekce a klepněte na tlačítko **Vytvořit**.

    ![Snímek obrazovky s možností Přidat kolekci ve studii 3T](./media/mongodb-mongochef/AddCollection.png)
4. Klikněte na položku nabídky **Kolekce** a potom klikněte na **Přidat dokument**.

    ![Snímek obrazovky s položkou nabídky Přidat dokument studia 3T](./media/mongodb-mongochef/AddDocument1.png)
5. V dialogovém okně Přidat dokument vložte následující a klepněte na **tlačítko Přidat dokument**.

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
6. Přidejte další dokument, tentokrát s následujícím obsahem:

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
7. Spusťte ukázkový dotaz. Můžete například vyhledat rodiny s příjmením "Andersen" a vrátit pole rodičů a státu.

    ![Snímek obrazovky s výsledky dotazu Mongo Chef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MongoDB pomocí rozhraní API Azure Cosmos DB pro MongoDB.
