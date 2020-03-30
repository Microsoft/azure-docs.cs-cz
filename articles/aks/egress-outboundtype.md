---
title: Přizpůsobení uživatelem definovaných tras (UDR) ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak definovat vlastní odchozí trasu ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: fa64294939ea487b3123d1db5ef6c8a5f30fcf72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129395"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Přizpůsobení odchozího přenosu clusteru pomocí uživatelem definované trasy (náhled)

Odchozí přenos z clusteru AKS lze přizpůsobit tak, aby vyhovoval konkrétním scénářům. Ve výchozím nastavení AKS zřídí standardní skladovou položku pro vyrovnávání zatížení, která má být nastavena a použita pro odchozí přenos. Výchozí nastavení však nemusí splňovat požadavky všech scénářů, pokud jsou veřejné IP adresy zakázány nebo jsou pro odchozí přenos dat vyžadovány další směrování.

Tento článek vás provede, jak přizpůsobit odchozí trasu clusteru pro podporu vlastních síťových scénářů, jako jsou ty, které zakážou veřejné IP adresy a vyžadují, aby cluster seděl za virtuálním síťovým zařízením (NVA).

> [!IMPORTANT]
> Funkce AKS preview jsou samoobslužné a jsou nabízeny na základě opt-in. Náhledy jsou poskytovány *tak, jak jsou* *k dispozici* a jsou vyloučeny ze smlouvy o úrovni služeb (SLA) a omezené záruky. AKS náhledy jsou částečně pokryty zákaznickou podporou na *základě nejlepšíúsilí.* Proto funkce nejsou určeny pro produkční použití. Další informace naleznete v následujících článcích podpory:
>
> * [Zásady podpory AKS](support-policies.md)
> * [Nejčastější dotazy k podpoře Azure](faq.md)

## <a name="prerequisites"></a>Požadavky
* Azure CLI verze 2.0.81 nebo vyšší
* Rozšíření Azure CLI Preview verze 0.4.28 nebo vyšší
* Verze rozhraní `2020-01-01` API nebo vyšší

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Instalace nejnovějšího rozšíření Azure CLI AKS Preview
Chcete-li nastavit odchozí typ clusteru, budete potřebovat rozšíření Rozšíření Azure CLI AKS Preview verze 0.4.18 nebo novější. Nainstalujte rozšíření Azure CLI AKS Preview pomocí příkazu az extension add a pak zkontrolujte všechny dostupné aktualizace pomocí následujícího příkazu aktualizace rozšíření az:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Omezení
* Během náhledu `outboundType` lze definovat pouze v době vytváření clusteru a nelze je později aktualizovat.
* Během předběžné `outboundType` verze aks clustery by měly používat Azure CNI. Kubenet je konfigurovatelný, použití vyžaduje ruční přidružení směrovací tabulky k podsíti AKS.
* Nastavení `outboundType` vyžaduje clustery AKS `VirtualMachineScaleSets` `load-balancer-sku` s `Standard`a `vm-set-type` a .
* Nastavení `outboundType` na hodnotu `UDR` vyžaduje uživatelem definovanou trasu s platným odchozím připojením pro cluster.
* Nastavení `outboundType` na hodnotu znamená, že ip zdroj příchozího `UDR` přenosu dat směrovaný do vyvažovače zatížení nemusí **odpovídat** cílové adrese odchozího odchozího odchozího přenosu dat clusteru.

## <a name="overview-of-outbound-types-in-aks"></a>Přehled odchozích typů v AKS

Cluster AKS lze přizpůsobit pomocí `outboundType` jedinečného systému vyrovnávání zatížení nebo uživatelem definovaného směrování.

> [!IMPORTANT]
> Odchozí typ ovlivňuje pouze odchozí provoz clusteru. Další informace naleznete [v tématu nastavení řadičů příchozího přenosu dat.](ingress-basic.md)

### <a name="outbound-type-of-loadbalancer"></a>Odchozí typ loadBalancer

Pokud `loadBalancer` je nastavena, AKS dokončí následující nastavení automaticky. Vyrovnávání zatížení se používá pro odchozí přenos dat prostřednictvím aks přiřazené veřejné IP. Odchozí typ podporuje `loadBalancer` služby Typu Kubernetes `loadBalancer`, které očekávají odchozí výstup z vykladače zatížení vytvořeného zprostředkovatelem prostředků AKS.

Následující nastavení provádí AKS.
   * Veřejná IP adresa je zřízena pro odchozí cluster.
   * Veřejná IP adresa je přiřazena prostředku vyrovnávání zatížení.
   * Back-endové fondy pro vyrovnávání zatížení jsou nastaveny pro uzly agenta v clusteru.

Níže je topologie sítě nasazená ve výchozích clusterech `outboundType` AKS, která používá aplikaci . `loadBalancer`

![odchozí typ-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Odchozí typ userDefinedRouting

> [!NOTE]
> Použití odchozího typu je pokročilý síťový scénář a vyžaduje správnou konfiguraci sítě.

Pokud `userDefinedRouting` je nastavena, AKS nebude automaticky konfigurovat cesty odchozího přenosu. Očekává se, že **uživatel**provede následující provedení .

Cluster musí být nasazen do existující virtuální sítě s nakonfigurovanou podsítí. V podsíti s odchozím připojením musí existovat platná uživatelem definovaná trasa (UDR).

Zprostředkovatel prostředků AKS nasadí standardní vyrovnávání zatížení (SLB). Správce zatížení není nakonfigurován s žádnými pravidly a [neúčtuje poplatek, dokud není umístěno pravidlo](https://azure.microsoft.com/pricing/details/load-balancer/). AKS **nebude** automaticky zřídit veřejnou IP adresu pro front-end SLB. AKS **nebude** automaticky konfigurovat back-endový fond vyrovnávání zatížení.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Nasazení clusteru s odchozím typem UDR a Azure Firewall

Pro ilustraci aplikace clusteru s odchozím typem pomocí uživatelem definované trasy lze cluster nakonfigurovat ve virtuální síti s partnerem brány Azure Firewall.

![Uzamčená topologie](media/egress-outboundtype/outboundtype-udr.png)

* Příchozí přenos dat je nucen tok přes filtry brány firewall
   * Izolovaná podsíť obsahuje interní systém vyrovnávání zatížení pro směrování do uzlů agenta.
   * Uzly agentů jsou izolovány ve vyhrazené podsíti.
* Odchozí požadavky začínají z uzlů agenta do interní IP adresy Azure Firewall pomocí uživatelem definované trasy
   * Požadavky z uzlů agenta AKS následují podle udr, který byl umístěn v podsíti, do které byl cluster AKS nasazen.
   * Azure Firewall se odevzdává z virtuální sítě z veřejného front-endu IP
   * Přístup k řídicí rovině AKS je chráněn souborem NSG, který povolil front-endovou IP adresu brány firewall.
   * Přístup k veřejnému internetu nebo jiným službám Azure toky na a z front-endu firewall ip adresy

### <a name="set-configuration-via-environment-variables"></a>Nastavení konfigurace pomocí proměnných prostředí

Definujte sadu proměnných prostředí, které mají být použity při vytváření prostředků.

```bash
PREFIX="contosofin"
RG="${PREFIX}-rg"
LOC="eastus"
NAME="${PREFIX}outboundudr"
AKS_NAME="${PREFIX}aks"
VNET_NAME="${PREFIX}vnet"
AKSSUBNET_NAME="${PREFIX}akssubnet"
SVCSUBNET_NAME="${PREFIX}svcsubnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}fw"
FWPUBLICIP_NAME="${PREFIX}fwpublicip"
FWIPCONFIG_NAME="${PREFIX}fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}fwrt"
FWROUTE_NAME="${PREFIX}fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}fwinternet"
DEVSUBNET_NAME="${PREFIX}dev"
```

Dále nastavte ID předplatného.

```azure-cli
# Get ARM Access Token and Subscription ID - This will be used for AuthN later.

ACCESS_TOKEN=$(az account get-access-token -o tsv --query 'accessToken')

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Vytvoření virtuální sítě s několika podsítěmi

Zřízení virtuální sítě se třemi samostatnými podsítěmi, jednou pro cluster, jednou pro bránu firewall a jednou pro příchozí přenos dat služby.

![Prázdná síťová topologie](media/egress-outboundtype/empty-network.png)

Vytvořte skupinu prostředků pro uložení všech prostředků.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Vytvořte dvě virtuální sítě pro hostování clusteru AKS a brány Azure Firewall. Každý z nich bude mít svou vlastní podsíť. Začněme se sítí AKS.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 100.64.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 100.64.1.0/24

# Dedicated subnet for K8s services

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $SVCSUBNET_NAME \
    --address-prefix 100.64.2.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 100.64.3.0/24
```

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>Vytvoření a nastavení brány Azure Firewall pomocí udr

Je nutné nakonfigurovat příchozí a odchozí pravidla brány Azure Firewall. Hlavním účelem brány firewall je umožnit organizacím nastavit podrobná pravidla příchozího přenosu dat a odchozího přenosu dat do a z clusteru AKS.

![Brána firewall a UDR](media/egress-outboundtype/firewall-udr.png)

Vytvořte standardní veřejný IP prostředek skladové položky, který bude použit jako front-endová adresa azure firewallu.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Zaregistrujte rozšíření náhledu cli a vytvořte bránu Azure Firewall.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

Dříve vytvořenou ADRESU IP lze nyní přiřadit k front-endu brány firewall.
> [!NOTE]
> Nastavení veřejné IP adresy do brány Azure Firewall může trvat několik minut.
> 
> Pokud jsou chyby opakovaně přijímány pomocí příkazu níže, odstraňte existující bránu firewall a veřejnou IP adresu a současně zřizujte veřejnou IP adresu a bránu Azure Firewall prostřednictvím portálu.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Po úspěšném dokončení předchozího příkazu uložte adresu IP front-end brány firewall pro konfiguraci později.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Vytvoření udr s směrováním do Azure Firewall

Azure automaticky směruje provoz mezi podsítěmi Azure, virtuálními sítěmi a místními sítěmi. Pokud chcete změnit některé z výchozího směrování Azure, uděláte to vytvořením směrovací tabulky.

Vytvořte prázdnou tabulku postupu, která bude přidružena k dané podsíti. Tabulka tras definuje další směrování jako azure firewall vytvořený výše. Každá podsíť může mít přidruženou žádnou nebo jednu směrovací tabulku.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Podívejte se na [dokumentaci k tabulce směrování virtuální sítě](../virtual-network/virtual-networks-udr-overview.md#user-defined) o tom, jak můžete přepsat výchozí systémové trasy Azure nebo přidat další trasy do směrovací tabulky podsítě.

## <a name="adding-network-firewall-rules"></a>Přidání pravidel síťové brány firewall

> [!WARNING]
> Níže je uveden jeden příklad přidání pravidla brány firewall. Všechny koncové body odchozího přenosu definované v [požadovaných koncových bodech odchozího přenosu](egress.md) musí být povoleny pravidly brány firewall aplikace, aby clustery AKS fungovaly. Bez těchto koncových bodů povoleno clusteru nelze pracovat.

Níže je uveden příklad pravidla sítě a aplikace. Přidáme síťové pravidlo, které umožňuje libovolný protokol, zdrojovou adresu, cílovou adresu a cílové porty. Přidáme také pravidlo aplikace pro **některé** koncové body vyžadované AKS.

V produkčním scénáři byste měli povolit pouze přístup k požadovaným koncovým bodům pro vaši aplikaci a ty, které jsou definovány v [aks požadované odchozí .](egress.md)

```
# Add Network FW Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'netrules' --protocols 'Any' --source-addresses '*' --destination-addresses '*' --destination-ports '*' --action allow --priority 100

# Add Application FW Rules
# IMPORTANT: Add AKS required egress endpoints

az network firewall application-rule create -g $RG -f $FWNAME \
    --collection-name 'AKS_Global_Required' \
    --action allow \
    --priority 100 \
    -n 'required' \
    --source-addresses '*' \
    --protocols 'http=80' 'https=443' \
    --target-fqdns \
        'aksrepos.azurecr.io' \
        '*blob.core.windows.net' \
        'mcr.microsoft.com' \
        '*cdn.mscr.io' \
        '*.data.mcr.microsoft.com' \
        'management.azure.com' \
        'login.microsoftonline.com' \
        'ntp.ubuntu.com' \
        'packages.microsoft.com' \
        'acs-mirror.azureedge.net'
```

Další informace o službě Azure Firewall najdete v dokumentaci k [Bráně Azure.](https://docs.microsoft.com/azure/firewall/overview)

## <a name="associate-the-route-table-to-aks"></a>Přidružení tabulky postupu k AKS

Chcete-li přidružit cluster k bráně firewall, musí vyhrazená podsíť pro podsíť clusteru odkazovat na výše vytvořenou směrovací tabulku. Přidružení lze provést vydáním příkazu do virtuální sítě, který obsahuje cluster i bránu firewall a aktualizuje směrovací tabulku podsítě clusteru.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Nasazení Služby AKS s odchozím typem UDR do stávající sítě

Nyní lze cluster AKS nasadit do existujícího nastavení virtuální sítě. Chcete-li nastavit odchozí typ clusteru na uživatelem definované směrování, musí být aks poskytnuta existující podsíť.

![aks-nasadit](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Vytvoření instančního objektu s přístupem k zřizování uvnitř existující virtuální sítě

Instanční objekt používá AKS k vytvoření prostředků clusteru. Instanční objekt předaný v době vytvoření se používá k vytvoření podkladových prostředků AKS, jako jsou virtuální servery, úložiště a nástroj pro vyrovnávání zatížení používané AKS. Pokud je uděleno příliš málo oprávnění, nebude moci zřídit cluster AKS.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Nyní nahraďte `APPID` a `PASSWORD` níže s appid instančního objektu a service principal heslo automaticky generované předchozí výstup příkazu. Budeme odkazovat na ID prostředku virtuální sítě udělit oprávnění k instančnímu objektu, takže AKS můžete nasadit prostředky do něj.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>Nasazení aks

Nakonec lze cluster AKS nasadit do existující podsítě, kterou jsme pro cluster vyčlenili. Cílová podsíť, do které má být nasazena, je definována proměnnou prostředí . `$SUBNETID` Proměnnou `$SUBNETID` jsme v předchozích krocích nedefinovali. Chcete-li nastavit hodnotu ID podsítě, můžete použít následující příkaz:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

Definujeme odchozí typ, který bude následovat UDR, který existuje v podsíti, což aks přeskočit nastavení a zřizování IP pro vyrovnávání zatížení, které nyní může být přísně interní.

Funkci AKS pro [rozsahy IP adres autorizované ho serveru API](api-server-authorized-ip-ranges.md) lze přidat, aby se omezil přístup k serveru API pouze na veřejný koncový bod brány firewall. Funkce autorizovaných rozsahů IP adres je v diagramu označena jako skupina nsg, která musí být předána pro přístup k rovině řízení. Pokud povolíte funkci autorizovaného rozsahu IP adres k omezení přístupu k serveru rozhraní API, musí vývojářské nástroje používat pole pro vývojáře z virtuální sítě brány firewall nebo je nutné přidat všechny koncové body vývojáře do autorizovaného rozsahu IP adres.

> [!TIP]
> Další funkce lze přidat do nasazení clusteru, například (privátní cluster)[]. Při použití autorizovaných rozsahů IP adres bude pro přístup k serveru rozhraní API vyžadován a jumpbox uvnitř sítě clusteru.

```azure-cli
az aks create -g $RG -n $AKS_NAME -l $LOC \
  --node-count 3 \
  --network-plugin azure --generate-ssh-keys \
  --service-cidr 192.168.0.0/16 \
  --dns-service-ip 192.168.0.10 \
  --docker-bridge-address 172.22.0.1/29 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --load-balancer-sku standard \
  --outbound-type userDefinedRouting \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
  ```

### <a name="enable-developer-access-to-the-api-server"></a>Povolení přístupu vývojářů k serveru rozhraní API

Vzhledem k nastavení autorizovaných rozsahů IP adres pro cluster je nutné přidat adresy IP nástrojů pro vývojáře do seznamu schválených rozsahů IP adres clusteru AKS pro přístup k serveru ROZHRANÍ API. Další možností je konfigurace jumpboxu s potřebnými nástroji uvnitř samostatné podsítě ve virtuální síti brány firewall.

Přidání další adresy IP do schválených rozsahů pomocí následujícího příkazu

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

### <a name="setup-the-internal-load-balancer"></a>Nastavení interního systému vyrovnávání zatížení

AKS nasadila s clusterem systém vyrovnávání zatížení, který lze nastavit jako [interní systém vyrovnávání zatížení](internal-lb.md).

Chcete-li vytvořit interní nástroj pro vyrovnávání zatížení, vytvořte manifest služby s názvem internal-lb.yaml s typem služby LoadBalancer a interní poznámkou azure-load balancer, jak je znázorněno v následujícím příkladu:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Nasazení interního nástrojpro vyrovnávání zatížení pomocí kubectl použít a zadejte název manifestu YAML:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Nasazení služby Kubernetes

Vzhledem k tomu, že odchozí typ clusteru je nastaven jako UDR, přidružování uzlů agenta jako back-endového fondu pro vydělávač zatížení není automaticky dokončena AKS v době vytvoření clusteru. Back-endové přidružení fondu je však zpracováno poskytovatelem cloudu Kubernetes Azure při nasazení služby Kubernetes.

Nasazení aplikace pro hlasování azure tak, že zkopírujete `example.yaml`yaml níže do souboru s názvem .

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Nasadit službu spuštěním:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Přidání pravidla DNAT do brány Azure Firewall

Chcete-li nakonfigurovat příchozí připojení, musí být pravidlo DNAT zapsáno do brány Azure Firewall. Chcete-li otestovat připojení k našemu clusteru, je definováno pravidlo pro front-endové adresy firewall pro směrování na interní IP adresu vystavenou interní službou.

Cílovou adresu lze přizpůsobit, protože se jedná o port brány firewall, ke které má být přístupná. Přeložená adresa musí být IP adresa interního systému vyrovnávání zatížení. Přeložený port musí být exponovaným portem pro vaši službu Kubernetes.

Budete muset zadat interní IP adresu přiřazenou k systému vyrovnávání zatížení vytvořenému službou Kubernetes. Načíst adresu spuštěním:

```bash
kubectl get services
```

Potřebná IP adresa bude uvedena ve sloupci EXTERNAL-IP podobně jako následující.

```bash
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
azure-vote-back    ClusterIP      192.168.92.209   <none>        6379/TCP       23m
azure-vote-front   LoadBalancer   192.168.19.183   100.64.2.5    80:32106/TCP   23m
kubernetes         ClusterIP      192.168.0.1      <none>        443/TCP        4d3h
```

```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address <INSERT IP OF K8s SERVICE>
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

> [!NOTE]
> Při odstraňování interní služby Kubernetes, pokud interní vyrovnávání zatížení již nepoužívá žádná služba, poskytovatel cloudu Azure odstraní interní vyrovnávání zatížení. Při dalším nasazení služby bude nasazen nástroj pro vyrovnávání zatížení, pokud s požadovanou konfigurací nenajdete žádný nástroj.

Chcete-li vyčistit prostředky Azure, odstraňte skupinu prostředků AKS.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Ověřit připojení

Přejděte na front-endovou IP adresu Azure Firewall v prohlížeči a ověřte připojení.

Měli byste vidět obrázek aplikace Hlasování Azure.

## <a name="next-steps"></a>Další kroky

Viz [Přehled UDR sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Podívejte [se, jak vytvořit, změnit nebo odstranit směrovací tabulku](https://docs.microsoft.com/azure/virtual-network/manage-route-table).
