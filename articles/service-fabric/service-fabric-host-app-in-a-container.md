---
title: Nasazení aplikace .NET v kontejneru do Azure Service Fabric | Microsoft Docs
description: Podívejte se, jak kontejnerizovat existující aplikaci .NET pomocí sady Visual Studio a jak místně ladit kontejnery v Service Fabricu. Kontejnerizovaná aplikace se odešle do registru kontejneru Azure a nasadí se do clusteru Service Fabricu. Po nasazení do Azure používá aplikace k uchovávání dat databázi SQL Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/18/2018
ms.author: ryanwi
ms.openlocfilehash: 7573746b91f057d83a299d54801785118cc1c878
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380127"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Kurz: Nasazení aplikace .NET v kontejneru Windows do Azure Service Fabricu

V tomto kurzu se dozvíte, jak kontejnerizovat existující aplikaci ASP.NET a pak ji zabalit jako aplikaci Service Fabricu.  Ve vývojovém clusteru Service Fabricu spustíte kontejnery nejdřív místně a pak aplikaci nasadíte do Azure.  Aplikace uchovává data ve službě [Azure SQL Database](/azure/sql-database/sql-database-technical-overview). 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kontejnerizovat existující aplikaci pomocí sady Visual Studio
> * Vytvoření databáze SQL Azure
> * Vytvořit registr kontejneru Azure
> * Nasadit aplikaci Service Fabricu v Azure

## <a name="prerequisites"></a>Požadavky

1. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
2. Nainstalujte [Docker CE pro Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description), abyste mohli spouštět kontejnery ve Windows 10.
3. Nainstalujte [modul runtime Service Fabric 6.2 nebo novější](service-fabric-get-started.md) a [sadu Service Fabric SDK 3.1](service-fabric-get-started.md) nebo novější.
4. Nainstalujte [sadu Visual Studio 2017](https://www.visualstudio.com/) (verzi 15.7 nebo novější) se sadami funkcí **Vývoj pro Azure** a **Vývoj pro ASP.NET a web**.
5. Nainstalujte [Azure PowerShell][link-azure-powershell-install].
 

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Stažení a spuštění aplikace Fabrikam Fiber CallCenter
Stáhněte si ukázkovou aplikaci [Fabrikam Fiber CallCenter][link-fabrikam-github].  Klikněte na odkaz pro **stažení archivu**.  Z adresáře *sourceCode* v souboru *fabrikam.zip* rozbalte soubor *sourceCode.zip* a extrahujte do počítače adresář *VS2015*.

Ověřte, že se aplikace Fabrikam Fiber CallCenter sestavila a spustila bez chyb.  Spusťte sadu Visual Studio jako **správce** a otevřete soubor [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Po stisknutí klávesy F5 proběhne ladění a spuštění aplikace.

![Ukázka webu Fabrikam][fabrikam-web-page]

## <a name="containerize-the-application"></a>Kontejnerizace aplikace
Klikněte pravým tlačítkem na projekt **FabrikamFiber.Web** > **Přidat** > **Podpora orchestrátoru kontejnerů**.  Vyberte **Service Fabric** jako orchestrátor kontejnerů a klikněte na **OK**.

Kliknutím na **Ano** přepnete Docker na kontejnery Windows.

V řešení se vytvoří nový projekt aplikace Service Fabric **FabrikamFiber.CallCenterApplication**.  Do existujícího projektu **FabrikamFiber.Web** se přidá soubor Docker.  Do projektu **FabrikamFiber.Web** se také přidá adresář **PackageRoot**, který obsahuje manifest služby a nastavení pro novou službu FabrikamFiber.Web. 

Kontejner je teď připravený k sestavení a zabalení do aplikace Service Fabric. Jakmile na svém počítači budete mít sestavenou image kontejneru, můžete ji nasdílet do jakéhokoli registru kontejnerů a stáhnout a spustit na jakémkoli hostiteli.

## <a name="create-an-azure-sql-db"></a>Vytvoření databáze SQL Azure
Při spuštění aplikace Fabrikam Fiber CallCenter v produkčním prostředí musí být data ukládána v databázi. V současné době neexistuje způsob, jak zaručit trvalost dat v kontejneru, proto na SQL Server v kontejneru nemůžete ukládat produkční data.

Doporučujeme [Azure SQL Database](/azure/sql-database/sql-database-get-started-powershell). K nastavení a spuštění spravované databáze SQL Serveru v Azure spusťte následující skript.  Podle potřeby upravte proměnné skriptu. *clientIP* je IP adresa vašeho vývojového počítače.  Pokud jste za podnikovou bránou firewall, IP adresa vašeho vývojového počítače nemusí být přístupná na internetu.  Můžete také nastavit pravidlo brány firewall serveru pro databázi SQL prostřednictvím portálu [Azure Portal](https://portal.azure.com), které vypíše IP adresy vašeho počítače.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The logical server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "24.18.117.76"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzureRmSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzureRmSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Creeate the database in the server.
New-AzureRmSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

## <a name="update-the-web-config"></a>Aktualizace webové konfigurace
Vraťte se do projektu **FabrikamFiber.Web** a aktualizujte připojovací řetězec v souboru **web.config** tak, aby odkazoval na SQL Server v kontejneru.  Aktualizujte část *Server* tohoto připojovacího řetězce na server vytvořený předchozím skriptem. 

```xml
<add name="FabrikamFiber-Express" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```
>[!NOTE]
>Pro účely místního ladění můžete použít jakýkoli SQL Server, kterému dáváte přednost, pokud je dostupný z vašeho hostitele. Nicméně **localdb** nepodporuje komunikaci `container -> host`. Pokud při sestavování vaší webové aplikace pro vydání chcete použít jinou databázi SQL, přidejte do souboru *web.release.config* další připojovací řetězec.

## <a name="run-the-containerized-application-locally"></a>Místní spuštění kontejnerizované aplikace
Když stisknete klávesu **F5**, proběhne ladění a spuštění aplikace v kontejneru v místním vývojovém clusteru Service Fabricu. Pokud se vám zobrazí okno se zprávou žádající o udělení oprávnění ke čtení a spouštění vašeho adresáře projektu sady Visual Studio pro skupinu ServiceFabricAllowedUsers, klikněte na **Ano**.

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů
Teď, když aplikace běží místně, začněte s přípravou nasazení do Azure.  Image kontejneru musí být uložené v registru kontejneru.  Pomocí následujícího skriptu vytvořte [registr kontejneru Azure](/azure/container-registry/container-registry-intro). Název registru kontejneru mohou zobrazovat jiná předplatná Azure, a proto musí být jedinečný.
Než aplikaci nasadíte do Azure, odešlete do tohoto registru image kontejneru.  Jakmile je aplikace nasazená v clusteru v Azure, image kontejneru se z tohoto registru odebere.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzureRmResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzureRMContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Vytvoření clusteru Service Fabricu v Azure
Aplikace Service Fabric se spouští v clusteru, což je síťově propojená sada virtuálních nebo fyzických počítačů.  Abyste mohli aplikaci nasadit do Azure, vytvořte v Azure nejdřív cluster Service Fabricu.

Můžete:
- Vytvořit testovací cluster v sadě Visual Studio. Tato možnost umožňuje vytvořit zabezpečený cluster přímo ze sady Visual Studio s použitím upřednostňované konfigurace. 
- [Vytvořit zabezpečený cluster ze šablony](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

V tomto kurzu vytvoříte cluster v sadě Visual Studio, což je nejvhodnější scénář pro účely testování. Pokud vytvoříte cluster jiným způsobem nebo použijete existující cluster, můžete zkopírovat a vložit svůj koncový bod připojení nebo ho zvolit ze svého předplatného. 

Při vytváření clusteru vyberte jednotku SKU, která podporuje spuštěné kontejnery. Operační systém Windows Server v uzlech clusteru musí být kompatibilní s operačním systémem Windows Server vašeho kontejneru. Další informace najdete v článku o [kompatibilitě operačního systému kontejneru a operačního systému hostitele s Windows Serverem](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). Tento kurz ve výchozím nastavení vytvoří image Dockeru založenou na Windows Serveru 2016 LTSC. Kontejnery založené na této imagi budou fungovat v clusterech vytvořených pomocí edice Windows Server 2016 Datacenter s produktem Containers. Pokud ale vytvoříte cluster nebo použijete existující cluster založený na edici Windows Server Datacenter Core 1709 s produktem Containers, musíte změnit image operačního systému Windows Server, na které je kontejner založený. Otevřete soubor **Dockerfile** v projektu **FabrikamFiber.Web**, okomentujte stávající příkaz `FROM` (založený na `windowsservercore-ltsc`) a zrušte komentář u příkazu `FROM` založeného na `windowsservercore-1709`. 

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt aplikace **FabrikamFiber.CallCenterApplication** a zvolte **Publikovat**.

2. Přihlaste se pomocí svého účtu Azure, abyste získali přístup ke svým předplatným. 

3. Vyberte rozevírací seznam **Koncový bod připojení** a v něm vyberte možnost **Vytvořit nový cluster**.    
        
4. V dialogovém okně **Vytvořit cluster** upravte následující nastavení:

    1. Do pole **Název clusteru** zadejte název svého clusteru a zadejte také předplatné a umístění, které chcete použít.
    2. Volitelné: Můžete upravit počet uzlů. Ve výchozím nastavení máte tři uzly, což je požadované minimum pro testovací scénáře pro Service Fabric.
    3. Vyberte kartu **Certifikát**. Na této kartě zadejte heslo, které se použije k zabezpečení certifikátu vašeho clusteru. Tento certifikát pomáhá zabezpečit váš cluster. Můžete také upravit cestu, kam chcete certifikát uložit. Sada Visual Studio může také importovat certifikát za vás, protože se jedná o požadovaný krok pro publikování aplikace do clusteru.
    4. Vyberte kartu **Podrobnosti virtuálního počítače**. Zadejte heslo, které chcete použít pro virtuální počítače, které tvoří cluster. Pomocí uživatelského jména a hesla je možné se vzdáleně připojit k virtuálním počítačům. Musíte také vybrat velikost virtuálních počítačů a v případě potřeby změnit image virtuálního počítače.
    5. Na kartě **Upřesnit** uveďte port aplikace, který se po nasazení clusteru otevře v nástroji pro vyrovnávání zatížení. V Průzkumníku řešení otevřete soubor FabrikamFiber.Web->PackageRoot->ServiceManifest.xml.  Port pro front-end web je uvedený v části **Koncový bod**.  Můžete také přidat existující klíč Application Insights, který se použije ke směrování souborů aplikačních protokolů.
    6. Až dokončíte úpravy nastavení, vyberte tlačítko **Vytvořit**. 
5. Vytvoření trvá několik minut. Po úplném vytvoření clusteru se zobrazí oznámení v okně výstupu.
    

## <a name="allow-your-application-running-in-azure-to-access-the-sql-db"></a>Povolení přístupu k databázi SQL pro aplikaci spuštěnou v Azure
V předchozích krocích jste vytvořili pravidlo brány firewall pro SQL, a zajistili jste tak přístup pro vaši místní aplikaci.  Teď je potřeba povolit přístup k databázi SQL i pro aplikaci spuštěnou v Azure.  Vytvořte [koncový bod služby virtuální sítě](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) pro cluster Service Fabricu a pak vytvořte pravidlo, které pro tento koncový bod povolí přístup k databázi SQL.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "fabrikamfiber.callcenterapplication_RG"
$resource = Get-AzureRmResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName 

# Get the virtual network by name.
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzureRmVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```
## <a name="deploy-the-application-to-azure"></a>Nasazení aplikace v Azure
Aplikace je teď připravená a můžete ji nasadit do clusteru v Azure přímo ze sady Visual Studio.  V Průzkumníku řešení klikněte pravým tlačítkem na projekt aplikace **FabrikamFiber.CallCenterApplication** a zvolte **Publikovat**.  V rozevíracím seznamu **Koncový bod připojení** vyberte koncový bod clusteru, který jste vytvořili v předchozím postupu.  V rozevíracím seznamu **Azure Container Registry** vyberte registr kontejneru, který jste vytvořili v předchozím postupu.  Kliknutím na **Publikovat** nasaďte aplikaci do clusteru v Azure.

![Publikování aplikace][publish-app]

Postupujte podle pokynů k nasazení v okně výstupu.  Po nasazení aplikace otevřete prohlížeč a zadejte adresu clusteru a port aplikace. Například, http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/.

![Ukázka webu Fabrikam][fabrikam-web-page-deployed]

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Nastavení průběžné integrace a nasazování (CI/CD) s využitím clusteru Service Fabric
Informace o použití Azure DevOps ke konfiguraci nasazení aplikace s CI/CD do clusteru Service Fabric najdete v článku [Kurz: Nasazení aplikace s CI/CD do clusteru Service Fabric](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). Postup popsaný v tomto kurzu je stejný i pro tento projekt (FabrikamFiber), stačí přeskočit stažení ukázky Voting a místo názvu úložiště Voting použít FabrikamFiber.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Po skončení nezapomeňte odebrat všechny prostředky, které jste vytvořili.  Nejjednodušší způsob, jak to udělat, je odebrat skupiny prostředků, které obsahují cluster Service Fabricu, databázi SQL Azure a Azure Container Registry.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzureRmResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzureRmResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzureRmResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Kontejnerizovat existující aplikaci pomocí sady Visual Studio
> * Vytvoření databáze SQL Azure
> * Vytvořit registr kontejneru Azure
> * Nasadit aplikaci Service Fabricu v Azure

V další části tohoto kurzu se dozvíte, jak [nasadit aplikaci typu kontejner s CI/CD do clusteru Service Fabric](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md).

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
