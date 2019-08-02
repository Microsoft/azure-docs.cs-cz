---
title: Ladění kontejnerů Windows pomocí Service Fabric a VS | Microsoft Docs
description: Naučte se ladit kontejnery Windows v Azure Service Fabric pomocí sady Visual Studio 2019.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 043623869163a27cdbbdd7115fca43486577ca7d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599437"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Postup: Ladění kontejnerů Windows v Azure Service Fabric pomocí sady Visual Studio 2019

Pomocí sady Visual Studio 2019 můžete ladit aplikace .NET v kontejnerech jako Service Fabric služby. V tomto článku se dozvíte, jak nakonfigurovat prostředí a potom ladit aplikaci .NET v kontejneru spuštěném v místním Service Fabricovém clusteru.

## <a name="prerequisites"></a>Požadavky

* V systému Windows 10 postupujte podle tohoto rychlého startu a [nakonfigurujte Windows 10 tak, aby se spouštěly kontejnery Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10) .
* V systému Windows Server 2016 postupujte podle tohoto rychlého startu a [Nakonfigurujte systém windows 2016 pro spouštění kontejnerů Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) .
* Místní Service Fabric prostředí nastavte po [přípravě vývojového prostředí ve Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) .

## <a name="configure-your-developer-environment-to-debug-containers"></a>Konfigurace vývojového prostředí pro ladění kontejnerů

1. Než budete pokračovat k dalšímu kroku, ujistěte se, že je služba Docker pro okno spuštěná.

1. Aby bylo možné podporovat překlad DNS mezi kontejnery, budete muset nastavit místní cluster pro vývoj pomocí názvu počítače. Tyto kroky jsou nezbytné také v případě, že chcete adresovat služby prostřednictvím reverzního proxy serveru.
   1. Otevřete PowerShell jako správce.
   2. Přejděte do složky pro nastavení clusteru sady SDK obvykle `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`.
   3. Spuštění skriptu`DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Můžete použít `-CreateOneNodeCluster` k nastavení clusteru s jedním uzlem. Ve výchozím nastavení se vytvoří místní cluster s pěti uzly.
      >

      Další informace o službě DNS v Service Fabric najdete v tématu [Služba DNS ve službě Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Další informace o Service Fabric použití reverzního proxy serveru ze služeb spuštěných v kontejneru najdete v tématu [reverzní proxy zvláštní zpracování pro služby běžící v kontejnerech](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Známá omezení při ladění kontejnerů v Service Fabric

Níže je uveden seznam známých omezení s kontejnery pro ladění v Service Fabric a možná řešení:

* Použití místního hostitele pro ClusterFQDNorIP nepodporuje překlad DNS v kontejnerech.
    * Řešení: Nastavení místního clusteru pomocí názvu počítače (viz výše)
* Spuštění Windows10 na virtuálním počítači nevrátí odpověď DNS zpátky do kontejneru.
    * Řešení: Zakázat přesměrování zpracování kontrolního součtu UDP pro protokol IPv4 na síťové kartě Virtual Machines
    * Spuštění Windows10 sníží výkon sítě v počítači.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Překlad služeb ve stejné aplikaci pomocí názvu služby DNS nefunguje na Windows10, pokud byla aplikace nasazená pomocí Docker Compose
    * Řešení: Použití ServiceName. ApplicationName k překladu koncových bodů služby
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Pokud se při použití IP adresy pro ClusterFQDNorIP mění primární IP adresa na hostiteli, přeruší se funkčnost služby DNS.
    * Řešení: Znovu vytvořte cluster pomocí nové primární IP adresy na hostiteli nebo použijte název počítače. Toto zlomek je záměrné.
* Pokud se plně kvalifikovaný název domény, který byl cluster vytvořil, nedá přeložit v síti, služba DNS se nezdaří.
    * Řešení: Znovu vytvořte místní cluster pomocí primární IP adresy hostitele. Tato chyba je záměrné.
* Při ladění kontejneru budou protokoly Docker k dispozici pouze v okně výstup sady Visual Studio, nikoli prostřednictvím rozhraní API pro Service Fabric, včetně Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Ladění aplikace .NET běžící v kontejnerech Docker na Service Fabric

1. Spusťte Visual Studio jako správce.

1. Otevřete existující aplikaci .NET nebo vytvořte novou.

1. Klikněte pravým tlačítkem na projekt a vyberte **Přidat-> kontejner Orchestrator Support-> Service Fabric**

1. Stisknutím klávesy **F5** spusťte ladění aplikace.

    Visual Studio podporuje konzolové a ASP.NET typy projektů pro .NET a .NET Core.

## <a name="next-steps"></a>Další postup
Další informace o možnostech Service Fabric a kontejnerů najdete v tématu Přehled kontejnerů Service Fabric] (Service-Fabric-Containers-overview.md).
