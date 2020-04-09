---
title: Zabezpečený provoz podu pomocí zásad sítě
titleSuffix: Azure Kubernetes Service
description: Zjistěte, jak zabezpečit provoz, který proudí do a z podů pomocí zásad sítě Kubernetes ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 01ba9e7353b6783d1b4fd1649291a64405fd9382
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886700"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Zabezpečený provoz mezi pody pomocí síťových zásad ve službě Azure Kubernetes Service (AKS)

Při spuštění moderních aplikací založených na mikroslužbách v Kubernetes, často chcete řídit, které součásti mohou komunikovat mezi sebou. Princip nejnižší oprávnění by měla být použita na jak může tok mezi pody v clusteru Služby Azure Kubernetes (AKS). Řekněme, že pravděpodobně budete chtít blokovat provoz přímo do back-endových aplikací. Funkce *Zásady sítě* v Kubernetes umožňuje definovat pravidla pro příchozí a odchozí přenosy mezi pody v clusteru.

Tento článek ukazuje, jak nainstalovat modul zásad sítě a vytvořit zásady sítě Kubernetes pro řízení toku provozu mezi pody v AKS. Zásady sítě by se měly používat pouze pro uzly a pody založené na Linuxu v AKS.

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalované a nakonfigurované azure CLI verze 2.0.61 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

> [!TIP]
> Pokud jste během náhledu použili funkci zásad sítě, doporučujeme [vytvořit nový cluster](#create-an-aks-cluster-and-enable-network-policy).
> 
> Pokud chcete pokračovat v používání existujících testovacích clusterů, které používaly zásady sítě během náhledu, upgradujte cluster na nové verze Kubernetes pro nejnovější verzi GA a pak nasaďte následující manifest YAML, abyste opravili server s chybami metrik y serveru a řídicí panel Kubernetes. Tato oprava je vyžadována pouze pro clustery, které používají modul zásad sítě Calico.
>
> Jako osvědčený postup zabezpečení [zkontrolujte obsah tohoto manifestu YAML,][calico-aks-cleanup] abyste pochopili, co se nasadí do clusteru AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Přehled zásad sítě

Všechny pody v clusteru AKS mohou ve výchozím nastavení odesílat a přijímat přenosy bez omezení. Chcete-li zlepšit zabezpečení, můžete definovat pravidla, která řídí tok provozu. Back-endové aplikace jsou často vystaveny pouze požadované front-endové služby, například. Nebo součásti databáze jsou přístupné pouze pro aplikační vrstvy, které se k nim připojují.

Zásady sítě je specifikace Kubernetes, která definuje zásady přístupu pro komunikaci mezi pody. Pomocí zásad sítě definujete uspořádanou sadu pravidel pro odesílání a přijímání přenosů a jejich použití na kolekci podů, které odpovídají jednomu nebo více selektorům popisků.

Tato pravidla zásad sítě jsou definována jako manifesty YAML. Síťové zásady mohou být zahrnuty jako součást širšího manifestu, který také vytvoří nasazení nebo službu.

### <a name="network-policy-options-in-aks"></a>Možnosti zásad sítě v AKS

Azure poskytuje dva způsoby implementace zásad sítě. Možnost zásad sítě zvolíte při vytváření clusteru AKS. Možnost zásad nelze změnit po vytvoření clusteru:

* Vlastní implementace Azure, nazvaná *Zásady sítě Azure*.
* *Calico Network Policies*, open-source řešení zabezpečení sítě a zabezpečení sítě založené [společností Tigera][tigera].

Obě implementace používají Linux *IPTables* k vynucení zadaných zásad. Zásady jsou přeloženy do sad povolených a nepovolených párů IP. Tyto páry jsou pak naprogramovány jako pravidla filtru IPTable.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Rozdíly mezi zásadami Azure a Calico a jejich možnostmi

| Schopnost                               | Azure                      | Kaliko                      |
|------------------------------------------|----------------------------|-----------------------------|
| Podporované platformy                      | Linux                      | Linux                       |
| Podporované možnosti sítě             | Azure CNI                  | Azure CNI a kubenet       |
| Soulad se specifikací Kubernetes | Všechny podporované typy zásad |  Všechny podporované typy zásad |
| Další funkce                      | Žádný                       | Rozšířený model zásad skládající se z globálních zásad sítě, globální síťové sady a koncového bodu hostitele. Další informace o `calicoctl` použití rozhraní se k dispozici pro správu těchto rozšířených funkcí naleznete v [tématu calicoctl user reference][calicoctl]. |
| Podpora                                  | S podporou týmu podpory Azure a inženýrství | Podpora komunity Calico. Další informace o další placené podpoře naleznete v [tématu Project Calico support options][calico-support]. |
| Protokolování                                  | Přidaná / odstraněná pravidla v IPTables jsou přihlášena ke každému hostiteli pod */var/log/azure-npm.log* | Další informace naleznete [v protokolech komponent Calico][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Vytvoření clusteru AKS a povolení síťových zásad

Chcete-li zobrazit zásady sítě v akci, pojďme vytvořit a rozbalit na zásadu, která definuje tok provozu:

* Odepřít veškerý provoz na pod.
* Povolit provoz na základě popisků pod.
* Povolit provoz na základě oboru názvů.

Nejprve vytvoříme cluster AKS, který podporuje zásady sítě. 

> [!IMPORTANT]
>
> Funkci zásad sítě lze povolit pouze při vytvoření clusteru. Nelze povolit zásady sítě v existujícím clusteru AKS.

Pokud chcete používat zásady sítě Azure, musíte použít [modul plug-in Azure CNI][azure-cni] a definovat vlastní virtuální síť a podsítě. Podrobnější informace o plánování požadovaných rozsahů podsítí naleznete v [tématu Konfigurace pokročilých sítí][use-advanced-networking]. Zásady sítě Calico lze použít buď s tímto stejným modulem plug-in Azure CNI nebo s plug-in Kubenet CNI.

Následující ukázkový skript:

* Vytvoří virtuální síť a podsíť.
* Vytvoří instanční objekt služby Azure Active Directory (Azure AD) pro použití s clusterem AKS.
* Přiřadí oprávnění *přispěvatele* pro objekt zabezpečení clusterové služby AKS ve virtuální síti.
* Vytvoří cluster AKS v definované virtuální síti a povolí zásady sítě.
    * Používá se možnost zásad sítě *Azure.* Chcete-li místo toho použít Calico `--network-policy calico` jako možnost zásad sítě, použijte parametr. Poznámka: Calico lze použít `--network-plugin azure` `--network-plugin kubenet`s jedním nebo .

Poskytněte vlastní bezpečné *SP_PASSWORD*. Proměnné *RESOURCE_GROUP_NAME* a *CLUSTER_NAME* můžete nahradit:

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

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

Vytvoření clusteru bude trvat několik minut. Když je cluster připraven, nakonfigurujte `kubectl` připojení ke clusteru Kubernetes pomocí příkazu [az aks get-credentials.][az-aks-get-credentials] Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového příkazu Kubernetes tak, aby je používalo:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Odepřít veškerý příchozí provoz do podu

Před definováním pravidel umožňujících určitý síťový provoz nejprve vytvořte zásadu sítě, která odepře veškerý provoz. Tato zásada poskytuje výchozí bod začít whitelist pouze požadovaný provoz. Můžete také jasně vidět, že provoz je vynechán, když je použita zásada sítě.

Pro ukázkové aplikační prostředí a pravidla provozu nejprve vytvořme obor názvů nazývaný *vývoj* pro spuštění ukázkových podů:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Vytvořte ukázkový back-end ový pod, který běží NGINX. Tento back-end pod lze simulovat ukázkové back-end webové aplikace. Vytvořte tento pod v oboru názvů *vývoje* a otevřete port *80,* který bude sloužit webovému provozu. Označte pod *pomocí app=webapp,role=backend,* abychom na něj mohli cílit pomocí zásad sítě v další části:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Vytvořte jiný pod a připojte terminálovou relaci k testování, že můžete úspěšně dosáhnout výchozí webové stránky NGINX:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na řádku prostředí `wget` potvrďte, že máte přístup k výchozí webové stránce NGINX:

```console
wget -qO- http://backend
```

Následující ukázkový výstup ukazuje, že výchozí webová stránka NGINX byla vrácena:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončete připojenou terminálovou relaci. Testovací pod je automaticky odstraněn.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Vytvoření a použití zásad sítě

Nyní, když jste potvrdili, že můžete použít základní webovou stránku NGINX na ukázkovém back-endovém podu, vytvořte zásady sítě, které popírají veškerý provoz. Vytvořte soubor `backend-policy.yaml` s názvem a vložte následující manifest YAML. Tento manifest používá *podSelector* k připojení zásady pody, které mají *app:webapp,role:backend* label, jako je ukázkový pod NGINX. V části *příchozí přenos*dat nejsou definována žádná pravidla , takže je odepřen veškerý příchozí provoz do podu:

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

Přejděte [https://shell.azure.com](https://shell.azure.com) na otevření Azure Cloud Shell ve vašem prohlížeči.

Použijte zásady sítě pomocí příkazu [kubectl apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Otestovat zásady sítě

Podívejme se, jestli můžete použít webové stránky NGINX na back-end pod znovu. Vytvořte další testovací modul a připojte terminálovou relaci:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na řádku prostředí `wget` použijte, abyste zjistili, zda máte přístup k výchozí webové stránce NGINX. Tentokrát nastavte hodnotu časového času na *2* sekundy. Zásady sítě nyní blokují veškerý příchozí provoz, takže stránku nelze načíst, jak je znázorněno v následujícím příkladu:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Ukončete připojenou terminálovou relaci. Testovací pod je automaticky odstraněn.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Povolit příchozí provoz na základě popisku podu

V předchozí části byl naplánován back-endový pod NGINX a byla vytvořena síťová zásada, která odepře laušit y veškerý provoz. Pojďme vytvořit front-end pod a aktualizovat zásady sítě povolit provoz z front-endpodů.

Aktualizujte zásady sítě tak, aby přenosy z podů s popisky *app:webapp, role:frontend* a v libovolném oboru názvů. Upravte předchozí soubor *back-end-policy.yaml* a přidejte pravidla příchozího přenosu dat *matchLabels* tak, aby manifest vypadal jako následující příklad:

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
> Tato zásada sítě používá pro pravidlo příchozího přenosu dat prvek *namespaceSelector* a prvek *podSelector.* Syntaxe YAML je důležité pro příchozí ho pravidla, která mají být aditivní. V tomto příkladu musí oba prvky odpovídat pro pravidlo příchozího přenosu dat, které má být použito. Verze Kubernetes před *1.12* nemusí interpretovat tyto prvky správně a omezit síťový provoz podle očekávání. Další informace o tomto chování naleznete v [tématu Chování selektorů do a z][policy-rules].

Použijte aktualizovanou zásadu sítě pomocí příkazu [kubectl apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

Naplánujte pod, který je označen jako *app=webapp,role=frontend* a připojte terminálovou relaci:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Na řádku prostředí `wget` použijte, abyste zjistili, zda máte přístup k výchozí webové stránce NGINX:

```console
wget -qO- http://backend
```

Vzhledem k tomu, že pravidlo příchozího přenosu dat umožňuje provoz s pody, které mají *aplikace popisky: webapp,role: front-end*, je povolen provoz z front-endového podu. Následující ukázkový výstup ukazuje výchozí vrácenou webovou stránku NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončete připojenou terminálovou relaci. Pod je automaticky odstraněn.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testování modulu bez odpovídajícího popisku

Zásady sítě umožňuje provoz z podů označené *aplikace: webapp, role: front-end*, ale měl by odepřít všechny ostatní provoz. Pojďme otestovat, zda jiný pod bez těchto štítků přístup back-end NGINX pod. Vytvořte další testovací modul a připojte terminálovou relaci:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Na řádku prostředí `wget` použijte, abyste zjistili, zda máte přístup k výchozí webové stránce NGINX. Zásady sítě blokují příchozí provoz, takže stránku nelze načíst, jak je znázorněno v následujícím příkladu:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Ukončete připojenou terminálovou relaci. Testovací pod je automaticky odstraněn.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Povolit provoz pouze z definovaného oboru názvů

V předchozích příkladech jste vytvořili zásadu sítě, která odepřela veškerý provoz, a poté jste zásadu aktualizovali tak, aby umožňovala provoz z podů s určitým popiskem. Další běžnou potřebou je omezit provoz pouze v rámci daného oboru názvů. Pokud byly předchozí příklady pro provoz v oboru názvů *vývoje,* vytvořte zásadu sítě, která zabrání přenosu z jiného oboru názvů, například *z produkčního prostředí,* aby se dostaly do podů.

Nejprve vytvořte nový obor názvů pro simulaci produkčního oboru názvů:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Naplánujte testovací pod v *produkčním* oboru názvů, který je označen jako *app=webapp,role=frontend*. Připojte terminálovou relaci:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Na řádku prostředí `wget` potvrďte, že máte přístup k výchozí webové stránce NGINX:

```console
wget -qO- http://backend.development
```

Vzhledem k tomu, že popisky pro pod odpovídají tomu, co je aktuálně povoleno v zásadách sítě, je povolen provoz. Zásady sítě se nedívají na obory názvů, pouze na popisky podů. Následující ukázkový výstup ukazuje výchozí vrácenou webovou stránku NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončete připojenou terminálovou relaci. Testovací pod je automaticky odstraněn.

```console
exit
```

### <a name="update-the-network-policy"></a>Aktualizace zásad sítě

Aktualizujte oddíl *namespace ingress pravidlaSelector* tak, aby povoluje provoz pouze z oboru názvů *vývoje.* Upravte soubor manifestu *back-end-policy.yaml,* jak je znázorněno v následujícím příkladu:

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

Ve složitějších příkladech můžete definovat více pravidel příchozího přenosu dat, jako je *výběr oboru názvů* a potom *podSelector*.

Použijte aktualizovanou zásadu sítě pomocí příkazu [kubectl apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Otestujte aktualizované zásady sítě

Naplánujte jiný pod v oboru názvů *produkční ho distančního* prostředí a připojte terminálovou relaci:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Na řádku prostředí `wget` použijte k zobrazení, že zásady sítě nyní odepře provoz:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Výstup ze zkušebního modulu:

```console
exit
```

S přenosy odepřen z oboru názvů *v produkčním prostředí,* naplánovat testovací pod zpět do oboru názvů *vývoje* a připojit terminálové relace:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Na řádku prostředí `wget` použijte k zobrazení, že zásady sítě umožňují přenos:

```console
wget -qO- http://backend
```

Provoz je povolen, protože pod je naplánováno v oboru názvů, který odpovídá co je povoleno v zásadách sítě. Následující ukázkový výstup ukazuje výchozí vrácenou webovou stránku NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Ukončete připojenou terminálovou relaci. Testovací pod je automaticky odstraněn.

```console
exit
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto článku jsme vytvořili dva obory názvů a použili zásady sítě. Chcete-li tyto prostředky vyčistit, použijte příkaz [kubectl delete][kubectl-delete] a zadejte názvy prostředků:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Další kroky

Další informace o síťových prostředcích najdete [v tématu Síťové koncepty pro aplikace ve službě Azure Kubernetes Service (AKS).][concepts-network]

Další informace o zásadách najdete v tématu [Zásady sítě Kubernetes][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
