---
title: Node.Js s využitím Angular aplikace pomocí rozhraní API MongoB (část 1)
titleSuffix: Azure Cosmos DB
description: V této sérii kurzů založených na videích zjistíte, jak vytvořit aplikaci MongoDB s Angular a Node postavenou na službě Azure Cosmos DB s použitím stejných rozhraní API, jako používáte pro MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18
ms.openlocfilehash: ea69e4c8ba74dc685dd0b0fb7ac61cc6d061a26f
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53137646"
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db"></a>Vytvoření aplikace MongoDB s Angular a službou Azure Cosmos DB 

Tento vícedílný kurz ukazuje, jak vytvořit novou aplikaci [MongoDB API](mongodb-introduction.md) s Express, Angular a Node.js (balík MEAN) a jak ji připojit k databázi Azure Cosmos DB. Azure Cosmos DB podporuje klientská připojení MongoDB, takže můžete místo MongoDB používat službu Azure Cosmos DB a přitom dál používat stejný kód jako pro aplikace MongoDB, ale s dalšími výhodami. Tyto výhody, které přináší služba Azure Cosmos DB, jsou snadné nasazení v cloudu, škálování, zabezpečení, globálně replikovaná data, podpora více modelů a mimořádná rychlost čtení a zápisu. 

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Umožňuje rychle vytvořit a dotazovat databáze dokumentů, párů klíč-hodnota a grafů, kterým přináší výhody možnosti globální distribuce a horizontálního škálování v jádru služby Azure Cosmos DB. 

Tento vícedílný kurz se zabývá následujícími úlohami:

> [!div class="checklist"]
> * [Vytvoření aplikace Node.js Express pomocí Angular CLI](tutorial-develop-mongodb-nodejs-part2.md)
> * [Vytvoření uživatelského rozhraní pomocí Angular](tutorial-develop-mongodb-nodejs-part3.md)
> * [Vytvoření účtu služby Azure Cosmos DB pomocí Azure CLI](tutorial-develop-mongodb-nodejs-part4.md) 
> * [Připojení ke službě Azure Cosmos DB pomocí Mongoose](tutorial-develop-mongodb-nodejs-part5.md)
> * [Přidání funkcí Post, Put a Delete do aplikace](tutorial-develop-mongodb-nodejs-part6.md)

Chcete vytvořit stejnou aplikaci pomocí React? Podívejte se na [sérii videokurzů pro React](tutorial-develop-mongodb-react.md).

## <a name="video-walkthrough"></a>Video s návodem

> [!VIDEO https://www.youtube.com/embed/vlZRP0mDabM]

## <a name="finished-project"></a>Dokončený projekt 

Tento kurz vás postupně provede jednotlivými kroky vytvoření aplikace. Pokud si chcete stáhnout dokončený projekt, v [úložišti angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) na GitHubu můžete získat hotovou aplikaci.

## <a name="next-steps"></a>Další postup

V této části kurzu jste provedli následující:

> [!div class="checklist"]
> * Podívali jste se na přehled kroků k vytvoření aplikace MEAN.js se službou Azure Cosmos DB. 

Můžete přejít k další části kurzu a vytvořit aplikaci Node.js Express.

> [!div class="nextstepaction"]
> [Vytvoření aplikace Node.js Express pomocí Angular CLI](tutorial-develop-mongodb-nodejs-part2.md)
