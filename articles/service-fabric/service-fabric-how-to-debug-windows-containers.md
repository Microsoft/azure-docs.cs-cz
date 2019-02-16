---
title: Ladění kontejnerů Windows s platformou Service Fabric a VS | Dokumentace Microsoftu
description: Zjistěte, jak ladit kontejnery Windows v Azure Service Fabric pomocí sady Visual Studio 2017.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: twhitney, mikhegn
ms.openlocfilehash: 9801db8a38a8c21aea26b42f4fe01bd4a43988c5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311218"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2017"></a>Postup: Ladění kontejnerů Windows v Azure Service Fabric pomocí sady Visual Studio 2017

S Visual Studio 2017 Update 7 (15.7) můžete ladit aplikace .NET v kontejnerech jako služeb Service Fabric. Tento článek ukazuje, jak nakonfigurovat prostředí a ladění aplikace .NET v kontejneru spuštěná v místním clusteru Service Fabric.

## <a name="prerequisites"></a>Požadavky

* Ve Windows 10, postupujte podle tohoto rychlého startu pro [konfigurace Windows 10 pro spouštění kontejnerů Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* V systému Windows Server 2016, postupujte podle tohoto rychlého startu pro [konfigurace Windows 2016 pro spouštění kontejnerů Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Nastavit místní prostředí Service Fabric pomocí následujících [Příprava vývojového prostředí ve Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Konfigurace prostředí pro vývojáře k ladění kontejnerů

1. Ujistěte se, že než budete pokračovat dalším krokem je spuštění Dockeru pro služby systému Windows.

1. Aby bylo možné podporovat překlad názvů DNS mezi kontejnery, budete muset nastavit místního vývojového clusteru použijete název počítače. Tyto kroky jsou také nezbytné, pokud chcete adresu služby přes reverzní proxy server.
    1. Otevřete PowerShell jako správce
    2. Přejděte do instalační složky sady SDK Cluster, obvykle `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`.
    3. Spusťte skript `DevClusterSetup.ps1`

       ``` PowerShell
         C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
       ```

    > [!NOTE]
    > Můžete použít `-CreateOneNodeCluster` nastavení clusteru s jedním uzlem. Výchozí hodnota vytvoří místní cluster pěti uzly.
    >

    Další informace týkající se služby DNS v Service Fabric najdete v tématu [služba DNS v Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Další informace o používání Service Fabric reverzní proxy server ze služby spuštěné v kontejneru najdete v tématu [zvláštní zacházení reverzní proxy server pro služby spuštěné v kontejnerech](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Známá omezení při ladění kontejnerů v Service Fabric

Tady je seznam známých omezení ladění kontejnerů v Service Fabric a jejich možná řešení:

* Použití místního hostitele pro ClusterFQDNorIP nebude podporovat překlad názvů DNS v kontejnerech.
    * Řešení: Nastavení místního clusteru pomocí názvu počítače (viz výše)
* Spuštění Windows 10 na virtuálním počítači neobdržela odpověď DNS zpět do kontejneru.
    * Řešení: Zakázat UDP kontrolního součtu pro protokol IPv4 na síťové KARTĚ Virtual Machines
    * Upozorňujeme, že se to snížit výkon sítě na počítači.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Řešení služeb ve stejné aplikaci pomocí DNS názvu služby nefunguje na Windows 10, pokud byla aplikace nasazena pomocí Docker Compose
    * Řešení: Použití servicename.applicationname přeložit koncové body služby
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Pokud používáte IP adresu pro ClusterFQDNorIP, Změna primární IP na hostiteli přeruší funkčnosti DNS.
    * Řešení: Znovu vytvořte cluster pomocí nové primární IP adresy na hostiteli nebo použijte název počítače. Toto chování je úmyslné.
* Pokud plně kvalifikovaný název domény clusteru byl vytvořen pomocí není možné přeložit v síti, se nezdaří DNS.
    * Řešení: Znovu vytvořte místní cluster pomocí primární IP adresy hostitele. Toto chování je úmyslné.
* Při ladění kontejneru dockeru protokoly budou k dispozici pouze v okně výstupu sady Visual Studio, ne přes rozhraní API Service Fabric, včetně Service Fabric Exploreru

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Ladění aplikace .NET spuštěné v kontejnerech dockeru v Service Fabric

1. Spuštění sady Visual Studio jako správce.

1. Stávající aplikace .NET otevřete nebo vytvořte novou.

1. Klikněte pravým tlačítkem na projekt a vyberte **Přidat -> Podpora Orchestrátoru kontejnerů -> Service Fabric**

1. Stisknutím klávesy **F5** zahájíte ladění aplikace.

    Visual Studio podporuje konzoly a typy projektů ASP.NET pro .NET a .NET Core.

## <a name="next-steps"></a>Další postup
Další informace o možnostech služby Service Fabric a kontejnery, přejděte prosím na tento odkaz: [Přehled platformy Service Fabric kontejnery](service-fabric-containers-overview.md).
