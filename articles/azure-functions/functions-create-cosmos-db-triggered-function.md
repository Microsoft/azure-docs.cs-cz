---
title: Vytvoření funkce aktivované službou Azure Cosmos DB
description: Pomocí služby Azure Functions vytvoříte funkci bez serveru, která se vyvolá při přidání dat do databáze ve službě Azure Cosmos DB.
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: how-to
ms.date: 04/28/2020
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: c7dc18d8186d7262154cc0718bb6ad77ebbb5d2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85829835"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Vytvoření funkce aktivované službou Azure Cosmos DB

Zjistěte, jak vytvořit funkci aktivovanou při přidání nebo změně dat ve službě Azure Cosmos DB. Další informace o službě Azure Cosmos DB najdete v tématu [Azure Cosmos DB: Databázové výpočetní prostředí bez serveru využívající Azure Functions](../cosmos-db/serverless-computing-database.md).

:::image type="content" source="./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png" alt-text="Kód Azure Cosmos DB":::

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

+ Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Před vytvořením triggeru potřebujete účet služby Azure Cosmos DB, který používá rozhraní SQL API.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>Vytvoření aplikace Azure Function App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Dál vytvoříte v nové aplikaci Function App funkci.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Vytvoření triggeru služby Azure Cosmos DB

1. Ve vaší aplikaci Function App vyberte v nabídce vlevo možnost **funkce** a v horní nabídce vyberte **Přidat** . 

1. Na stránce **Nová funkce** zadejte `cosmos` do vyhledávacího pole a pak zvolte šablonu **triggeru Azure Cosmos DB** .

   :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-choose-cosmos.png" alt-text="Kód Azure Cosmos DB":::


1. Nakonfigurujte novou aktivační událost s nastavením, jak je uvedeno v následující tabulce:

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Nová funkce** | Přijměte výchozí název. | Název funkce |
    | **Připojení účtu databáze Cosmos DB** | Přijměte výchozí nový název. | Vyberte **Nový**, **databázový účet** , který jste vytvořili dříve, a pak klikněte na **OK**. Tato akce vytvoří nastavení aplikace pro připojení k účtu. Toto nastavení vazba použije k připojení k databázi. |
    | **Název databáze** | Úlohy | Název databáze, která obsahuje kolekci, která se má monitorovat |
    | **Název kolekce** | Položky | Název kolekce, která se má monitorovat |
    | **Název kolekce pro zapůjčení** | leases | Název kolekce, do které se mají ukládat zapůjčení |
    | **Vytvořit kolekci zapůjčení, pokud neexistuje** | Yes | Kontroluje existenci kolekce zapůjčení a automaticky ji vytvoří. |

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png" alt-text="Kód Azure Cosmos DB":::

1. Vyberte **Vytvořit funkci**. 

    Azure vytvoří funkci triggeru Cosmos DB.

1. Chcete-li zobrazit kód funkce založené na šablonách, vyberte **kód + test**.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png" alt-text="Kód Azure Cosmos DB":::

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
    | **ID kontejneru** | Položky | Název nového kontejneru. Musí se shodovat s názvem definovaným ve vazbě vaší funkce.  |
    | **[Klíč oddílu](../cosmos-db/partition-data.md)** | /kategorie|Klíč oddílu, který rovnoměrně distribuuje data do jednotlivých oddílů. Výběr správného klíče oddílu je důležitý při vytváření výkonného kontejneru. | 
    | **Propustnost** |400 RU| Použijte výchozí hodnotu. Pokud budete chtít snížit latenci, můžete propustnost později navýšit. |    

1. Kliknutím na tlačítko **OK** vytvořte kontejner položek. Vytvoření kontejneru může trvat krátkou dobu.

Po tom, co kontejner zadaný ve vazbě funkce existuje, můžete funkci otestovat přidáním položek do tohoto nového kontejneru.

## <a name="test-the-function"></a>Testování funkce

1. Rozbalte kontejner nové **položky** v Průzkumník dat, zvolte položku **položky**a vyberte možnost **Nová položka**.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png" alt-text="Kód Azure Cosmos DB"
    }
    ```

1. Přepněte na první kartu prohlížeče, která obsahuje vaši funkci na portálu. Rozbalte protokoly funkce a ověřte, že nový dokument aktivoval funkci. Všimněte si, že se do protokolů zapsala hodnota ID dokumentu `task1`. 

    ![Zobrazte si zprávy v protokolech.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (Volitelné) Vraťte se ke svému dokumentu, proveďte nějakou změnu a klikněte na **Aktualizovat**. Pak přejděte zpět do protokolů funkce a ověřte, že aktualizace také aktivovala funkci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Vytvořili jste funkci, která se spouští při přidání nebo úpravě dokumentu ve službě Azure Cosmos DB. Další informace o funkcích aktivovaných službou Azure Cosmos DB najdete v tématu [Vazby Azure Cosmos DB ve službě Azure Functions](functions-bindings-cosmosdb.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
