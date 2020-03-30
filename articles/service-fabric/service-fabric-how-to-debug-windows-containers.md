---
title: Ladění kontejnerů Windows s využitím služby Service Fabric a VS
description: Zjistěte, jak ladit kontejnery Windows ve službě Azure Fabric pomocí Visual Studia 2019.
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 2a00a352d09562ffe46dc8e6e63a5d4963ac3a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127631"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Postup: Ladění kontejnerů Windows ve službě Azure Fabric pomocí Visual Studia 2019

Pomocí Visual Studio 2019 můžete ladit aplikace .NET v kontejnerech jako služby Service Fabric. Tento článek ukazuje, jak nakonfigurovat prostředí a pak ladit aplikaci .NET v kontejneru spuštěném v místním clusteru Service Fabric.

## <a name="prerequisites"></a>Požadavky

* Ve Windows 10 postupujte podle tohoto rychlého startu [a nakonfigurujte Windows 10 pro spouštění kontejnerů Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* Na Windows Serveru 2016 můžete na tomto rychlém startu [nakonfigurovat Windows 2016 tak, aby spouštěl kontejnery Windows.](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Nastavení místního prostředí Service Fabric podle následujících možností [Příprava vývojového prostředí v systému Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Konfigurace vývojářského prostředí pro ladění kontejnerů

1. Ujistěte se, že Docker for Window služba běží před pokračováním v dalším kroku.

1. Chcete-li podporovat rozlišení DNS mezi kontejnery, budete muset nastavit místní vývojový cluster pomocí názvu počítače. Tyto kroky jsou také nezbytné, pokud chcete řešit služby prostřednictvím reverzní proxy serveru.
   1. Otevření PowerShellu jako správce
   2. Přejděte obvykle do instalační složky `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`clusteru SDK .
   3. Spuštění skriptu`DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Můžete použít `-CreateOneNodeCluster` k nastavení clusteru s jedním uzlem. Výchozí nastavení vytvoří místní cluster pěti uzlů.
      >

      Další informace o službě DNS ve službě Fabric najdete v tématu [Služba DNS ve službě Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Další informace o používání reverzního proxy serveru Service Fabric ze služeb spuštěných v kontejneru naleznete v [tématu Reverzní proxy speciální zpracování pro služby spuštěné v kontejnerech](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Známá omezení při ladění kontejnerů v service fabric

Níže je uveden seznam známých omezení s ladicími kontejnery v service fabric a možná řešení:

* Použití localhost pro ClusterFQDNorIP nebude podporovat rozlišení DNS v kontejnerech.
    * Řešení: Nastavení místního clusteru pomocí názvu počítače (viz výše)
* Spuštění windows10 ve virtuálním počítači nezíská odpověď DNS zpět do kontejneru.
    * Řešení: Zakázání nezátěže kontrolního součtu UDP pro IPv4 na nic virtuálních počítačů
    * Spuštění systému Windows10 sníží výkon sítě v počítači.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Řešení služeb ve stejné aplikaci pomocí názvu služby DNS nefunguje v systému Windows10, pokud byla aplikace nasazena pomocí Docker Compose
    * Řešení: K vyřešení koncových bodů služby použijte název_aplikace servicename.applicationname.
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Pokud používáte IP adresu pro ClusterFQDNorIP, změna primární IP adresy na hostiteli přeruší funkčnost DNS.
    * Řešení: Znovu vytvořte cluster pomocí nové primární IP adresy na hostiteli nebo použijte název počítače. Toto rozbití je záměrné.
* Pokud není fQDN, pomocí kterého byl cluster vytvořen, v síti nelze řešit, služba DNS se nezdaří.
    * Řešení: Znovu vytvořte místní cluster pomocí primární IP adresy hostitele. Toto selhání je záměrné.
* Při ladění kontejneru budou protokoly dockeru k dispozici pouze ve výstupním okně sady Visual Studio, nikoli prostřednictvím rozhraní API service fabric, včetně Průzkumníka prostředků služby

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Ladění aplikace .NET spuštěná v kontejnerech dockeru na service fabric

1. Spusťte sadu Visual Studio jako správce.

1. Otevřete existující aplikaci .NET nebo vytvořte novou.

1. Klepněte pravým tlačítkem myši na projekt a vyberte **přidat podporu -> kontejneru Orchestrator -> Service Fabric**

1. Stisknutím **klávesy F5** spusťte ladění aplikace.

    Visual Studio podporuje konzolové a ASP.NET typy projektů pro .NET a .NET Core.

## <a name="next-steps"></a>Další kroky
Další informace o možnostech service fabric a kontejnery, naleznete [v tématu Service Fabric kontejnery přehled](service-fabric-containers-overview.md).
