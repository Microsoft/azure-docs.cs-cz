---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4860532e59227618ce819772887556719ecb53fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "76020922"
---
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. V dialogovém okně **publikovat** vyberte možnost **Microsoft Azure App Service**, zvolte **vytvořit novou**a pak vyberte **publikovat**.

   ![Vybrat cíl publikování](./media/webjobs-publish-netcore/pick-publish-target.png)

1. V dialogovém okně **vytvořit App Service** použijte nastavení hostování, jak je uvedeno v tabulce pod obrázkem:

    ![Dialogové okno Vytvoření služby App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název jednoznačně identifikující novou aplikaci funkcí. |
    | **Předplatné** | Zvolte vaše předplatné. | Předplatné Azure, které se má použít. |
    | **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Název skupiny prostředků, ve které chcete vytvořit aplikaci funkcí. Pokud chcete vytvořit novou skupinu prostředků, zvolte **Nová**.|
    | **[Plán hostování](../articles/app-service/overview-hosting-plans.md)** | Plán služby App Service | [Plán služby App Service](../articles/app-service/overview-hosting-plans.md) určuje umístění, velikost a funkce farmy webových serverů, která je hostitelem vaší aplikace. Při hostování více aplikací můžete ušetřit peníze, pokud nakonfigurujete webové aplikace tak, aby sdílely jeden plán služby App Service. Plány App Service definují oblast, velikost instance, počet škálování a SKU (Free, Shared, Basic, Standard nebo Premium). Pokud chcete vytvořit nový plán App Service, vyberte **Nový** . |

1. Kliknutím na **vytvořit** vytvoříte webovou úlohu a související prostředky v Azure pomocí těchto nastavení a nasadíte kód projektu.