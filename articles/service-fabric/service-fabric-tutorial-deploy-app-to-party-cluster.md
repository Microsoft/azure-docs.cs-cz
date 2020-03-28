---
title: Nasazení aplikace Service Fabric do clusteru v Azure
description: Zjistěte, jak nasadit existující aplikaci do nově vytvořeného clusteru Azure Service Fabric z Visual Studia.
author: athinanthny
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 9951610732cbb1c5884a7b7e830033f427db0ab1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75646003"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Kurz: Nasazení aplikace Service Fabric do clusteru v Azure

Tento kurz je druhá část série. Ukáže vám, jak nasadit aplikaci Azure Service Fabric do nového clusteru v Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvořte cluster.
> * Nasadit aplikaci do vzdáleného clusteru pomocí sady Visual Studio

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Sestavit aplikaci .NET pro Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Nasadit aplikaci do vzdáleného clusteru
> * [Přidejte koncový bod HTTPS do ASP.NET služby front-endu Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md).
> * [Nakonfigurovat CI/CD s využitím služby Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Nastavte monitorování a diagnostiku pro aplikaci](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Nainstalujte Sivisual Studio 2019](https://www.visualstudio.com/)a nainstalujte **úlohy vývoje a** **ASP.NET azure a vývoj webových** aplikací.
* [Nainstalujte sadu Service Fabric SDK](service-fabric-get-started.md).

> [!NOTE]
> Bezplatný účet nemusí splňovat požadavky na vytvoření virtuálního počítače. Tím zabráníte dokončení kurzu. Kromě toho nepracovní nebo neškolní účet může dojít k problémům s oprávněním při vytváření certifikátu na keyvault přidružené ke clusteru. Pokud dojde k chybě související s vytvořením certifikátu, použijte portál k vytvoření clusteru. 

## <a name="download-the-voting-sample-application"></a>Stažení ukázkové hlasovací aplikace

Pokud jste nesestavili ukázkovou hlasovací aplikaci v [první části této série kurzů](service-fabric-tutorial-create-dotnet-app.md), můžete si ji stáhnout. V příkazovém okně spusťte následující kód pro klonování ukázkového úložiště aplikací do místního počítače.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Otevřete aplikaci v sadě Visual Studio, spouštějte ji jako správce a vytvořte aplikaci.

## <a name="create-a-cluster"></a>Vytvoření clusteru

Teď, když je aplikace připravená, vytvoříte cluster Service Fabric a pak ji nasadíte do clusteru. [Cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) je síťová sada virtuálních nebo fyzických počítačů, do kterých jsou vaše mikroslužby nasazeny a spravovány.

V tomto kurzu vytvoříte nový cluster testování tří uzlů v ide sady Visual Studio a potom publikujete aplikaci do tohoto clusteru. Informace o vytvoření produkčního clusteru naleznete v [kurzu Vytvořit a spravovat cluster.](service-fabric-tutorial-create-vnet-and-windows-cluster.md) Aplikaci můžete také nasadit do existujícího clusteru, který jste dříve vytvořili na [webu Azure Portal](https://portal.azure.com), pomocí skriptů [PowerShellu](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) nebo [Azure CLI](./scripts/cli-create-cluster.md) nebo ze [šablony Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> Hlasovací aplikace a mnoho dalších aplikací, použijte Service Fabric reverzní proxy komunikovat mezi službami. Clustery vytvořené z aplikace Visual Studio mají ve výchozím nastavení povolený reverzní proxy server. Pokud nasazujete do existujícího clusteru, musíte [povolit reverzní proxy server v clusteru,](service-fabric-reverseproxy-setup.md) aby aplikace Hlasování fungovala.


### <a name="find-the-votingweb-service-endpoint"></a>Vyhledání koncového bodu služby VotingWeb

Front-end webová služba aplikace Hlasování naslouchá na konkrétním portu (8080, pokud jste v postupovali podle kroků v [první části této série kurzů](service-fabric-tutorial-create-dotnet-app.md). Když se aplikace nasadí do clusteru v Azure, běží cluster i aplikace na pozadí služby Azure Load Balancer. Port aplikace musí být otevřen v azure vyrovnávání zatížení pomocí pravidla. Pravidlo odesílá příchozí provoz prostřednictvím systému vyrovnávání zatížení webové službě. Tento port se nachází v souboru **VotingWeb/PackageRoot/ServiceManifest.xml** v elementu **Endpoint**. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Poznamenejte si koncový bod služby, který je potřeba v pozdějším kroku.  Pokud nasazujete do existujícího clusteru, otevřete tento port vytvořením pravidla vyrovnávání zatížení a sondou v Azure balancer pomocí [skriptu PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) nebo prostřednictvím správce zatížení pro tento cluster na [portálu Azure](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Vytvoření testovacího clusteru v Azure
V Průzkumníku řešení klikněte pravým tlačítkem na aplikaci **Voting** a vyberte **Publikovat**.

V **části Koncový bod připojení**vyberte Vytvořit nový **cluster**.  Pokud nasazujete do existujícího clusteru, vyberte koncový bod clusteru ze seznamu.  Otevře se dialogové okno Vytvořit cluster prostředků infrastruktury služeb.

Na kartě **Cluster** zadejte **název clusteru** (například "mytestcluster"), vyberte předplatné, vyberte oblast pro cluster (například střední USA), zadejte počet uzlů clusteru (doporučujeme tři uzly pro testovací cluster) a zadejte skupinu prostředků (například "mytestclustergroup"). Klikněte na **Další**.

![Vytvoření clusteru](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

Na kartě **Certifikát** zadejte heslo a výstupní cestu pro certifikát clusteru. Certifikát podepsaný svým držitelem je vytvořen jako soubor PFX a uložen do zadané výstupní cesty.  Certifikát se používá pro zabezpečení mezi uzly i pro zabezpečení mezi klientem a uzly.  Nepoužívejte certifikát podepsaný svým držitelem pro produkční clustery.  Tento certifikát používá Visual Studio k ověření pomocí clusteru a nasazení aplikací. Vyberte **importcertifikát,** chcete-li nainstalovat PFX do úložiště certifikátů CurrentUser\My v počítači.  Klikněte na **Další**.

![Vytvoření clusteru](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

Na kartě **Podrobnosti virtuálního** počítače zadejte **uživatelské jméno** a **heslo** pro účet správce clusteru.  Vyberte **bitovou kopii virtuálního počítače** pro uzly clusteru a **velikost virtuálního počítače** pro každý uzel clusteru.  Klikněte na kartu **Upřesnit.**

![Vytvoření clusteru](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

V **portech**zadejte koncový bod služby VotingWeb z předchozího kroku (například 8080).  Po vytvoření clusteru se tyto porty aplikací otevřou v azure balancer předávat provoz do clusteru.  Kliknutím na **Vytvořit** vytvořte cluster, který trvá několik minut.

![Vytvoření clusteru](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Publikování aplikace do clusteru

Když je nový cluster připraven, můžete nasadit hlasovací aplikaci přímo z Visual Studia.

V Průzkumníku řešení klikněte pravým tlačítkem na aplikaci **Voting** a vyberte **Publikovat**. Zobrazí se dialogové okno **Publikovat**.

V **aplikaci Connection Endpoint**vyberte koncový bod pro cluster, který jste vytvořili v předchozím kroku.  Například "mytestcluster.southcentral.cloudapp.azure.com:19000". Pokud vyberete **upřesnit parametry připojení**, informace o certifikátu by měly být automaticky vyplněny.  
![Publikování aplikace Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Vyberte **Publikovat**.

Po nasazení aplikace otevřete prohlížeč a zadejte adresu clusteru následovanou **:8080**. Případně zadejte jiný port, pokud je nakonfigurovaný. Příklad: `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Zobrazí se aplikace spuštěná v clusteru v Azure. Na hlasovací webové stránce zkuste přidávat a odstraňovat možnosti hlasování a hlasovat pro jednu nebo několik z těchto možností.

![Ukázková hlasovací aplikace Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Další kroky
V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořte cluster.
> * Nasadit aplikaci do vzdáleného clusteru pomocí sady Visual Studio

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Povolení HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
