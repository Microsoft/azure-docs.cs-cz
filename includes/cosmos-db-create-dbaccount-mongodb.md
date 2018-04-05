---
title: Vytvoření účtu Azure Cosmos DB s rozhraním MongoDB API
description: Popisuje, jak vytvořit účet Azure Cosmos DB s rozhraním MongoDB API na portálu Azure Portal.
services: cosmos-db
author: mimig1
ms.service: cosmos-db
ms.topic: include
ms.date: 03/20/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 02ea0e011642313b885bc48ec48104fa2789da81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
1. V novém okně se přihlaste k portálu [Azure Portal](https://portal.azure.com/).
2. V levé nabídce klikněte na **Vytvořit prostředek**, pak na **Databáze** a nakonec v části **Azure Cosmos DB** klikněte na **Vytvořit**.
   
   ![Snímek obrazovky portálu Azure Portal se zvýrazněním položek Další služby a Databáze Azure Cosmos](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. V okně **Nový účet** určete jako rozhraní API možnost **MongoDB** a zadejte požadovanou konfiguraci účtu Azure Cosmos DB.
 
    ![Snímek okna Nový v Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)

    * **ID** musí být jedinečný název, který chcete používat k identifikaci svého účtu Azure Cosmos DB. Může obsahovat pouze malá písmena, číslice a znak spojovníku (-) a musí se skládat ze 3 až 50 znaků.
    * **Předplatné** je vaše předplatné Azure. Portál ho doplní za vás.
    * **Skupina prostředků** je název skupiny prostředků pro váš účet Azure Cosmos DB.
    * **Umístění** je zeměpisná poloha, kde se nachází vaše instance Azure Cosmos DB. Zvolte polohu co nejblíž vašim uživatelům.

4. Kliknutím na **Vytvořit** vytvořte účet.
5. Na panelu nástrojů klikněte na **Oznámení** a sledujte proces nasazení.

    ![Oznámení o zahájení nasazení](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  Po dokončení nasazení otevřete nový účet na dlaždici Všechny prostředky. 

    ![Účet služby Azure Cosmos DB na dlaždici Všechny prostředky](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
