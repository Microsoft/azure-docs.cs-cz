---
title: Ladění Windows kontejnery s Service Fabric a VS | Microsoft Docs
description: Zjistěte, jak k ladění Windows kontejnerů v Azure Service Fabric pomocí Visual Studio 2017.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/14/2018
ms.author: mikhegn
ms.openlocfilehash: bca33fe187668d38d4451b2de5b9e54d86e40ba9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655001"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2017"></a>Postupy: ladění Windows kontejnerů v Azure Service Fabric pomocí Visual Studio 2017

Visual Studio 2017 aktualizace 7 (15.7) můžete ladit aplikace .NET v kontejnerech jako služby Service Fabric. Tento článek ukazuje, jak nakonfigurovat vaše prostředí a potom ladit aplikace .NET v kontejneru spuštěné v místní cluster Service Fabric.

## <a name="prerequisites"></a>Požadavky

* Ve Windows 10, postupujte podle tento rychlý start pro [konfigurace Windows 10 ke spuštění Windows kontejnery](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* Na Windows Server 2016, použijte tento rychlý start pro [konfigurace 2016 Windows ke spuštění Windows kontejnery](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Nastavení místního prostředí Service Fabric pomocí následujících [Příprava vývojového prostředí v systému Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Konfigurace prostředí vývojáře k ladění kontejnerů

1. Ujistěte se, že než budete pokračovat dalším krokem je spuštěn Docker pro služby systému Windows.

1. Aby bylo možné podporovat překlad DNS mezi kontejnery, budete muset nastavit místní vývojový cluster, pomocí názvu počítače.
    1. Otevřete PowerShell jako správce
    1. Přejděte do složky instalace sady SDK clusteru, obvykle `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`
    1. Spusťte skript `DevClusterSetup.ps1` s parametrem `-UseMachineName`

    ``` PowerShell
      C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1 -UseMachineName
    ```

    > [!NOTE]
    > Můžete použít `-CreateOneNodeCluster` nastavení clusteru s jedním uzlem. Výchozí hodnota se vytvoří místní cluster pěti uzly.
    >

    Další informace týkající se služby DNS v Service Fabric najdete v tématu [služba DNS v Azure Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-dnsservice).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Známá omezení při ladění kontejnerů v Service Fabric

Níže je seznam známá omezení s ladění kontejnerů v Service Fabric a možná řešení:

* Použití localhost pro ClusterFQDNorIP nebudou podporovat překlad názvů DNS v kontejnerech.
    * Řešení: Nastavte místní cluster pomocí názvu počítače (viz výše)
* Spuštění Windows10 ve virtuálním počítači nebude získat odpovědi DNS zpět do kontejneru.
    * Řešení: Zakázat odstranění kontrolního součtu protokolu UDP pro protokol IPv4 na síťový adaptér virtuálních počítačů
    * Upozorňujeme, že se to snížit výkon sítě na počítači.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Řešení služeb ve stejné aplikaci pomocí DNS název služby nefunguje na Windows10, pokud byla aplikace nasazena pomocí Docker Compose
    * Řešení: Použití servicename.applicationname přeložit koncové body služby
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Pokud se používá IP adresu pro ClusterFQDNorIP, změně primární IP na hostiteli budou přerušeny funkce DNS.
    * Řešení: Znovu vytvořte cluster pomocí nové primární IP na hostiteli, nebo použijte název počítače. Toto chování je úmyslné.
* Pokud není plně kvalifikovaný název domény clusteru byla vytvořena s přeložit v síti, se nezdaří DNS.
    * Řešení: Znovu vytvořte místní cluster pomocí primární IP hostitele. Toto chování je úmyslné.
* Při ladění kontejner, protokoly docker bude k dispozici pouze v okně výstupu sady Visual Studio, ne přes rozhraní API služby infrastruktury, včetně Service Fabric Exploreru

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Ladění aplikace .NET v kontejnerech docker systémem Service Fabric

1. Visual Studio spusťte jako správce.

1. Otevření existující aplikace .NET nebo vytvořte novou.

1. Klikněte pravým tlačítkem na projekt a vyberte **Přidat -> Orchestrator podpora kontejnerů -> Service Fabric**

1. Stiskněte klávesu **F5** spustit ladění aplikace.

    Visual Studio podporuje konzoly a typů projektu ASP.NET pro rozhraní .NET a .NET Core.

## <a name="next-steps"></a>Další postup
Další informace o možnostech Service Fabric a kontejnerů, postupujte podle tohoto odkazu: [Service Fabric kontejnery přehled](service-fabric-containers-overview.md).