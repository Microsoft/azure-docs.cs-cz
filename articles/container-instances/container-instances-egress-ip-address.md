---
title: Konfigurace statické odchozí IP adresy
description: Konfigurace brány Azure firewall a uživatelem definovaných tras pro Azure Container Instances úlohy, které používají veřejnou IP adresu brány firewall pro příchozí a odchozí přenosy
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 497645b9fe7f908cc9b8b4d7ed0ba5e201570160
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566564"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Konfigurace jedné veřejné IP adresy pro odchozí a příchozí provoz do skupiny kontejnerů

Nastavení [skupiny kontejnerů](container-instances-container-groups.md) s externí IP adresou umožňuje externím klientům používat IP adresu pro přístup ke kontejneru ve skupině. Prohlížeč může například získat přístup k webové aplikaci běžící v kontejneru. V současné době ale skupina kontejnerů používá pro odchozí přenosy jinou IP adresu. Tato výstupní IP adresa není vystavená programově, což usnadňuje monitorování a konfiguraci pravidel brány firewall klienta.

Tento článek popisuje postup konfigurace skupiny kontejnerů ve [virtuální síti](container-instances-virtual-network-concepts.md) integrované s [Azure firewall](../firewall/overview.md). Nastavením trasy definované uživatelem na skupinu kontejnerů a pravidel brány firewall můžete směrovat a identifikovat provoz do a ze skupiny kontejnerů. Příchozí a odchozí skupina kontejnerů používá veřejnou IP adresu brány firewall. Jednu výstupní IP adresu může používat víc skupin kontejnerů nasazených v podsíti virtuální sítě, která je delegovaná na Azure Container Instances.

V tomto článku vytvoříte pomocí rozhraní příkazového řádku Azure CLI prostředky pro tento scénář:

* Skupiny kontejnerů nasazené v delegované podsíti [ve virtuální síti](container-instances-vnet.md) 
* Brána firewall Azure nasazená v síti se statickou veřejnou IP adresou
* Trasa definovaná uživatelem v podsíti skupin kontejnerů
* Pravidlo překladu adres (NAT) pro příchozí bránu firewall a pravidlo pro aplikace pro odchozí přenosy

Pak ověřte příchozí a odchozí přenosy z ukázkových skupin kontejnerů přes bránu firewall.

## <a name="deploy-aci-in-a-virtual-network"></a>Nasazení ACI ve virtuální síti

V typickém případě je možné, že už máte virtuální síť Azure, do které chcete nasadit skupinu kontejnerů. Pro demonstrační účely následující příkazy při vytváření skupiny kontejnerů vytvoří virtuální síť a podsíť. Podsíť je delegovaná na Azure Container Instances. 

Skupina kontejnerů spustí z image malou webovou aplikaci `aci-helloworld` . Jak je vidět v dalších článcích v dokumentaci, tento obrázek zabalí malou webovou aplikaci napsanou v Node.js, která slouží jako statická stránka HTML.

Pokud ho potřebujete, nejdřív vytvořte skupinu prostředků Azure pomocí příkazu [AZ Group Create][az-group-create] . Například:

```azurecli
az group create --name myResourceGroup --location eastus
```

Pro zjednodušení následujících příkladů příkazů použijte proměnnou prostředí pro název skupiny prostředků:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Vytvořte skupinu kontejnerů pomocí příkazu [AZ Container Create][az-container-create] :

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> Upravte hodnotu `--subnet address-prefix` pro adresní prostor IP adres, který potřebujete ve vaší podsíti. Nejnižší podporovaná podsíť je/29, která poskytuje osm IP adres. Některé IP adresy jsou rezervované pro použití v Azure.

Pro použití v pozdějším kroku získáte privátní IP adresu skupiny kontejnerů spuštěním příkazu [az Container show] [az-Container-show] Command:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Nasazení Azure Firewall v síti

V následujících částech použijte Azure CLI k nasazení brány firewall Azure ve virtuální síti. Základní informace najdete v tématu [kurz: nasazení a konfigurace Azure firewall pomocí Azure Portal](../firewall/deploy-cli.md).

Nejprve pomocí funkce [AZ Network VNet Subnet Create][az-network-vnet-subnet-create] přidejte podsíť s názvem AzureFirewallSubnet pro bránu firewall. AzureFirewallSubnet je *požadovaný* název této podsítě.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Pomocí následujících [příkazů rozhraní příkazového řádku Azure](../firewall/deploy-cli.md) vytvořte v podsíti bránu firewall.

Pokud ještě není nainstalovaná, přidejte do Azure CLI rozšíření brány firewall pomocí příkazu [AZ Extension Add][az-extension-add] :

```azurecli
az extension add --name azure-firewall
```

Vytvoření prostředků brány firewall:

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

Aktualizujte konfiguraci brány firewall pomocí příkazu [AZ Network firewall Update][az-network-firewall-update] :

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

Privátní IP adresu brány firewall získáte pomocí příkazu [AZ Network firewall IP-config list][az-network-firewall-ip-config-list] . Tato privátní IP adresa se používá v pozdějším příkazu.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
Veřejnou IP adresu brány firewall získáte pomocí příkazu [AZ Network Public-IP show][az-network-public-ip-show] . Tato veřejná IP adresa se používá v pozdějším příkazu.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>Definování trasy definované uživatelem v ACI podsíti

Pokud chcete přesměrovat provoz do brány firewall Azure, definujte v podsíti ACI cestu definovanou pro použití. Další informace najdete v tématu [směrování síťového provozu](../virtual-network/tutorial-create-route-table-cli.md). 

### <a name="create-route-table"></a>Vytvoření směrovací tabulky

Nejdřív spusťte následující příkaz [AZ Network Route-Table Create][az-network-route-table-create] a vytvořte tabulku směrování. Vytvořte směrovací tabulku ve stejné oblasti jako virtuální síť.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Vytvořit trasu

Spuštěním [AZ Network-Route-Table Route Create][az-network-route-table-route-create] vytvořte trasu v tabulce směrování. Pokud chcete směrovat provoz do brány firewall, nastavte typ dalšího segmentu směrování na `VirtualAppliance` a předejte privátní IP adresu brány firewall jako adresu dalšího segmentu směrování.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Přidružit tabulku směrování k ACI podsíti

Spuštěním příkazu [AZ Network VNet Subnet Update][az-network-vnet-subnet-update] proveďte přidružení směrovací tabulky k podsíti delegované do Azure Container Instances.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Konfigurace pravidel pro bránu firewall

Ve výchozím nastavení Azure Firewall odepírá (blokuje) příchozí a odchozí provoz. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>Konfigurace pravidla NAT v bráně firewall na ACI podsíť

Vytvořte [pravidlo překladu adres (NAT)](../firewall/rule-processing.md) brány firewall pro překlad a filtrování příchozího internetového provozu do kontejneru aplikace, který jste spustili dříve v síti. Podrobnosti najdete v tématu věnovaném [filtrování příchozího internetového provozu pomocí Azure firewall DNAT](../firewall/tutorial-firewall-dnat.md)

Vytvořte pravidlo a kolekci překladu adres (NAT) pomocí příkazu [AZ Network Firewall NAT-Rule Create][az-network-firewall-nat-rule-create] :

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

Přidejte pravidla překladu adres (NAT) podle potřeby pro filtrování provozu na jiné IP adresy v podsíti. Například jiné skupiny kontejnerů v podsíti můžou vystavovat IP adresy pro příchozí provoz nebo jiné interní IP adresy, které se dají skupině kontejnerů přiřadit až po restartu.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Vytvořit pravidlo odchozí aplikace v bráně firewall

Spusťte následující příkaz [AZ Network firewall Application-Rule Create][az-network-firewall-application-rule-create] a vytvořte v bráně firewall odchozí pravidlo. Toto ukázkové pravidlo umožňuje přístup z podsítě delegované k Azure Container Instances plně kvalifikovanému názvu domény `checkip.dyndns.org` . Přístup HTTP k webu se používá v pozdějším kroku k potvrzení odchozí IP adresy z Azure Container Instances.

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>Test přístupu ke skupině kontejnerů přes bránu firewall

V následujících oddílech se ověří, jestli je podsíť delegovaná k Azure Container Instances správně nakonfigurovaná za bránou firewall Azure. Předchozí kroky směrovaly příchozí provoz do podsítě a odchozí provoz z podsítě přes bránu firewall.

### <a name="test-ingress-to-a-container-group"></a>Test příchozího přenosu na skupinu kontejnerů

Otestujte příchozí přístup k *kontejneru AppContainer* běžícímu ve virtuální síti tak, že přejdete na veřejnou IP adresu brány firewall. Dříve jste uložili veřejnou IP adresu do proměnné $FW _PUBLIC_IP:

```bash
echo $FW_PUBLIC_IP
```

Výstup se podobá tomuto:

```console
52.142.18.133
```

Pokud je pravidlo překladu adres (NAT) brány firewall správně nakonfigurované, zobrazí se při zadání veřejné IP adresy brány firewall v prohlížeči tyto informace:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="Přejít na veřejnou IP adresu brány firewall":::

### <a name="test-egress-from-a-container-group"></a>Výstup testu ze skupiny kontejnerů


Do virtuální sítě nasaďte následující vzorový kontejner. Při spuštění pošle jeden požadavek HTTP na `http://checkip.dyndns.org` , který zobrazuje IP adresu odesílatele (výstupní IP adresa). Pokud je pravidlo aplikace v bráně firewall správně nakonfigurováno, je vrácena veřejná IP adresa brány firewall.

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Zobrazte protokoly kontejneru a potvrďte, že IP adresa je shodná s veřejnou IP adresou brány firewall.

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

Výstup se podobá tomuto:

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>Další kroky

V tomto článku nastavíte skupiny kontejnerů ve virtuální síti za bránou firewall Azure. Nakonfigurovali jste uživatelem definovaná směrování a pravidla překladu adres (NAT) a aplikace v bráně firewall. Pomocí této konfigurace můžete nastavit jednu statickou IP adresu pro příchozí přenos dat a výstup z Azure Container Instances.

Další informace o správě provozu a ochraně prostředků Azure najdete v dokumentaci k [Azure firewall](../firewall/index.yml) .



[az-group-create]: /cli/azure/group#az-group-create
[az-container-create]: /cli/azure/container#az-container-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az-network-public-ip-show
[az-network-route-table-create]:/cli/azure/network/route-table/#az-network-route-table-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-update
[az-container-exec]: /cli/azure/container#az-container-exec
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-open-port]: /cli/azure/vm#az-vm-open-port
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create






