---
title: Nastavení privátního koncového bodu pomocí privátního odkazu
description: Nastavte privátní koncový bod v registru kontejneru a povolte přístup přes privátní odkaz v místní virtuální síti. Přístup k privátním linkám je funkce úrovně Premium Service.
ms.topic: article
ms.date: 03/31/2021
ms.openlocfilehash: d3c7c573b0ffc08a85f5cbe5cc62d3f7c052f0af
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781428"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Připojení soukromě ke službě Azure Container Registry pomocí privátního odkazu Azure


Omezte přístup k registru přiřazením privátních IP adres virtuální sítě do koncových bodů registru a pomocí [privátního propojení Azure](../private-link/private-link-overview.md). Síťový provoz mezi klienty ve virtuální síti a soukromými koncovými body registru projde virtuální sítí a privátním odkazem v páteřní síti Microsoftu a odstraní tak expozici veřejného Internetu. Privátní odkaz taky umožňuje privátní přístup k registru z místního úložiště prostřednictvím privátního partnerského vztahu [Azure ExpressRoute](../expressroute/expressroute-introduction.MD) nebo [brány VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

[Nastavení DNS](../private-link/private-endpoint-overview.md#dns-configuration) pro privátní koncové body registru můžete nakonfigurovat tak, aby se nastavení přeložila na přidělenou privátní IP adresu registru. Díky konfiguraci DNS můžou klienti a služby v síti nadále přistupovat k registru v plně kvalifikovaném názvu domény registru, jako je *myregistry.azurecr.IO*. 

Tato funkce je k dispozici na úrovni služby Registry kontejneru **Premium** . V současné době je možné pro registr nastavit maximálně 10 privátních koncových bodů. Informace o úrovních a omezeních služby registru najdete v tématu [Azure Container Registry úrovně](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Požadavky

* Pokud chcete použít kroky Azure CLI v tomto článku, doporučujeme Azure CLI verze 2.6.0 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli]. Nebo spusťte v [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Pokud ještě nemáte registr kontejnerů, vytvořte ho (je potřeba Premium úrovně) a [importujte](container-registry-import-images.md) ukázkovou veřejnou image, jako je například `mcr.microsoft.com/hello-world` Microsoft Container Registry. K vytvoření registru použijte například [Azure Portal][quickstart-portal] nebo rozhraní příkazového [řádku Azure][quickstart-cli] .
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

Když vytvoříte virtuální počítač, Azure ve výchozím nastavení vytvoří virtuální síť ve stejné skupině prostředků. Název virtuální sítě je založený na názvu virtuálního počítače. Například pokud pojmenujete virtuální počítač *myDockerVM*, výchozí název virtuální sítě je *myDockerVMVNET* s podsítí s názvem *myDockerVMSubnet*. Nastavte tyto hodnoty v proměnných prostředí spuštěním příkazu [AZ Network VNet list][az-network-vnet-list] :

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

Vytvořte [privátní zónu Azure DNS](../dns/private-dns-privatednszone.md) pro privátní doménu služby Azure Container Registry. V pozdějších krocích vytvoříte záznamy DNS pro doménu registru v této zóně DNS. Další informace najdete v části [Možnosti konfigurace DNS](#dns-configuration-options)dále v tomto článku.

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

### <a name="get-endpoint-ip-configuration"></a>Získat konfiguraci IP adresy koncového bodu

Pokud chcete nakonfigurovat záznamy DNS, Získejte konfiguraci protokolu IP privátního koncového bodu. Přidružená k síťovému rozhraní privátního koncového bodu v tomto příkladu jsou dvě privátní IP adresy pro registr kontejneru: jeden pro samotný registr a druhý pro datový koncový bod registru. 

Nejdřív spusťte [AZ Network Private-Endpoint show][az-network-private-endpoint-show] pro dotazování privátního koncového bodu pro ID síťového rozhraní:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Následující [AZ Network nic show][az-network-nic-show] Commands získá privátní IP adresy pro registr kontejnerů a datový koncový bod registru:

```azurecli
REGISTRY_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateIpAddress" \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateIpAddress" \
  --output tsv)

# An FQDN is associated with each IP address in the IP configurations

REGISTRY_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateLinkConnectionProperties.fqdns" \
  --output tsv)

DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateLinkConnectionProperties.fqdns" \
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
  --ipv4-address $REGISTRY_PRIVATE_IP

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

1. Při vytváření registru na portálu na kartě **základy** v části **SKU** vyberte **Premium**.
1. Vyberte kartu **Sítě**.
1. V **Možnosti připojení k síti** vyberte **privátní koncový bod**  >  **a přidat**.
1. Zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Zadejte název existující skupiny nebo vytvořte novou.|
    | Name | Zadejte jedinečný název. |
    | Vytváření |Vybrat **registr**|
    | **Sítě** | |
    | Virtuální síť| Vyberte virtuální síť, ve které je nasazený virtuální počítač, například *myDockerVMVNET*. |
    | Podsíť | Vyberte podsíť, například *myDockerVMSubnet* , kde se virtuální počítač nasadí. |
    |**Integrace Privátní DNS**||
    |Integrovat s privátní zónou DNS |Vyberte **Ano**. |
    |Zóna Privátního DNS |Vybrat *(nové) privatelink.azurecr.IO* |
    |||
1. Nakonfigurujte zbývající nastavení registru a potom vyberte **zkontrolovat + vytvořit**.

  ![Vytvoření registru s privátním koncovým bodem](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Vytvoření privátního koncového bodu – existující registr

1. Na portálu přejděte do registru kontejneru.
1. V části **Nastavení** vyberte **sítě**.
1. Na kartě **privátní koncové body** vyberte **+ soukromý koncový bod**.
1. Na kartě **základy** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Zadejte název existující skupiny nebo vytvořte novou.|
    | **Podrobnosti o instancích** |  |
    | Name | Zadejte název. |
    |Oblast|Vyberte oblast.|
    |||
5. Vyberte **Další: prostředek**.
6. Zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | **V adresáři vyberte připojit k prostředku Azure**.|
    | Předplatné| Vyberte své předplatné. |
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
    |Integrovat s privátní zónou DNS |Vyberte **Ano**. |
    |Zóna Privátního DNS |Vybrat *(nové) privatelink.azurecr.IO* |
    |||

1. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci. 
2. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**.

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
1. Na kartě **veřejný přístup** vyberte v části **Povolení přístupu k veřejné síti** možnost **zakázáno**. Pak vyberte **Uložit**.

## <a name="validate-private-link-connection"></a>Ověřit připojení privátního propojení

Měli byste ověřit, že se prostředky v podsíti privátního koncového bodu připojují k vašemu registru přes privátní IP adresu a že mají správnou integraci privátních zón DNS.

Pokud chcete ověřit připojení privátního propojení s protokolem SSH k virtuálnímu počítači, který jste nastavili ve virtuální síti.

Spusťte nástroj, například `nslookup` nebo `dig` , abyste mohli vyhledat IP adresu vašeho registru prostřednictvím privátního propojení. Například:

```bash
dig $REGISTRY_NAME.azurecr.io
```

Příklad výstupu ukazuje IP adresu registru v adresním prostoru podsítě:

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

Porovnejte tento výsledek s veřejnou IP adresou ve `dig` výstupu pro stejný registr prostřednictvím veřejného koncového bodu:

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
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

Když nastavíte připojení privátního koncového bodu pomocí kroků v tomto článku, registr automaticky akceptuje připojení z klientů a služeb, které mají oprávnění Azure RBAC v registru. Koncový bod můžete nastavit tak, aby vyžadoval ruční schválení připojení. Informace o tom, jak schvalovat a odmítat připojení privátních koncových bodů, najdete v tématu [Správa připojení privátního koncového bodu](../private-link/manage-private-endpoint.md).

> [!IMPORTANT]
> Pokud v současné době odstraníte privátní koncový bod z registru, může být také nutné odstranit odkaz virtuální sítě na soukromou zónu. Pokud se propojení neodstraní, může se zobrazit chyba podobná této `unresolvable host` .

## <a name="dns-configuration-options"></a>Možnosti konfigurace DNS

Privátní koncový bod v tomto příkladu se integruje s privátní zónou DNS přidruženou k základní virtuální síti. Tento instalační program používá přímo Azure poskytovanou službu DNS k překladu veřejného plně kvalifikovaného názvu domény registru na jeho privátní IP adresy ve virtuální síti. 

Privátní propojení podporuje další scénáře konfigurace služby DNS, které používají privátní zónu, včetně vlastních řešení DNS. Můžete mít například vlastní řešení DNS nasazené ve virtuální síti nebo v místním prostředí v síti, ke které se připojujete pomocí brány VPN nebo Azure ExpressRoute. 

Pokud chcete v těchto scénářích přeložit veřejný plně kvalifikovaný název domény registru na privátní IP adresu, musíte nakonfigurovat server pro přeposílání na úrovni serveru na službu Azure DNS (168.63.129.16). Přesné možnosti konfigurace a postup závisí na vašich stávajících sítích a DNS. Příklady najdete v tématu [Konfigurace DNS privátního koncového bodu Azure](../private-link/private-endpoint-dns.md).

> [!IMPORTANT]
> Pokud máte vysokou dostupnost, kterou jste vytvořili privátní koncové body v několika oblastech, doporučujeme použít v každé oblasti samostatnou skupinu prostředků a umístit virtuální síť a přidruženou privátní zónu DNS. Tato konfigurace také zabraňuje nepředvídatelnému rozlišení DNS způsobenému sdílením stejné privátní zóny DNS.

### <a name="manually-configure-dns-records"></a>Ruční konfigurace záznamů DNS

V některých případech může být potřeba ručně nakonfigurovat záznamy DNS v privátní zóně místo používání privátní zóny poskytované Azure. Nezapomeňte vytvořit záznamy pro každý z následujících koncových bodů: koncový bod registru, koncový bod dat registru a koncový bod dat pro všechny další místní repliky. Pokud nejsou všechny záznamy nakonfigurované, může být registr nedosažitelný.

> [!IMPORTANT]
> Pokud později přidáte novou repliku, budete muset ručně přidat nový záznam DNS pro koncový bod dat v této oblasti. Například pokud vytvoříte repliku *myregistry* v umístění northeurope, přidejte záznam pro `myregistry.northeurope.data.azurecr.io` .

Plně kvalifikované názvy domény a privátní IP adresy, které potřebujete k vytvoření záznamů DNS, jsou přidružené k síťovému rozhraní privátního koncového bodu. Tyto informace můžete získat pomocí Azure CLI nebo z portálu:

* Pomocí Azure CLI spusťte příkaz [AZ Network nic show][az-network-nic-show] . Příklady příkazů najdete v části [získání konfigurace IP adresy koncového bodu](#get-endpoint-ip-configuration)výše v tomto článku.

* Na portálu přejděte do svého privátního koncového bodu a vyberte **Konfigurace DNS**.

Po vytvoření záznamů DNS zajistěte, aby plně kvalifikované názvy domén registru byly správně přeloženy na odpovídající privátní IP adresy.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili všechny prostředky Azure ve stejné skupině prostředků a už je nepotřebujete, můžete je případně odstranit pomocí jednoho příkazu [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name $RESOURCE_GROUP
```

Pokud chcete prostředky vyčistit na portálu, přejděte do skupiny prostředků. Po načtení skupiny prostředků klikněte na **Odstranit skupinu prostředků** a odeberte skupinu prostředků a prostředky, které jsou tam uložené.

## <a name="next-steps"></a>Další kroky

* Další informace o privátních odkazech najdete v dokumentaci k [privátním odkazům Azure](../private-link/private-link-overview.md) .

* Pokud potřebujete nastavit pravidla přístupu k registru z za bránou firewall klienta, přečtěte si téma [Konfigurace pravidel pro přístup ke službě Azure Container Registry za bránou firewall](container-registry-firewall-access-rules.md).

* [Řešení potíží s připojením k privátnímu koncovému bodu Azure](../private-link/troubleshoot-private-endpoint-connectivity.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az_acr_private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az_acr_private_endpoint_connection_approve
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az_network_private_endpoint_create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az_network_private_endpoint_show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az_network_private_dns_zone_create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az_network_private_dns_link_vnet_create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_add_record
[az-network-nic-show]: /cli/azure/network/nic#az_network_nic_show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
