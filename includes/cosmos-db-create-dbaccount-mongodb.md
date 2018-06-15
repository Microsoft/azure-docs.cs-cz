---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 690c5f07a2b0f7a88e16f0b0bbbaa9ca78e37317
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31429230"
---
1. V novém okně se přihlaste k webu [Azure Portal](https://portal.azure.com/).
2. V levé nabídce klikněte na **Vytvořit prostředek**, pak na **Databáze** a nakonec v části **Azure Cosmos DB** klikněte na **Vytvořit**.
   
   ![Snímek obrazovky webu Azure Portal se zvýrazněním položek Další služby a Databáze Azure Cosmos](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. V okně **Nový účet** určete jako rozhraní API možnost **MongoDB** a zadejte požadovanou konfiguraci účtu Azure Cosmos DB.
 
    * **ID** musí být jedinečný název, který chcete používat k identifikaci svého účtu Azure Cosmos DB. Může obsahovat pouze malá písmena, číslice a znak spojovníku (-) a musí se skládat ze 3 až 50 znaků.
    * **Předplatné** je vaše předplatné Azure. Portál ho doplní za vás.
    * **Skupina prostředků** je název skupiny prostředků pro váš účet Azure Cosmos DB. Vyberte**Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. V zájmu jednoduchosti můžete použít název, který se shoduje s vaším ID.
    * **Umístění** je zeměpisná poloha, kde se nachází vaše instance Azure Cosmos DB. Zvolte polohu co nejblíž vašim uživatelům.

    Poté klikněte na **Vytvořit**.

    ![Stránka nového účtu pro službu Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Vytvoření účtu trvá několik minut. Počkejte, až portál zobrazí zprávu **Blahopřejeme! Váš účet služby Azure Cosmos DB s rozhraním MongoDB API je připraven**.

    ![Podokno Oznámení portálu Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
