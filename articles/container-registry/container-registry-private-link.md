---
title: Nastavit soukromý odkaz
description: Nastavení privátního koncového bodu v registru kontejnerů a povolení privátního propojení v místní virtuální síti
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498912"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Konfigurace privátního propojení Azure pro registr kontejnerů Azure 

Můžete nastavit [privátní koncový bod](../private-link/private-endpoint-overview.md) pro váš registr kontejnerů Azure tak, aby klienti ve virtuální síti Azure bezpečně přistupovat k registru přes [privátní propojení](../private-link/private-link-overview.md). Privátní koncový bod používá adresu IP z adresního prostoru virtuální sítě pro váš registr. Síťový provoz mezi klienty ve virtuální síti a registru prochází virtuální sítí a privátním odkazem v páteřní síti společnosti Microsoft, což eliminuje expozici z veřejného internetu.

Nastavení DNS můžete [nakonfigurovat](../private-link/private-endpoint-overview.md#dns-configuration) pro soukromý koncový bod tak, aby se nastavení přelože lože na přidělenou privátní IP adresu registru. S konfigurací DNS mohou klienti a služby v síti nadále přistupovat k registru pod plně kvalifikovaným názvem domény, například *myregistry.azurecr.io*.

Tato funkce je k dispozici ve vrstvě služby registru **kontejneru Premium.** Informace o vrstvách a limitech služby registru naleznete v tématu [Azure Container Registry SKU](container-registry-skus.md).

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a platí určitá [omezení.](#preview-limitations) Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="preview-limitations"></a>Omezení náhledu

* V současné době nelze nastavit soukromé propojení s privátním koncovým bodem v [geograficky replikovaném registru](container-registry-geo-replication.md). 

## <a name="prerequisites"></a>Požadavky

* Chcete-li použít kroky azure cli v tomto článku, doporučujeme Azure CLI verze 2.2.0 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli]. Nebo spustit v [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Pokud ještě nemáte registr kontejnerů, vytvořte jeden (požadovaná úroveň Premium) `hello-world` a vysuňte ukázkovou bitovou kopii, například z Docker Hubu. Například použijte [portál Azure][quickstart-portal] nebo [Azure CLI][quickstart-cli] k vytvoření registru.
* Pokud chcete nakonfigurovat přístup k registru pomocí privátního odkazu v jiném předplatném Azure, musíte v tomto předplatném zaregistrovat poskytovatele prostředků pro Registr kontejnerů Azure. Například:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Příklady Azure CLI v tomto článku používají následující proměnné prostředí. Nahraďte hodnoty vhodné pro vaše prostředí. Všechny příklady jsou formátovány pro prostředí Bash:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Vytvoření virtuálního počítače s podporou Dockeru

Pro testovací účely použijte virtuální počítač Ubuntu s podporou Dockeru pro přístup k registru kontejnerů Azure. Pokud chcete do registru použít ověřování Azure Active Directory, nainstalujte na virtuální počítač také [rozhraní příkazového příkazu Konazure.][azure-cli] Pokud už máte virtuální počítač Azure, přeskočte tento krok vytvoření.

Můžete použít stejnou skupinu prostředků pro váš virtuální počítač a registr kontejnerů. Toto nastavení zjednodušuje vyčištění na konci, ale není vyžadováno. Pokud se rozhodnete vytvořit samostatnou skupinu prostředků pro virtuální počítač a virtuální síť, spusťte [vytvoření skupiny AZ][az-group-create]:

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Nyní nasadit výchozí Ubuntu Azure virtuální počítač s [az vm vytvořit][az-vm-create]. Následující příklad vytvoří virtuální hod s názvem *myDockerVM*.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po dokončení příkazu, poznamenejte si `publicIpAddress` zobrazení v příkazovém příkazu Azure. Tato adresa slouží k připojení SSH k virtuálnímu virtuálnímu provozu.

### <a name="install-docker-on-the-vm"></a>Instalace Dockeru na virtuální ms

Po spuštění virtuálního virtuálního provozu vytvořte připojení SSH k virtuálnímu virtuálnímu jemu. Nahraďte *publicIpAddress* veřejnou IP adresou vašeho virtuálního počítače.

```bash
ssh azureuser@publicIpAddress
```

Spusťte následující příkazy pro instalaci Dockeru na virtuální počítač Ubuntu:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Po instalaci spusťte následující příkaz a ověřte, že Docker na virtuálním počítači funguje správně:

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

Postupujte podle kroků v [části Instalace nastavení příkazového příkazu Azure s aptem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) k instalaci příkazového příkazového příkazu Azure do virtuálního počítače Ubuntu. Například:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Ukončete připojení SSH.

## <a name="set-up-private-link---cli"></a>Nastavit soukromý odkaz – cli

### <a name="get-network-and-subnet-names"></a>Získání názvů sítí a podsítí

Pokud je ještě nemáte, budete k nastavení privátního propojení potřebovat názvy virtuální sítě a podsítě. V tomto příkladu použijete stejnou podsíť pro virtuální ho svitek a soukromý koncový bod registru. V mnoha scénářích byste však nastavili koncový bod v samostatné podsíti. 

Když vytvoříte virtuální počítač, Azure ve výchozím nastavení vytvoří virtuální síť ve stejné skupině prostředků. Název virtuální sítě je založen na názvu virtuálního počítače. Pokud například pojmenujete virtuální počítač *myDockerVM*, výchozí název virtuální sítě je *myDockerVMVNET*, s podsítí s názvem *myDockerVMSubnet*. Nastavte tyto hodnoty v proměnných prostředí spuštěním příkazu [az network vnet list:][az-network-vnet-list]

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

### <a name="disable-network-policies-in-subnet"></a>Zakázání síťových zásad v podsíti

[Zakažte zásady sítě,](../private-link/disable-private-endpoint-network-policy.md) jako jsou skupiny zabezpečení sítě v podsíti pro soukromý koncový bod. Aktualizace konfigurace podsítě aktualizací [sítě AZ][az-network-vnet-subnet-update]network vnet :

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Konfigurace privátní zóny DNS

Vytvořte privátní zónu DNS pro priviate doménu registru kontejneru Azure. V pozdějších krocích vytvoříte záznamy DNS pro doménu registru v této zóně DNS.

Chcete-li použít privátní zónu k přepsání výchozího rozlišení DNS pro registr kontejnerů Azure, musí být zóna pojmenována **privatelink.azurecr.io**. Chcete-li vytvořit privátní zónu, spusťte následující příkaz [vytvoření zóny az network private-dns:][az-network-private-dns-zone-create]

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Vytvoření přidružení odkazu

Spusťte [vytvoření virtuální sítě az privátní dns pro][az-network-private-dns-link-vnet-create] přidružení privátní zóny k virtuální síti. Tento příklad vytvoří odkaz s názvem *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Vytvoření koncového bodu soukromého registru

V této části vytvořte soukromý koncový bod registru ve virtuální síti. Nejprve získejte ID prostředku registru:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Spusťte příkaz [vytvoření privátního koncového bodu sítě az][az-network-private-endpoint-create] a vytvořte soukromý koncový bod registru.

Následující příklad vytvoří koncový bod *myPrivateEndpoint* a připojení služby *myConnection*. Chcete-li zadat prostředek registru kontejneru `--group-ids registry`pro koncový bod, předavažte :

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

### <a name="get-private-ip-addresses"></a>Získání soukromých IP adres

Spuštění [množin privátního koncového bodu sítě AZ pro][az-network-private-endpoint-show] dotazování koncového bodu pro ID síťového rozhraní:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Přidružené k síťovému rozhraní jsou dvě privátní IP adresy pro registr kontejneru: jedna pro samotný registr a jedna pro koncový bod dat registru. Spusťte následující příkazy [az resource show,][az-resource-show] abyste získali privátní IP adresy pro registr kontejneru a koncový bod dat registru:

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

Následující příkazy vytvářejí záznamy DNS v privátní zóně pro koncový bod registru a jeho koncový bod dat. Například pokud máte registr s názvem *myregistry* v oblasti *westeurope,* názvy koncových bodů jsou `myregistry.azurecr.io` a `myregistry.westeurope.data.azurecr.io`. 

Nejprve [spusťte][az-network-private-dns-record-set-a-create] sadu záznamů az network private dns a create pro vytvoření prázdných sad záznamů A pro koncový bod registru a koncový bod dat:

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

Spusťte příkaz [az network private-dns-set add-record][az-network-private-dns-record-set-a-add-record] a vytvořte záznamy A pro koncový bod registru a koncový bod dat:

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

Privátní propojení je nyní nakonfigurováno a připraveno k použití.

## <a name="set-up-private-link---portal"></a>Nastavit soukromý odkaz - portál

Následující kroky předpokládají, že už máte virtuální síť a podsíť nastavenou s virtuálním počítačem pro testování. Můžete také [vytvořit novou virtuální síť a podsíť](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu

1. Na portálu přejděte do registru kontejnerů.
1. V části **Nastavení**vyberte **Soukromá připojení koncových bodů (Preview).**
1. Vyberte **+ soukromý koncový bod**.
1. Na kartě **Základy** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Zadejte název existující skupiny nebo vytvořte novou.|
    | **Podrobnosti instance** |  |
    | Name (Název) | Zadejte jedinečný název. |
    |Region (Oblast)|Vyberte oblast.|
    |||
5. Vyberte **další: Zdroj**.
6. Zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |Způsob připojení  | V **yberte Připojit k prostředku Azure v mém adresáři**.|
    | Předplatné| Vyberte své předplatné. |
    | Typ prostředku | Vyberte **microsoft.containerregistry/registry .** |
    | Prostředek |Vyberte název registru.|
    |Cílový dílčí zdroj |Vybrat **registr**|
    |||
7. Vyberte **další: Konfigurace**.
8. Zadejte nebo vyberte informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    |**Síťové služby**| |
    | Virtuální síť| Vyberte virtuální síť, ve které se váš virtuální počítač nasazuje, například *myDockerVMVNET*. |
    | Podsíť | Vyberte podsíť, například *myDockerVMSubnet,* kde se nasazuje váš virtuální počítač. |
    |**Privátní integrace DNS**||
    |Integrace se soukromou zónou DNS |Vyberte **ano**. |
    |Soukromá zóna DNS |Vybrat *(nový) privatelink.azurecr.io* |
    |||

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvoření,** kde Azure ověřuje vaši konfiguraci. 
2. Když se zobrazí zpráva **Ověření předáno,** vyberte **vytvořit**.

Po vytvoření privátního koncového bodu se nastavení DNS v privátní zóně zobrazí na stránce **Přehled** koncového bodu.

![Nastavení DNS koncového bodu](./media/container-registry-private-link/private-endpoint-overview.png)

Váš soukromý odkaz je nyní nakonfigurován a připraven k použití.

## <a name="validate-private-link-connection"></a>Ověřit připojení k soukromému propojení

Měli byste ověřit, zda se prostředky v podsíti privátního koncového bodu připojují k registru přes privátní IP adresu a mají správnou privátní integraci zóny DNS.

Chcete-li ověřit připojení privátní ho spojení, SSH k virtuálnímu počítači, který jste nastavili ve virtuální síti.

Spuštěním `nslookup` příkazu převyřešením adresy IP registru prostředpou privátního odkazu:

```bash
nslookup $registryName.azurecr.io
```

Příklad výstupu ukazuje adresu IP registru v adresním prostoru podsítě:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Porovnejte tento výsledek `nslookup` s veřejnou IP adresou ve výstupu pro stejný registr přes veřejný koncový bod:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Operace registru přes privátní propojení

Ověřte také, zda můžete provádět operace registru z virtuálního počítače v podsíti. Vytvořte připojení SSH k virtuálnímu počítači a spusťte [přihlášení az acr][az-acr-login] pro přihlášení do registru. V závislosti na konfiguraci virtuálního počítače může být `sudo`nutné předponu následující příkazy s .

```bash
az acr login --name $registryName
```

Proveďte operace `docker pull` registru, například vyžádat ukázkovou bitovou kopii z registru. Nahraďte `hello-world:v1` bitovou kopii a značku vhodnou pro váš registr, s předponou s názvem přihlašovacího serveru registru (všechna malá písmena):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker úspěšně natáhne image do virtuálního mísu.

## <a name="manage-private-endpoint-connections"></a>Správa připojení privátního koncového bodu

Spravujte privátní připojení koncového bodu registru pomocí portálu Azure nebo pomocí příkazů ve skupině příkazů [az acr private-endpoint-connection.][az-acr-private-endpoint-connection] Operace zahrnují schvalování, odstraňování, vypáčování, odmítání nebo zobrazení podrobností o soukromých koncových bodech připojení registru.

Chcete-li například vypsat soukromá připojení koncového bodu registru, spusťte příkaz [az acr private-endpoint-connection.][az-acr-private-endpoint-connection-list] Například:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

Při nastavování připojení soukromého koncového bodu pomocí kroků v tomto článku registr automaticky přijímá připojení od klientů a služeb, které mají oprávnění RBAC v registru. Můžete nastavit koncový bod tak, aby vyžadoval ruční schválení připojení. Informace o tom, jak schvalovat a zamítat připojení privátního koncového bodu, naleznete v [tématu Správa připojení privátního koncového bodu](../private-link/manage-private-endpoint.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili všechny prostředky Azure ve stejné skupině prostředků a už je nepotřebujete, můžete je volitelně odstranit pomocí jediného [příkazu odstranění skupiny az:](/cli/azure/group)

```azurecli
az group delete --name $resourceGroup
```

Chcete-li vyčistit prostředky na portálu, přejděte do skupiny prostředků. Po načtení skupiny prostředků odejměte skupinu prostředků kliknutím na **Odstranit skupinu prostředků** odeberte skupinu prostředků a prostředky, které jsou v ní uloženy.

## <a name="next-steps"></a>Další kroky

* Další informace o privátní link najdete v dokumentaci [k Privátní odkaz Azure.](../private-link/private-link-overview.md)
* Alternativou k privátnímu propojení je nastavení pravidel přístupu k síti pro omezení přístupu k registru. Další informace najdete v [tématu Omezení přístupu k registru kontejnerů Azure pomocí virtuální sítě Azure nebo pravidel brány firewall](container-registry-vnet.md).

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
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
