---
title: Vytvoření clusteru se systémem Linux Service Fabric v Azure
description: Naučte se nasadit cluster Service Fabric s Linuxem do existující virtuální sítě Azure s použitím rozhraní příkazového řádku Azure.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: mvc
ms.openlocfilehash: c4b71328ce59284f8870407c9492d24afe9acd8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88586916"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Nasazení clusteru se systémem Linux Service Fabric do virtuální sítě Azure

V tomto článku se dozvíte, jak nasadit cluster se systémem Linux Service Fabric do [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md) pomocí rozhraní příkazového řádku Azure a šablony. Po dokončení budete mít v cloudu spuštěný cluster, do kterého budete moct nasazovat aplikace. Pokud chcete pomocí PowerShellu vytvořit cluster s Windows, přečtěte si článek [Vytvoření zabezpečeného clusteru s Windows v Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="prerequisites"></a>Požadavky

Než začnete:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* Nainstalujte si [rozhraní příkazového řádku Service Fabric](service-fabric-cli.md).
* Instalace rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli)
* Základní informace o klíčových konceptech clusterů najdete v tématu [Přehled clusterů Azure](service-fabric-azure-clusters-overview.md) .
* [Naplánujte a připravte](service-fabric-cluster-azure-deployment-preparation.md) nasazení produkčního clusteru.

Následující postupy vytvoří cluster se sedmi uzly Service Fabric. Pokud chcete vypočítat náklady vzniklé používáním clusteru Service Fabric v Azure, použijte [cenovou kalkulačku funkcí Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Stažení a prozkoumání šablony

Stáhněte si následující soubory šablon Resource Manageru:

Pro Ubuntu 16,04 LTS:

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.jsna][parameters]

Pro Ubuntu 18,04 LTS:

* [AzureDeploy.json][template2]
* [AzureDeploy.Parameters.jsna][parameters2]

V případě Ubuntu 18,04 LTS je rozdíl mezi těmito dvěma šablonami 
* atribut **vmImageSku** nastaven na hodnotu "18,04-LTS"
* **typeHandlerVersion** všech uzlů je nastavené na 1,1.
* Microsoft. ServiceFabric/Clusters – prostředek
   - **apiVersion** je nastavená na "2019-03-01" nebo vyšší
   - vlastnost **vmImage** je nastavena na hodnotu "Ubuntu18_04".

Tato šablona nasadí zabezpečený cluster sedmi virtuálních počítačů a tří typů uzlů do virtuální sítě.  Další ukázkové šablony najdete na [GitHubu](https://github.com/Azure-Samples/service-fabric-cluster-templates). Šablona [AzureDeploy.json][template] nasadí řadu prostředků včetně následujících.

### <a name="service-fabric-cluster"></a>Cluster Service Fabric

V prostředku **Microsoft.ServiceFabric/clusters** se nasazuje cluster s Linuxem s těmito charakteristikami:

* tři typy uzlů
* pět uzlů v typu primárního uzlu (konfigurovatelné v parametrech šablony), jeden uzel v každém z ostatních typů uzlů
* OS: (Ubuntu 16,04 LTS/Ubuntu 18,04 LTS) (konfigurovatelné v parametrech šablony)
* zabezpečení pomocí certifikátu (možnost konfigurace v parametrech šablony)
* [služba DNS](service-fabric-dnsservice.md) je povolena
* bronzová [úroveň odolnosti](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) (možnost konfigurace v parametrech šablony)
* stříbrná [úroveň spolehlivosti](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) (možnost konfigurace v parametrech šablony)
* koncový bod připojení klienta: 19000 (možnost konfigurace v parametrech šablony)
* koncový bod brány HTTP: 19080 (možnost konfigurace v parametrech šablony)

### <a name="azure-load-balancer"></a>Nástroj pro vyrovnávání zatížení Azure

V prostředku **Microsoft.Network/loadBalancers** dochází ke konfiguraci nástroje pro vyrovnávání zatížení a k nastavení sond a pravidel pro následující porty:

* koncový bod připojení klienta: 19000
* koncový bod brány HTTP: 19080
* port aplikací: 80
* port aplikací: 443

### <a name="virtual-network-and-subnet"></a>Virtuální síť a podsíť

Názvy virtuální sítě a podsítě jsou deklarované v parametrech šablony.  Adresní prostory virtuální sítě a podsítě se taky deklarují v parametrech šablony a konfigurují v prostředku **Microsoft.Network/virtualNetworks**:

* adresní prostory virtuální sítě: 10.0.0.0/16
* adresní prostor podsítě Service Fabric: 10.0.2.0/24

Pokud jsou potřebné další porty aplikací, je třeba upravit prostředek Microsoft.Network/loadBalancers, aby povoloval příchozí provoz.

## <a name="set-template-parameters"></a>Nastavení parametrů šablony

Soubor **AzureDeploy. Parameters** deklaruje mnoho hodnot, které se používají k nasazení clusteru a přidružených prostředků. Některé parametry, které možná budete muset upravit pro své nasazení:

|Parametr|Příklad hodnoty|Poznámky|
|---|---||
|adminUserName|vmadmin| Uživatelské jméno správce pro virtuální počítače clusteru. |
|adminPassword|Password#1234| Heslo správce pro virtuální počítače clusteru.|
|clusterName|mysfcluster123| Název clusteru. |
|location|southcentralus| Umístění clusteru. |
|certificateThumbprint|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který se dříve odeslal do trezoru klíčů, vyplňte hodnotu kryptografického otisku certifikátu SHA1. Příklad: „6190390162C988701DB5676EB81083EA608DCCF3“. </p>|
|certificateUrlValue|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte URL certifikátu. Například "https: \/ /mykeyvault.Vault.Azure.NET:443/Secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte hodnotu zdrojového trezoru. Například: /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Nasazení virtuální sítě a clusteru

Dále nastavte topologii sítě a nasaďte cluster Service Fabric. Šablona Resource Manageru **AzureDeploy.json** vytvoří virtuální síť a podsíť pro Service Fabric. Šablona také nasadí cluster s povoleným zabezpečením pomocí certifikátu.  Pro produkční clustery používejte certifikát clusteru od certifikační autority (CA). K zabezpečení testovacích clusterů můžete použít certifikát podepsaný svým držitelem.

Šablona v tomto článku nasadí cluster, který používá kryptografický otisk certifikátu k identifikaci certifikátu clusteru.  Žádné dva certifikáty nemohou mít stejný kryptografický otisk, což ztěžuje správu certifikátů. Přepnutím nasazeného clusteru z použití kryptografických otisků certifikátů na použití běžných názvů certifikátů je Správa certifikátů mnohem jednodušší.  Pokud se chcete dozvědět, jak cluster aktualizovat tak, aby používal běžné názvy certifikátů pro správu certifikátů, přečtěte si téma [Změna clusteru do společné správy názvů certifikátů](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-using-an-existing-certificate"></a>Vytvoření clusteru s použitím existujícího certifikátu

Následující skript pomocí příkazu [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) a šablony nasadí nový cluster zabezpečený pomocí existujícího certifikátu. Příkaz také vytvoří v Azure nový trezor klíčů a odešle váš certifikát.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Vytvoření clusteru s použitím nového certifikátu podepsaného svým držitelem

Následující skript pomocí příkazu [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) a šablony nasadí do Azure nový cluster. Příkaz také vytvoří nový trezor klíčů v Azure, přidá do trezoru klíčů nový certifikát podepsaný svým držitelem a stáhne soubor certifikátu místně.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json \
   --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password \
   --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Připojení k zabezpečenému clusteru

Připojte se ke clusteru pomocí příkazu rozhraní příkazového řádku Service Fabric `sfctl cluster select` a svého klíče.  Volbu **--no-verify** použijte pouze v případě certifikátu podepsaného svým držitelem.

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Pomocí příkazu `sfctl cluster health` zkontrolujte, že jste připojeni a cluster je v pořádku.

```console
sfctl cluster health
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nechcete ihned pokračovat dalším článkem, můžete [cluster odstranit](./service-fabric-tutorial-delete-cluster.md), aby se vám neúčtovaly poplatky.

## <a name="next-steps"></a>Další kroky

Naučte se [škálovat cluster](service-fabric-tutorial-scale-cluster.md).

Šablona v tomto článku nasadí cluster, který používá kryptografický otisk certifikátu k identifikaci certifikátu clusteru.  Žádné dva certifikáty nemohou mít stejný kryptografický otisk, což ztěžuje správu certifikátů. Přepnutím nasazeného clusteru z použití kryptografických otisků certifikátů na použití běžných názvů certifikátů je Správa certifikátů mnohem jednodušší.  Pokud se chcete dozvědět, jak cluster aktualizovat tak, aby používal běžné názvy certifikátů pro správu certifikátů, přečtěte si téma [Změna clusteru do společné správy názvů certifikátů](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
[template2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.json
[parameters2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.Parameters.json
