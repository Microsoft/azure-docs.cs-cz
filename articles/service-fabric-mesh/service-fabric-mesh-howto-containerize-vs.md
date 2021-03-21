---
title: Kontejnerizace stávající aplikace v .NET pro Service Fabric Mesh
description: Přidejte Service Fabric podporu orchestrace kontejnerů na mřížku pro projekty ASP.NET a konzolu, které používají úplné rozhraní .NET Framework.
author: georgewallace
ms.author: gwallace
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: b9c5053a2a49c942cc89bd50c65e13f3a2f8d9d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625872"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Kontejnerizace stávající aplikace v .NET pro Service Fabric Mesh

> [!IMPORTANT]
> Náhled sítě Azure Service Fabric je vyřazený. Nová nasazení již nebudou povolena prostřednictvím rozhraní API pro Service Fabric sítě. Podpora stávajících nasazení bude pokračovat do 28. dubna 2021.
> 
> Podrobnosti najdete v tématu [vyřazení náhledu do sítě Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

V tomto článku se dozvíte, jak přidat Service Fabric podporu orchestrace kontejnerů na mřížku do existující aplikace .NET.

V aplikaci Visual Studio 2017 můžete přidat podporu kontejneru do ASP.NET a konzolových projektů, které používají úplné rozhraní .NET Framework.

> [!NOTE]
> Projekty .NET **Core** nejsou aktuálně podporovány.

## <a name="prerequisites"></a>Předpoklady

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


>[!NOTE]
> Od 2. listopadu 2020 se [limity četnosti stahování vztahují](https://docs.docker.com/docker-hub/download-rate-limit/) na anonymní a ověřené požadavky na Docker Hub z účtů bezplatného plánu Docker a vynutila IP adresa. Další podrobnosti najdete v tématu [ověřování pomocí Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub).
>
> Aby nedocházelo k omezení četnosti, ujistěte se, že výchozí hodnota `FROM microsoft/aspnet:4.7.2-windowsservercore-1803 AS base` ve vašem souboru Dockerfile je nahrazena hodnotou `FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-1803 AS base`

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