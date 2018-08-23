---
title: Použití studia 3T (MongoChef) pomocí služby Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o použití studia 3T pomocí účtu rozhraní MongoDB API služby Azure Cosmos DB
keywords: mongochef studia 3T
services: cosmos-db
author: slyons
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2018
ms.author: sclyon
ms.openlocfilehash: af66f413055c01d9a4c3e94c7d3b9c5f1d5917b0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055429"
---
# <a name="azure-cosmos-db-use-studio-3t-with-a-mongodb-api-account"></a>Azure Cosmos DB: Použití studia 3T pomocí účtu rozhraní MongoDB API

Pro připojení k účtu MongoDB API služby Azure Cosmos DB, musíte mít:

* Stáhněte a nainstalujte [studia 3T](https://studio3t.com/) (dříve označované jako MongoChef)
* Azure Cosmos DB mají [připojovací řetězec](connect-mongodb-account.md) informací pro váš účet MongoDB

## <a name="create-the-connection-in-studio-3t"></a>Vytvoření připojení v studia 3T
Pokud chcete přidat do správce Studio 3T připojení účtu služby Azure Cosmos DB, proveďte následující kroky:

1. Načíst informace o připojení služby Azure Cosmos DB u vašeho účtu rozhraní MongoDB API pomocí pokynů [připojení aplikace MongoDB ke službě Azure Cosmos DB](connect-mongodb-account.md) článku.

    ![Snímek obrazovky stránky připojovacího řetězce](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Klikněte na tlačítko **připojit** Správce připojení, potom klikněte na tlačítko **nové připojení**

    ![Snímek obrazovky Správce připojení Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. V **nové připojení** okno na **Server** kartu, zadejte HOSTITELI (plně kvalifikovaný název domény) účtu služby Azure Cosmos DB a PORT.

    ![Snímek obrazovky znázorňující kartu Studio 3T připojení správce serveru](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. V **nové připojení** okno na **ověřování** kartu, vybrat režim ověřování **Basic (MONGODB CR nebo SCARM – SHA-1)** a zadejte uživatelské jméno a heslo.  Přijměte výchozí ověřování databáze (správce) nebo zadejte vlastní hodnotu.

    ![Snímek obrazovky znázorňující kartu Studio 3T připojení Správce ověřování](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. V **nové připojení** okno na **SSL** kartě **protokolu SSL používá pro připojení** zaškrtávací políčko a **přijmout certifikáty SSL podepsaný svým držitelem serveru**  přepínač.

    ![Snímek obrazovky znázorňující kartu Studio 3T připojení správce SSL](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Klikněte na tlačítko **Test připojení** tlačítko ověřit informace o připojení, klikněte na tlačítko **OK** vrátit do okna nové připojení, a potom klikněte na **Uložit**.

    ![Snímek obrazovky okna Studio 3T test připojení](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Použití studia 3T k vytvoření databáze, kolekce a dokumenty
Pokud chcete vytvořit databázi, kolekci a dokumenty použití studia 3T, proveďte následující kroky:

1. V **Správce připojení**, zvýrazněte připojení a klikněte na tlačítko **připojit**.

    ![Snímek obrazovky Správce připojení Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Klikněte pravým tlačítkem na hostitele a zvolte **přidat databázi**.  Zadejte název databáze a klikněte na tlačítko **OK**.

    ![Snímek obrazovky s možností přidat databázi Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Klikněte pravým tlačítkem na databázi a zvolte **přidat kolekci**.  Zadejte název kolekce a klikněte na tlačítko **vytvořit**.

    ![Snímek obrazovky s možností Studio 3T přidat kolekci](./media/mongodb-mongochef/AddCollection.png)
4. Klikněte na tlačítko **kolekce** nabídka položek, klikněte na **přidat dokumentu**.

    ![Snímek obrazovky s položky nabídky Studio 3T přidat dokument](./media/mongodb-mongochef/AddDocument1.png)
5. V dialogovém okně Přidat dokumentu vložte následující údaje a pak klikněte na tlačítko **přidat dokumentu**.

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
6. Přidáte jiného dokumentu, tentokrát s následujícím obsahem:

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
7. Spuštění ukázkového dotazu. Například vyhledejte rodině příjmení "Andersen" a vrátí pole nadřazené položky a stavu.

    ![Výsledky dotazu Mongo Chef snímek obrazovky](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Další postup
* Prozkoumejte rozhraní API MongoDB služby Azure Cosmos DB [ukázky](mongodb-samples.md).
