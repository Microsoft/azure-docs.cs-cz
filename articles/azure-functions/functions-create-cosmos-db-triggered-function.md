---
title: Vytvoření funkce aktivované službou Azure Cosmos DB | Dokumentace Microsoftu
description: Pomocí služby Azure Functions vytvoříte funkci bez serveru, která se vyvolá při přidání dat do databáze ve službě Azure Cosmos DB.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: azure-functions; cosmos-db
ms.devlang: multiple
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: eb909384805c96ce656cacf86adceedc55613c0f
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49113654"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Vytvoření funkce aktivované službou Azure Cosmos DB

Zjistěte, jak vytvořit funkci aktivovanou při přidání nebo změně dat ve službě Azure Cosmos DB. Další informace o službě Azure Cosmos DB najdete v tématu [Azure Cosmos DB: Databázové výpočetní prostředí bez serveru využívající Azure Functions](..\cosmos-db\serverless-computing-database.md).

![Zobrazte si zprávy v protokolech.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

+ Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Před vytvořením triggeru potřebujete účet služby Azure Cosmos DB, který používá rozhraní SQL API.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>Vytvoření aplikace Azure Function App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Dál vytvoříte v nové aplikaci Function App funkci.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Vytvoření triggeru služby Azure Cosmos DB

1. Rozbalte aplikaci Function App a klikněte na tlačítko **+** vedle položky **Funkce**. Pokud jde o první funkci ve vaší aplikaci Function App, vyberte **Na portálu** a potom **Pokračovat**. V opačném případě přejděte ke třetímu kroku.

   ![Stručný úvod do služby Functions na webu Azure Portal](./media/functions-create-cosmos-db-triggered-function/function-app-quickstart-choose-portal.png)

1. Zvolte **Další šablony** a potom **Dokončit a zobrazit šablony**.

    ![Rychlý start služby Functions – výběr dalších šablon](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

1. Do vyhledávacího pole zadejte `cosmos` a zvolte šablonu **Trigger Azure Cosmos DB**.

1. Pokud se zobrazí výzva, výběrem možnosti **Instalovat** nainstalujte do aplikace funkcí rozšíření Azure Storage a případné závislosti. Po úspěšném dokončení instalace vyberte **Pokračovat**.

    ![Instalace rozšíření vazby](./media/functions-create-cosmos-db-triggered-function/functions-create-cosmos-db-trigger-portal.png)

1. Nakonfigurujte novou aktivační událost s nastaveními uvedenými v tabulce pod obrázkem.

    ![Vytvoření funkce aktivované službou Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Název** | Výchozí | Použijte výchozí název funkce navrhovaný šablonou.|
    | **Připojení účtu Azure Cosmos DB** | Nové nastavení | Vyberte **Nové**, vyberte **Předplatné** a **Databázový účet**, které jste předtím vytvořili, a zvolte **Vybrat**. Vytvoří se nastavení aplikace pro připojení k vašemu účtu. Toto nastavení vazba použije k připojení k databázi. |
    | **Název kolekce** | Items | Název kolekce, která se má monitorovat. |
    | **Vytvořit kolekci zapůjčení, pokud neexistuje** | Zaškrtnuté | Kolekce ještě neexistuje, takže ji vytvořte. |
    | **Název databáze** | Úlohy | Název databáze s kolekcí, která se má monitorovat. |

1. Kliknutím na **Vytvořit** vytvořte funkci aktivovanou službou Azure Cosmos DB. Po vytvoření funkce se zobrazí kód funkce založené na šabloně.  

    ![Šablona funkce Cosmos DB v jazyce C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Tato šablona funkce zapíše do protokolů počet dokumentů a ID prvního dokumentu.

Potom se připojíte ke svému účtu služby Azure Cosmos DB a vytvoříte v databázi `Tasks` kolekci `Items`.

## <a name="create-the-items-collection"></a>Vytvoření kolekce Items (Položky)

1. Na nové kartě prohlížeče otevřete druhou instanci webu [Azure Portal](https://portal.azure.com).

1. Na levé straně portálu rozbalte pruh ikon, do vyhledávacího pole zadejte `cosmos` a vyberte **Azure Cosmos DB**.

    ![Vyhledání služby Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Zvolte váš účet služby Azure Cosmos DB a vyberte **Průzkumník dat**. 

1. V části **Kolekce** zvolte **taskDatabase** a vyberte **Nová kolekce**.

    ![Vytvoření kolekce](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

1. V části **Přidat kolekci** použijte nastavení uvedená v tabulce pod obrázkem. 

    ![Definování kolekce taskCollection](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)

    | Nastavení|Navrhovaná hodnota|Popis |
    | ---|---|--- |
    | **ID databáze** | Úlohy |Název nové databáze. Musí se shodovat s názvem definovaným ve vazbě vaší funkce. |
    | **ID kolekce** | Items | Název nové kolekce. Musí se shodovat s názvem definovaným ve vazbě vaší funkce.  |
    | **Kapacita úložiště** | Pevná (10 GB)|Použijte výchozí hodnotu. Tato hodnota je kapacita úložiště databáze. |
    | **Propustnost** |400 RU| Použijte výchozí hodnotu. Pokud budete chtít snížit latenci, můžete propustnost později navýšit. |
    | **[Klíč oddílu](../cosmos-db/partition-data.md#best-practices-when-choosing-a-partition-key)** | /kategorie|Klíč oddílu, který rovnoměrně distribuuje data do jednotlivých oddílů. Výběr správného klíče oddílu je důležitý pro vytvoření výkonné kolekce. | 

1. Kliknutím na **OK** vytvořte kolekci Items. Vytvoření kolekce může chvíli trvat.

Jakmile bude existovat kolekce definovaná ve vazbě funkce, můžete funkci otestovat přidáním dokumentů do této nové kolekce.

## <a name="test-the-function"></a>Testování funkce

1. V Průzkumníku dat rozbalte novou kolekci **taskCollection**, zvolte **Dokumenty** a vyberte **Nový dokument**.

    ![Vytvoření dokumentu v kolekci taskCollection](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

1. Nahraďte obsah nového dokumentu následujícím obsahem a pak zvolte **Uložit**.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Přepněte na první kartu prohlížeče, která obsahuje vaši funkci na portálu. Rozbalte protokoly funkce a ověřte, že nový dokument aktivoval funkci. Všimněte si, že se do protokolů zapsala hodnota ID dokumentu `task1`. 

    ![Zobrazte si zprávy v protokolech.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (Volitelné) Vraťte se ke svému dokumentu, proveďte nějakou změnu a klikněte na **Aktualizovat**. Pak přejděte zpět do protokolů funkce a ověřte, že aktualizace také aktivovala funkci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Vytvořili jste funkci, která se spouští při přidání nebo úpravě dokumentu ve službě Azure Cosmos DB.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Další informace o funkcích aktivovaných službou Azure Cosmos DB najdete v tématu [Vazby Azure Cosmos DB ve službě Azure Functions](functions-bindings-cosmosdb.md).
