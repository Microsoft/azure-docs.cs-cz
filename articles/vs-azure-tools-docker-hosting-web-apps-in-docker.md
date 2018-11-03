---
title: Nasazení kontejneru Dockeru ASP.NET do Azure Container Registry (ACR) | Dokumentace Microsoftu
description: Zjistěte, jak nasadit webovou aplikaci ASP.NET Core do registru kontejnerů pomocí Visual Studio Tools for Docker
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
ms.openlocfilehash: 2170fb7eebedcaddb91a4a24940b1e226c104f2c
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969269"
---
# <a name="deploy-an-aspnet-container-to-a-container-registry-using-visual-studio"></a>Nasazení kontejneru ASP.NET do služby container registry pomocí sady Visual Studio
## <a name="overview"></a>Přehled
Docker je modul jednoduchý kontejner, podobně jako v některých ohledech k virtuálnímu počítači, který můžete použít k hostování aplikací a služeb.
Tento kurz vás provede kontejnerizované aplikace můžete publikovat pomocí sady Visual Studio [Azure Container Registry](https://azure.microsoft.com/services/container-registry).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/dotnet/?utm_source=acr-publish-doc&utm_medium=docs&utm_campaign=docs) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte nejnovější verzi [Visual Studio 2017](https://azure.microsoft.com/downloads/) s úlohou "vývoj aplikací ASP.NET a web"
* Nainstalujte [Docker pro Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. Vytvoření webové aplikace ASP.NET Core
Následující kroky vás provedou vytvořením základní aplikaci ASP.NET Core, který se použije v tomto kurzu.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-publish-your-container-to-azure-container-registry"></a>2. Publikování vašeho kontejneru do služby Azure Container Registry
1. Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a zvolte **publikovat**.
2. V dialogovém okně Publikovat cíl, vyberte **Container Registry** kartu.
3. Zvolte **nový registr kontejneru Azure** a klikněte na tlačítko **publikovat**.
4. Vyplňte požadované hodnoty v **vytvořit nový registr kontejneru Azure**.

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Předpona DNS** | Globálně jedinečný název | Název, který jednoznačně identifikuje vašeho registru kontejneru. |
    | **Předplatné** | Vaše předplatné | Předplatné Azure, které se má použít. |
    | **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Název skupiny prostředků, ve kterém chcete vytvořit registr kontejnerů. Pokud chcete vytvořit novou skupinu prostředků, zvolte **Nová**.|
    | **[SKLADOVÁ POLOŽKA](https://docs.microsoft.com/azure/container-registry/container-registry-skus)** | Standard | Úrovně služby do registru kontejneru  |
    | **Umístění registru** | Vám nejbližším místě | Zvolte umístění, do [oblasti](https://azure.microsoft.com/regions/) vaší blízkosti nebo v blízkosti jiných služeb, které budou používat službu container registry. |
    ![Visual Studio vytvořit dialogové okno Azure Container Registry][0]
5. Klikněte na **Vytvořit**

Můžete nyní využít kontejneru z registru na každém hostiteli podporuje spuštění imagí Dockeru, například [Azure Container Instances](./container-instances/container-instances-tutorial-deploy-app.md).

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/vs-acr-provisioning-dialog.png
