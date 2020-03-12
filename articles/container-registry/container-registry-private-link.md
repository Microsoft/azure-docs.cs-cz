---
title: Nastavit privátní odkaz
description: Nastavení privátního koncového bodu v registru kontejnerů a povolení privátního odkazu v místní virtuální síti
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: b7dcf2d1eb1a77ea8b9660318ed2a7d4ec183b42
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128394"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Konfigurace privátního odkazu Azure pro službu Azure Container Registry 

Pro službu Azure Container Registry můžete nastavit [privátní koncový bod](../private-link/private-endpoint-overview.md) tak, aby klienti ve službě Azure Virtual Network měli zabezpečený přístup k registru prostřednictvím [privátního odkazu](../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro váš registr. Síťový provoz mezi klienty ve virtuální síti a v registru projde virtuální sítí a privátním odkazem v páteřní síti Microsoftu a odstraní tak expozici veřejného Internetu.

Pro soukromý koncový bod můžete [nakonfigurovat nastavení DNS](../private-link/private-endpoint-overview.md#dns-configuration) tak, aby se nastavení přeložila na přidělenou privátní IP adresu v registru. Díky konfiguraci DNS můžou klienti a služby v síti nadále přistupovat k registru v plně kvalifikovaném názvu domény registru, jako je *myregistry.azurecr.IO*.

Tato funkce je k dispozici na úrovni služby Registry kontejneru **Premium** . Informace o úrovních a omezeních služby registru najdete v tématu [Azure Container Registry SKU](container-registry-skus.md).

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a platí některá [omezení](#preview-limitations) . Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="preview-limitations"></a>Omezení verze Preview

* V současné době nemůžete nastavit privátní odkaz s privátním koncovým bodem v [geograficky replikovaném registru](container-registry-geo-replication.md). 

## <a name="prerequisites"></a>Předpoklady

* Pokud chcete použít kroky Azure CLI v tomto článku, doporučujeme Azure CLI verze 2.2.0 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli]. Nebo spusťte v [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Pokud ještě nemáte registr kontejnerů, vytvořte ho (je potřeba Premium úrovně) a nahrajte do něj ukázkovou image, například `hello-world` z Docker Hub. K vytvoření registru použijte například [Azure Portal][quickstart-portal] nebo rozhraní příkazového [řádku Azure][quickstart-cli] . 

Příklady rozhraní příkazového řádku Azure CLI v tomto článku používají následující proměnné prostředí. Nahraďte hodnoty vhodné pro vaše prostředí. Všechny příklady jsou formátovány pro prostředí bash:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Vytvoření virtuálního počítače s podporou Docker

Pro účely testování použijte k přístupu ke službě Azure Container Registry virtuální počítač Ubuntu s podporou Docker. Pokud chcete v registru použít Azure Active Directory ověřování, nainstalujte také rozhraní příkazového [řádku Azure CLI][azure-cli] na virtuálním počítači. Pokud už máte virtuální počítač Azure, přeskočte tento krok vytvoření.

Můžete použít stejnou skupinu prostředků pro váš virtuální počítač a registr kontejneru. Tato instalace zjednodušuje vyčištění na konci, ale nevyžaduje se. Pokud se rozhodnete vytvořit samostatnou skupinu prostředků pro virtuální počítač a virtuální síť, spusťte příkaz [AZ Group Create][az-group-create]:

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Teď nasaďte výchozí Ubuntu virtuální počítač Azure pomocí [AZ VM Create][az-vm-create]. Následující příklad vytvoří virtuální počítač s názvem *myDockerVM*.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Když se příkaz dokončí, poznamenejte si `publicIpAddress` zobrazované rozhraním Azure CLI. Tato adresa slouží k vytvoření připojení SSH k virtuálnímu počítači.

### <a name="install-docker-on-the-vm"></a>Instalace Docker na virtuálním počítači

Po spuštění virtuálního počítače vytvořte připojení SSH k virtuálnímu počítači. Nahraďte *publicIpAddress* veřejnou IP adresou vašeho virtuálního počítače.

```bash
ssh azureuser@publicIpAddress
```

Spusťte následující příkazy pro instalaci Docker na virtuálním počítači s Ubuntu:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Po instalaci spusťte následující příkaz, který ověří, jestli je na virtuálním počítači správně spuštěný Docker:

```bash
sudo docker run -it hello-world
```

Výstup:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Podle postupu v části [instalace Azure CLI pomocí apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) nainstalujte rozhraní příkazového řádku Azure do svého virtuálního počítače s Ubuntu. Příklad:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Ukončete připojení SSH.

## <a name="set-up-private-link---cli"></a>Nastavení privátního propojení – CLI

### <a name="get-network-and-subnet-names"></a>Získání názvů sítí a podsítí

Pokud je ještě nemáte, budete k nastavení privátního odkazu potřebovat názvy virtuální sítě a podsítě. V tomto příkladu použijete stejnou podsíť pro virtuální počítač a privátní koncový bod registru. V mnoha scénářích ale byste koncový bod nastavili v samostatné podsíti. 

Když vytvoříte virtuální počítač, Azure ve výchozím nastavení vytvoří virtuální síť ve stejné skupině prostředků. Název virtuální sítě je založený na názvu virtuálního počítače. Například pokud pojmenujete virtuální počítač *myDockerVM*, výchozí název virtuální sítě je *myDockerVMVNET*s podsítí s názvem *myDockerVMSubnet*. Nastavte tyto hodnoty v proměnných prostředí spuštěním příkazu [AZ Network VNet list][az-network-vnet-list] :

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>Zakázání zásad sítě v podsíti

[Zakažte zásady sítě](../private-link/disable-private-endpoint-network-policy.md) , jako jsou třeba skupiny zabezpečení sítě v podsíti pro soukromý koncový bod. Aktualizujte konfiguraci podsítě pomocí [AZ Network VNet Subnet Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Konfigurace privátní zóny DNS

Vytvořte privátní zónu DNS pro priviate doménu služby Azure Container Registry. V pozdějších krocích vytvoříte záznamy DNS pro doménu registru v rámci této zóny DNS.

Pokud chcete použít privátní zónu k přepsání výchozího překladu DNS pro službu Azure Container Registry, musí mít zóna název **privatelink.azurecr.IO**. Pro vytvoření privátní zóny spusťte následující příkaz [AZ Network Private-DNS Zone Create][az-network-private-dns-zone-create] :

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Vytvoří odkaz na přidružení.

Spusťte [AZ Network Private-DNS Link VNet Create][az-network-private-dns-link-vnet-create] a přidružte svoji privátní zónu k virtuální síti. Tento příklad vytvoří odkaz s názvem *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Vytvoření koncového bodu privátního registru

V této části vytvoříte privátní koncový bod registru ve virtuální síti. Nejprve Získejte ID prostředku svého registru:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Spuštěním příkazu [AZ Network Private-Endpoint Create][az-network-private-endpoint-create] vytvořte privátní koncový bod registru.

Následující příklad vytvoří koncový bod *myPrivateEndpoint* a připojení služby *mojepripojeni*. Chcete-li zadat prostředek registru kontejneru pro koncový bod, předejte `--group-ids registry`:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Získání privátních IP adres

Spuštění [AZ Network Private-Endpoint show][az-network-private-endpoint-show] pro dotazování koncového bodu pro ID síťového rozhraní:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Přidružená k síťovému rozhraní jsou dvě privátní IP adresy pro registr kontejneru: jeden pro samotný registr a druhý pro datový koncový bod registru. Pro získání privátních IP adres pro Registry kontejnerů a datového koncového bodu registru spusťte následující příkaz [AZ Resource show][az-resource-show] :

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>Vytvoření záznamů DNS v privátní zóně

Následující příkazy vytvoří záznamy DNS v privátní zóně pro koncový bod registru a jeho datový koncový bod. Pokud máte například registr s názvem *myregistry* v oblasti *westeurope* , názvy koncových bodů jsou `myregistry.azurecr.io` a `myregistry.westeurope.data.azurecr.io`. 

Nejdřív spusťte [AZ Network Private-DNS Record-set a Create][az-network-private-dns-record-set-a-create] vytvoří prázdné sady záznamů pro koncový bod registru a koncový bod dat:

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

Spusťte [záznam AZ Network Private-DNS Record-set a příkaz Add-Record][az-network-private-dns-record-set-a-add-record] pro vytvoření záznamů pro koncový bod registru a koncový bod dat:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

Privátní odkaz je teď nakonfigurovaný a připravený k použití.

## <a name="set-up-private-link---portal"></a>Nastavení privátního propojení – portál

V následujících krocích se předpokládá, že už máte virtuální síť a podsíť nastavené s virtuálním počítačem pro testování. Můžete také [vytvořit novou virtuální síť a podsíť](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

1. Na portálu přejděte do registru kontejneru.
1. V části **Nastavení**vyberte **připojení privátního koncového bodu (Preview)** .
1. Vyberte **+ soukromý koncový bod**.
1. Na kartě **základy** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Zadejte název existující skupiny nebo vytvořte novou.|
    | **Podrobnosti instance** |  |
    | Název | Zadejte jedinečný název. |
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
    |**Networking**| |
    | Virtuální síť| Vyberte virtuální síť, ve které je nasazený virtuální počítač, například *myDockerVMVNET*. |
    | Podsíť | Vyberte podsíť, například *myDockerVMSubnet* , kde se virtuální počítač nasadí. |
    |**Integrace Privátní DNS**||
    |Integrace s privátní zónou DNS |Vyberte **Ano**. |
    |Zóna Privátní DNS |Vybrat *(nové) privatelink.azurecr.IO* |
    |||

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci. 
2. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**.

Po vytvoření privátního koncového bodu se nastavení DNS v privátní zóně zobrazí na stránce s **přehledem** koncového bodu.

![Nastavení DNS koncového bodu](./media/container-registry-private-link/private-endpoint-overview.png)

Vaše privátní propojení je teď nakonfigurované a připravené k použití.

## <a name="validate-private-link-connection"></a>Ověřit připojení privátního propojení

Měli byste ověřit, že se prostředky v podsíti privátního koncového bodu připojují k vašemu registru přes privátní IP adresu a že mají správnou integraci privátních zón DNS.

Pokud chcete ověřit připojení privátního propojení s protokolem SSH k virtuálnímu počítači, který jste nastavili ve virtuální síti.

Spusťte příkaz `nslookup`, který přeloží IP adresu vašeho registru prostřednictvím privátního odkazu:

```bash
nslookup $registryName.azurecr.io
```

Příklad výstupu ukazuje IP adresu registru v adresním prostoru podsítě:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Porovnejte tento výsledek s veřejnou IP adresou ve `nslookup` výstup pro stejný registr prostřednictvím veřejného koncového bodu:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Operace s registrem přes privátní propojení

Ověřte také, že můžete provádět operace registru z virtuálního počítače v podsíti. Vytvořte připojení SSH k virtuálnímu počítači a spuštěním [AZ ACR Login][az-acr-login] Přihlaste se do svého registru. V závislosti na konfiguraci virtuálních počítačů možná budete muset pomocí `sudo`napředt následující příkazy.

```bash
az acr login --name $registryName
```

Proveďte operace v registru, jako je například `docker pull`, aby si z registru stáhli ukázkovou bitovou kopii. Nahraďte `hello-world:v1` obrázkem a označením vhodným pro váš registr s předponou názvu přihlašovacího serveru registru (všechna malá písmena):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker úspěšně načte image do virtuálního počítače.

## <a name="manage-private-endpoint-connections"></a>Správa připojení privátního koncového bodu

Pomocí Azure Portal spravujte připojení privátního koncového bodu registru nebo pomocí příkazů ve skupině příkazů [AZ ACR Private-Endpoint-Connection][az-acr-private-endpoint-connection] . Operace zahrnují schválení, odstranění, výpis, odmítnutí nebo zobrazení podrobností o připojeních privátních koncových bodů registru.

Pokud například chcete zobrazit seznam připojení privátního koncového bodu k registru, spusťte příkaz [AZ ACR Private-Endpoint-Connection list][az-acr-private-endpoint-connection-list] . Příklad:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

Když nastavíte připojení privátního koncového bodu pomocí kroků v tomto článku, registr automaticky akceptuje připojení z klientů a služeb, které mají oprávnění RBAC v registru. Koncový bod můžete nastavit tak, aby vyžadoval ruční schválení připojení. Informace o tom, jak schvalovat a odmítat připojení privátních koncových bodů, najdete v tématu [Správa připojení privátního koncového bodu](../private-link/manage-private-endpoint.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili všechny prostředky Azure ve stejné skupině prostředků a už je nepotřebujete, můžete je případně odstranit pomocí jednoho příkazu [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name $resourceGroup
```

Pokud chcete prostředky vyčistit na portálu, přejděte do skupiny prostředků. Po načtení skupiny prostředků klikněte na **Odstranit skupinu prostředků** a odeberte skupinu prostředků a prostředky, které jsou tam uložené.

## <a name="next-steps"></a>Další kroky

* Další informace o privátních odkazech najdete v dokumentaci k [privátním odkazům Azure](../private-link/private-link-overview.md) .
* Alternativou k privátnímu propojení je nastavení pravidel přístupu k síti, aby se omezil přístup k registru. Další informace najdete v tématu [omezení přístupu ke službě Azure Container Registry pomocí virtuální sítě Azure nebo pravidel brány firewall](container-registry-vnet.md).

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
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
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns-zone/create#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns-link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
