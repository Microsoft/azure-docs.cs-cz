---
title: Zabezpečení pod provozu pomocí zásad sítě
titleSuffix: Azure Kubernetes Service
description: Naučte se zabezpečit provoz, který se zachází do lusků, pomocí zásad sítě Kubernetes ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 17e14859ecdfe11872d5b0526d755d01bc1b034a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577848"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Zabezpečení provozu mezi lusky pomocí zásad sítě ve službě Azure Kubernetes Service (AKS)

Když v Kubernetes spustíte moderní aplikace založené na mikroslužbách, často chcete určit, které součásti můžou vzájemně komunikovat. Zásada minimálního oprávnění by měla být použita na to, jak přenos dat může procházet mezi lusky v clusteru služby Azure Kubernetes (AKS). Řekněme, že pravděpodobně chcete zablokovat provoz přímo do back-endové aplikace. Funkce *zásady sítě* v Kubernetes vám umožňuje definovat pravidla pro příchozí a odchozí provoz mezi lusky v clusteru.

V tomto článku se dozvíte, jak nainstalovat modul zásad sítě a vytvořit zásady sítě Kubernetes pro řízení toku provozu mezi lusky v AKS. Zásady sítě by se měly používat jenom pro uzly se systémem Linux a lusky v AKS.

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.61 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

> [!TIP]
> Pokud jste ve verzi Preview použili funkci síťové zásady, doporučujeme [vytvořit nový cluster](#create-an-aks-cluster-and-enable-network-policy).
> 
> Pokud chcete pokračovat v používání stávajících testovacích clusterů, které používaly zásady sítě během verze Preview, upgradujte cluster na nové verze Kubernetes pro nejnovější verzi GA a pak nasaďte následující manifest YAML, abyste opravili selhání serveru metrik a řídicího panelu Kubernetes. Tato oprava se vyžaduje jenom u clusterů, které používaly modul zásad sítě Calico.
>
> Z hlediska zabezpečení je nejvhodnější [zkontrolovat obsah tohoto MANIFESTU YAML][calico-aks-cleanup] a pochopit, co se nasazuje do clusteru AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Přehled zásad sítě

Všechny lusky v clusteru AKS můžou ve výchozím nastavení odesílat a přijímat přenosy bez omezení. Pro zvýšení zabezpečení můžete definovat pravidla, která řídí tok přenosů. Back-endové aplikace se často zveřejňují jenom pro požadované front-endové služby, například. Nebo součásti databáze jsou přístupné pouze pro aplikační vrstvy, které k nim připojovat.

Zásada sítě je specifikace Kubernetes, která definuje zásady přístupu pro komunikaci mezi lusky. Pomocí zásad sítě můžete definovat uspořádanou sadu pravidel pro odesílání a příjem provozu a použít je pro kolekci lusků, která odpovídá jednomu nebo více selektorům popisku.

Tato pravidla zásad sítě se definují jako YAML manifesty. Zásady sítě je možné zahrnout do širšího manifestu, který také vytváří nasazení nebo službu.

### <a name="network-policy-options-in-aks"></a>Možnosti zásad sítě v AKS

Azure poskytuje dva způsoby, jak implementovat zásady sítě. Při vytváření clusteru AKS zvolíte možnost Zásady sítě. Po vytvoření clusteru se možnost Zásady nedá změnit:

* Vlastní implementace Azure označovaná jako *zásady sítě Azure*.
* *Calico zásady sítě*, což je open source řešení zabezpečení sítě a sítě založené na [Tigera][tigera].

Obě implementace používají Linux *softwaru iptables* k vykonání zadaných zásad. Zásady jsou přeloženy do sad povolených a nepovolených párů IP adres. Tyto páry se pak naprogramují jako pravidla filtru IPTable.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Rozdíly mezi zásadami Azure a Calico a jejich funkcemi

| Schopnost                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Podporované platformy                      | Linux                      | Linux, Windows Server 2019 (Preview)  |
| Podporované síťové možnosti             | CNI Azure                  | Azure CNI (Windows Server 2019 a Linux) a kubenet (Linux)  |
| Dodržování předpisů pomocí specifikace Kubernetes | Všechny podporované typy zásad |  Všechny podporované typy zásad |
| Další funkce                      | Žádné                       | Model rozšířených zásad skládající se z globálních síťových zásad, globální síťové sady a koncového bodu hostitele. Další informace o použití rozhraní příkazového `calicoctl` řádku ke správě těchto rozšířených funkcí naleznete v tématu [calicoctl User reference][calicoctl]. |
| Podpora                                  | Podporováno technickou podporou a technickým týmem pro Azure | Podpora komunity Calico. Další informace o další placené podpoře najdete v tématu [Možnosti podpory pro Project Calico][calico-support]. |
| protokolování                                  | Pravidla přidaná/Odstraněná v softwaru iptables se protokolují na všech hostitelích pod */var/log/Azure-npm.log* . | Další informace najdete v tématu [protokoly komponent Calico][calico-logs] . |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Vytvoření clusteru AKS a povolení zásad sítě

Pokud chcete zobrazit zásady sítě v akci, vytvoříme a pak rozbalíme zásadu, která definuje tok přenosů:

* Odepřít veškerý provoz do pod.
* Povoluje provoz na základě popisků pod.
* Povolte provoz založený na oboru názvů.

Nejdřív vytvořte cluster AKS, který podporuje zásady sítě.

> [!IMPORTANT]
>
> Funkce zásady sítě se dá povolit, jenom když je cluster vytvořený. V existujícím clusteru AKS nemůžete povolit síťové zásady.

Pokud chcete používat zásady sítě Azure, musíte použít [modul plug-in Azure CNI][azure-cni] a definovat vlastní virtuální síť a podsítě. Podrobnější informace o tom, jak naplánovat požadované rozsahy podsítí, najdete v tématu [Konfigurace pokročilých sítí][use-advanced-networking]. Zásady sítě Calico se daly použít buď s tímto stejným modulem plug-in Azure CNI, nebo s modulem plug-in Kubenet CNI.

Následující vzorový skript:

* Vytvoří virtuální síť a podsíť.
* Vytvoří instanční objekt služby Azure Active Directory (Azure AD) pro použití s clusterem AKS.
* Přiřadí oprávnění *přispěvatele* pro objekt služby Cluster AKS ve virtuální síti.
* Vytvoří v definované virtuální síti cluster AKS a povolí zásady sítě.
    * Použije se možnost zásady _sítě Azure_ . Pokud chcete místo toho použít Calico jako zásadu sítě, použijte `--network-policy calico` parametr. Poznámka: Calico lze použít s buď `--network-plugin azure` nebo `--network-plugin kubenet` .

Všimněte si, že místo použití instančního objektu můžete pro oprávnění použít spravovanou identitu. Další informace najdete v tématu [použití spravovaných identit](use-managed-identity.md).

Zadejte vlastní zabezpečený *sp_password*. Můžete nahradit proměnné *RESOURCE_GROUP_NAME* a *CLUSTER_NAME* :

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)
```

### <a name="create-an-aks-cluster-for-azure-network-policies"></a>Vytvoření clusteru AKS pro zásady sítě Azure

Vytvořte cluster AKS a zadejte virtuální síť, informace o instančním objektu a *Azure* pro modul plug-in sítě a zásady sítě.

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-plugin azure \
    --network-policy azure
```

Vytvoření clusteru bude trvat několik minut. Až bude cluster připravený, nakonfigurujte `kubectl` pro připojení ke clusteru Kubernetes pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] . Tento příkaz stáhne přihlašovací údaje a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

### <a name="create-an-aks-cluster-for-calico-network-policies"></a>Vytvoření clusteru AKS pro zásady sítě Calico

Vytvořte cluster AKS a zadejte virtuální síť, informace o instančním objektu, službu *Azure* pro modul plug-in sítě a *Calico* pro zásady sítě. Použití *Calico* jako zásady sítě umožňuje sítě Calico na fondech uzlů Linux i Windows.

Pokud plánujete přidat do clusteru fondy uzlů Windows, zahrňte `windows-admin-username` `windows-admin-password` parametry a, které splňují požadavky na heslo k [Windows serveru][windows-server-password]. Chcete-li používat Calico s fondy uzlů Windows, je také nutné zaregistrovat `Microsoft.ContainerService/EnableAKSWindowsCalico` .

Zaregistrujte `EnableAKSWindowsCalico` příznak funkce pomocí příkazu [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAKSWindowsCalico"
```

 Stav registrace můžete zjistit pomocí příkazu [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAKSWindowsCalico')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> V tuto chvíli je použití zásad sítě Calico s uzly Windows k dispozici pro nové clustery pomocí Kubernetes verze 1,20 nebo novější s Calico 3.17.2 a vyžaduje použití Azure CNI Networking. Uzly Windows v clusterech AKS s povoleným Calico mají taky [přímý návratový Server (DSR)][dsr] , který je ve výchozím nastavení povolený.
>
> U clusterů s pouze fondy uzlů pro Linux se systémem Kubernetes 1,20 a staršími verzemi Calico bude verze Calico automaticky upgradována na 3.17.2.

Zásady sítě Calico s uzly Windows jsou momentálně ve verzi Preview.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Vytvořte uživatelské jméno, které se použije jako přihlašovací údaje správce pro kontejnery Windows serveru v clusteru. Následující příkazy zobrazí výzvu k zadání uživatelského jména a nastaví WINDOWS_USERNAME pro použití v pozdějším příkazu (Nezapomeňte, že příkazy v tomto článku jsou zadány do prostředí BASH).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.2 \
    --network-plugin azure \
    --network-policy calico
```

Vytvoření clusteru bude trvat několik minut. Ve výchozím nastavení se cluster vytvoří jenom s fondem uzlů Linux. Pokud chcete používat fondy uzlů Windows, můžete je přidat. Například:

```azurecli
az aks nodepool add \
    --resource-group $RESOURCE_GROUP_NAME \
    --cluster-name $CLUSTER_NAME \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Až bude cluster připravený, nakonfigurujte `kubectl` pro připojení ke clusteru Kubernetes pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] . Tento příkaz stáhne přihlašovací údaje a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Odepřít veškerý příchozí provoz do pod

Před definováním pravidel pro povolení konkrétního síťového provozu vytvořte nejprve zásadu sítě, která zamítne veškerý provoz. Tato zásada vám poskytne výchozí bod pro začátek vytváření povolených jenom pro požadovaný provoz. Můžete také jasně vidět, že při použití zásad sítě dojde k přerušení provozu.

V případě ukázkových prostředí aplikace a pravidel přenosů nejprve vytvoříme obor názvů s názvem *vývoj* pro spuštění příkladu lusků:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Vytvořte příklad back-endu pod, na kterém běží NGINX. Pomocí tohoto back-endu se dá simulovat Ukázková webová aplikace v back-endu. Vytvořte tuto položku pod oborem názvů pro *vývoj* a otevřete port *80* pro obsloužení webového provozu. Popište ho jako *App = WebApp, role = back-end* , abyste se mohli na něj zaměřit pomocí zásad sítě v následující části:

```console
kubectl run backend --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --labels app=webapp,role=backend --namespace development --expose --port 80
```

Vytvořte další pod a připojte relaci terminálu k otestování, jestli můžete úspěšně dosáhnout výchozí webové stránky NGINX:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Na příkazovém řádku prostředí použijte `wget` k potvrzení, že máte přístup k výchozí webové stránce Nginx:

```console
wget -qO- http://backend
```

Následující vzorový výstup ukazuje, že se vrátila výchozí webová stránka NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončete připojenou relaci Terminálové programu. Test pod je automaticky odstraněn.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Vytvoření a použití zásad sítě

Teď, když jste se potvrdili, že můžete použít základní webovou stránku NGINX na ukázkovém back-endu pod, vytvořit zásadu sítě, která zamítne veškerý provoz. Vytvořte soubor s názvem `backend-policy.yaml` a vložte následující manifest YAML. Tento manifest používá *podSelector* k připojení zásady k luskům, které mají *aplikaci: WebApp, role: back-end* , jako je ukázka Nginx pod. V rámci příchozího přenosu nejsou definována žádná pravidla, takže veškerý příchozí provoz do *uzlu pod je* odepřen:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

[https://shell.azure.com](https://shell.azure.com)V prohlížeči otevřete Azure Cloud Shell.

Použijte zásady sítě pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testování zásad sítě

Pojďme se podívat, jestli můžete použít webovou stránku NGINX v back-endu pod. Vytvořte další test pod a připojte relaci terminálu:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Na příkazovém řádku prostředí použijte `wget` k zobrazení, zda máte přístup k výchozí webové stránce Nginx. Tentokrát nastavte hodnotu časového limitu na *2* sekund. Zásada sítě teď blokuje veškerý příchozí provoz, takže stránku nejde načíst, jak je znázorněno v následujícím příkladu:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Ukončete připojenou relaci Terminálové programu. Test pod je automaticky odstraněn.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Povolit příchozí provoz na základě popisku pod

V předchozí části se naplánoval back-end NGINX pod tím, že se vytvořila zásada sítě pro zamítnutí veškerého provozu. Pojďme vytvořit front-end pod a aktualizovat zásady sítě tak, aby umožňovaly provoz z front-endovéch lusků.

Aktualizujte zásady sítě tak, aby povolovaly provoz z lusků s aplikací Labels *: WebApp, role: front-end* a v jakémkoli oboru názvů. Upravte předchozí soubor *back-end-Policy. yaml* a přidejte *matchLabels* pravidla pro příchozí, aby váš manifest vypadal jako v následujícím příkladu:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Tato zásada sítě používá *namespaceSelector* a element *podSelector* pro pravidlo příchozího přenosu dat. Syntaxe YAML je důležitá pro doplňková pravidla příchozího přenosu dat. V tomto příkladu musí oba elementy odpovídat pro použití pravidla příchozího přenosu dat. Verze Kubernetes starší než *1,12* nemusí tyto prvky správně interpretovat a omezit síťový provoz podle očekávání. Další informace o tomto chování najdete v tématu [chování a od selektorů][policy-rules].

Použijte aktualizované zásady sítě pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

Naplánujte pod označený jako *App = WebApp, role = front-end* a připojte relaci terminálu:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

Na příkazovém řádku prostředí použijte `wget` k zobrazení, zda máte přístup k výchozí webové stránce Nginx:

```console
wget -qO- http://backend
```

Vzhledem k tomu, že pravidlo příchozího přenosu dat povoluje provoz s lusky, které mají aplikaci Labels *: WebApp, role: front*-end, je povolen provoz z front-endu pod. Následující příklad výstupu ukazuje výchozí webovou stránku NGINX, která se vrátila:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončete připojenou relaci Terminálové programu. Pole pod je automaticky odstraněno.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Test pod bez odpovídajícího popisku

Zásady sítě umožňují provoz z lusků s označením *aplikace: WebApp, role: front-end*, ale měla by Odepřít všechny ostatní přenosy. Pojďme se podívat, jestli jiný pod ním bez těchto popisků může mít přístup k back-endové NGINX pod. Vytvořte další test pod a připojte relaci terminálu:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Na příkazovém řádku prostředí použijte `wget` k zobrazení, zda máte přístup k výchozí webové stránce Nginx. Zásada sítě blokuje příchozí provoz, takže nelze načíst stránku, jak je znázorněno v následujícím příkladu:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Ukončete připojenou relaci Terminálové programu. Test pod je automaticky odstraněn.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Povolení provozu pouze v rámci definovaného oboru názvů

V předchozích příkladech jste vytvořili zásadu sítě, která odepřela veškerý provoz, a pak aktualizovali zásady tak, aby povolovala provoz z lusků s určitým popiskem. Další běžnou potřebou je omezit provoz jenom na v rámci daného oboru názvů. Pokud předchozí příklady byly pro provoz v oboru názvů pro *vývoj* , vytvořte zásady sítě, které brání provozu z jiného oboru názvů, jako je například *Výroba*, od dosažení lusků.

Nejdřív vytvořte nový obor názvů pro simulaci produkčního oboru názvů:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Naplánujte test pod v *produkčním* oboru názvů, který je označený jako *App = WebApp, role = front-endu*. Připojit relaci terminálu:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

Na příkazovém řádku prostředí použijte `wget` k potvrzení, že máte přístup k výchozí webové stránce Nginx:

```console
wget -qO- http://backend.development
```

Vzhledem k tomu, že se jmenovky pod shodují s tím, co je aktuálně povoleno v zásadách sítě, je povolen provoz. Zásada sítě se nezobrazuje na oborech názvů, jenom na jmenovky pod. Následující příklad výstupu ukazuje výchozí webovou stránku NGINX, která se vrátila:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončete připojenou relaci Terminálové programu. Test pod je automaticky odstraněn.

```console
exit
```

### <a name="update-the-network-policy"></a>Aktualizace zásad sítě

Pojďme aktualizovat oddíl *namespaceSelector* pravidla příchozího přenosu dat, aby povoloval jenom přenosy z oboru názvů pro *vývoj* . Upravte soubor manifestu *back-end-Policy. yaml* , jak je znázorněno v následujícím příkladu:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

V složitějších příkladech můžete definovat více pravidel příchozího přenosu dat, jako je *namespaceSelector* , a pak *podSelector*.

Použijte aktualizované zásady sítě pomocí příkazu [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Otestování aktualizovaných síťových zásad

Naplánujte další pod v *produkčním* oboru názvů a připojte relaci terminálu:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

Na příkazovém řádku prostředí použijte `wget` k zobrazení, že síťové zásady nyní zakazuje provoz:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Ukončete test pod:

```console
exit
```

Když se provoz zamítl z *produkčního* oboru názvů, naplánujte ho zpátky v oboru názvů pro *vývoj* a připojte relaci Terminálové služby:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

Na příkazovém řádku prostředí použijte `wget` k tomu, abyste viděli, že síťové zásady povolují přenosy:

```console
wget -qO- http://backend
```

Provoz je povolen, protože v oboru názvů je naplánováno, které odpovídá tomu, co je v zásadách sítě povoleno. Následující vzorový výstup ukazuje výchozí webovou stránku NGINX, která se vrátila:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončete připojenou relaci Terminálové programu. Test pod je automaticky odstraněn.

```console
exit
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto článku jsme vytvořili dva obory názvů a použili jste zásady sítě. K vyčištění těchto prostředků použijte příkaz [kubectl Delete][kubectl-delete] a zadejte názvy prostředků:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Další kroky

Další informace o síťových prostředcích najdete v tématu [Koncepty sítě pro aplikace ve službě Azure Kubernetes Service (AKS)][concepts-network].

Další informace o zásadách najdete v tématu [zásady sítě Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/maintenance/troubleshoot/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip