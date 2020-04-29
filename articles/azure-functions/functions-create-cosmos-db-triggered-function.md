---
title: Vytvoření funkce aktivované službou Azure Cosmos DB
description: Pomocí služby Azure Functions vytvoříte funkci bez serveru, která se vyvolá při přidání dat do databáze ve službě Azure Cosmos DB.
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: how-to
ms.date: 10/02/2018
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 6045c61dc9837667bfaf01c685f687fcf5816e4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80754213"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Vytvoření funkce aktivované službou Azure Cosmos DB

Zjistěte, jak vytvořit funkci aktivovanou při přidání nebo změně dat ve službě Azure Cosmos DB. Další informace o službě Azure Cosmos DB najdete v tématu [Azure Cosmos DB: Databázové výpočetní prostředí bez serveru využívající Azure Functions](../cosmos-db/serverless-computing-database.md).

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

1. Rozbalte aplikaci Function App a klikněte na **+** tlačítko vedle položky **funkce**. Pokud jde o první funkci ve vaší aplikaci Function App, vyberte **Na portálu** a potom **Pokračovat**. V opačném případě přejděte ke třetímu kroku.

   ![Stručný úvod do služby Functions na webu Azure Portal](./media/functions-create-cosmos-db-triggered-function/function-app-quickstart-choose-portal.png)

1. Zvolte **Další šablony** a potom **Dokončit a zobrazit šablony**.

    ![Rychlý start služby Functions – výběr dalších šablon](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

1. Do vyhledávacího pole zadejte `cosmos` a zvolte šablonu **Trigger Azure Cosmos DB**.

1. Pokud se zobrazí výzva, vyberte **instalovat** a nainstalujte Azure Cosmos DB rozšíření do aplikace Function App. Po úspěšném dokončení instalace vyberte **Pokračovat**.

    ![Instalace rozšíření vazby](./media/functions-create-cosmos-db-triggered-function/functions-create-cosmos-db-trigger-portal.png)

1. Nakonfigurujte novou aktivační událost s nastaveními uvedenými v tabulce pod obrázkem.

    ![Vytvoření funkce aktivované službou Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Název** | Výchozí | Použijte výchozí název funkce navrhovaný šablonou.|
    | **Připojení účtu Azure Cosmos DB** | Nové nastavení | Vyberte **Nové**, vyberte **Předplatné** a **Databázový účet**, které jste předtím vytvořili, a zvolte **Vybrat**. Vytvoří se nastavení aplikace pro připojení k vašemu účtu. Toto nastavení vazba použije k připojení k databázi. |
    | **Název kontejneru** | Items | Název kontejneru, který se má monitorovat |
    | **Vytvořit kontejner zapůjčení, pokud neexistuje** | Zaškrtnuté | Kontejner ještě neexistuje, proto ho vytvořte. |
    | **Název databáze** | Úlohy | Název databáze s kontejnerem, který se má monitorovat |

1. Kliknutím na **Vytvořit** vytvořte funkci aktivovanou službou Azure Cosmos DB. Po vytvoření funkce se zobrazí kód funkce založené na šabloně.  

    ![Šablona funkce Cosmos DB v jazyce C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Tato šablona funkce zapíše do protokolů počet dokumentů a ID prvního dokumentu.

V dalším kroku se připojíte k účtu Azure Cosmos DB a vytvoříte `Items` kontejner v `Tasks` databázi.

## <a name="create-the-items-container"></a>Vytvořit kontejner položek

1. Na nové kartě prohlížeče otevřete druhou instanci webu [Azure Portal](https://portal.azure.com).

1. Na levé straně portálu rozbalte pruh ikon, do vyhledávacího pole zadejte `cosmos` a vyberte **Azure Cosmos DB**.

    ![Vyhledání služby Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Zvolte váš účet služby Azure Cosmos DB a vyberte **Průzkumník dat**. 

1. V části **SQL API**zvolte možnost databáze **úkolů** a vyberte **Nový kontejner**.

    ![Vytvoření kontejneru](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. V části **Přidat kontejner**použijte nastavení uvedená v tabulce pod obrázkem. 

    ![Definování kontejneru úkoly](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | Nastavení|Navrhovaná hodnota|Popis |
    | ---|---|--- |
    | **ID databáze** | Úlohy |Název nové databáze. Musí se shodovat s názvem definovaným ve vazbě vaší funkce. |
    | **ID kontejneru** | Items | Název nového kontejneru. Musí se shodovat s názvem definovaným ve vazbě vaší funkce.  |
    | **[Klíč oddílu](../cosmos-db/partition-data.md)** | /kategorie|Klíč oddílu, který rovnoměrně distribuuje data do jednotlivých oddílů. Výběr správného klíče oddílu je důležitý při vytváření výkonného kontejneru. | 
    | **Propustnost** |400 RU| Použijte výchozí hodnotu. Pokud budete chtít snížit latenci, můžete propustnost později navýšit. |    

1. Kliknutím na tlačítko **OK** vytvořte kontejner položek. Vytvoření kontejneru může trvat krátkou dobu.

Po tom, co kontejner zadaný ve vazbě funkce existuje, můžete funkci otestovat přidáním položek do tohoto nového kontejneru.

## <a name="test-the-function"></a>Testování funkce

1. Rozbalte kontejner nové **položky** v Průzkumník dat, zvolte položku **položky**a vyberte možnost **Nová položka**.

    ![Vytvoření položky v kontejneru položek](./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png)

1. Nahraďte obsah nové položky následujícím obsahem a pak zvolte **Uložit**.

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

Vytvořili jste funkci, která se spouští při přidání nebo úpravě dokumentu ve službě Azure Cosmos DB. Další informace o funkcích aktivovaných službou Azure Cosmos DB najdete v tématu [Vazby Azure Cosmos DB ve službě Azure Functions](functions-bindings-cosmosdb.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
