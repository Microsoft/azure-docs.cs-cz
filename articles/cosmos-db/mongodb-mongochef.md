---
title: Připojení k rozhraní API Azure Cosmos DB pro MongoDB pomocí studia 3T
titleSuffix: Azure Cosmos DB
description: Naučte se, jak se připojit k Cosmos DB pomocí sady Studio 3T a rozhraní Azure Cosmos DB API pro MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 46cac6720bbef133d6b3f7a269cf97a81cddd0c5
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062114"
---
# <a name="connect-to-cosmos-account-using-studio-3t"></a>Připojení k účtu Cosmos pomocí studia 3T

Pokud se chcete připojit k účtu Cosmos pomocí rozhraní API služby Azure Cosmos DB pro MongoDB, musíte:

* Stažení a instalace [studia 3T](https://studio3t.com/)
* Informace o [připojovacím řetězci](connect-mongodb-account.md) Cosmos DB

> [!NOTE]
> V současné době jsou podporovány Robo 3T v 1.2 a nižší s rozhraním API Cosmos DB pro MongoDB. 

## <a name="create-the-connection-in-studio-3t"></a>Vytvoření připojení v studia 3T
Pokud chcete přidat účet Cosmos do Správce připojení studia 3T, proveďte následující kroky:

1. Načtěte informace o připojení pro účet Cosmos s nakonfigurovaným rozhraním API Azure Cosmos DB pro MongoDB podle pokynů v tématu [připojení aplikace MongoDB pro Azure Cosmos DB](connect-mongodb-account.md) článku.

    ![Snímek obrazovky se stránkou připojovacího řetězce](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Klikněte na tlačítko **připojit** Správce připojení, potom klikněte na tlačítko **nové připojení**

    ![Snímek obrazovky Správce připojení Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. V **nové připojení** okno na **Server** kartu, zadejte HOSTITELI (plně kvalifikovaný název domény) účtu služby Azure Cosmos DB a PORT.

    ![Snímek obrazovky s kartou serveru správce připojení Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. V **nové připojení** okno na **ověřování** kartu, vybrat režim ověřování **Basic (MONGODB CR nebo SCARM – SHA-1)** a zadejte uživatelské jméno a heslo.  Přijměte výchozí ověřování databáze (správce) nebo zadejte vlastní hodnotu.

    ![Snímek obrazovky s kartou pro ověření Správce připojení Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. V **nové připojení** okno na **SSL** kartě **protokolu SSL používá pro připojení** zaškrtávací políčko a **přijmout certifikáty SSL podepsaný svým držitelem serveru**  přepínač.

    ![Snímek obrazovky s kartou SSL pro správce připojení Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Klikněte na tlačítko **Test připojení** tlačítko ověřit informace o připojení, klikněte na tlačítko **OK** vrátit do okna nové připojení, a potom klikněte na **Uložit**.

    ![Snímek obrazovky okna 3T test Connection pro Studio](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Použití studia 3T k vytvoření databáze, kolekce a dokumenty
Pokud chcete vytvořit databázi, kolekci a dokumenty použití studia 3T, proveďte následující kroky:

1. V **Správce připojení**, zvýrazněte připojení a klikněte na tlačítko **připojit**.

    ![Snímek obrazovky Správce připojení Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Klikněte pravým tlačítkem na hostitele a zvolte **přidat databázi**.  Zadejte název databáze a klikněte na tlačítko **OK**.

    ![Snímek obrazovky s možností přidání databáze Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Klikněte pravým tlačítkem na databázi a zvolte **přidat kolekci**.  Zadejte název kolekce a klikněte na tlačítko **vytvořit**.

    ![Snímek obrazovky s možností přidání kolekce v Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Klikněte na tlačítko **kolekce** nabídka položek, klikněte na **přidat dokumentu**.

    ![Snímek obrazovky s položkou nabídky přidat dokument v Studio 3T](./media/mongodb-mongochef/AddDocument1.png)
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

    ![Snímek obrazovky s výsledky dotazu Mongo](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.
