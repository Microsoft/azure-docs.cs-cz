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
ms.openlocfilehash: ab91ea7aa6e621dabc5cac83fe818dbf175214b6
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428834"
---
# <a name="tutorial-clean-up-resources"></a>Kurz: vyčištění prostředků

Pokud jste dokončili všechny kroky v tomto kurzu více částí, vytvořili jste službu App Service, plán hostování služby App Service a účet úložiště ve skupině prostředků.  Také jste vytvořili registraci aplikace ve službě Azure AD.  Pokud už je nepotřebujete, odstraňte tyto prostředky a registraci aplikace, abyste nepokračovali v navýšení poplatků.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Odstraňte prostředky Azure vytvořené během tohoto kurzu.

## <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků
V [Azure Portal](https://portal.azure.com)v nabídce portál vyberte **skupiny prostředků** a vyberte skupinu prostředků, která obsahuje službu App Service a plán služby App Service.

Vyberte **Odstranit skupinu prostředků** a odstraňte skupinu prostředků a všechny prostředky.

:::image type="content" alt-text="Odstranění skupiny prostředků" source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

Spuštění tohoto příkazu může trvat několik minut.

## <a name="delete-the-app-registration"></a>Odstranit registraci aplikace
V nabídce portál vyberte položku **Azure Active Directory** a pak **Registrace aplikací** a potom aplikaci, kterou jste vytvořili.
:::image type="content" alt-text="Vybrat registraci aplikace" source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

V přehledu registrace aplikace vyberte **Odstranit**.
:::image type="content" alt-text="Odstranit registraci aplikace" source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Odstraňte prostředky Azure vytvořené během tohoto kurzu.

Naučte se, jak připojit [aplikaci .NET Core](tutorial-dotnetcore-sqldb-app.md), [aplikaci Python](tutorial-python-postgresql-app.md), [aplikaci Java](tutorial-java-spring-cosmosdb.md)nebo [ aplikaciNode.js](tutorial-nodejs-mongodb-app.md) k databázi.