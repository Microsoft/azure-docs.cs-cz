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
ms.date: 07/12/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: fe6df20d294a3b1802d396085c36a6587dc45730
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249077"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Kurz: Nasazení aplikace Service Fabric do clusteru v Azure

Tento kurz je druhá část série. Ukáže vám, jak nasadit aplikaci Azure Service Fabric do nového clusteru v Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvořit Party cluster
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

Pokud jste nesestavili ukázkovou hlasovací aplikaci v [první části této série kurzů](service-fabric-tutorial-create-dotnet-app.md), můžete si ji stáhnout. V příkazovém okně naklonujte spuštěním následujícího kódu úložiště ukázkové aplikace do místního počítače.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

## <a name="publish-to-a-service-fabric-cluster"></a>Publikování do clusteru Service Fabric

Aplikace je teď připravená a přímo ze sady Visual Studio ji můžete nasadit do clusteru. [Cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) je síťově propojená sada virtuálních nebo fyzických počítačů, ve které se nasazují a spravují mikroslužby.

Pro účely tohoto kurzu máte dvě možnosti nasazení hlasovací aplikace do clusteru Service Fabric pomocí sady Visual Studio:

* Publikovat do zkušebního (Party) clusteru. 
* Publikovat do existujícího clusteru ve vašem předplatném. Clustery Service Fabric můžete vytvořit prostřednictvím webu [Azure Portal](https://portal.azure.com), pomocí skriptů [PowerShellu](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) nebo [Azure CLI](./scripts/cli-create-cluster.md) nebo ze [šablony Azure Resource Manageru](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> Řada služeb ke komunikaci mezi sebou používá reverzní proxy server. Clustery vytvořené v sadě Visual Studio a Party Clustery mají ve výchozím nastavení reverzní proxy server povolený. Pokud používáte existující cluster, musíte [v clusteru povolit reverzní proxy server](service-fabric-reverseproxy-setup.md).


### <a name="find-the-voting-web-service-endpoint-for-your-azure-subscription"></a>Vyhledání koncového bodu webové hlasovací služby pro vaše předplatné Azure

Pokud chcete publikovat hlasovací aplikaci do vlastního předplatného Azure, vyhledejte koncový bod webové služby front-endu. Pokud používáte Party cluster, připojte se k portu 8080 s použitím automaticky otevřené ukázkové hlasovací aplikace. Není nutné ji konfigurovat v nástroji pro vyrovnávání zatížení Party clusteru.

Webová služba front-endu naslouchá na určitém portu. Když se aplikace nasadí do clusteru v Azure, běží cluster i aplikace na pozadí služby Azure Load Balancer. Port aplikace musí být otevřený pomocí pravidla v nástroji pro vyrovnávání zatížení Azure pro daný cluster. Otevřený port propouští příchozí provoz do webové služby. Tento port se nachází v souboru **VotingWeb/PackageRoot/ServiceManifest.xml** v elementu **Endpoint**. Příkladem je port 8080.

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Pro vaše předplatné Azure otevřete tento port v Azure pomocí pravidla vyrovnávání zatížení prostřednictvím [skriptu PowerShellu](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) nebo pomocí nástroje pro vyrovnávání zatížení pro tento cluster na webu [Azure Portal](https://portal.azure.com).

### <a name="join-a-party-cluster"></a>Připojení k Party clusteru

> [!NOTE]
>  Pokud chcete publikovat aplikaci ve vlastním clusteru v rámci předplatného Azure, přeskočte k části [Publikování aplikace pomocí sady Visual Studio](#publish-the-application-by-using-visual-studio). 

Party Clustery jsou bezplatné, časově omezené clustery Service Fabric hostované v Azure a provozované týmem Service Fabric. Kdokoli je může použít k nasazování aplikací a seznámení se s platformou. Cluster k zajištění zabezpečení mezi uzly i mezi klientem a uzlem využívá jeden certifikát podepsaný svým držitelem.

Přihlaste se a [připojte se ke clusteru Windows](https://aka.ms/tryservicefabric). Pokud si do počítače chcete stáhnout certifikát PFX, vyberte odkaz **PFX**. Vyberte odkaz **How to connect to a secure Party cluster?** (Jak se připojit k zabezpečenému Party Clusteru?) a zkopírujte heslo certifikátu. Certifikát, heslo certifikátu a hodnotu **Koncový bod připojení** použijete v následujících krocích.

![PFX a koncový bod připojení](./media/service-fabric-quickstart-dotnet/party-cluster-cert.png)

> [!Note]
> Každou hodinu je k dispozici omezený počet Party clusterů. Pokud při pokusu o registraci Party clusteru dojde k chybě, počkejte a zkuste to znovu. Případně podle kroků v kurzu [Nasazení aplikace .NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application) vytvořte ve svém předplatném Azure cluster Service Fabric a nasaďte aplikaci do něj. Pokud ještě nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
>

Na svém počítači s Windows nainstalujte soubor PFX do úložiště certifikátů **CurrentUser\My**.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

Zapamatujte si kryptografický otisk pro následující krok.

> [!Note]
> Webová front-end služba je ve výchozím nastavení nakonfigurovaná k naslouchání příchozímu provozu na portu 8080. Port 8080 je v Party clusteru otevřený. Pokud potřebujete změnit port aplikace, změňte ho na některý z portů, které jsou v Party clusteru otevřené.
>

### <a name="publish-the-application-by-using-visual-studio"></a>Publikování aplikace pomocí sady Visual Studio

Aplikace je teď připravená a přímo ze sady Visual Studio ji můžete nasadit do clusteru.

1. V Průzkumníku řešení klikněte pravým tlačítkem na **Voting**. Zvolte **Publikovat**. Zobrazí se dialogové okno **Publikovat**.

2. Do pole **Koncový bod připojení** zkopírujte **Koncový bod připojení** ze stránky Party clusteru nebo z vašeho předplatného Azure. Příklad: `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Vyberte **Rozšířené parametry připojení**.  Ujistěte se, že hodnoty **FindValue** a **ServerCertThumbprint** odpovídají kryptografickému otisku certifikátu nainstalovaného v předchozím kroku pro Party cluster nebo certifikátu odpovídajícímu vašemu předplatnému Azure.

    ![Publikování aplikace Service Fabric](./media/service-fabric-quickstart-dotnet/publish-app.png)

    Každá aplikace v clusteru musí mít jedinečný název. Party clustery jsou veřejné a sdílené prostředí a může dojít ke konfliktu s již existující aplikací. Pokud dojde ke konfliktu názvů, přejmenujte projekt sady Visual Studio a opakujte nasazení.

3. Vyberte **Publikovat**.

4. Pokud chcete přejít do své hlasovací aplikace v clusteru, otevřete prohlížeč a zadejte adresu clusteru následovanou **:8080**. Případně zadejte jiný port, pokud je nakonfigurovaný. Příklad: `http://zwin7fh14scd.westus.cloudapp.azure.com:8080`. Zobrazí se aplikace spuštěná v clusteru v Azure. Na hlasovací webové stránce zkuste přidávat a odstraňovat možnosti hlasování a hlasovat pro jednu nebo několik z těchto možností.

    ![Ukázková hlasovací aplikace Service Fabric](./media/service-fabric-quickstart-dotnet/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Povolení HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
