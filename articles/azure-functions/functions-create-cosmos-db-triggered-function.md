---
title: Vytvoření funkce aktivované službou Azure Cosmos DB | Dokumentace Microsoftu
description: Pomocí služby Azure Functions vytvoříte funkci bez serveru, která se vyvolá při přidání dat do databáze ve službě Azure Cosmos DB.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/27/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 9ba7d8c403a7778a52b858a41ad41bca405cb199
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31513981"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Vytvoření funkce aktivované službou Azure Cosmos DB

Zjistěte, jak vytvořit funkci aktivovanou při přidání nebo změně dat ve službě Azure Cosmos DB. Další informace o službě Azure Cosmos DB najdete v tématu [Azure Cosmos DB: Databázové výpočetní prostředí bez serveru využívající Azure Functions](..\cosmos-db\serverless-computing-database.md).

![Zobrazte si zprávy v protokolech.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

+ Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-an-azure-function-app"></a>Vytvoření aplikace Azure Function App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Dál vytvoříte v nové aplikaci Function App funkci.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Vytvoření triggeru služby Azure Cosmos DB

1. Rozbalte aplikaci Function App a klikněte na tlačítko **+** vedle položky **Funkce**. Pokud jde o první funkci ve vaší aplikaci Function App, vyberte možnost **Vlastní funkce**. Zobrazí se kompletní sada šablon funkcí.

    ![Stručný úvod do služby Functions na webu Azure Portal](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

2. Do vyhledávacího pole zadejte `cosmos` a zvolte jazyk požadovaný pro šablonu funkce aktivované službou Azure Cosmos DB.

    ![Výběr funkce aktivované službou Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/select-cosmos-db-trigger-portal.png)

3. Nakonfigurujte novou aktivační událost s nastaveními uvedenými v tabulce pod obrázkem.

    ![Vytvoření funkce aktivované službou Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)
    
    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Název** | Výchozí | Použijte výchozí název funkce navrhovaný šablonou. |
    | **Název kolekce** | Items | Název kolekce, která se má monitorovat. |
    | **Vytvořit kolekci zapůjčení, pokud neexistuje** | Zaškrtnuté | Kolekce ještě neexistuje, takže ji vytvořte. |
    | **Název databáze** | Úlohy | Název databáze s kolekcí, která se má monitorovat. |

4. Vedle popisku **Připojení účtu služby Azure Cosmos DB** vyberte **Nový** a zvolte existující účet služby Cosmos DB nebo vyberte možnost **+ Vytvořit nový**. 
 
    ![Konfigurace připojení služby Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-create-CosmosDB.png)

6. Při vytváření nového účtu služby Cosmos DB použijte nastavení pro **Nový účet** uvedená v tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **ID** | Název databáze | Jedinečné ID databáze Azure Cosmos DB  |
    | **Rozhraní API** | SQL | V tomto tématu se používá rozhraní SQL API.  |
    | **Předplatné** | předplatné Azure | Předplatné, ve kterém se tento nový účet služby Cosmos DB vytvoří.  |
    | **Skupina prostředků** | myResourceGroup |  Použijte existující skupinu prostředků, která obsahuje vaši aplikací funkcí. |
    | **Umístění**  | WestEurope | Vyberte umístění blízko vaší aplikaci funkcí nebo jiným aplikacím, které používají uložené dokumenty.  |

6. Kliknutím na **OK** vytvořte databázi. Vytvoření databáze může trvat několik minut. Po vytvoření databáze se připojovací řetězec databáze uloží jako nastavení aplikace funkcí. Název tohoto nastavení aplikace se vloží do **Připojení účtu služby Azure Cosmos DB**. 

7. Kliknutím na **Vytvořit** vytvořte funkci aktivovanou službou Azure Cosmos DB. Po vytvoření funkce se zobrazí kód funkce založené na šabloně.  

    ![Šablona funkce Cosmos DB v jazyce C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Tato šablona funkce zapíše do protokolů počet dokumentů a ID prvního dokumentu. 

Dále se připojíte ke svému účtu služby Azure Cosmos DB a vytvoříte v databázi kolekci **Úlohy**. 

## <a name="create-the-items-collection"></a>Vytvoření kolekce Items (Položky)

1. Na nové kartě prohlížeče otevřete druhou instanci webu [Azure Portal](https://portal.azure.com). 

2. Na levé straně portálu rozbalte pruh ikon, do vyhledávacího pole zadejte `cosmos` a vyberte **Azure Cosmos DB**.

    ![Vyhledání služby Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

2. Zvolte váš účet služby Azure Cosmos DB a vyberte **Průzkumník dat**. 
 
3. V části **Kolekce** zvolte **taskDatabase** a vyberte **Nová kolekce**.

    ![Vytvoření kolekce](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

4. V části **Přidat kolekci** použijte nastavení uvedená v tabulce pod obrázkem. 
 
    ![Definování kolekce taskCollection](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)
 
    | Nastavení|Navrhovaná hodnota|Popis |
    | ---|---|--- |
    | **ID databáze** | Úlohy |Název nové databáze. Musí se shodovat s názvem definovaným ve vazbě vaší funkce. |
    | **ID kolekce** | Items | Název nové kolekce. Musí se shodovat s názvem definovaným ve vazbě vaší funkce.  |
    | **Kapacita úložiště** | Pevná (10 GB)|Použijte výchozí hodnotu. Tato hodnota je kapacita úložiště databáze. |
    | **Propustnost** |400 RU| Použijte výchozí hodnotu. Pokud budete chtít snížit latenci, můžete propustnost později navýšit. |
    | **[Klíč oddílu](../cosmos-db/partition-data.md#design-for-scale)** | /kategorie|Klíč oddílu, který rovnoměrně distribuuje data do jednotlivých oddílů. Výběr správného klíče oddílu je důležitý pro vytvoření výkonné kolekce. | 

1. Kliknutím na **OK** vytvořte kolekci **Úlohy**. Vytvoření kolekce může chvíli trvat.

Jakmile bude existovat kolekce definovaná ve vazbě funkce, můžete funkci otestovat přidáním dokumentů do této nové kolekce.

## <a name="test-the-function"></a>Testování funkce

1. V Průzkumníku dat rozbalte novou kolekci **taskCollection**, zvolte **Dokumenty** a vyberte **Nový dokument**.

    ![Vytvoření dokumentu v kolekci taskCollection](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

2. Nahraďte obsah nového dokumentu následujícím obsahem a pak zvolte **Uložit**.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Přepněte na první kartu prohlížeče, která obsahuje vaši funkci na portálu. Rozbalte protokoly funkce a ověřte, že nový dokument aktivoval funkci. Všimněte si, že se do protokolů zapsala hodnota ID dokumentu `task1`. 

    ![Zobrazte si zprávy v protokolech.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

4. (Volitelné) Vraťte se ke svému dokumentu, proveďte nějakou změnu a klikněte na **Aktualizovat**. Pak přejděte zpět do protokolů funkce a ověřte, že aktualizace také aktivovala funkci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Vytvořili jste funkci, která se spouští při přidání nebo úpravě dokumentu ve službě Azure Cosmos DB.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Další informace o funkcích aktivovaných službou Azure Cosmos DB najdete v tématu [Vazby Azure Cosmos DB ve službě Azure Functions](functions-bindings-cosmosdb.md).
