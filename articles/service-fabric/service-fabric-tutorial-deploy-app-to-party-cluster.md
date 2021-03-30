---
title: Nasazení aplikace Service Fabric do clusteru v Azure
description: Naučte se, jak nasadit existující aplikaci do nově vytvořeného clusteru Azure Service Fabric ze sady Visual Studio.
author: athinanthny
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: e35b655dc8b735214de891884fe40fb951dd16cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91441288"
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
> * [Přidejte koncový bod HTTPS do ASP.NET Core front-endové služby](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md).
> * [Nakonfigurovat CI/CD s využitím služby Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Nastavte monitorování a diagnostiku pro aplikaci](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Nainstalujte Visual Studio 2019](https://www.visualstudio.com/)a nainstalujte úlohy vývoje pro **vývoj a vývoj pro Azure** a vývoj **webů** .
* [Nainstalujte sadu Service Fabric SDK](service-fabric-get-started.md).

> [!NOTE]
> Bezplatný účet nemusí splňovat požadavky na vytvoření virtuálního počítače. Tím se zabrání dokončení kurzu. Kromě toho může mít nepracovní nebo neškolní účet problémy s oprávněním při vytváření certifikátu v trezoru klíčů přidruženém ke clusteru. Pokud dojde k chybě související s vytvořením certifikátu, použijte místo toho portál k vytvoření clusteru. 

## <a name="download-the-voting-sample-application"></a>Stažení ukázkové hlasovací aplikace

Pokud jste nesestavili ukázkovou hlasovací aplikaci v [první části této série kurzů](service-fabric-tutorial-create-dotnet-app.md), můžete si ji stáhnout. V příkazovém okně spusťte následující kód, který naklonuje úložiště ukázkové aplikace do místního počítače.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Otevřete aplikaci v aplikaci Visual Studio, spusťte jako správce a sestavte aplikaci.

## <a name="create-a-cluster"></a>Vytvoření clusteru

Teď, když je aplikace připravená, vytvoříte Cluster Service Fabric a pak nasadíte aplikaci do clusteru. [Cluster Service Fabric](./service-fabric-deploy-anywhere.md) je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují.

V tomto kurzu vytvoříte v integrovaném vývojovém prostředí sady Visual Studio nový cluster testovacího clusteru a pak aplikaci publikujete do tohoto clusteru. Informace o vytváření produkčního clusteru najdete v [kurzu Vytvoření a Správa clusteru](service-fabric-tutorial-create-vnet-and-windows-cluster.md) . Aplikaci můžete nasadit taky do existujícího clusteru, který jste dříve vytvořili prostřednictvím [Azure Portal](https://portal.azure.com), pomocí [PowerShellu](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) nebo skriptů [Azure CLI](./scripts/cli-create-cluster.md) nebo ze [šablony Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> Hlasovací aplikace a řada dalších aplikací, ke komunikaci mezi službami použijte Service Fabric reverzní proxy server. Clustery vytvořené ze sady Visual Studio mají ve výchozím nastavení reverzní proxy server povolený. Pokud nasazujete na existující cluster, musíte [Povolit reverzní proxy server v clusteru](service-fabric-reverseproxy-setup.md) , aby mohla hlasovací aplikace fungovat.


### <a name="find-the-votingweb-service-endpoint"></a>Vyhledání koncového bodu služby VotingWeb

Front-end webová služba hlasovací aplikace naslouchá na určitém portu (8080, pokud jste postupovali podle kroků v [první části této série kurzů](service-fabric-tutorial-create-dotnet-app.md). Když se aplikace nasadí do clusteru v Azure, běží cluster i aplikace na pozadí služby Azure Load Balancer. Port aplikace musí být otevřen v nástroji pro vyrovnávání zatížení Azure pomocí pravidla. Pravidlo odešle příchozí přenos prostřednictvím nástroje pro vyrovnávání zatížení do webové služby. Tento port se nachází v souboru **VotingWeb/PackageRoot/ServiceManifest.xml** v elementu **Endpoint**. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Poznamenejte si koncový bod služby, který je potřeba v pozdějším kroku.  Pokud nasazujete na existující cluster, otevřete tento port vytvořením pravidla vyrovnávání zatížení a sondou v nástroji pro vyrovnávání zatížení Azure pomocí [skriptu PowerShellu](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) nebo prostřednictvím nástroje pro vyrovnávání zatížení pro tento cluster v [Azure Portal](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Vytvoření testovacího clusteru v Azure
V Průzkumníku řešení klikněte pravým tlačítkem na aplikaci **Voting** a vyberte **Publikovat**.

V **koncový bod připojení** vyberte **vytvořit nový cluster**.  Pokud nasazujete na existující cluster, vyberte koncový bod clusteru ze seznamu.  Otevře se dialogové okno vytvořit cluster Service Fabric.

Na kartě **cluster** zadejte **název clusteru** (například "mytestcluster"), vyberte své předplatné, vyberte oblast clusteru (například střed USA – jih), zadejte počet uzlů clusteru (pro testovací cluster doporučujeme tři uzly) a zadejte skupinu prostředků (například "mytestclustergroup"). Klikněte na **Next** (Další).

![Snímek obrazovky se zobrazí v dialogovém okně vytvořit Service Fabric clusteru na kartě cluster.](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

Na kartě **certifikát** zadejte heslo a výstupní cestu pro certifikát clusteru. Certifikát podepsaný svým držitelem se vytvoří jako soubor PFX a uloží se do zadané výstupní cesty.  Certifikát se používá pro zabezpečení mezi uzly i mezi klientem a uzlem.  Pro produkční clustery nepoužívejte certifikát podepsaný svým držitelem.  Tento certifikát používá Visual Studio k ověřování pomocí clusteru a nasazování aplikací. Vyberte **importovat certifikát** a nainstalujte PFX do úložiště certifikátů do currentuser\my vašeho počítače.  Klikněte na **Next** (Další).

![Snímek obrazovky se zobrazí na kartě certifikát v dialogovém okně vytvořit cluster Service Fabric.](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

Na kartě **Podrobnosti o virtuálním počítači** zadejte **uživatelské jméno** a **heslo** pro účet správce clusteru.  Vyberte **bitovou kopii virtuálního počítače** pro uzly clusteru a **Velikost virtuálního počítače** pro každý uzel clusteru.  Klikněte na kartu **Upřesnit** .

![Snímek obrazovky se zobrazí karta Podrobnosti V M dialogového okna vytvořit cluster Service Fabric.](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

V části **porty** Zadejte koncový bod služby VotingWeb z předchozího kroku (například 8080).  Po vytvoření clusteru se tyto porty aplikace otevřou v nástroji pro vyrovnávání zatížení Azure, abyste přesměrovali provoz do clusteru.  Kliknutím na **vytvořit** vytvořte cluster, který trvá několik minut.

![Snímek obrazovky se zobrazí na kartě Upřesnit v dialogovém okně vytvořit cluster Service Fabric.](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Publikování aplikace do clusteru

Když je nový cluster připravený, můžete hlasovací aplikaci nasadit přímo ze sady Visual Studio.

V Průzkumníku řešení klikněte pravým tlačítkem na aplikaci **Voting** a vyberte **Publikovat**. Zobrazí se dialogové okno **Publikovat**.

V části **koncový bod připojení** vyberte koncový bod pro cluster, který jste vytvořili v předchozím kroku.  Například "mytestcluster.southcentral.cloudapp.azure.com:19000". Pokud vyberete **rozšířené parametry připojení**, informace o certifikátu by měly být automaticky vyplněné.  
![Publikování aplikace Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Vyberte **Publikovat**.

Jakmile je aplikace nasazená, otevřete prohlížeč a zadejte adresu clusteru následovanou **: 8080**. Případně zadejte jiný port, pokud je nakonfigurovaný. Příklad: `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Zobrazí se aplikace spuštěná v clusteru v Azure. Na hlasovací webové stránce zkuste přidávat a odstraňovat možnosti hlasování a hlasovat pro jednu nebo několik z těchto možností.

![Ukázková hlasovací aplikace Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Další kroky
V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořte cluster.
> * Nasadit aplikaci do vzdáleného clusteru pomocí sady Visual Studio

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Povolení HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
