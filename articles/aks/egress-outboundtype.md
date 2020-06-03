---
title: Přizpůsobení uživatelsky definovaných tras (UDR) ve službě Azure Kubernetes Service (AKS)
description: Naučte se definovat vlastní výstupní trasu ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 06/05/2020
ms.openlocfilehash: d62f40fb835bfe6993ad31ddd20cfdea1d9135c2
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310865"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Přizpůsobení výstupů clusteru pomocí uživatelsky definované trasy

Odchozí přenos dat z clusteru AKS se dá přizpůsobit tak, aby vyhovoval specifickým scénářům. Ve výchozím nastavení AKS zřídí standardní SKU Load Balancer, který se má nastavit a použít k odchozímu přenosu dat. Výchozí nastavení ale nemusí splňovat požadavky všech scénářů, pokud jsou veřejné IP adresy zakázané nebo jsou pro odchozí přenosy nutné další segmenty směrování.

Tento článek vás seznámí s postupem přizpůsobení odchozí trasy clusteru pro podporu vlastních síťových scénářů, jako jsou například ty, které nepovolují veřejné IP adresy a vyžadují, aby se cluster zacházel za virtuálním síťovým zařízením (síťové virtuální zařízení).

## <a name="prerequisites"></a>Požadavky
* Azure CLI verze 2.0.81 nebo vyšší
* Verze rozhraní API `2020-01-01` nebo vyšší


## <a name="limitations"></a>Omezení
* OutboundType se dá definovat jenom v době vytváření clusteru a nedá se aktualizovat později.
* Nastavení `outboundType` vyžaduje clustery AKS s `vm-set-type` `VirtualMachineScaleSets` a `load-balancer-sku` z `Standard` .
* Nastavení `outboundType` na hodnotu `UDR` vyžaduje trasu definovanou uživatelem s platným odchozím připojením pro cluster.
* Nastavení `outboundType` na hodnotu `UDR` znamená, že IP adresa příchozího přenosu dat směrovaného do nástroje pro vyrovnávání zatížení nemusí **odpovídat** cílové adrese odchozího výstupu clusteru.

## <a name="overview-of-outbound-types-in-aks"></a>Přehled odchozích typů v AKS

Cluster AKS se dá přizpůsobit jedinečným typem pro `outboundType` Vyrovnávání zatížení nebo uživatelem definovaným směrováním.

> [!IMPORTANT]
> Typ odchozího přenosu ovlivňuje jenom výstupní přenos vašeho clusteru. Další informace najdete v tématu [nastavení řadičů](ingress-basic.md) příchozího přenosu dat.

> [!NOTE]
> Můžete použít vlastní [směrovací tabulku][byo-route-table] s udr a kubenet sítí.

### <a name="outbound-type-of-loadbalancer"></a>Odchozí typ vyrovnávání zatížení

Pokud `loadBalancer` je nastavená, AKS dokončí následující konfiguraci automaticky. Nástroj pro vyrovnávání zatížení se používá k odchozímu přenosu prostřednictvím AKS s přiřazenou veřejnou IP adresou. Odchozí typ `loadBalancer` podporuje služby Kubernetes typu `loadBalancer` , které očekávají výstup z nástroje pro vyrovnávání zatížení vytvořeného poskytovatelem prostředků AKS.

Následující konfigurace se provádí pomocí AKS.
   * Veřejná IP adresa se zřídí pro odchozí výstup clusteru.
   * Veřejné IP adresy se přiřadí prostředku nástroje pro vyrovnávání zatížení.
   * Back-endové fondy pro nástroj pro vyrovnávání zatížení jsou nastaveny pro uzly agentů v clusteru.

Níže je síťová topologie nasazená v clusterech AKS ve výchozím nastavení, která `outboundType` používají `loadBalancer` .

![outboundtype-kg](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Odchozí typ userDefinedRouting

> [!NOTE]
> Použití odchozího typu je pokročilý scénář sítě a vyžaduje správnou konfiguraci sítě.

Pokud `userDefinedRouting` je nastavená, AKS nebude automaticky konfigurovat výstupní cesty. Tento **uživatel**by měl provést následující:

Cluster AKS musí být nasazený do existující virtuální sítě s nakonfigurovanou podsítí. Při použití standardní architektury nástroje pro vyrovnávání zatížení (SLB) musíte nastavit explicitní výstup. To vyžaduje odeslání požadavků na výstup do zařízení, například brány firewall, brány, Prem nebo, aby bylo možné provést výstup pomocí veřejné IP adresy přiřazené ke standardnímu nástroji pro vyrovnávání zatížení nebo ke konkrétnímu uzlu.

Poskytovatel prostředků AKS nasadí standardní nástroj pro vyrovnávání zatížení (SLB). Nástroj pro vyrovnávání zatížení není nakonfigurovaný s žádným pravidlem a [neúčtuje poplatek, dokud se pravidlo neuloží](https://azure.microsoft.com/pricing/details/load-balancer/). AKS nebude **automaticky zřizovat** veřejnou IP adresu pro front-end služby SLB. AKS nebude **automaticky konfigurovat** back-end fond nástroje pro vyrovnávání zatížení.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Nasazení clusteru s odchozím typem UDR a Azure Firewall

K ilustraci aplikace clusteru s odchozím typem pomocí uživatelsky definované trasy můžete cluster nakonfigurovat ve virtuální síti s partnerským Azure Firewall.

![Zamčená topologie](media/egress-outboundtype/outboundtype-udr.png)

* Příchozí přenos dat do filtrů brány firewall je nucený.
   * Izolovaná podsíť obsahuje interní nástroj pro vyrovnávání zatížení pro směrování do uzlů agentů.
   * Uzly agenta jsou izolované ve vyhrazené podsíti.
* Odchozí požadavky začínají z uzlů agentů na Azure Firewall interní IP adresu pomocí uživatelem definované trasy.
   * Požadavky z uzlů agentů AKS se řídí UDR, který byl umístěn v podsíti, do které byl nasazen cluster AKS.
   * VyAzure Firewall výstup z virtuální sítě z front-endu veřejné IP adresy
   * Přístup k rovině ovládacího prvku AKS je chráněný NSG, který má povolenou IP adresu front-endu firewallu.
   * Přístup k veřejným internetům nebo jiným službám Azure, ke kterým se dostanete, do a z IP adresy front-endu brány firewall

### <a name="set-configuration-via-environment-variables"></a>Nastavení konfigurace pomocí proměnných prostředí

Definujte sadu proměnných prostředí, které se mají použít při vytváření prostředků.

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

V dalším kroku nastavte ID předplatných.

```azure-cli

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Vytvoření virtuální sítě s několika podsítěmi

Zřídit virtuální síť se třemi samostatnými podsítěmi, jednu pro cluster, jednu pro bránu firewall a jednu pro příchozí provoz.

![Prázdná topologie sítě](media/egress-outboundtype/empty-network.png)

Vytvořte skupinu prostředků pro uložení všech prostředků.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Vytvořte dvě virtuální sítě pro hostování clusteru AKS a Azure Firewall. Každá z nich bude mít vlastní podsíť. Pojďme začít používat AKS síť.

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

## <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Vytvoření a nastavení Azure Firewall s UDR

Musí být nakonfigurovaná pravidla příchozího a odchozího Azure Firewall. Hlavním účelem brány firewall je umožnit organizacím konfigurovat podrobná pravidla příchozího provozu a odchozí přenos dat do clusteru AKS a z něj.

![Brána firewall a UDR](media/egress-outboundtype/firewall-udr.png)

Vytvořte prostředek veřejné IP adresy standardní SKU, který se bude používat jako Azure Firewall adresa front-endu.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Zaregistrujte verzi Preview rozhraní příkazového řádku pro vytvoření Azure Firewall.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

IP adresa, kterou jste vytvořili dříve, se teď dá přiřadit ke front-endu brány firewall.
> [!NOTE]
> Nastavení veřejné IP adresy na Azure Firewall může trvat několik minut.
> 
> Pokud jsou v níže uvedeném příkazu opakovaně přijímány chyby, odstraňte existující bránu firewall a veřejnou IP adresu a zajistěte si veřejnou IP adresu a Azure Firewall prostřednictvím portálu.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Po úspěšném dokončení předchozího příkazu uložte IP adresu front-endu firewallu pro pozdější konfiguraci.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!Note]
> Pokud používáte zabezpečený přístup k serveru rozhraní AKS API s [povolenými rozsahy IP adres](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), musíte přidat veřejnou IP adresu brány firewall do povoleného rozsahu IP adres.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Vytvoření UDR s segmentem směrování na Azure Firewall

> [!IMPORTANT]
> Výstupní typ UDR vyžaduje trasu pro 0.0.0.0/0 a cíl dalšího segmentu směrování síťové virtuální zařízení (síťové virtuální zařízení) v tabulce směrování.

Azure automaticky směruje provoz mezi podsítěmi Azure, virtuálními sítěmi a místními sítěmi. Pokud chcete změnit některý z výchozích směrování Azure, provedete to tak, že vytvoříte směrovací tabulku.

Vytvořte prázdnou směrovací tabulku, kterou chcete přidružit k dané podsíti. Směrovací tabulka bude definovat další segment směrování, jak Azure Firewall vytvořili výše. Každá podsíť může mít přidruženou žádnou nebo jednu směrovací tabulku.

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Informace o tom, jak můžete přepsat výchozí systémové trasy Azure nebo přidat další trasy do směrovací tabulky podsítě, najdete v [dokumentaci k tabulce směrování virtuální sítě](../virtual-network/virtual-networks-udr-overview.md#user-defined) .

## <a name="adding-network-firewall-rules"></a>Přidávají se pravidla brány firewall sítě.

> [!WARNING]
> Níže uvádíme jeden příklad přidání pravidla brány firewall. Všechny koncové body odchozího přenosu definované v [požadovaných koncových bodech odchozího](egress.md) přenosu musí být povoleny pravidly brány firewall aplikací pro fungování clusterů AKS. Bez povolených koncových bodů nemůže váš cluster fungovat.

Níže je uveden příklad pravidla sítě a aplikace. Přidáme síťové pravidlo, které povoluje jakýkoli protokol, zdrojovou adresu, cílovou IP adresu a cílový port. Přidáme také pravidlo aplikace pro **některé** koncové body, které vyžaduje AKS.

V produkčním scénáři byste měli povolit přístup jenom k požadovaným koncovým bodům vaší aplikace a ty definované v [AKS vyžadují výstup](egress.md).

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

Další informace o službě Azure Firewall najdete v [dokumentaci k Azure firewall](https://docs.microsoft.com/azure/firewall/overview) .

## <a name="associate-the-route-table-to-aks"></a>Přidružení směrovací tabulky k AKS

K přidružení clusteru k bráně firewall musí vyhrazená podsíť pro podsíť clusteru odkazovat na tabulku směrování vytvořenou výše. Přidružení se dá udělat vyvoláním příkazu do virtuální sítě, která drží cluster i bránu firewall k aktualizaci směrovací tabulky podsítě clusteru.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Nasazení AKS s odchozím typem UDR do existující sítě

Cluster AKS se teď dá nasadit do existující virtuální sítě. Aby bylo možné nastavit typ odchozího clusteru na uživatelem definované směrování, musí být k dispozici existující podsíť AKS.

![AKS – nasazení](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Vytvoření instančního objektu s přístupem ke zřízení v existující virtuální síti

Objekt služby používá AKS k vytváření prostředků clusteru. Instanční objekt předaný v čase vytvoření slouží k vytvoření základních prostředků AKS, jako jsou virtuální počítače, úložiště a nástroje pro vyrovnávání zatížení používané v AKS. Pokud je uděleno příliš málo oprávnění, nebude moci zřídit cluster AKS.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Nyní nahraďte `APPID` a `PASSWORD` níže objektem AppID a heslo instančního objektu, které jsou automaticky generovány předchozím výstupem příkazu. Provedeme odkaz na ID prostředku virtuální sítě, aby byla udělena oprávnění instančnímu objektu, aby AKS mohli do něj nasadit prostředky.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>Nasazení AKS

A konečně, cluster AKS se dá nasadit do existující podsítě, kterou jsme pro cluster vyhradí. Cílová podsíť, do které se má nasadit, je definována s proměnnou prostředí `$SUBNETID` . V předchozích krocích jsme nedefinovali `$SUBNETID` proměnnou. Chcete-li nastavit hodnotu ID podsítě, můžete použít následující příkaz:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

Definujte typ odchozího připojení, který se bude řídit UDR, která existuje v podsíti, a umožňuje AKS přeskočit nastavení a zřizování IP pro nástroj pro vyrovnávání zatížení, který teď může být výhradně interní.

Funkci AKS pro [rozsahy IP adres autorizovaných serverem API](api-server-authorized-ip-ranges.md) lze přidat k omezení přístupu serveru rozhraní API pouze k veřejnému koncovému bodu brány firewall. Funkce autorizované rozsahy IP adres je v diagramu označena jako NSG, která musí být předána pro přístup k rovině ovládacího prvku. Když povolíte funkci schváleného rozsahu IP adres pro omezení přístupu k serveru rozhraní API, nástroje pro vývojáře musí použít JumpBox z virtuální sítě brány firewall nebo musíte přidat všechny koncové body pro vývojáře do povoleného rozsahu IP adres.

> [!TIP]
> Do nasazení clusteru je možné přidat další funkce, jako je (privátní cluster) []. Při použití autorizovaných rozsahů IP adres se v síti s clustery vyžaduje JumpBox k přístupu k serveru rozhraní API.

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

### <a name="enable-developer-access-to-the-api-server"></a>Povolení přístupu pro vývojáře k serveru rozhraní API

Kvůli povoleným rozsahům IP adres clusteru musíte přidat IP adresy vývojářských nástrojů do seznamu clusterů AKS schválených rozsahů IP adres pro přístup k serveru rozhraní API. Další možností je nakonfigurovat JumpBox s potřebnými nástroji v samostatné podsíti ve virtuální síti brány firewall.

Pomocí následujícího příkazu přidejte do schválených rozsahů další IP adresu.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] můžete nakonfigurovat `kubectl` připojení k nově vytvořenému clusteru Kubernetes. 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="set-up-the-internal-load-balancer"></a>Nastavení interního nástroje pro vyrovnávání zatížení

AKS nasadil Nástroj pro vyrovnávání zatížení s clusterem, který se dá nastavit jako [interní nástroj pro vyrovnávání zatížení](internal-lb.md).

Pokud chcete vytvořit interní nástroj pro vyrovnávání zatížení, vytvořte v něm manifest služby s názvem Internal-9,1. yaml s typem služby a službou Azure-Load Balancer – interní anotaci, jak je znázorněno v následujícím příkladu:

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

Nasaďte interní nástroj pro vyrovnávání zatížení pomocí kubectl použít a zadejte název manifestu YAML:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Nasazení služby Kubernetes

Vzhledem k tomu, že typ odchozího clusteru je nastaven jako UDR, přidružení uzlů agentů jako back-end fond pro nástroj pro vyrovnávání zatížení není v době vytvoření clusteru automaticky dokončeno pomocí AKS. Přidružení fondu back-endu se ale při nasazení služby Kubernetes zpracovává poskytovatelem cloudu Azure Kubernetes.

Nasaďte aplikaci hlasovací aplikace v Azure zkopírováním níže uvedeného YAML do souboru s názvem `example.yaml` .

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

Nasaďte službu spuštěním:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Přidat pravidlo DNAT do Azure Firewall

Aby bylo možné konfigurovat příchozí připojení, musí být do Azure Firewall zapsáno pravidlo DNAT. K otestování připojení k našemu clusteru je definováno pravidlo pro veřejnou IP adresu front-endu firewall pro směrování do interní IP adresy vystavené interní službou.

Cílovou adresu lze přizpůsobit, protože se jedná o port v bráně firewall, který má být k dispozici. Přeložená adresa musí být IP adresa interního nástroje pro vyrovnávání zatížení. Přeložený port musí být vystavený port pro vaši službu Kubernetes.

Budete muset zadat interní IP adresu přiřazenou k nástroji pro vyrovnávání zatížení vytvořenému službou Kubernetes. Načtěte adresu spuštěním:

```bash
kubectl get services
```

Požadovaná IP adresa bude uvedena ve sloupci EXTERNAL-IP, podobně jako v následujícím příkladu.

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
> Pokud Kubernetes interní službu Load Balancer už nepoužívá žádná služba, poskytovatel cloudu Azure odstraní interní nástroj pro vyrovnávání zatížení. Při dalším nasazení služby se nástroj pro vyrovnávání zatížení nasadí, pokud se nenalezne s požadovanou konfigurací.

Pokud chcete vyčistit prostředky Azure, odstraňte skupinu prostředků AKS.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Ověřit připojení

V prohlížeči přejděte na IP adresu front-endu Azure Firewall a ověřte připojení.

Měla by se zobrazit obrázek aplikace s hlasovacími aplikacemi Azure.

## <a name="next-steps"></a>Další kroky

Viz [Přehled služby Azure Networking udr](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Přečtěte si téma [jak vytvořit, změnit nebo odstranit směrovací tabulku](https://docs.microsoft.com/azure/virtual-network/manage-route-table).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
