---
title: Kontejnerizace stávající aplikace v .NET pro Service Fabric Mesh
description: Přidejte podporu orchestrace kontejneru service fabric pro ASP.NET a konzolové projekty, které používají úplnou architekturu .NET Framework.
author: dkkapur
ms.author: dekapur
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: d67ea5bb7df5910ec87e69adf3c414c303bf0182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462050"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Kontejnerizace stávající aplikace v .NET pro Service Fabric Mesh

Tento článek ukazuje, jak přidat podporu orchestrace kontejneru service fabric mesh do existující aplikace .NET.

V sadě Visual Studio 2017 můžete přidat podporu kontejnerizace pro projekty ASP.NET a konzoly, které používají úplnou architekturu .NET Framework.

> [!NOTE]
> **Základní** projekty .NET nejsou aktuálně podporovány.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte předplatné Azure, můžete [si vytvořit bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

* Ujistěte se, že jste [nastavili vývojové prostředí](service-fabric-mesh-howto-setup-developer-environment-sdk.md). To zahrnuje instalaci service fabric runtime, SDK, Docker, Visual Studio 2017 a vytvoření místního clusteru.

## <a name="open-an-existing-net-app"></a>Otevření existující aplikace .NET

Otevřete aplikaci, do které chcete přidat podporu orchestrace kontejneru.

Pokud byste chtěli vyzkoušet příklad, můžete použít ukázku kódu [eShopu.](https://github.com/MikkelHegn/ContainersSFLab) Zbytek tohoto článku bude předpokládat, že používáme tento projekt, i když můžete použít tyto kroky na vlastní projekt.

Získejte kopii projektu **eShopu:**

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Jakmile se stáhne, otevřete v Sadě Visual Studio 2017 **kontejnerySFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**.

## <a name="add-container-support"></a>Přidat podporu kontejneru
 
Přidejte podporu orchestrace kontejnerů do existujícího projektu ASP.NET nebo konzoly pomocí nástrojů Service Fabric Mesh následujícím způsobem:

V průzkumníku řešení Visual Studio klikněte pravým tlačítkem myši na název projektu (v příkladu **eShopLegacyWebForms)** a pak zvolte **Přidat** > **podporu orchestratorů kontejnerů**.
Zobrazí se dialogové okno **Přidat podporu orchestrátoru kontejneru.**

![Visual Studio přidat kontejner orchestrátor dialogové okno](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

V rozevíracím souboru zvolte **Síť service fabric** a klepněte na tlačítko **OK**.

Nástroj pak ověří, že Docker je nainstalován, přidá Dockerfile do projektu a zatáčím image dockeru pro váš projekt.  
Do vašeho řešení je přidán projekt aplikace Service Fabric Mesh. Obsahuje vaše profily publikování sítě a konfigurační soubory. Název projektu je stejný jako název projektu, s názvem "Aplikace" je zřetězený až do konce, například **eShopLegacyWebFormsApplication**. 

V novém projektu Mesh uvidíte dvě složky, o kterých byste měli vědět:
- **Prostředky aplikace,** které obsahují soubory YAML, které popisují další prostředky sítě, jako je síť.
- **Service Resources,** který obsahuje soubor service.yaml, který popisuje, jak by měla vaše aplikace běžet při nasazení.

Po přidání podpory orchestrace kontejnerů do vaší aplikace můžete stisknutím **klávesy F5** ladit aplikaci .NET v místním clusteru Service Fabric Mesh. Zde je aplikace eShop ASP.NET spuštěná v clusteru Service Fabric Mesh: 

![Aplikace eShop](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Teď můžete publikovat aplikaci do azure service fabric mesh.

## <a name="next-steps"></a>Další kroky

Podívejte se, jak publikovat aplikaci pro service fabric mesh: [Tutorial- Nasazení aplikace Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)