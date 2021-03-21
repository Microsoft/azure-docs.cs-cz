---
title: Kurz pro MongoDB, reakci a Node.js pro Azure
description: V této sérii kurzů založených na videích zjistíte, jak vytvořit aplikaci MongoDB s React a Node.js postavenou na službě Azure Cosmos DB s použitím stejných rozhraní API, jako používáte pro MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: jopapa
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: 1425b89e42450123c1696ddcee4458e1f69b8a6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348564"
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>Vytvoření aplikace MongoDB s React a službou Azure Cosmos DB  
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Tento vícedílný videokurz ukazuje, jak vytvořit aplikaci pro sledování hrdinů s front-endem v React. Aplikace použila pro server uzel a expresní verzi, připojí se k databázi Cosmos s nakonfigurovaným [rozhraním API Azure Cosmos DB pro MongoDB](mongodb-introduction.md)a pak připojí reakce front-end k serverové části aplikace. V tomto kurzu také ukážeme, Cosmos DB jak v Azure Portal a v případě, že nasadíte aplikaci na Internet a jak nasazovat aplikaci na Internet, aby všichni mohli sledovat své oblíbené Heroes. 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) podporuje kompatibilitu s protokolem MongoDB a umožňuje klientům používat Azure Cosmos DB místo MongoDB.  

Tento vícedílný kurz se zabývá následujícími úlohami:

> [!div class="checklist"]
> * Úvod
> * Nastavení projektu
> * Vytvoření uživatelského rozhraní pomocí React
> * Vytvoření účtu služby Azure Cosmos DB pomocí webu Azure Portal
> * Připojení ke službě Azure Cosmos DB pomocí Mongoose
> * Přidání operací React pro vytvoření, aktualizaci a odstranění do aplikace

Chcete vytvořit stejnou aplikaci pomocí Angular? Podívejte se na [sérii videokurzů pro Angular](tutorial-develop-mongodb-nodejs.md).

## <a name="prerequisites"></a>Předpoklady
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>Dokončený projekt
Hotovou aplikaci můžete získat [z GitHubu](https://github.com/Azure-Samples/react-cosmosdb).

## <a name="introduction"></a>Úvod 

V tomto videu Burke Holland představí službu Azure Cosmos DB a provede vás aplikací, která se vytváří v této sérií videí. 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>Nastavení projektu

Toto video ukazuje, jak ve stejném projektu nastavit Express a React. Burke pak podrobně projde kód v tomto projektu.

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>Vytvoření uživatelského rozhraní

Toto video ukazuje, jak vytvořit uživatelské rozhraní aplikace pomocí React. 

> [!NOTE]
> Šablony stylů CSS zmiňované v tomto videu najdete v [úložišti react-cosmosdb na GitHubu](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css).

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Připojení ke službě Azure Cosmos DB

Toto video ukazuje, jak vytvořit účet služby Azure Cosmos DB na webu Azure Portal, nainstalovat balíčky MongoDB a Mongoose a pak aplikaci připojit k nově vytvořenému účtu pomocí připojovacího řetězce služby Azure Cosmos DB. 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>Načítání a vytváření hrdinů v aplikaci

Toto video ukazuje, jak číst Heroes a vytvářet Heroes v databázi Cosmos a jak testovat tyto metody pomocí funkce post a uživatelského rozhraní s reakce. 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>Odstranění a aktualizace hrdinů v aplikaci

Toto video ukazuje, jak z aplikace odstranit a aktualizovat hrdiny a jak zobrazit aktualizace v uživatelském rozhraní. 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>Dokončení aplikace

Toto video ukazuje, jak dokončit aplikaci a napojování uživatelského rozhraní na rozhraní API back-endu. 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem na webu Azure Portal. 

1. V nabídce vlevo na portálu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na název vytvořeného prostředku. 
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření aplikace s React, Node, Express a službou Azure Cosmos DB 
> * Vytvoření účtu služby Azure Cosmos DB
> * Připojení aplikace k účtu služby Azure Cosmos DB
> * Testování aplikace pomocí nástroje Postman
> * Spuštění aplikace a přidání hrdinů do databáze

Můžete pokračovat k dalšímu kurzu, kde zjistíte, jak importovat data MongoDB do služby Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importování dat MongoDB do databáze Azure Cosmos](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
