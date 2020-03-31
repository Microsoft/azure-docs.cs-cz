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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76020922"
---
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. V dialogovém okně **Publikovat** vyberte **Microsoft Azure App Service**, zvolte Vytvořit **nový**a pak vyberte **Publikovat**.

   ![Vybrat cíl publikování](./media/webjobs-publish-netcore/pick-publish-target.png)

1. V dialogovém okně **Vytvořit službu App Service** použijte nastavení hostování, jak je uvedeno v tabulce pod obrázkem:

    ![Dialogové okno Vytvoření služby App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název jednoznačně identifikující novou aplikaci funkcí. |
    | **Předplatné** | Zvolte vaše předplatné. | Předplatné Azure, které se má použít. |
    | **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Název skupiny prostředků, ve které chcete vytvořit aplikaci funkcí. Pokud chcete vytvořit novou skupinu prostředků, zvolte **Nová**.|
    | **[Hostingový plán](../articles/app-service/overview-hosting-plans.md)** | Plán služby App Service | [Plán služby App Service](../articles/app-service/overview-hosting-plans.md) určuje umístění, velikost a funkce farmy webových serverů, která je hostitelem vaší aplikace. Při hostování více aplikací můžete ušetřit peníze, pokud nakonfigurujete webové aplikace tak, aby sdílely jeden plán služby App Service. Plány služby App Service definují oblast, velikost instance, počet škálování a skladovou položku (free, shared, basic, standard nebo premium). Chcete-li vytvořit nový plán služby App Service, zvolte **Nový.** |

1. Kliknutím na **Vytvořit** vytvořte webovou úlohu a související prostředky v Azure s těmito nastaveními a nasaďte kód projektu.