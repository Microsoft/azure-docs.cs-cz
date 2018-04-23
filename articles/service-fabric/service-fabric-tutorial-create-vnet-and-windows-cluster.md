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
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: b85c3030984758a0c425fb526a32fcd8eff974c9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Kurz: Nasazení clusteru Service Fabric s Windows do virtuální sítě Azure
Tento kurz je první částí série. Naučíte se nasadit cluster Service Fabric s Windows do [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md) a [skupiny zabezpečení sítě](../virtual-network/virtual-networks-nsg.md) s použitím PowerShellu a šablony. Po dokončení budete mít v cloudu spuštěný cluster, do kterého budete moct nasazovat aplikace.  Pokud chcete vytvořit cluster s Linuxem pomocí Azure CLI, přečtěte si téma [Vytvoření zabezpečeného clusteru s Linuxem v Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Tento kurz popisuje produkční scénář.  Pokud chcete rychle vytvořit malý cluster pro účely testování, přečtěte si téma [Vytvoření testovacího clusteru se třemi uzly](./scripts/service-fabric-powershell-create-test-cluster.md).

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
> * [Nasazení API Managementu se Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Požadavky
Než začnete s tímto kurzem:
- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nainstalujte sadu [Service Fabric SDK a modul PowerShellu](service-fabric-get-started.md).
- Nainstalujte [modul Azure PowerShellu verze 4.1 nebo vyšší](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Provedením následujících postupů se vytvoří cluster Service Fabric s pěti uzly. Pokud chcete vypočítat náklady vzniklé používáním clusteru Service Fabric v Azure, použijte [cenovou kalkulačku funkcí Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Klíčové koncepty
[Cluster Service Fabric](service-fabric-deploy-anywhere.md) je síťově propojená sada virtuálních nebo fyzických počítačů, ve které se nasazují a spravují mikroslužby. Clustery je možné škálovat na tisíce počítačů. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Každému uzlu je přiřazen název uzlu (řetězec). Uzly mají určité charakteristiky, například vlastnosti umístění.

Typ uzlu definuje velikost, počet a vlastnosti pro sadu virtuálních počítačů v clusteru. Každý definovaný typ uzlu je nastavený jako [škálovací sada virtuálních počítačů](/azure/virtual-machine-scale-sets/), tedy výpočetní prostředek Azure, který se používá k nasazení a správě kolekce virtuálních počítačů jako sady. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. Typy uzlů se používají k definování rolí pro sadu uzlů clusteru, například „front-end“ nebo „back-end“.  Cluster může mít více než jeden typ uzlu, ale v případě produkčních clusterů musí existovat alespoň pět virtuálních počítačů primárního typu (nebo minimálně tři virtuální počítače v případě testovacích clusterů).  V uzlech primárního typu jsou umístěny [systémové služby Service Fabric](service-fabric-technical-overview.md#system-services).

Cluster je zabezpečený pomocí certifikátu clusteru. Certifikát clusteru je certifikát X.509, který se používá k zabezpečení komunikace mezi uzly a k ověřování koncových bodů správy clusteru v klientovi pro správu.  Certifikát clusteru také poskytuje zabezpečení SSL pro rozhraní API pro správu prostřednictvím protokolu HTTPS a pro Service Fabric Explorer používaný prostřednictvím protokolu HTTPS. Pro testovací clustery jsou užitečné certifikáty podepsané svým držitelem.  Pro produkční clustery používejte certifikát clusteru od certifikační autority (CA).

Certifikát clusteru musí:

- obsahovat privátní klíč,
- být vytvořen pro výměnu klíčů, tedy musí umožňovat export do souboru Personal Information Exchange (.pfx),
- mít název subjektu odpovídající doméně, kterou používáte pro přístup ke clusteru Service Fabric. Tato shoda se vyžaduje kvůli zajištění zabezpečení SSL pro koncové body správy prostřednictvím protokolu HTTPS a pro Service Fabric Explorer clusteru. Není možné získat certifikát SSL od certifikační autority (CA) pro doménu .cloudapp.azure.com. Pro svůj cluster musíte získat název vlastní domény. Pokud požádáte o certifikát od certifikační autority, musí název subjektu certifikátu odpovídat názvu vlastní domény, který používáte pro svůj cluster.

Ke správě certifikátů pro clustery Service Fabric v Azure se používá služba Azure Key Vault.  Když je cluster nasazený v Azure, poskytovatel prostředků Azure, který je zodpovědný za vytváření clusterů Service Fabric, si vyžádá certifikáty ze služby Key Vault a nainstaluje je do virtuálních počítačů v clusteru.

Tento kurz ukazuje cluster s pěti uzly jednoho typu. V případě každého nasazení produkčního clusteru je však důležitým krokem [plánování kapacity](service-fabric-cluster-capacity.md). Zde je uvedeno několik bodů, které je vhodné vzít v úvahu v rámci procesu.

- Počet uzlů a typy uzlů, které váš cluster potřebuje 
- Vlastnosti každého typu uzlu (například velikost, zda je primární, připojení k internetu a počet virtuálních počítačů)
- Spolehlivost a odolnost clusteru

## <a name="download-and-explore-the-template"></a>Stažení a prozkoumání šablony
Stáhněte si následující soubory šablon Resource Manageru:
- [vnet-cluster.json][template]
- [vnet-cluster.parameters.json][parameters]

Šablona [vnet-cluster.json][template] nasadí řadu prostředků včetně následujících. 

### <a name="service-fabric-cluster"></a>Cluster Service Fabric
Cluster s Windows je nasazen s následujícími charakteristikami:
- jeden typ uzlu 
- pět uzlů primárního typu (možnost konfigurace v parametrech šablony)
- operační systém: Windows Server 2016 Datacenter s kontejnery (možnost konfigurace v parametrech šablony)
- zabezpečení pomocí certifikátu (možnost konfigurace v parametrech šablony)
- [reverzní proxy server](service-fabric-reverseproxy.md) je povolen
- [služba DNS](service-fabric-dnsservice.md) je povolena
- bronzová [úroveň odolnosti](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) (možnost konfigurace v parametrech šablony)
- stříbrná [úroveň spolehlivosti](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) (možnost konfigurace v parametrech šablony)
- koncový bod připojení klienta: 19000 (možnost konfigurace v parametrech šablony)
- koncový bod brány HTTP: 19080 (možnost konfigurace v parametrech šablony)

### <a name="azure-load-balancer"></a>Nástroj pro vyrovnávání zatížení Azure
Nasazení nástroje pro vyrovnávání zatížení a nastavení sond a pravidel se provádí pro následující porty:
- koncový bod připojení klienta: 19000
- koncový bod brány HTTP: 19080 
- port aplikací: 80
- port aplikací: 443
- reverzní proxy server Service Fabric: 19081

Pokud jsou potřebné další porty aplikací, je třeba upravit prostředky Microsoft.Network/loadBalancers prostředků a Microsoft.Network/networkSecurityGroups, aby byl povolen příchozí provoz.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuální síť, podsíť a skupina zabezpečení sítě
Názvy virtuální sítě, podsítě a skupiny zabezpečení sítě jsou deklarované v parametrech šablony.  Adresní prostory virtuální sítě a podsítě jsou také deklarované v parametrech šablony:
- adresní prostor virtuální sítě: 172.16.0.0/20
- adresní prostor podsítě Service Fabric: 172.16.2.0/23

Následující pravidla pro příchozí provoz jsou ve skupině zabezpečení sítě povolená. Hodnoty portů můžete změnit změnou proměnných šablony.
- ClientConnectionEndpoint (TCP): 19000
- HttpGatewayEndpoint (HTTP/TCP): 19080
- SMB : 445
- Internodecommunication – 1025, 1026, 1027
- Rozsah dočasných portů – 49152 až 65534 (potřebných je alespoň 256 portů)
- Porty pro použití aplikací: 80 a 443
- Rozsah portů aplikací – 49152 až 65534 (používají se pro komunikaci služeb, nejsou však otevírány v nástroji pro vyrovnávání zatížení)
- Všechny ostatní porty jsou blokované

Pokud jsou potřebné další porty aplikací, je třeba upravit prostředky Microsoft.Network/loadBalancers prostředků a Microsoft.Network/networkSecurityGroups, aby byl povolen příchozí provoz.

## <a name="set-template-parameters"></a>Nastavení parametrů šablony
Soubor s parametry [vnet-cluster.parameters.json][parameters] deklaruje mnoho hodnot používaných pro nasazení clusteru a přidružených prostředků. Některé parametry, které možná budete muset upravit pro své nasazení:

|Parametr|Příklad hodnoty|Poznámky|
|---|---||
|adminUserName|vmadmin| Uživatelské jméno správce pro virtuální počítače clusteru. |
|adminPassword|Password#1234| Heslo správce pro virtuální počítače clusteru.|
|clusterName|mysfcluster123| Název clusteru. |
|location|southcentralus| Umístění clusteru. |
|certificateThumbprint|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte hodnotu kryptografického otisku certifikátu. Například: 6190390162C988701DB5676EB81083EA608DCCF3</p>. | 
|certificateUrlValue|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná. </p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte URL certifikátu. Například https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346.</p>|
|sourceVaultValue||<p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte hodnotu zdrojového trezoru. Například: /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Nasazení virtuální sítě a clusteru
Dále nastavte topologii sítě a nasaďte cluster Service Fabric. Šablona Resource Manageru [vnet-cluster.json][template] vytvoří virtuální síť a také podsíť a skupinu zabezpečení sítě pro Service Fabric. Šablona také nasadí cluster s povoleným zabezpečením pomocí certifikátu.  Pro produkční clustery používejte certifikát clusteru od certifikační autority (CA). K zabezpečení testovacích clusterů můžete použít certifikát podepsaný svým držitelem.

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
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-cluster.json" `
-ParameterFile "$templatepath\vnet-cluster.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateFile $certpath
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
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-cluster.json" `
-ParameterFile "$templatepath\vnet-cluster.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateSubjectName $subname

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

Modul PowerShellu pro **Service Fabric** poskytuje řadu rutin pro správu clusterů Service Fabric, aplikací a služeb.  Pomocí rutiny [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) se připojte k zabezpečenému clusteru. Podrobnosti o kryptografickém otisku certifikátu a koncovém bodu připojení se nacházejí ve výstupu z předchozího kroku.

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
V dalších článcích v této sérii kurzů se používá cluster, který jste vytvořili. Pokud nechcete ihned pokračovat dalším článkem, můžete cluster a trezor klíčů odstranit, aby se vám neúčtovaly poplatky. Nejjednodušší způsob, jak odstranit cluster a všechny prostředky, které využívá, je odstranit příslušnou skupinu prostředků.

Pomocí rutiny [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) odstraňte skupinu prostředků a všechny prostředky clusteru.  Odstraňte také skupinu prostředků obsahující trezor klíčů.

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>Další kroky
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


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.parameters.json
