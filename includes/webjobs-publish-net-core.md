---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e208b52c67f173bd0d289715b63562df656b1ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009744"
---
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. V dialogovém okně **publikovat** vyberte **Azure** pro **cíl**a pak vyberte **Další**. 

1. Pro **konkrétní cíl**vyberte **Azure WebJobs** a pak vyberte **Další**.

1. Vyberte **vytvořit novou webovou úlohu Azure**.

   ![Vybrat cíl publikování](./media/webjobs-publish-netcore/pick-publish-target.png)

1. V dialogovém okně **App Service (Windows)** použijte nastavení hostování v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Globálně jedinečný název | Název jednoznačně identifikující novou aplikaci funkcí. |
    | **Předplatné** | Zvolte vaše předplatné. | Předplatné Azure, které se má použít. |
    | **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Název skupiny prostředků, ve které chcete vytvořit aplikaci funkcí. Pokud chcete vytvořit novou skupinu prostředků, zvolte **Nová**.|
    | **[Plán Hosting](../articles/app-service/overview-hosting-plans.md)** | Plán služby App Service | [Plán služby App Service](../articles/app-service/overview-hosting-plans.md) určuje umístění, velikost a funkce farmy webových serverů, která je hostitelem vaší aplikace. Při hostování více aplikací můžete ušetřit peníze, pokud nakonfigurujete webové aplikace tak, aby sdílely jeden plán služby App Service. Plány App Service definují oblast, velikost instance, počet škálování a SKU (Free, Shared, Basic, Standard nebo Premium). Pokud chcete vytvořit nový plán App Service, vyberte **Nový** . |

    ![Dialogové okno Create App Service (Vytvoření služby App Service)](./media/webjobs-publish-netcore/app-service-dialog.png)

1. Výběrem možnosti **vytvořit** vytvoříte webovou úlohu a související prostředky v Azure pomocí těchto nastavení a nasadíte kód projektu.