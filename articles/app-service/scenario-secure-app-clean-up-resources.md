---
title: Kurz – vyčištění prostředků | Azure
description: V tomto kurzu se naučíte vyčistit prostředky Azure přidělené při vytváření webové aplikace.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: bf03fe9d920298d49e79a9a0febf7e09e94eb6ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905515"
---
# <a name="tutorial-clean-up-resources"></a>Kurz: vyčištění prostředků

Pokud jste dokončili všechny kroky v tomto kurzu, vytvořili jste v tomto kurzu službu App Service, plán hostování služby App Service a účet úložiště ve skupině prostředků. Také jste vytvořili registraci aplikace v Azure Active Directory. Pokud už je nepotřebujete, odstraňte tyto prostředky a registraci aplikace, abyste nepokračovali v navýšení poplatků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Odstraňte prostředky Azure vytvořené během tohoto kurzu.

## <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků

V [Azure Portal](https://portal.azure.com)v nabídce portál vyberte **skupiny prostředků** a vyberte skupinu prostředků, která obsahuje službu App Service a plán služby App Service.

Vyberte **Odstranit skupinu prostředků** a odstraňte skupinu prostředků a všechny prostředky.

:::image type="content" alt-text="Snímek obrazovky, který ukazuje odstranění skupiny prostředků." source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

Spuštění tohoto příkazu může trvat několik minut.

## <a name="delete-the-app-registration"></a>Odstranit registraci aplikace

Z nabídky portál vyberte **Azure Active Directory**  >  **Registrace aplikací**. Pak vyberte aplikaci, kterou jste vytvořili.
:::image type="content" alt-text="Snímek obrazovky, který zobrazuje výběr registrace aplikace" source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

V přehledu registrace aplikace vyberte **Odstranit**.
:::image type="content" alt-text="Snímek obrazovky, který ukazuje odstranění registrace aplikace" source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Odstraňte prostředky Azure vytvořené během tohoto kurzu.

Naučte se, jak připojit [aplikaci .NET Core](tutorial-dotnetcore-sqldb-app.md), [aplikaci Python](tutorial-python-postgresql-app.md), [aplikaci Java](tutorial-java-spring-cosmosdb.md)nebo [ aplikaciNode.js](tutorial-nodejs-mongodb-app.md) k databázi.