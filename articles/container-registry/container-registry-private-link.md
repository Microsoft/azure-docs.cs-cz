---
title: Nastavit privátní odkaz
description: Nastavte privátní koncový bod v registru kontejneru a povolte přístup přes privátní odkaz v místní virtuální síti. Přístup k privátním linkám je funkce úrovně Premium Service.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: da07d35ad944db8e9b8a7bac0602fff23cd222d8
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488741"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Připojení soukromě ke službě Azure Container Registry pomocí privátního odkazu Azure


Omezte přístup k registru přiřazením privátních IP adres virtuální sítě do koncových bodů registru a pomocí [privátního propojení Azure](../private-link/private-link-overview.md). Síťový provoz mezi klienty ve virtuální síti a soukromými koncovými body registru projde virtuální sítí a privátním odkazem v páteřní síti Microsoftu a odstraní tak expozici veřejného Internetu. Privátní odkaz taky umožňuje privátní přístup k registru z místního úložiště prostřednictvím privátního partnerského vztahu [Azure ExpressRoute](../expressroute/expressroute-introduction.MD) nebo [brány VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

[Nastavení DNS](../private-link/private-endpoint-overview.md#dns-configuration) pro privátní koncové body registru můžete nakonfigurovat tak, aby se nastavení přeložila na přidělenou privátní IP adresu registru. Díky konfiguraci DNS můžou klienti a služby v síti nadále přistupovat k registru v plně kvalifikovaném názvu domény registru, jako je *myregistry.azurecr.IO*. 

Tato funkce je k dispozici na úrovni služby Registry kontejneru **Premium** . V současné době je možné pro registr nastavit maximálně 10 privátních koncových bodů. Informace o úrovních a omezeních služby registru najdete v tématu [Azure Container Registry úrovně](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Požadavky

* Pokud chcete použít kroky Azure CLI v tomto článku, doporučujeme Azure CLI verze 2.6.0 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli]. Nebo spusťte v [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Pokud ještě nemáte registr kontejnerů, vytvořte ho (je potřeba Premium úrovně) a [naimportujte](container-registry-import-images.md) ukázkovou image, jako je například `hello-world` z dokovacího centra. K vytvoření registru použijte například [Azure Portal][quickstart-portal] nebo rozhraní příkazového [řádku Azure][quickstart-cli] .
* Pokud chcete nakonfigurovat přístup k registru pomocí privátního odkazu v jiném předplatném Azure, musíte zaregistrovat poskytovatele prostředků pro Azure Container Registry v tomto předplatném. Například:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Příklady rozhraní příkazového řádku Azure CLI v tomto článku používají následující proměnné prostředí. Nahraďte hodnoty vhodné pro vaše prostředí. Všechny příklady jsou formátovány pro prostředí bash:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Nastavení privátního propojení – CLI

### <a name="get-network-and-subnet-names"></a>Získání názvů sítí a podsítí

Pokud je ještě nemáte, budete k nastavení privátního odkazu potřebovat názvy virtuální sítě a podsítě. V tomto příkladu použijete stejnou podsíť pro virtuální počítač a privátní koncový bod registru. V mnoha scénářích ale byste koncový bod nastavili v samostatné podsíti. 

Když vytvoříte virtuální počítač, Azure ve výchozím nastavení vytvoří virtuální síť ve stejné skupině prostředků. Název virtuální sítě je založený na názvu virtuálního počítače. Například pokud pojmenujete virtuální počítač *myDockerVM*, výchozí název virtuální sítě je *myDockerVMVNET*s podsítí s názvem *myDockerVMSubnet*. Nastavte tyto hodnoty v proměnných prostředí spuštěním příkazu [AZ Network VNet list][az-network-vnet-list] :

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>Zakázání zásad sítě v podsíti

[Zakažte zásady sítě](../private-link/disable-private-endpoint-network-policy.md) , jako jsou třeba skupiny zabezpečení sítě v podsíti pro soukromý koncový bod. Aktualizujte konfiguraci podsítě pomocí [AZ Network VNet Subnet Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Konfigurace privátní zóny DNS

Vytvořte privátní zónu DNS pro privátní doménu služby Azure Container Registry. V pozdějších krocích vytvoříte záznamy DNS pro doménu registru v této zóně DNS.

Pokud chcete použít privátní zónu k přepsání výchozího překladu DNS pro službu Azure Container Registry, musí mít zóna název **privatelink.azurecr.IO**. Pro vytvoření privátní zóny spusťte následující příkaz [AZ Network Private-DNS Zone Create][az-network-private-dns-zone-create] :

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Vytvoří odkaz na přidružení.

Spusťte [AZ Network Private-DNS Link VNet Create][az-network-private-dns-link-vnet-create] a přidružte svoji privátní zónu k virtuální síti. Tento příklad vytvoří odkaz s názvem *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Vytvoření koncového bodu privátního registru

V této části vytvoříte privátní koncový bod registru ve virtuální síti. Nejprve Získejte ID prostředku svého registru:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Spuštěním příkazu [AZ Network Private-Endpoint Create][az-network-private-endpoint-create] vytvořte privátní koncový bod registru.

Následující příklad vytvoří koncový bod *myPrivateEndpoint* a připojení služby *mojepripojeni*. Chcete-li určit prostředek registru kontejneru pro koncový bod, předejte `--group-ids registry` :

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Získání privátních IP adres

Spuštění [AZ Network Private-Endpoint show][az-network-private-endpoint-show] pro dotazování koncového bodu pro ID síťového rozhraní:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Přidruženo k síťovému rozhraní v tomto příkladu jsou dvě privátní IP adresy pro registr kontejneru: jeden pro samotný registr a druhý pro datový koncový bod registru. Následující příkaz [AZ Resource show][az-resource-show] Commands získá privátní IP adresy pro registr kontejnerů a datový koncový bod registru:

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> Pokud je registr [geograficky replikovaný](container-registry-geo-replication.md), proveďte dotaz na další datový koncový bod pro každou repliku registru.

### <a name="create-dns-records-in-the-private-zone"></a>Vytvoření záznamů DNS v privátní zóně

Následující příkazy vytvoří záznamy DNS v privátní zóně pro koncový bod registru a jeho datový koncový bod. Pokud máte například registr s názvem *myregistry* v oblasti *westeurope* , názvy koncových bodů jsou `myregistry.azurecr.io` a `myregistry.westeurope.data.azurecr.io` . 

> [!NOTE]
> Pokud je registr [geograficky replikovaný](container-registry-geo-replication.md), vytvořte v dalších záznamy DNS pro IP adresu koncového bodu dat každé repliky.

Nejdřív spusťte [AZ Network Private-DNS Record-set a Create][az-network-private-dns-record-set-a-create] vytvoří prázdné sady záznamů pro koncový bod registru a koncový bod dat:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Spusťte [záznam AZ Network Private-DNS Record-set a příkaz Add-Record][az-network-private-dns-record-set-a-add-record] pro vytvoření záznamů pro koncový bod registru a koncový bod dat:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

Privátní odkaz je teď nakonfigurovaný a připravený k použití.

## <a name="set-up-private-link---portal"></a>Nastavení privátního propojení – portál

Nastavte privátní odkaz při vytváření registru nebo přidejte privátní odkaz do existujícího registru. V následujících krocích se předpokládá, že už máte virtuální síť a podsíť nastavené s virtuálním počítačem pro testování. Můžete také [vytvořit novou virtuální síť a podsíť](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint---new-registry"></a>Vytvoření privátního koncového bodu – nový registr

1. Při vytváření registru na portálu na kartě **základy** v části **SKU**vyberte **Premium**.
1. Vyberte kartu **síť** .
1. V **Možnosti připojení k síti**vyberte **privátní koncový bod**  >  **a přidat**.
1. Zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Předplatné | Vyberte předplatné. |
    | Skupina prostředků | Zadejte název existující skupiny nebo vytvořte novou.|
    | Name | Zadejte jedinečný název. |
    | Vytváření |Vybrat **registr**|
    | **Sítě** | |
    | Virtuální síť| Vyberte virtuální síť, ve které je nasazený virtuální počítač, například *myDockerVMVNET*. |
    | Podsíť | Vyberte podsíť, například *myDockerVMSubnet* , kde se virtuální počítač nasadí. |
    |**Integrace Privátní DNS**||
    |Integrace s privátní zónou DNS |Vyberte **Ano**. |
    |Zóna Privátní DNS |Vybrat *(nové) privatelink.azurecr.IO* |
    |||
1. Nakonfigurujte zbývající nastavení registru a potom vyberte **zkontrolovat + vytvořit**.

  ![Vytvoření registru s privátním koncovým bodem](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Vytvoření privátního koncového bodu – existující registr

1. Na portálu přejděte do registru kontejneru.
1. V části **Nastavení**vyberte **sítě**.
1. Na kartě **privátní koncové body** vyberte **+ soukromý koncový bod**.
1. Na kartě **základy** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte předplatné. |
    | Skupina prostředků | Zadejte název existující skupiny nebo vytvořte novou.|
    | **Podrobnosti instance** |  |
    | Name | Zadejte název. |
    |Region (Oblast)|Vyberte oblast.|
    |||
5. Vyberte **Další: prostředek**.
6. Zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | **V adresáři vyberte připojit k prostředku Azure**.|
    | Předplatné| Vyberte předplatné. |
    | Typ prostředku | Vyberte **Microsoft. ContainerRegistry/Registry**. |
    | Prostředek |Vyberte název registru.|
    |Cílový podprostředek |Vybrat **registr**|
    |||
7. Vyberte **Další: Konfigurace**.
8. Zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**Sítě**| |
    | Virtuální síť| Vyberte virtuální síť, ve které je nasazený virtuální počítač, například *myDockerVMVNET*. |
    | Podsíť | Vyberte podsíť, například *myDockerVMSubnet* , kde se virtuální počítač nasadí. |
    |**Integrace Privátní DNS**||
    |Integrace s privátní zónou DNS |Vyberte **Ano**. |
    |Zóna Privátní DNS |Vybrat *(nové) privatelink.azurecr.IO* |
    |||

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci. 
2. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**.

Po vytvoření privátního koncového bodu se nastavení DNS v privátní zóně zobrazí na stránce **privátní koncové body** na portálu:

1. Na portálu přejděte do registru kontejneru a vyberte **nastavení > sítě**.
1. Na kartě **privátní koncové body** vyberte privátní koncový bod, který jste vytvořili.
1. Na stránce **Přehled** zkontrolujte nastavení odkazu a vlastní nastavení DNS.

  ![Nastavení DNS koncového bodu](./media/container-registry-private-link/private-endpoint-overview.png)

Vaše privátní propojení je teď nakonfigurované a připravené k použití.

## <a name="disable-public-access"></a>Zakázat veřejný přístup

V mnoha scénářích zakažte přístup k registru z veřejných sítí. Tato konfigurace brání klientům mimo virtuální síť dosáhnout koncových bodů registru. 

### <a name="disable-public-access---cli"></a>Zakázat veřejný přístup – CLI

Pokud chcete zakázat veřejný přístup pomocí rozhraní příkazového řádku Azure, spusťte příkaz [AZ ACR Update][az-acr-update] a nastavte `--public-network-enabled` na `false` . 

> [!NOTE]
> Argument vyžaduje rozhraní příkazového `public-network-enabled` řádku Azure CLI 2.6.0 nebo novější. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Zakázat veřejný přístup – portál

1. Na portálu přejděte do registru kontejneru a vyberte **nastavení > sítě**.
1. Na kartě **veřejný přístup** vyberte v části **Povolení přístupu k veřejné síti**možnost **zakázáno**. Potom vyberte **Uložit**.

## <a name="validate-private-link-connection"></a>Ověřit připojení privátního propojení

Měli byste ověřit, že se prostředky v podsíti privátního koncového bodu připojují k vašemu registru přes privátní IP adresu a že mají správnou integraci privátních zón DNS.

Pokud chcete ověřit připojení privátního propojení s protokolem SSH k virtuálnímu počítači, který jste nastavili ve virtuální síti.

Spusťte `nslookup` příkaz pro překlad IP adresy vašeho registru prostřednictvím privátního odkazu:

```bash
nslookup $REGISTRY_NAME.azurecr.io
```

Příklad výstupu ukazuje IP adresu registru v adresním prostoru podsítě:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Porovnejte tento výsledek s veřejnou IP adresou ve `nslookup` výstupu pro stejný registr prostřednictvím veřejného koncového bodu:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Operace s registrem přes privátní propojení

Ověřte také, že můžete provádět operace registru z virtuálního počítače v podsíti. Vytvořte připojení SSH k virtuálnímu počítači a spuštěním [AZ ACR Login][az-acr-login] Přihlaste se do svého registru. V závislosti na konfiguraci virtuálního počítače možná budete muset pomocí příkazu napředt následující příkazy `sudo` .

```bash
az acr login --name $REGISTRY_NAME
```

Proveďte operace v registru, jako je například `docker pull` získání ukázkové image z registru. Nahraďte `hello-world:v1` obrázkem a značkou, která je vhodná pro váš registr, s předponou názvu přihlašovacího serveru registru (bez malých písmen):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker úspěšně načte image do virtuálního počítače.

## <a name="manage-private-endpoint-connections"></a>Správa připojení privátního koncového bodu

Pomocí Azure Portal spravujte připojení privátního koncového bodu registru nebo pomocí příkazů ve skupině příkazů [AZ ACR Private-Endpoint-Connection][az-acr-private-endpoint-connection] . Operace zahrnují schválení, odstranění, výpis, odmítnutí nebo zobrazení podrobností o připojeních privátních koncových bodů registru.

Pokud například chcete zobrazit seznam připojení privátního koncového bodu k registru, spusťte příkaz [AZ ACR Private-Endpoint-Connection list][az-acr-private-endpoint-connection-list] . Například:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Když nastavíte připojení privátního koncového bodu pomocí kroků v tomto článku, registr automaticky akceptuje připojení z klientů a služeb, které mají oprávnění RBAC v registru. Koncový bod můžete nastavit tak, aby vyžadoval ruční schválení připojení. Informace o tom, jak schvalovat a odmítat připojení privátních koncových bodů, najdete v tématu [Správa připojení privátního koncového bodu](../private-link/manage-private-endpoint.md).

## <a name="add-zone-records-for-replicas"></a>Přidání záznamů zóny pro repliky

Jak je uvedeno v tomto článku, když přidáte připojení privátního koncového bodu k registru, vytvoří se záznamy DNS v `privatelink.azurecr.io` zóně pro registr a koncové body dat v oblastech, kde se registr [replikuje](container-registry-geo-replication.md). 

Pokud později přidáte novou repliku, budete muset ručně přidat záznam nové zóny pro koncový bod dat v této oblasti. Například pokud vytvoříte repliku *myregistry* v umístění *northeurope* , přidejte záznam zóny pro `myregistry.northeurope.data.azurecr.io` . Postup najdete v tématu [Vytvoření záznamů DNS v privátní zóně](#create-dns-records-in-the-private-zone) v tomto článku.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili všechny prostředky Azure ve stejné skupině prostředků a už je nepotřebujete, můžete je případně odstranit pomocí jednoho příkazu [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name $RESOURCE_GROUP
```

Pokud chcete prostředky vyčistit na portálu, přejděte do skupiny prostředků. Po načtení skupiny prostředků klikněte na **Odstranit skupinu prostředků** a odeberte skupinu prostředků a prostředky, které jsou tam uložené.

## <a name="next-steps"></a>Další kroky

* Další informace o privátních odkazech najdete v dokumentaci k [privátním odkazům Azure](../private-link/private-link-overview.md) .
* Pokud potřebujete nastavit pravidla přístupu k registru z za bránou firewall klienta, přečtěte si téma [Konfigurace pravidel pro přístup ke službě Azure Container Registry za bránou firewall](container-registry-firewall-access-rules.md).

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
