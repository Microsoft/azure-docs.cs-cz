---
title: Vytvoření clusteru Service Fabric s Windows v Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak nasadit cluster Service Fabric s Windows do virtuální sítě Azure a skupiny zabezpečení sítě s použitím PowerShellu.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 13d741d97e90b4aca40614d09f67538c479f67e3
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312255"
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Kurz: Nasazení clusteru Service Fabric s Windows do virtuální sítě Azure

Tento kurz je první částí série. Naučíte se nasadit cluster Service Fabric s Windows do [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md) a [skupiny zabezpečení sítě](../virtual-network/virtual-networks-nsg.md) s použitím PowerShellu a šablony. Po dokončení budete mít v cloudu spuštěný cluster, do kterého budete moct nasazovat aplikace.  Pokud chcete vytvořit cluster s Linuxem pomocí Azure CLI, přečtěte si téma [Vytvoření zabezpečeného clusteru s Linuxem v Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Tento kurz popisuje produkční scénář.  Pokud chcete rychle vytvořit cluster menší pro účely testování, najdete v článku [vytvoření testovacího clusteru](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě v Azure pomocí PowerShellu
> * Vytvoření trezoru klíčů a nahrání certifikátu
> * Vytvoření zabezpečeného clusteru Service Fabric v Azure PowerShellu
> * Zabezpečení clusteru pomocí certifikátu X.509
> * Připojení ke clusteru pomocí prostředí PowerShell
> * Odebrání clusteru

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného clusteru v Azure
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * [Odstranění clusteru](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte sadu [Service Fabric SDK a modul PowerShellu](service-fabric-get-started.md).
* Nainstalujte [modul Azure PowerShellu verze 4.1 nebo vyšší](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
* Projděte si klíčové koncepty [Azure clusterů](service-fabric-azure-clusters-overview.md)

Následující postupy vytvářejí sedm uzly clusteru Service Fabric. Pokud chcete vypočítat náklady vzniklé používáním clusteru Service Fabric v Azure, použijte [cenovou kalkulačku funkcí Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Stažení a prozkoumání šablony

Stáhněte si následující soubory šablon Resource Manageru:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Tato šablona nasadí zabezpečený cluster sedmi virtuálních počítačů a tři typy uzlů do virtuální sítě a skupinu zabezpečení sítě.  Další ukázkové šablony najdete na [GitHubu](https://github.com/Azure-Samples/service-fabric-cluster-templates).  Šablona [azuredeploy.json][template] nasadí řadu prostředků včetně následujících.

### <a name="service-fabric-cluster"></a>Cluster Service Fabric

V prostředku **Microsoft.ServiceFabric/clusters** se konfiguruje cluster s Windows s těmito charakteristikami:

* tři typy uzlů
* pět uzlů primárního typu (možnost konfigurace v parametrech šablony), jeden uzel v jiných typech dvěma uzly
* Operační systém: Windows Server 2016 Datacenter s kontejnery (možnost konfigurace v parametrech šablony)
* zabezpečení pomocí certifikátu (možnost konfigurace v parametrech šablony)
* [reverzní proxy server](service-fabric-reverseproxy.md) je povolen
* [služba DNS](service-fabric-dnsservice.md) je povolena
* bronzová [úroveň odolnosti](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) (možnost konfigurace v parametrech šablony)
* stříbrná [úroveň spolehlivosti](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) (možnost konfigurace v parametrech šablony)
* koncový bod připojení klienta: 19000 (možnost konfigurace v parametrech šablony)
* Koncový bod brány HTTP: 19080 (možnost konfigurace v parametrech šablony)

### <a name="azure-load-balancer"></a>Nástroj pro vyrovnávání zatížení Azure

V prostředku **Microsoft.Network/loadBalancers** dochází ke konfiguraci nástroje pro vyrovnávání zatížení a k nastavení sond a pravidel pro následující porty:

* koncový bod připojení klienta: 19000
* Koncový bod brány HTTP: 19080
* port aplikací: 80
* port aplikací: 443
* Service Fabric reverzního proxy serveru: 19081

Pokud jsou potřebné další porty aplikací, je potřeba upravit prostředky **Microsoft.Network/loadBalancers** a **Microsoft.Network/networkSecurityGroups**, aby byl povolený příchozí provoz.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuální síť, podsíť a skupina zabezpečení sítě

Názvy virtuální sítě, podsítě a skupiny zabezpečení sítě jsou deklarované v parametrech šablony.  Adresní prostory virtuální sítě a podsítě se taky deklarují v parametrech šablony a konfigurují v prostředku **Microsoft.Network/virtualNetworks**:

* adresní prostor virtuální sítě: 172.16.0.0/20
* Adresní prostor podsítě Service Fabric: 172.16.2.0/23

V prostředku **Microsoft.Network/networkSecurityGroups** jsou povolená následující pravidla pro příchozí provoz. Hodnoty portů můžete změnit změnou proměnných šablony.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunication – 1025, 1026, 1027
* Rozsah dočasných portů – 49152 až 65534 (potřebných je alespoň 256 portů)
* Porty pro použití aplikací: 80 a 443
* Rozsah portů aplikací – 49152 až 65534 (používají se pro komunikaci služeb, nejsou však otevírány v nástroji pro vyrovnávání zatížení)
* Všechny ostatní porty jsou blokované

Pokud jsou potřebné další porty aplikací, je potřeba upravit prostředky **Microsoft.Network/loadBalancers** a **Microsoft.Network/networkSecurityGroups**, aby byl povolený příchozí provoz.

### <a name="windows-defender"></a>Windows Defender
Ve výchozím nastavení [antivirové ochrany v programu Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) je nainstalovaná a funkční v systému Windows Server 2016. Uživatelské rozhraní je nainstalované ve výchozím nastavení na některých jednotkách SKU, ale není potřeba.  Pro každý uzel typu/škálovací sady virtuálních počítačů deklarované v šabloně [Azure VM Antimalwarové rozšíření](/azure/virtual-machines/extensions/iaas-antimalware-windows) se používá k vyloučení adresáře Service Fabric a procesy:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>Nastavení parametrů šablony

Soubor s parametry [azuredeploy.parameters.json][parameters] deklaruje mnoho hodnot používaných pro nasazení clusteru a přidružených prostředků. Některé parametry, které možná budete muset upravit pro své nasazení:

|Parametr|Příklad hodnoty|Poznámky|
|---|---||
|adminUserName|vmadmin| Uživatelské jméno správce pro cluster virtuálních počítačů.[Požadavky na uživatelské jméno pro virtuální počítač](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm) |
|adminPassword|Password#1234| Heslo správce pro virtuální počítače clusteru. [Požadavky na heslo pro virtuální počítač](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm)|
|clusterName|mysfcluster123| Název clusteru. Může obsahovat jenom písmena a číslice. Může mít délku 3 až 23 znaků.|
|location|southcentralus| Umístění clusteru. |
|certificateThumbprint|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který se dříve odeslal do trezoru klíčů, vyplňte hodnotu kryptografického otisku certifikátu SHA1. Například: 6190390162C988701DB5676EB81083EA608DCCF3</p>. |
|certificateUrlValue|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná. </p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte URL certifikátu. Například https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346.</p>|
|sourceVaultValue||<p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte hodnotu zdrojového trezoru. Například: /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Nasazení virtuální sítě a clusteru

Dále nastavte topologii sítě a nasaďte cluster Service Fabric. Šablona Resource Manageru [azuredeploy.json][template] vytvoří virtuální síť a také podsíť a skupinu zabezpečení sítě pro Service Fabric. Šablona také nasadí cluster s povoleným zabezpečením pomocí certifikátu.  Pro produkční clustery používejte certifikát clusteru od certifikační autority (CA). K zabezpečení testovacích clusterů můžete použít certifikát podepsaný svým držitelem.

Šablona v tomto článku se nasadí cluster, který používá kryptografický otisk certifikátu pro identifikaci certifikátu clusteru.  Žádné dva certifikáty můžou mít se stejným kryptografickým otiskem, což znesnadňuje správy certifikátů. Přepínání nasazeném clusteru pomocí kryptografické otisky certifikátů k běžnému názvu certifikátu pomocí certifikátu značně zjednodušuje správu.  Zjistěte, jak aktualizovat cluster používat běžné názvy certifikátů pro správu certifikátů, přečtěte si téma [clusteru změnit na společný název správy certifikátů](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-using-an-existing-certificate"></a>Vytvoření clusteru s použitím existujícího certifikátu

Následující skript pomocí rutiny [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) a šablony nasadí nový cluster do Azure. Rutina také vytvoří v Azure nový trezor klíčů a odešle váš certifikát.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # must match the clusterName parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Vytvoření clusteru s použitím nového certifikátu podepsaného svým držitelem

Následující skript pomocí rutiny [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) a šablony nasadí nový cluster do Azure. Rutina také vytvoří v Azure nový trezor klíčů, přidá do něj nový certifikát podepsaný svým držitelem a místně stáhne soubor certifikátu.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Připojení k zabezpečenému clusteru

Připojte se ke clusteru pomocí modulu PowerShellu pro Service Fabric nainstalovaného spolu se sadou Service Fabric SDK.  Nejprve nainstalujte certifikát do osobního úložiště (Moje) aktuálního uživatele na počítači.  Spusťte následující příkaz PowerShellu:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Nyní jste připraveni připojit se ke svému zabezpečenému clusteru.

Modul PowerShellu pro **Service Fabric** poskytuje řadu rutin pro správu clusterů Service Fabric, aplikací a služeb.  Pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) se připojte k zabezpečenému clusteru. Podrobnosti o kryptografickém otisku certifikátu SHA1 a koncovém bodu připojení se nacházejí ve výstupu z předchozího kroku.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Pomocí rutiny [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) zkontrolujte, že jste připojeni a cluster je v pořádku.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V dalších článcích v této sérii kurzů se používá cluster, který jste právě vytvořili. Pokud nechcete ihned pokračovat dalším článkem, můžete [cluster odstranit](service-fabric-cluster-delete.md), aby se vám neúčtovaly poplatky.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření virtuální sítě v Azure pomocí PowerShellu
> * Vytvoření trezoru klíčů a nahrání certifikátu
> * Vytvoření zabezpečeného clusteru Service Fabric v Azure pomocí PowerShellu
> * Zabezpečení clusteru pomocí certifikátu X.509
> * Připojení ke clusteru pomocí prostředí PowerShell
> * Odebrání clusteru

Dále přejděte k následujícímu kurzu a naučte se svůj cluster škálovat.
> [!div class="nextstepaction"]
> [Škálování clusteru](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
