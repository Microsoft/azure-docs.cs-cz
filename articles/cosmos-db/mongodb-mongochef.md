---
title: Použití studia 3T k připojení k rozhraní API Azure Cosmos DB pro MongoDB
description: Přečtěte si, jak se připojit k rozhraní API Azure Cosmos DB pro MongoDB pomocí studia 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: a02aaadf8c774557eb182acf041b6f19337a0de8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096508"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Připojení k účtu Azure Cosmos pomocí studia 3T
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Pokud se chcete připojit k rozhraní API Azure Cosmos DB pro MongoDB pomocí studia 3T, musíte:

* Stáhněte a nainstalujte [Studio 3T](https://studio3t.com/).
* Mít informace o [připojovacím řetězci](connect-mongodb-account.md) účtu Azure Cosmos.

## <a name="create-the-connection-in-studio-3t"></a>Vytvoření připojení v Studio 3T

Pokud chcete přidat účet Azure Cosmos do Správce připojení studia 3T, použijte následující postup:

1. Pomocí pokynů v tématu [připojení aplikace MongoDB k Azure Cosmos DB](connect-mongodb-account.md) článku načtěte informace o připojení pro účet MongoDB API pro váš Azure Cosmos DB.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="Snímek obrazovky se stránkou připojovacího řetězce":::

2. Kliknutím na **připojit** otevřete Správce připojení a pak klikněte na **nové připojení** .

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="Snímek obrazovky se stránkou připojovacího řetězce":::
3. V okně **nové připojení** na kartě **Server** zadejte hostitele (FQDN) účtu Azure Cosmos a portu.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="Snímek obrazovky se stránkou připojovacího řetězce":::
4. V okně **nové připojení** na kartě **ověřování** vyberte možnost režim ověřování **Basic (MONGODB-CR nebo SCARM-SHA-1)** a zadejte uživatelské jméno a heslo.  Přijměte výchozí ověřovací databázi (správce) nebo zadejte vlastní hodnotu.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="Snímek obrazovky se stránkou připojovacího řetězce":::
5. V okně **nové připojení** na kartě **SSL** zaškrtněte políčko **používat protokol SSL pro připojení** a přepínač pro **příjem certifikátů SSL přihlášený držitelem** .

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="Snímek obrazovky se stránkou připojovacího řetězce":::
6. Kliknutím na tlačítko **Testovat připojení** ověřte informace o připojení, kliknutím na tlačítko **OK** se vraťte do okna nové připojení a pak klikněte na **Uložit** .

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="Snímek obrazovky se stránkou připojovacího řetězce":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Použití studia 3T k vytvoření databáze, kolekce a dokumentů
Pokud chcete vytvořit databázi, kolekci a dokumenty pomocí nástroje Studio 3T, proveďte následující kroky:

1. V **programu Správce připojení** zvýrazněte připojení a klikněte na **připojit** .

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="Snímek obrazovky se stránkou připojovacího řetězce":::
2. Klikněte pravým tlačítkem na hostitele a vyberte **Přidat databázi** .  Zadejte název databáze a klikněte na tlačítko **OK** .

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="Snímek obrazovky se stránkou připojovacího řetězce":::
3. Klikněte pravým tlačítkem na databázi a vyberte **přidat kolekci** .  Zadejte název kolekce a klikněte na **vytvořit** .

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="Snímek obrazovky se stránkou připojovacího řetězce":::
4. Klikněte na položku nabídky **kolekce** a pak klikněte na **Přidat dokument** .

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="Snímek obrazovky se stránkou připojovacího řetězce" },
        "isRegistered": false
    }
    ```

7. Spustí ukázkový dotaz. Hledejte například skupiny s názvem ' Andersen ' a vraťte pole nadřazených a státních stavů.

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="Snímek obrazovky se stránkou připojovacího řetězce":::

## <a name="next-steps"></a>Další kroky

- Naučte se [používat Robo 3T](mongodb-robomongo.md) s rozhraním API Azure Cosmos DB pro MongoDB.
- Prozkoumejte [ukázky](mongodb-samples.md) MONGODB s rozhraním API Azure Cosmos DB pro MongoDB.
