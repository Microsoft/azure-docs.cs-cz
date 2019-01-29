---
title: Nasazení aplikace Service Fabric do clusteru v Azure | Microsoft Docs
description: Zjistěte, jak nasadit aplikaci do clusteru ze sady Visual Studio.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/14/2019
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: 2ddc0376e256a977d7d14ea10b610fcd5861e7c8
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100559"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Kurz: Nasazení aplikace Service Fabric do clusteru v Azure

Tento kurz je druhá část série. Ukáže vám, jak nasadit aplikaci Azure Service Fabric do nového clusteru v Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření clusteru.
> * Nasadit aplikaci do vzdáleného clusteru pomocí sady Visual Studio

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Sestavit aplikaci .NET pro Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Nasadit aplikaci do vzdáleného clusteru
> * [Přidat koncový bod HTTPS do front-end služby ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Nakonfigurovat CI/CD s využitím služby Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Nastavit monitorování a diagnostiku aplikace](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Nainstalujte sadu Visual Studio 2017](https://www.visualstudio.com/) se sadami funkcí **Vývoj pro Azure** a **Vývoj pro ASP.NET a web**.
* [Nainstalujte sadu Service Fabric SDK](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Stažení ukázkové hlasovací aplikace

Pokud jste nesestavili ukázkovou hlasovací aplikaci v [první části této série kurzů](service-fabric-tutorial-create-dotnet-app.md), můžete si ji stáhnout. V příkazovém řádku spusťte následující kód, naklonujte úložiště ukázkové aplikace do místního počítače.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Otevřete aplikaci v sadě Visual Studio, spuštěný v režimu správce a sestavte aplikaci.

## <a name="create-a-cluster"></a>Vytvoření clusteru

Teď, když je aplikace připravená, můžete vytvořit cluster Service Fabric a pak nasadíte aplikaci do clusteru. [Cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) je síťově propojená sada virtuálních nebo fyzických počítačů, ve které se nasazují a spravují mikroslužby.

V tomto kurzu vytvořte nový test clusteru se třemi uzly v integrovaném vývojovém prostředí sady Visual Studio a pak publikujte aplikaci do tohoto clusteru. Najdete v článku [vytvořit a spravovat cluster kurzu](service-fabric-tutorial-create-vnet-and-windows-cluster.md) informace o vytváření clusteru výroby. Můžete také nasadit aplikaci do existujícího clusteru, který jste dříve vytvořili prostřednictvím [webu Azure portal](https://portal.azure.com), s použitím [Powershellu](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) nebo [rozhraní příkazového řádku Azure](./scripts/cli-create-cluster.md) skripty, nebo z [Šablony azure Resource Manageru](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> Hlasovací aplikace a mnoho dalších aplikací, použít reverzní proxy Service Fabric pro komunikaci mezi službami. Clustery vytvořené v sadě Visual Studio mají povolené ve výchozím nastavení reverzní proxy server. Pokud nasazení provádíte do existujícího clusteru, je nutné [povolit reverzní proxy server v clusteru](service-fabric-reverseproxy-setup.md) hlasovací aplikace fungovat.


### <a name="find-the-votingweb-service-endpoint"></a>Vyhledání koncového bodu služby VotingWeb

Front-endové webové službě hlasovací aplikace naslouchá na určitém portu (8080, pokud jste v postupovali podle kroků v [první části této série kurzů](service-fabric-tutorial-create-dotnet-app.md). Když se aplikace nasadí do clusteru v Azure, běží cluster i aplikace na pozadí služby Azure Load Balancer. Port aplikace musí být otevřít ve službě Azure load balancer pomocí pravidla. Toto pravidlo odešle příchozí provoz prostřednictvím nástroje pro vyrovnávání zatížení k webové službě. Tento port se nachází v souboru **VotingWeb/PackageRoot/ServiceManifest.xml** v elementu **Endpoint**. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Poznamenejte si koncový bod služby, který je nutný v pozdějším kroku.  Pokud provádíte nasazení do existujícího clusteru, tento port otevřít tak, že vytvoříte Vyrovnávání zatížení se pravidlo a kontroly v pomocí nástroje pro vyrovnávání zatížení Azure [skript prostředí PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) nebo prostřednictvím nástroje pro vyrovnávání zatížení pro tento cluster [webu Azure portal ](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Vytvoření testovacího clusteru v Azure
V Průzkumníku řešení klikněte pravým tlačítkem na aplikaci **Voting** a vyberte **Publikovat**.

V **koncový bod připojení**vyberte **vytvořit nový Cluster**.  Pokud provádíte nasazení do existujícího clusteru, vyberte koncový bod clusteru ze seznamu.  Otevře se dialogové okno Vytvořit Cluster Service Fabric.

V **clusteru** kartu, zadejte **název clusteru** (například "mytestcluster"), vyberte své předplatné, vyberte oblast pro cluster (například střed USA – jih), zadejte počet uzlů clusteru (jsme Doporučujeme tři uzly clusteru testu) a zadejte skupinu prostředků (například "mytestclustergroup"). Klikněte na **Další**.

![Vytvoření clusteru](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

V **certifikát** kartu, zadejte heslo a výstupní cesta certifikátu clusteru. Certifikát podepsaný svým držitelem je vytvořen jako soubor PFX a uloží do zadané výstupní cesty.  Certifikát se používá pro zabezpečení mezi uzly a uzel klienta.  Certifikát podepsaný svým držitelem by neměla používat pro produkční clustery.  Tento certifikát slouží k ověření v clusteru a nasazení aplikací pomocí sady Visual Studio. Vyberte **importovat certifikát** do úložiště certifikátů CurrentUser\My počítače nainstalujte soubor PFX.  Klikněte na **Další**.

![Vytvoření clusteru](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

V **podrobnosti virtuálního počítače** kartu, zadejte **uživatelské jméno** a **heslo** pro účet Správce clusteru.  Vyberte **image virtuálního počítače** pro uzly clusteru a **velikost virtuálního počítače** pro každý uzel clusteru.  Klikněte na tlačítko **Upřesnit** kartu.

![Vytvoření clusteru](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

V **porty**, zadejte koncový bod služby VotingWeb z předchozího kroku (např. 8080).  Po vytvoření clusteru jsou tyto porty aplikace otevřít ve službě Azure load balancer směrovat provoz do clusteru.  Klikněte na tlačítko **vytvořit** k vytvoření clusteru trvá několik minut.

![Vytvoření clusteru](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Publikujte aplikaci do clusteru

Pokud bude nový cluster připravený, můžete nasadit aplikaci Voting přímo ze sady Visual Studio.

V Průzkumníku řešení klikněte pravým tlačítkem na aplikaci **Voting** a vyberte **Publikovat**. Zobrazí se dialogové okno **Publikovat**.

V **koncový bod připojení**, vyberte koncový bod clusteru, kterou jste vytvořili v předchozím kroku.  Například "mytestcluster.southcentral.cloudapp.azure.com:19000". Pokud vyberete **rozšířené parametry připojení**, informace o certifikátu by měl být automaticky vyplněný.  
![Publikovat aplikaci Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Vyberte **Publikovat**.

Jakmile je aplikace nasazená, otevřete prohlížeč a zadejte adresu clusteru, za nímž následuje **: 8080**. Případně zadejte jiný port, pokud je nakonfigurovaný. Příklad: `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Zobrazí se aplikace spuštěná v clusteru v Azure. Na hlasovací webové stránce zkuste přidávat a odstraňovat možnosti hlasování a hlasovat pro jednu nebo několik z těchto možností.

![Ukázková hlasovací aplikace Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Další postup
V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření clusteru.
> * Nasadit aplikaci do vzdáleného clusteru pomocí sady Visual Studio

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Povolení HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
