---
title: Kontejnerizace existující aplikace .NET pro Service Fabric mřížky | Dokumentace Microsoftu
description: Přidává síť na existující aplikace v .NET
services: service-fabric-mesh
keywords: kontejnerizace služby prostředků infrastruktury sítě
author: tylermsft
ms.author: twhitney
ms.date: 11/08/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 6f71f45d435b6be3358f79d8b6e72e4636d92ab6
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52893488"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Kontejnerizace existující aplikace .NET pro Service Fabric mřížky

Tento článek ukazuje, jak přidat do existující aplikace .NET služby prostředků infrastruktury sítě podpora Orchestrace kontejnerů.

V sadě Visual Studio 2017 můžete přidat kontejnerizace podporu pro projekty ASP.NET a konzoly, které používají úplné rozhraní .NET framework.

> [!NOTE]
> .NET **core** projekty se momentálně nepodporují.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Ujistěte se, že jste [nastavení vývojového prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md). To zahrnuje instalaci modulu runtime Service Fabric, sady SDK, Docker, Visual Studio 2017 a vytváří místní cluster.

## <a name="open-an-existing-net-app"></a>Otevřete existující aplikaci .NET

Otevřete aplikaci, ke kterému chcete přidat podporu Orchestrace kontejnerů.

Pokud jste chtěli vyzkoušet příklad, můžete použít [eShop](https://github.com/MikkelHegn/ContainersSFLab) vzorový kód. Zbývající část tohoto článku bude předpokládat, že používáme tento projekt, i když použijete tyto kroky na svůj projekt.

Získat kopii systému **eShop** projektu:

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Jakmile, který byl stažen, v sadě Visual Studio 2017 otevřete **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**.

## <a name="add-container-support"></a>Přidání podpory kontejneru
 
Přidání podpory Orchestrace kontejnerů do existujícího projektu ASP.NET nebo konzoly nástroje Service Fabric sítě následujícím způsobem:

V Průzkumníku řešení sady Visual Studio klikněte pravým tlačítkem na název projektu (v tomto příkladu **eShopLegacyWebForms**) a klikněte na tlačítko **přidat** > **podporu Orchestrátoru kontejnerů** .
**Přidat podporu Orchestrátoru kontejnerů** se zobrazí dialogové okno.

![Visual Studio přidat dialog kontejner nástroje orchestrator](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Zvolte **sítě pro Service Fabric** z rozevíracího seznamu a potom klikněte na tlačítko **OK**.

Nástroj potom ověří, zda Docker nainstalovaný, do vašeho projektu přidá soubor Dockerfile a stáhne image dockeru pro váš projekt.  
Projekt aplikace Service Fabric sítě se přidá do vašeho řešení. Obsahuje vaše síť publikovat profily a konfigurační soubory. Název projektu je stejný jako název vašeho projektu s aplikací zřetězeny do konce, například **eShopLegacyWebFormsApplication**. 

V novém projektu sítě uvidíte dvě složky, které byste měli vědět:
- **Prostředky aplikace** obsahující YAML soubory, které popisují další prostředky sítě, jako je například síť.
- **Prostředky služby** obsahující service.yaml soubor, který popisuje, jak vaše aplikace by měl spustit po nasazení.

Jakmile podpora Orchestrace kontejnerů se přidá do vaší aplikace, můžete stisknout **F5** ladění aplikací .NET v místním clusteru Service Fabric mřížky. Tady je eShop aplikaci ASP.NET spuštěnou v clusteru Service Fabric sítě: 

![eShop aplikace](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Aplikaci můžete publikovat do Azure Service Fabric mřížky.

## <a name="next-steps"></a>Další postup

Zjistěte, jak publikovat aplikaci Service Fabric mřížky: [kurzu – nasazení aplikace Service Fabric mřížky](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)