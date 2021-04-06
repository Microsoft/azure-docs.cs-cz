---
title: Nasazení aplikace .NET v kontejneru do Azure Service Fabric
description: Podívejte se, jak kontejnerizovat existující aplikaci .NET pomocí sady Visual Studio a jak místně ladit kontejnery v Service Fabricu. Kontejnerizovaná aplikace se odešle do registru kontejneru Azure a nasadí se do clusteru Service Fabricu. Po nasazení do Azure používá aplikace k uchovávání dat databázi SQL Azure.
ms.topic: tutorial
ms.date: 07/08/2019
ms.openlocfilehash: 7930651a0faa5f37336c15557e2a0f068d613011
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936712"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Kurz: Nasazení aplikace .NET v kontejneru Windows do Azure Service Fabricu

V tomto kurzu se dozvíte, jak kontejnerizovat existující aplikaci ASP.NET a pak ji zabalit jako aplikaci Service Fabricu.  Ve vývojovém clusteru Service Fabricu spustíte kontejnery nejdřív místně a pak aplikaci nasadíte do Azure.  Aplikace uchovává data ve službě [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Kontejnerizovat existující aplikaci pomocí sady Visual Studio
> * Vytvoření databáze v Azure SQL Database
> * Vytvoření registru kontejneru Azure
> * Nasadit aplikaci Service Fabricu v Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

1. Pokud nemáte předplatné Azure, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
2. Povolte funkce Windows **Hyper-V** a **kontejnery**.
3. Nainstalujte [Docker Desktop pro Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) , abyste mohli spouštět kontejnery ve Windows 10.
4. Nainstalujte [modul runtime Service Fabric 6.2 nebo novější](service-fabric-get-started.md) a [sadu Service Fabric SDK 3.1](service-fabric-get-started.md) nebo novější.
5. Nainstalujte [Visual Studio 2019 verze 16,1](https://www.visualstudio.com/) nebo novější s úlohami vývoje pro vývoj a **ASP.NET a webový vývoj** pro **Azure** .
6. Nainstalovat [Azure PowerShell][link-azure-powershell-install]

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Stažení a spuštění aplikace Fabrikam Fiber CallCenter

1. Stáhněte si ukázkovou aplikaci [Fabrikam Fiber CallCenter][link-fabrikam-github].  Klikněte na odkaz pro **stažení archivu**.  Z adresáře *sourceCode* v souboru *fabrikam.zip* rozbalte soubor *sourceCode.zip* a extrahujte do počítače adresář *VS2015*.

2. Ověřte, že se aplikace Fabrikam Fiber CallCenter sestavila a spustila bez chyb.  Spusťte sadu Visual Studio jako **správce** a otevřete soubor [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Po stisknutí klávesy F5 proběhne ladění a spuštění aplikace.

   ![Snímek obrazovky domovské stránky aplikace Fabrikam Fiber CallCenter spuštěné na místním hostiteli. Stránka zobrazuje řídicí panel se seznamem volání podpory.][fabrikam-web-page]

## <a name="create-an-azure-sql-db"></a>Vytvoření databáze SQL Azure

Při spuštění aplikace Fabrikam Fiber CallCenter v produkčním prostředí musí být data ukládána v databázi. V současné době neexistuje způsob, jak zaručit trvalost dat v kontejneru, proto na SQL Server v kontejneru nemůžete ukládat produkční data.

Doporučujeme [Azure SQL Database](../azure-sql/database/powershell-script-content-guide.md). K nastavení a spuštění spravované databáze SQL Serveru v Azure spusťte následující skript.  Podle potřeby upravte proměnné skriptu. *clientIP* je IP adresa vašeho vývojového počítače. Poznamenejte si název serveru výstupu, který skript vybral.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzAccount -SubscriptionId $subscriptionID

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Create the database in the server.
New-AzSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

> [!TIP]
> Pokud jste za podnikovou bránou firewall, IP adresa vašeho vývojového počítače nemusí být přístupná na internetu. Pokud chcete ověřit, že má databáze správnou IP adresu pro pravidlo brány firewall, přejděte na web [Azure Portal](https://portal.azure.com) a v části Databáze SQL vyhledejte svou databázi. Klikněte na její název a pak v části Přehled klikněte na Nastavit bránu firewall serveru. IP adresa klienta je IP adresa vašeho počítače pro vývoj. Ujistěte se, že se shoduje s IP adresou v pravidle AllowClient.

## <a name="update-the-web-config"></a>Aktualizace webové konfigurace

Vraťte se do projektu **FabrikamFiber.Web** a aktualizujte připojovací řetězec v souboru **web.config** tak, aby odkazoval na SQL Server v kontejneru.  Aktualizujte část připojovacího řetězce *serveru* tak, aby byl název serveru vytvořený předchozím skriptem. Měl by být podobný jako "fab-fiber-751718376.database.windows.net". V následujícím kódu XML potřebujete aktualizovat pouze `connectionString` atribut; `providerName` `name` atributy a není nutné měnit.

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```

>[!NOTE]
>Pro účely místního ladění můžete použít jakýkoli SQL Server, kterému dáváte přednost, pokud je dostupný z vašeho hostitele. Nicméně **localdb** nepodporuje komunikaci `container -> host`. Pokud při sestavování vaší webové aplikace pro vydání chcete použít jinou databázi SQL, přidejte do souboru *web.release.config* další připojovací řetězec.

## <a name="containerize-the-application"></a>Kontejnerizace aplikace

1. Klikněte pravým tlačítkem na projekt **FabrikamFiber.Web** > **Přidat** > **Podpora orchestrátoru kontejnerů**.  Vyberte **Service Fabric** jako orchestrátor kontejnerů a klikněte na **OK**.

2. Pokud se zobrazí výzva, klikněte na **Ano** a teď přepnete Docker na kontejnery Windows.

   V řešení se vytvoří nový projekt aplikace Service Fabric **FabrikamFiber.CallCenterApplication**.  Do existujícího projektu **FabrikamFiber.Web** se přidá soubor Docker.  Do projektu **FabrikamFiber.Web** se také přidá adresář **PackageRoot**, který obsahuje manifest služby a nastavení pro novou službu FabrikamFiber.Web.

   Kontejner je teď připravený k sestavení a zabalení do aplikace Service Fabric. Jakmile na svém počítači budete mít sestavenou image kontejneru, můžete ji nasdílet do jakéhokoli registru kontejnerů a stáhnout a spustit na jakémkoli hostiteli.

## <a name="run-the-containerized-application-locally"></a>Místní spuštění kontejnerizované aplikace

Když stisknete klávesu **F5**, proběhne ladění a spuštění aplikace v kontejneru v místním vývojovém clusteru Service Fabricu. Pokud se vám zobrazí okno se zprávou žádající o udělení oprávnění ke čtení a spouštění vašeho adresáře projektu sady Visual Studio pro skupinu ServiceFabricAllowedUsers, klikněte na **Ano**.

Pokud příkaz F5 spustí výjimku, například následující, pak nebyla do brány firewall databáze Azure přidána správná IP adresa.

```text
System.Data.SqlClient.SqlException
HResult=0x80131904
Message=Cannot open server 'fab-fiber-751718376' requested by the login. Client with IP address '123.456.789.012' is not allowed to access the server.  To enable access, use the Windows Azure Management Portal or run sp_set_firewall_rule on the master database to create a firewall rule for this IP address or address range.  It may take up to five minutes for this change to take effect.
Source=.Net SqlClient Data Provider
StackTrace:
<Cannot evaluate the exception stack trace>
```

Pokud chcete do brány firewall Azure Database přidat příslušnou IP adresu, spusťte následující příkaz.

```powershell
# The IP address of your development computer that accesses the SQL DB.
$clientIPNew = "<client IP from the Error Message>"

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClientNew" -StartIpAddress $clientIPNew -EndIpAddress $clientIPNew
```

## <a name="create-a-container-registry"></a>Vytvoření registru kontejneru

Teď, když aplikace běží místně, začněte s přípravou nasazení do Azure.  Image kontejneru musí být uložené v registru kontejneru.  Pomocí následujícího skriptu vytvořte [registr kontejneru Azure](../container-registry/container-registry-intro.md). Název registru kontejneru mohou zobrazovat jiná předplatná Azure, a proto musí být jedinečný.
Než aplikaci nasadíte do Azure, odešlete do tohoto registru image kontejneru.  Jakmile je aplikace nasazená v clusteru v Azure, image kontejneru se z tohoto registru odebere.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Vytvoření clusteru Service Fabricu v Azure

Aplikace Service Fabric se spouští v clusteru, což je síťově propojená sada virtuálních nebo fyzických počítačů.  Než budete moct nasadit aplikaci do Azure, vytvořte v Azure Cluster Service Fabric.

Další možnosti:

* Vytvořit testovací cluster v sadě Visual Studio. Tato možnost umožňuje vytvořit zabezpečený cluster přímo ze sady Visual Studio s použitím upřednostňované konfigurace.
* [Vytvořit zabezpečený cluster ze šablony](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

V tomto kurzu vytvoříte cluster v sadě Visual Studio, což je nejvhodnější scénář pro účely testování. Pokud vytvoříte cluster jiným způsobem nebo použijete existující cluster, můžete zkopírovat a vložit svůj koncový bod připojení nebo ho zvolit ze svého předplatného.

Než začnete, otevřete FabrikamFiber. Web-> PackageRoot-> ServiceManifest.xml v Průzkumník řešení. Poznamenejte si port pro webový front-end, který je uvedený v **koncovém bodu**.

Při vytváření clusteru:

1. V Průzkumníku řešení klikněte pravým tlačítkem na projekt aplikace **FabrikamFiber.CallCenterApplication** a zvolte **Publikovat**.
2. Přihlaste se pomocí svého účtu Azure, abyste získali přístup ke svým předplatným.
3. Pod rozevíracím seznamem **koncového bodu připojení** vyberte možnost **vytvořit nový cluster...** .
4. V dialogovém okně **Vytvořit cluster** upravte následující nastavení:

    a. Do pole **Název clusteru** zadejte název svého clusteru a zadejte také předplatné a umístění, které chcete použít. Poznamenejte si název skupiny prostředků clusteru.

    b. Volitelné: Můžete upravit počet uzlů. Ve výchozím nastavení máte tři uzly, což je požadované minimum pro testovací scénáře pro Service Fabric.

    c. Vyberte kartu **certifikát** . Na této kartě zadejte heslo, které chcete použít k zabezpečení certifikátu vašeho clusteru. Tento certifikát pomáhá zabezpečit váš cluster. Můžete také upravit cestu, kam chcete certifikát uložit. Sada Visual Studio může také importovat certifikát za vás, protože se jedná o požadovaný krok pro publikování aplikace do clusteru.

    >[!NOTE]
    >Poznamenejte si cestu ke složce, do které se tento certifikát naimportuje. Dalším krokem po vytvoření clusteru je import tohoto certifikátu.

    d. Vyberte kartu **Podrobnosti o virtuálním počítači** . Zadejte heslo, které chcete použít pro Virtual Machines (virtuální počítač), který tvoří cluster. Pomocí uživatelského jména a hesla je možné se vzdáleně připojit k virtuálním počítačům. Musíte také vybrat velikost virtuálních počítačů a v případě potřeby změnit image virtuálního počítače.

    > [!IMPORTANT]
    > Vyberte SKLADOVOU položku, která podporuje spuštěné kontejnery. Operační systém Windows Server v uzlech clusteru musí být kompatibilní s operačním systémem Windows Server vašeho kontejneru. Další informace najdete v článku o [kompatibilitě operačního systému kontejneru a operačního systému hostitele s Windows Serverem](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). Tento kurz ve výchozím nastavení vytvoří image Dockeru založenou na Windows Serveru 2016 LTSC. Kontejnery založené na této imagi budou fungovat v clusterech vytvořených pomocí edice Windows Server 2016 Datacenter s produktem Containers. Pokud však vytvoříte cluster nebo použijete existující cluster založený na jiné verzi systému Windows Server, je nutné změnit bitovou kopii operačního systému, na které je kontejner založen. Otevřete **souboru Dockerfile** v projektu **FabrikamFiber. Web** , zakomentujte všechny existující `FROM` příkazy na základě předchozí verze Windows serveru a přidejte `FROM` příkaz založený na značce požadované verze ze [stránky Windows Server Core dockerhubu](https://hub.docker.com/_/microsoft-windows-servercore). Další informace o vydáních jádra systému Windows Server, podpoře časových os a způsobu správy verzí najdete v tématu [informace o vydání jádra Windows serveru](/windows-server/get-started/windows-server-release-info). 

    e. Na kartě **Upřesnit** uveďte port aplikace, který se po nasazení clusteru otevře v nástroji pro vyrovnávání zatížení. Jedná se o port, který jste si poznamenali před zahájením vytváření clusteru. Můžete také přidat existující klíč Application Insights, který se použije ke směrování souborů aplikačních protokolů.

    f. Až dokončíte úpravy nastavení, vyberte tlačítko **Vytvořit**.

5. Vytvoření trvá několik minut. Po úplném vytvoření clusteru se zobrazí oznámení v okně výstupu.

## <a name="install-the-imported-certificate"></a>Nainstalovat importovaný certifikát

Nainstalujte certifikát importovaný jako součást kroku vytvoření clusteru, do umístění **aktuálního úložiště uživatele** a zadejte heslo k privátnímu klíči, které jste zadali.

Instalaci můžete potvrdit otevřením **možnosti spravovat certifikáty uživatelů** v Ovládacích panelech a potvrzením, že certifikát je nainstalovaný v části **certifikáty –**  ->  **osobní**  ->  **certifikáty** uživatele. Certifikát by měl být jako *[název clusteru]*. *[Umístění clusteru]*. cloudapp.Azure.com, např. *fabrikamfibercallcenter.southcentralus.cloudapp.Azure.com*. 

## <a name="allow-your-application-running-in-azure-to-access-sql-database"></a>Umožněte aplikaci běžící v Azure přístup k SQL Database

V předchozích krocích jste vytvořili pravidlo brány firewall pro SQL, a zajistili jste tak přístup pro vaši místní aplikaci.  Teď je potřeba povolit přístup k databázi SQL i pro aplikaci spuštěnou v Azure.  Vytvořte [koncový bod služby pro virtuální síť](../azure-sql/database/vnet-service-endpoint-rule-overview.md) pro cluster Service Fabricu a pak vytvořte pravidlo, které pro tento koncový bod povolí přístup k databázi SQL. Nezapomeňte zadat proměnnou skupiny prostředků clusteru, na kterou jste si poznamenali při vytváření clusteru.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName

# Get the virtual network by name.
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```

## <a name="deploy-the-application-to-azure"></a>Nasazení aplikace do Azure

Aplikace je teď připravená a můžete ji nasadit do clusteru v Azure přímo ze sady Visual Studio.  V Průzkumníku řešení klikněte pravým tlačítkem na projekt aplikace **FabrikamFiber.CallCenterApplication** a zvolte **Publikovat**.  V rozevíracím seznamu **Koncový bod připojení** vyberte koncový bod clusteru, který jste vytvořili v předchozím postupu.  V rozevíracím seznamu **Azure Container Registry** vyberte registr kontejneru, který jste vytvořili v předchozím postupu.  Kliknutím na **Publikovat** nasaďte aplikaci do clusteru v Azure.

![Publikování aplikace][publish-app]

Postupujte podle pokynů k nasazení v okně výstupu. Po nasazení aplikace otevřete prohlížeč a zadejte adresu clusteru a port aplikace. Například, `http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/`.

![Snímek obrazovky domovské stránky aplikace Fabrikam Fiber CallCenter běžící na azure.com Stránka zobrazuje řídicí panel se seznamem volání podpory.][fabrikam-web-page-deployed]

Pokud se stránka nepovede načíst nebo se nedotazuje na certifikát, zkuste otevřít cestu k Průzkumníkovi, například `https://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:19080/Explorer` a vyberte nově nainstalovaný certifikát.

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Nastavení průběžné integrace a nasazování (CI/CD) s využitím clusteru Service Fabric

Informace o použití Azure DevOps ke konfiguraci nasazení aplikace s CI/CD do clusteru Service Fabric najdete v článku [Kurz: Nasazení aplikace s CI/CD do clusteru Service Fabric](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). Postup popsaný v tomto kurzu je stejný i pro tento projekt (FabrikamFiber), stačí přeskočit stažení ukázky Voting a místo názvu úložiště Voting použít FabrikamFiber.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po skončení nezapomeňte odebrat všechny prostředky, které jste vytvořili.  Nejjednodušší způsob, jak to udělat, je odebrat skupiny prostředků, které obsahují cluster Service Fabricu, databázi SQL Azure a Azure Container Registry.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Kontejnerizovat existující aplikaci pomocí sady Visual Studio
> * Vytvoření databáze v Azure SQL Database
> * Vytvoření registru kontejneru Azure
> * Nasadit aplikaci Service Fabricu v Azure

V další části tohoto kurzu se dozvíte, jak [nasadit aplikaci typu kontejner s CI/CD do clusteru Service Fabric](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md).

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-Az-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png