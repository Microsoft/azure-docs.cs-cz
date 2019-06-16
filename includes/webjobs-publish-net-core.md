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
ms.openlocfilehash: 97387e24d5b55c1438a69da1a1fd0a9bc1720e47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66136160"
---
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. V **publikovat** dialogového okna, vyberte **Microsoft Azure App Service**, zvolte **vytvořit nový**a pak vyberte **publikovat**.

   ![Cíl publikování výběru](./media/webjobs-publish-netcore/pick-publish-target.png)

1. V **vytvořit službu App Service** dialogové okno, použijte nastavení hostování podle uvedené v tabulce pod obrázkem:

    ![Dialogové okno Vytvořit službu App Service](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název jednoznačně identifikující novou aplikaci funkcí. |
    | **Předplatné** | Vaše předplatné | Předplatné Azure, které se má použít. |
    | **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Název skupiny prostředků, ve které chcete vytvořit aplikaci funkcí. Pokud chcete vytvořit novou skupinu prostředků, zvolte **Nová**.|
    | **[Plán hostování](../articles/app-service/overview-hosting-plans.md)** | Plán služby App Service | [Plán služby App Service](../articles/app-service/overview-hosting-plans.md) určuje umístění, velikost a funkce farmy webových serverů, která je hostitelem vaší aplikace. Při hostování více aplikací můžete ušetřit peníze, pokud nakonfigurujete webové aplikace tak, aby sdílely jeden plán služby App Service. Plány služby App Service definují oblasti, velikosti instance, počet škálování a SKU (Free, Shared, Basic, Standard nebo Premium). Zvolte **nový** vytvořit nový plán služby App Service. |

1. Klikněte na tlačítko **vytvořit** vytvořit ve webové úloze a související prostředky v Azure s těmito nastaveními a nasadí kód projektu.