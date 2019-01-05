---
title: Vytvořit aplikaci Angular s rozhraním API služby Azure Cosmos DB pro MongoDB – vytvoření účtu Cosmos
titleSuffix: Azure Cosmos DB
description: Čtvrtá část série kurzů týkající se vytvoření aplikace MongoDB s Angular a Node postavené na službě Azure Cosmos DB s použitím stejných rozhraní API, jako používáte pro MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: 8320204f75e583dae0449f83e7c38f6638371c2a
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035110"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>Vytvořit aplikaci Angular s rozhraním API služby Azure Cosmos DB pro MongoDB – vytvoření účtu Cosmos

Tento vícedílný kurz ukazuje, jak vytvořit novou aplikaci napsané v Node.js s Express a Angular a připojte ho k vaší [Cosmos účtu nakonfigurovanému pro Cosmos DB přes rozhraní API pro MongoDB](mongodb-introduction.md).

Čtvrtá část kurzu vychází z [části 3](tutorial-develop-mongodb-nodejs-part3.md) a zabývá se následujícími úlohami:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků Azure pomocí Azure CLI
> * Vytvoření účtu Cosmos pomocí rozhraní příkazového řádku Azure

## <a name="video-walkthrough"></a>Video s návodem

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Požadavky

Před zahájením této části kurzu se ujistěte, že jste dokončili kroky v [části 3](tutorial-develop-mongodb-nodejs-part3.md) tohoto kurzu. 

V této části kurzu můžete použít Azure Cloud Shell (v internetovém prohlížeči) nebo místně nainstalované [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Tento kurz vás postupně provede jednotlivými kroky vytvoření aplikace. Pokud si chcete stáhnout dokončený projekt, v [úložišti angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) na GitHubu můžete získat hotovou aplikaci.

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Pomocí příkazu [`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create) vytvořte účet služby Azure Cosmos DB.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* Místo `<cosmosdb-name>` použijte vlastní jedinečný název účtu služby Azure Cosmos DB. Tento název musí být jedinečný mezi všemi názvy účtů služby Azure Cosmos DB v Azure.
* Nastavení `--kind MongoDB` umožňuje, aby služba Azure Cosmos DB měla klientská připojení MongoDB.

Dokončení příkazu může pár minut trvat. Po dokončení se v okně terminálu zobrazí informace o nové databázi. 

Po vytvoření účtu služby Azure Cosmos DB:
1. Otevřete nové okno prohlížeče a přejděte na adresu [https://portal.azure.com](https://portal.azure.com)
1. Klikněte na logo služby Azure Cosmos DB ![Ikona služby Azure Cosmos DB na webu Azure Portal](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) na levém panelu a zobrazí se všechny účty služby Azure Cosmos DB, které máte.
1. Klikněte na účet služby Azure Cosmos DB, který jste právě vytvořili, vyberte kartu **Přehled** a přejděte dolů k zobrazení mapy s umístěním databáze. 

    ![Nový účet služby Azure Cosmos DB na webu Azure Portal](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. V levém navigačním panelu přejděte dolů a klikněte na kartu **Globální replikace dat**. Zobrazí se mapa s vyznačením různých oblastí, do kterých můžete replikovat. Můžete například kliknout na Austrálie – jihovýchod nebo Austrálie – východ a replikovat svá data do Austrálie. Další informace o globální replikaci najdete v tématu [Globální distribuce dat pomocí služby Azure Cosmos DB](distribute-data-globally.md). Prozatím ponecháme jenom jednu instanci a až budeme chtít replikovat, budeme vědět jak.

    ![Nový účet služby Azure Cosmos DB na webu Azure Portal](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>Další postup

V této části kurzu jste provedli následující:

> [!div class="checklist"]
> * Vytvořili jste skupinu prostředků Azure pomocí Azure CLI.
> * Vytvořili jste účet služby Azure Cosmos DB pomocí Azure CLI.

Můžete přejít k další části kurzu a připojit službu Azure Cosmos DB ke své aplikaci pomocí Mongoose.

> [!div class="nextstepaction"]
> [Připojení ke službě Azure Cosmos DB pomocí Mongoose](tutorial-develop-mongodb-nodejs-part5.md)
