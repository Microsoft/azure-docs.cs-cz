---
title: Vytvoření clusteru Service Fabric s Linuxem v Azure | Dokumentace Microsoftu
description: V tomto kurzu se naučíte nasadit cluster Service Fabric s Linuxem do existující virtuální sítě Azure s použitím Azure CLI.
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
ms.openlocfilehash: 63d5ae371b4852be96f764b91ec44f1befbd469b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="tutorial-deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Kurz: Nasazení clusteru Service Fabric s Linuxem do virtuální sítě Azure
Tento kurz je první částí série. Naučíte se nasadit cluster Service Fabric s Linuxem do [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md) a [skupiny zabezpečení sítě](../virtual-network/virtual-networks-nsg.md) s použitím rozhraní příkazového řádku Azure a šablony. Po dokončení budete mít v cloudu spuštěný cluster, do kterého budete moct nasazovat aplikace. Pokud chcete pomocí PowerShellu vytvořit cluster s Windows, přečtěte si článek [Vytvoření zabezpečeného clusteru s Windows v Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit virtuální síť v Azure s použitím rozhraní příkazového řádku Azure
> * Vytvořit zabezpečený cluster Service Fabric v Azure s použitím rozhraní příkazového řádku Azure
> * Zabezpečit cluster pomocí certifikátu X.509
> * Připojit se ke clusteru s použitím rozhraní příkazového řádku Service Fabric
> * Odebrat cluster

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného clusteru v Azure
> * [Horizontální snížení nebo navýšení kapacity clusteru](service-fabric-tutorial-scale-cluster.md)
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * [Nasazení API Managementu se Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Požadavky
Než začnete s tímto kurzem:
- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nainstalujte si [rozhraní příkazového řádku Service Fabric](service-fabric-cli.md).
- Nainstalujte si [Azure CLI 2.0](/cli/azure/install-azure-cli).

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

V tomto kurzu se nasadí cluster s pěti uzly jednoho typu. V případě každého nasazení produkčního clusteru je však důležitým krokem [plánování kapacity](service-fabric-cluster-capacity.md). Zde je uvedeno několik bodů, které je vhodné vzít v úvahu v rámci procesu.

- Počet uzlů a typy uzlů, které váš cluster potřebuje 
- Vlastnosti každého typu uzlu (například velikost, zda je primární, připojení k internetu a počet virtuálních počítačů)
- Spolehlivost a odolnost clusteru

## <a name="download-and-explore-the-template"></a>Stažení a prozkoumání šablony
Stáhněte si následující soubory šablon Resource Manageru:
- [vnet-linuxcluster.json][template]
- [vnet-linuxcluster.parameters.json][parameters]

Šablona [vnet-linuxcluster.json][template] nasadí řadu prostředků včetně následujících.

### <a name="service-fabric-cluster"></a>Cluster Service Fabric
Cluster s Linuxem je nasazen s následujícími charakteristikami:
- jeden typ uzlu 
- pět uzlů primárního typu (možnost konfigurace v parametrech šablony)
- operační systém: Ubuntu 16.04 LTS (možnost konfigurace v parametrech šablony)
- zabezpečení pomocí certifikátu (možnost konfigurace v parametrech šablony)
- [služba DNS](service-fabric-dnsservice.md) je povolena
- bronzová [úroveň odolnosti](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) (možnost konfigurace v parametrech šablony)
- stříbrná [úroveň spolehlivosti](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) (možnost konfigurace v parametrech šablony)
- koncový bod připojení klienta: 19000 (možnost konfigurace v parametrech šablony)
- koncový bod brány HTTP: 19080 (možnost konfigurace v parametrech šablony)

### <a name="azure-load-balancer"></a>Nástroj pro vyrovnávání zatížení Azure
Nasazení nástroje pro vyrovnávání zatížení a nastavení testů a pravidel se provádí pro následující porty:
- koncový bod připojení klienta: 19000
- koncový bod brány HTTP: 19080 
- port aplikací: 80
- port aplikací: 443

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuální síť, podsíť a skupina zabezpečení sítě
Názvy virtuální sítě, podsítě a skupiny zabezpečení sítě jsou deklarované v parametrech šablony.  Adresní prostory virtuální sítě a podsítě jsou také deklarované v parametrech šablony:
- adresní prostory virtuální sítě: 10.0.0.0/16
- adresní prostor podsítě Service Fabric: 10.0.2.0/24

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
|certificateThumbprint|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte hodnotu kryptografického otisku certifikátu. Příklad: „6190390162C988701DB5676EB81083EA608DCCF3“. </p>| 
|certificateUrlValue|| <p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte URL certifikátu. Například https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346.</p>|
|sourceVaultValue||<p>Pokud vytváříte certifikát podepsaný svým držitelem nebo poskytujete soubor certifikátu, měla by být hodnota prázdná.</p><p>Pokud chcete použít existující certifikát, který byl dříve odeslán do trezoru klíčů, vyplňte hodnotu zdrojového trezoru. Například: /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Nasazení virtuální sítě a clusteru
Dále nastavte topologii sítě a nasaďte cluster Service Fabric. Šablona Resource Manageru [vnet-linuxcluster.json][template] vytvoří virtuální síť a také podsíť a skupinu zabezpečení sítě pro Service Fabric. Šablona také nasadí cluster s povoleným zabezpečením pomocí certifikátu.  Pro produkční clustery používejte certifikát clusteru od certifikační autority (CA). K zabezpečení testovacích clusterů můžete použít certifikát podepsaný svým držitelem.

Následující skript pomocí příkazu [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) a šablony nasadí nový cluster zabezpečený pomocí existujícího certifikátu. Příkaz také vytvoří v Azure nový trezor klíčů a odešle váš certifikát.

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
   --template-file vnet-linuxcluster.json --parameter-file vnet-linuxcluster.parameters.json
```

## <a name="connect-to-the-secure-cluster"></a>Připojení k zabezpečenému clusteru
Připojte se ke clusteru pomocí příkazu rozhraní příkazového řádku Service Fabric `sfctl cluster select` a svého klíče.  Volbu **--no-verify** použijte pouze v případě certifikátu podepsaného svým držitelem.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Pomocí příkazu `sfctl cluster health` zkontrolujte, že jste připojeni a cluster je v pořádku.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
V dalších článcích v této sérii kurzů se používá cluster, který jste právě vytvořili. Pokud nechcete ihned pokračovat dalším článkem, můžete cluster odstranit, aby se vám neúčtovaly poplatky. Nejjednodušší způsob, jak odstranit cluster a všechny prostředky, které využívá, je odstranit příslušnou skupinu prostředků.

Přihlaste se k Azure a vyberte ID předplatného, pro které chcete cluster odebrat.  Své ID předplatného můžete zjistit po přihlášení k webu [Azure Portal](http://portal.azure.com). Pomocí příkazu [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete) odstraňte skupinu prostředků a všechny prostředky clusteru.

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit virtuální síť v Azure s použitím rozhraní příkazového řádku Azure
> * Vytvořit zabezpečený cluster Service Fabric v Azure s použitím rozhraní příkazového řádku Azure
> * Zabezpečit cluster pomocí certifikátu X.509
> * Připojit se ke clusteru s použitím rozhraní příkazového řádku Service Fabric
> * Odebrat cluster

Dále přejděte k následujícímu kurzu a naučte se svůj cluster škálovat.
> [!div class="nextstepaction"]
> [Škálování clusteru](service-fabric-tutorial-scale-cluster.md)


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.parameters.json
