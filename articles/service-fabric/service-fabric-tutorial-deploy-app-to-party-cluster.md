---
title: Nasazení aplikace Service Fabric do clusteru v Azure | Microsoft Docs
description: Zjistěte, jak nasadit aplikaci do clusteru ze sady Visual Studio.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 37c27ae71eddcb5a35b9baeae250bee232c7acb7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74213206"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Kurz: Nasazení aplikace Service Fabric do clusteru v Azure

Tento kurz je druhá část série. Ukáže vám, jak nasadit aplikaci Azure Service Fabric do nového clusteru v Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Create a cluster.
> * Nasadit aplikaci do vzdáleného clusteru pomocí sady Visual Studio

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Sestavit aplikaci .NET pro Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Nasadit aplikaci do vzdáleného clusteru
> * [Přidat koncový bod HTTPS do front-end služby ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Nakonfigurovat CI/CD s využitím služby Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Nastavit monitorování a diagnostiku aplikace](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Install Visual Studio 2019](https://www.visualstudio.com/), and install the **Azure development** and **ASP.NET and web development** workloads.
* [Nainstalujte sadu Service Fabric SDK](service-fabric-get-started.md).

> [!NOTE]
> A free account may not meet the requirements to create a virtual machine. This will prevent the completion of the tutorial. In addition, a non-work or non-school account may encounter permission issues while creating the certificate on the keyvault associated with the cluster. If you experience an error related to certificate creation use the Portal to create the cluster instead. 

## <a name="download-the-voting-sample-application"></a>Stažení ukázkové hlasovací aplikace

Pokud jste nesestavili ukázkovou hlasovací aplikaci v [první části této série kurzů](service-fabric-tutorial-create-dotnet-app.md), můžete si ji stáhnout. In a command window, run the following code to clone the sample application repository to your local machine.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Open the application in Visual Studio, running as administrator, and build the application.

## <a name="create-a-cluster"></a>Vytvoření clusteru

Now that the application is ready, you create a Service Fabric cluster and then deploy the application to the cluster. [Cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) je síťově propojená sada virtuálních nebo fyzických počítačů, ve které se nasazují a spravují mikroslužby.

In this tutorial, you create a new three node test cluster in the Visual Studio IDE and then publish the application to that cluster. See the [Create and manage a cluster tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md) for information on creating a production cluster. You can also deploy the application to an existing cluster that you previously created through the [Azure portal](https://portal.azure.com), by using [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) or [Azure CLI](./scripts/cli-create-cluster.md) scripts, or from an [Azure Resource Manager template](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> The Voting application, and many other applications, use the Service Fabric reverse proxy to communicate between services. Clusters created from Visual Studio have the reverse proxy enabled by default. If you're deploying to an existing cluster, you must [enable the reverse proxy in the cluster](service-fabric-reverseproxy-setup.md) for the Voting application to work.


### <a name="find-the-votingweb-service-endpoint"></a>Vyhledání koncového bodu služby VotingWeb

The front-end web service of the Voting application is listening on a specific port (8080 if you in followed the steps in [part one of this tutorial series](service-fabric-tutorial-create-dotnet-app.md). Když se aplikace nasadí do clusteru v Azure, běží cluster i aplikace na pozadí služby Azure Load Balancer. The application port must be opened in the Azure load balancer by using a rule. The rule sends inbound traffic through the load balancer to the web service. Tento port se nachází v souboru **VotingWeb/PackageRoot/ServiceManifest.xml** v elementu **Endpoint**. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Take note of the service endpoint, which is needed in a later step.  If you're deploying to an existing cluster, open this port by creating a load-balancing rule and probe in the Azure load balancer using a [PowerShell script](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) or via the load balancer for this cluster in the [Azure portal](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Create a test cluster in Azure
V Průzkumníku řešení klikněte pravým tlačítkem na aplikaci **Voting** a vyberte **Publikovat**.

In **Connection Endpoint**, select **Create New Cluster**.  If you're deploying to an existing cluster, select the cluster endpoint from the list.  The Create Service Fabric Cluster dialog opens.

In the **Cluster** tab, enter the **Cluster name** (for example, "mytestcluster"), select your subscription, select a region for the cluster (such as South Central US), enter the number of cluster nodes (we recommend three nodes for a test cluster), and enter a resource group (such as "mytestclustergroup"). Klikněte na **Další**.

![Vytvoření clusteru](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

In the **Certificate** tab, enter the password and output path for the cluster certificate. A self-signed certificate is created as a PFX file and saved to the specified output path.  The certificate is used for both node-to-node and client-to-node security.  Don't use a self-signed certificate for production clusters.  This certificate is used by Visual Studio to authenticate with the cluster and deploy applications. Select **Import certificate** to install the PFX in the CurrentUser\My certificate store of your computer.  Klikněte na **Další**.

![Vytvoření clusteru](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

In the **VM Detail** tab, enter the **User name** and **Password** for the cluster admin account.  Select the **Virtual machine image** for the cluster nodes and the **Virtual machine size** for each cluster node.  Click the **Advanced** tab.

![Vytvoření clusteru](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

In **Ports**, enter the VotingWeb service endpoint from the previous step (for example, 8080).  When the cluster is created, these application ports are opened in the Azure load balancer to forward traffic to the cluster.  Click **Create** to create the cluster, which takes several minutes.

![Vytvoření clusteru](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Publish the application to the cluster

When the new cluster is ready, you can deploy the Voting application directly from Visual Studio.

V Průzkumníku řešení klikněte pravým tlačítkem na aplikaci **Voting** a vyberte **Publikovat**. Zobrazí se dialogové okno **Publikovat**.

In **Connection Endpoint**, select the endpoint for the cluster you created in the previous step.  For example, "mytestcluster.southcentral.cloudapp.azure.com:19000". If you select **Advanced Connection Parameters**, the certificate information should be auto-filled.  
![Publish a Service Fabric application](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Vyberte **Publikovat**.

Once the application is deployed, open a browser and enter the cluster address followed by **:8080**. Případně zadejte jiný port, pokud je nakonfigurovaný. Příklad: `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Zobrazí se aplikace spuštěná v clusteru v Azure. Na hlasovací webové stránce zkuste přidávat a odstraňovat možnosti hlasování a hlasovat pro jednu nebo několik z těchto možností.

![Ukázková hlasovací aplikace Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Další kroky
V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Create a cluster.
> * Nasadit aplikaci do vzdáleného clusteru pomocí sady Visual Studio

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Povolení HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
