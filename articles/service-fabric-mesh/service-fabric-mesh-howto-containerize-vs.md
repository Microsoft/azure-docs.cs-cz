---
title: Kontejnerizace stávající aplikace v .NET pro Service Fabric Mesh
description: Přidejte Service Fabric podporu orchestrace kontejnerů na mřížku pro projekty ASP.NET a konzolu, které používají úplné rozhraní .NET Framework.
author: georgewallace
ms.author: gwallace
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: a995f30872216a8b704d3d1714bbece4bb8271f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840060"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Kontejnerizace stávající aplikace v .NET pro Service Fabric Mesh

V tomto článku se dozvíte, jak přidat Service Fabric podporu orchestrace kontejnerů na mřížku do existující aplikace .NET.

V aplikaci Visual Studio 2017 můžete přidat podporu kontejneru do ASP.NET a konzolových projektů, které používají úplné rozhraní .NET Framework.

> [!NOTE]
> Projekty .NET **Core** nejsou aktuálně podporovány.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Ujistěte se, že jste [nastavili vývojové prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md). To zahrnuje instalaci modulu runtime Service Fabric, sady SDK, Docker, sady Visual Studio 2017 a vytvoření místního clusteru.

## <a name="open-an-existing-net-app"></a>Otevření existující aplikace .NET

Otevřete aplikaci, do které chcete přidat podporu orchestrace kontejnerů.

Pokud byste chtěli vyzkoušet příklad, můžete použít ukázku kódu [eshop](https://github.com/MikkelHegn/ContainersSFLab) . Zbytek tohoto článku bude předpokládat, že používáme tento projekt, i když můžete použít tyto kroky na vlastní projekt.

Získat kopii projektu **eshop** :

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Po stažení v aplikaci Visual Studio 2017 Open **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**.

## <a name="add-container-support"></a>Přidat podporu kontejneru
 
Přidejte podporu orchestrace kontejnerů do existujícího projektu ASP.NET nebo konzole pomocí Service Fabricch nástrojů pro mřížku, jak je znázorněno níže:

V Průzkumníku řešení sady Visual Studio klikněte pravým tlačítkem myši na název projektu (v příkladu **eShopLegacyWebForms**) a pak zvolte **Přidat**  >  **podporu kontejneru Orchestrator**.
Zobrazí se dialogové okno **Přidat podporu produktu Orchestrator pro kontejner** .

![Dialogové okno Přidat kontejner nástroje Orchestrator pro Visual Studio](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

V rozevíracím seznamu vyberte **Service Fabric síť** a pak klikněte na **OK**.

Nástroj potom ověří, že je Docker nainstalovaný, přidá do projektu souboru Dockerfile a napředá image Docker pro váš projekt.  
Do vašeho řešení se přidá projekt aplikace Service Fabric mřížka. Obsahuje vaše sítě publikační profil a konfigurační soubory. Název projektu je stejný jako název projektu a "aplikace" zřetězené na konec, například **eShopLegacyWebFormsApplication**. 

V novém projektu sítě se zobrazí dvě složky, na které byste měli vědět:
- **Prostředky aplikací** , které obsahují YAML soubory, které popisují další síťové prostředky, jako je například síť.
- **Prostředky služby** , které obsahují soubor Service. yaml, který popisuje, jak by měla být aplikace spuštěna po nasazení.

Až se do aplikace přidá podpora orchestrace kontejnerů, můžete stisknutím klávesy **F5** ladit aplikaci .NET v místním clusteru Service Fabric sítě. Tady je aplikace eShop ASP.NET spuštěná v clusteru Service Fabricch sítí: 

![aplikace eShop](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Teď můžete aplikaci publikovat do Azure Service Fabric sítě.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak publikovat aplikaci do Service Fabric sítě: [kurz – nasazení Service Fabric mřížky aplikace](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)