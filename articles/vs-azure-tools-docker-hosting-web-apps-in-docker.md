---
title: Nasadit kontejner ASP.NET Docker pro Azure kontejneru registru (ACR) | Microsoft Docs
description: Další informace o použití nástroje sady Visual Studio pro Docker nasazení webové aplikace ASP.NET Core do registru kontejneru
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: ''
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: mlearned
ms.openlocfilehash: 4442c1d763f4ed21a5efeedbe957727254e2a0b8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658467"
---
# <a name="deploy-an-aspnet-container-to-a-container-registry-using-visual-studio"></a>Nasadit kontejner ASP.NET do kontejneru registru, pomocí sady Visual Studio
## <a name="overview"></a>Přehled
Docker je modul lightweight kontejneru, podobně jako v některých způsobech virtuální počítač, který můžete použít k hostování aplikací a služeb.
Tento kurz vás provede pomocí sady Visual Studio k publikování aplikace kontejnerizované [registru kontejner Azure](https://azure.microsoft.com/en-us/services/container-registry).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/dotnet/?utm_source=acr-publish-doc&utm_medium=docs&utm_campaign=docs) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte nejnovější verzi [Visual Studio 2017](https://azure.microsoft.com/en-us/downloads/) se zatížením, "ASP.NET a webové vývoj"
* Nainstalujte [Docker pro Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. Vytvoření webové aplikace ASP.NET Core
Následující postup vás provede vytvořením základní aplikace ASP.NET Core, který se použije v tomto kurzu.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-publish-your-container-to-azure-container-registry"></a>2. Publikování vašeho kontejneru do registru kontejner Azure
1. Klikněte pravým tlačítkem na projekt v **Průzkumníku řešení** a zvolte **publikovat**.
2. V dialogovém okně cíl publikování, vyberte **kontejneru registru** kartě.
3. Zvolte **nový kontejner registru Azure** a klikněte na tlačítko **publikovat**.
4. Zadejte požadované hodnoty v **vytvořit nové registru kontejner Azure**.

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Předpona DNS** | Globálně jedinečný název | Název, který jednoznačně identifikuje registr kontejneru. |
    | **Předplatné** | Vaše předplatné | Předplatné Azure, které se má použít. |
    | **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Název skupiny prostředků, ve které chcete vytvořit kontejner registr. Zvolte **nový** vytvořit novou skupinu prostředků.|
    | **[SKU](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-skus)** | Standard | Úroveň služby registru kontejneru  |
    | **Umístění v registru** | Umístění blízko vás | Vyberte umístění v [oblast](https://azure.microsoft.com/regions/) okolo vás nebo téměř jiných služeb, které budou používat registr kontejneru. |
    ![Visual Studio vytvořit dialogové okno registru kontejner Azure][0]
5. Klikněte na **Vytvořit**

Vám může nyní pro vyžádání obsahu kontejneru z registru na každém hostiteli umožňuje spustit imagí Dockeru, například [instancí kontejnerů Azure](./container-instances/container-instances-tutorial-deploy-app.md).

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/vs-acr-provisioning-dialog.png
