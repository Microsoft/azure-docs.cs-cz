---
title: Otevřít síť služby (Preview)
description: Otevřená síť (OSM) ve službě Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 0052c8d2f9b85c34d50a3e9d01253ecaf2d02bab
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106709"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Otevřít doplněk AKS pro síť služby (Preview)

## <a name="overview"></a>Přehled

[Otevřená síť (osm)](https://docs.openservicemesh.io/) je odlehčená a rozšiřitelná cloudová cloudová služba, která umožňuje uživatelům jednotně spravovat, zabezpečit a získávat funkce pozorování pro vysoce dynamická prostředí mikroslužeb.

OSM spustí na Kubernetes rovinu ovládacího prvku založené na zástupné, dá se nakonfigurovat s rozhraními API [SMI](https://smi-spec.io/) a funguje tak, že se proxy server zástupné vloží jako kontejner vozíku vedle každé instance aplikace. Proxy zástupné obsahuje a spouští pravidla týkající se zásad řízení přístupu, implementuje konfiguraci směrování a zachycuje metriky. Rovina ovládacího prvku průběžně konfiguruje proxy, aby bylo zajištěno, že zásady a pravidla směrování jsou aktuální a že jsou servery proxy v dobrém stavu.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Možnosti a funkce

OSM poskytuje následující sadu funkcí a funkcí, které poskytují cloudovou nativní službu pro vaše clustery Azure Kubernetes Service (AKS):

- Zabezpečená komunikace mezi službami a povolením mTLS

- Snadné připojení aplikací k síti tím, že povolíte automatické vkládání postranního vozíku zástupné proxy

- Snadné a transparentní konfigurace pro přenos dat při nasazení

- Možnost definovat a spustit jemně odstupňované zásady řízení přístupu pro služby

- Pozorování a přehledy metriky aplikací pro služby ladění a monitorování

- Integrace s externími službami Certificate Management Services/řešení s připojeným rozhraním

## <a name="scenarios"></a>Scénáře

OSM může pomáhat vašim AKS nasazením v těchto scénářích:

- Poskytování šifrované komunikace mezi koncovými body služby nasazenými v clusteru

- Ověřování provozu přes protokol HTTP/HTTPS i přenos TCP do sítě

- Konfigurace váženého řízení provozu mezi dvěma nebo více službami pro nasazení A/B nebo Kanárské

- Shromažďování a zobrazování klíčových ukazatelů výkonu z provozu aplikace

## <a name="osm-service-quotas-and-limits-preview"></a>Kvóty a limity služby OSM (Preview)

Omezení OSM verze Preview pro kvóty služeb a omezení najdete na [stránce kvóty AKS a místní omezení](https://docs.microsoft.com/azure/aks/quotas-skus-regions).

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/install-osm-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/install-osm-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/install-osm-binary-windows.md)]

::: zone-end

> [!WARNING]
> Nepokoušejte se instalovat OSM z binárního souboru pomocí `osm install` . Výsledkem bude instalace OSM, která není integrovaná jako doplněk pro AKS.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>Registrace `AKS-OpenServiceMesh` funkce Preview

Pokud chcete vytvořit cluster AKS, který může používat doplněk otevřít síť, musíte `AKS-OpenServiceMesh` u svého předplatného povolit příznak funkce.

Příznak funkce Zaregistrujte `AKS-OpenServiceMesh` pomocí příkazu [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Zobrazení stavu v _registraci_ trvá několik minut. Pomocí příkazu [AZ Feature list][az-feature-list] ověřte stav registrace:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků _Microsoft. ContainerService_ pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Instalace doplňku Azure Kubernetes Service (AKS) pro nový cluster AKS (OSM)

Pro nový scénář nasazení clusteru AKS začínáte značkou nového nasazení clusteru AKS a povolíte doplněk OSM v rámci operace CREATE elementu cluster.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

V Azure přidělíte související prostředky skupině prostředků. Vytvořte skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků s názvem _myOsmAksGroup_ v umístění _eastus2_ (region):

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Nasazení clusteru AKS s povoleným doplňkem OSM

Nyní nasadíte nový cluster AKS s povoleným doplňkem OSM.

> [!NOTE]
> Uvědomte si prosím, že následující příkaz pro nasazení AKS využívá dočasné disky s operačním systémem. Další informace o [dočasných discích s operačním systémem pro AKS najdete v](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os) tématu.

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Získat přihlašovací údaje pro přístup ke clusteru AKS

Získá přihlašovací údaje pro přístup k novému spravovanému clusteru Kubernetes.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Povolit doplněk Azure Kubernetes Service (AKS) pro existující cluster AKS pro OSM (Open Service oky)

Pro existující scénář clusteru AKS povolíte doplněk OSM ke stávajícímu clusteru AKS, který už je nasazený.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Povolení doplňku OSM pro existující cluster AKS

Pokud chcete povolit doplněk AKS OSM, budete muset spustit příkaz, který `az aks enable-addons --addons` předáte parametru. `open-service-mesh`

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

Měl by se zobrazit výstup podobný následujícímu výstupu pro potvrzení, že doplněk AKS OSM byl nainstalován.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>Ověřit instalaci doplňku OSM pro AKS

K dispozici je několik příkazů ke kontrole všech komponent doplňku AKS OSM, které jsou povoleny a spuštěny:

Nejdřív můžeme zadat dotaz na profily doplňků clusteru a ověřit tak stav povolených doplňků. Následující příkaz by měl vrátit hodnotu "true".

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Následující `kubectl` příkazy oznámí stav osm-Controller.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>Přístup k doplňku AKS OSM

V současné době máte přístup k konfiguraci kontroleru OSM pomocí configmap a ke konfiguraci řadiče. Chcete-li zobrazit nastavení konfigurace řadiče OSM, proveďte dotaz na osm-config configmap prostřednictvím `kubectl` pro zobrazení nastavení konfigurace.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Výstup OSM configmap by měl vypadat takto:

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254/32,168.63.129.16/32,<YOUR_API_SERVER_PUBLIC_IP>/32",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Všimněte si, že **permissive_traffic_policy_mode** je nakonfigurován na **hodnotu true**. Režim zásad povolujících přenosů v OSM je režim, ve kterém se vynucuje vynucování zásad provozu [SMI](https://smi-spec.io/) . V tomto režimu OSM automaticky zjišťuje služby, které jsou součástí pravidel zásad provozu sítě a programů pro všechny servery proxy zástupné, aby mohly komunikovat s těmito službami.

> [!WARNING]
> Než budete pokračovat, ověřte prosím, jestli je režim zásad povolující provoz nastavený na true, pokud ne, změňte ho na **true** pomocí příkazu níže.

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Nasazení nové aplikace, která bude spravovaná pomocí OSM (Open Service New) Azure Kubernetes Service (AKS)

### <a name="before-you-begin"></a>Než začnete

V krocích popsaných v tomto článku se předpokládá, že jste vytvořili cluster AKS (Kubernetes `1.19+` a vyšší s povoleným KUBERNETES RBAC), navázali jste `kubectl` připojení ke clusteru (Pokud potřebujete pomáhat s kteroukoli z těchto položek, najdete je v článku [rychlý Start AKS](./kubernetes-walkthrough.md)a nainstalovali jste doplněk AKS osm.

Musíte mít nainstalované následující zdroje:

- Rozhraní příkazového řádku Azure, verze 2.20.0 nebo novější
- `aks-preview`Verze rozšíření 0.5.5 nebo novější
- OSM verze v 0.8.0 nebo novější
- apt – získat JQ instalace

### <a name="create-namespaces-for-the-application"></a>Vytvořit obory názvů pro aplikaci

V tomto návodu použijeme aplikaci OSM Bookstore, která má následující služby Kubernetes:

- bookbuyer
- bookthief
- knihkupectví
- bookwarehouse

Vytvořte obory názvů pro každou z těchto součástí aplikace.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Měl by se zobrazit následující výstup:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Připojit obory názvů, které se mají spravovat pomocí OSM

Když přidáte obory názvů do sítě OSM, umožníte tak, aby řadič OSM automaticky vložil kontejnery proxy zástupné vozíku do vaší aplikace. Spuštěním následujícího příkazu připojte obory názvů aplikace OSM Bookstore.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Měl by se zobrazit následující výstup:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Nasazení aplikace Bookstore do clusteru AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Všechny výstupy nasazení jsou shrnuty níže.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="checkpoint-what-got-installed"></a>Kontrolní bod: co je nainstalované?

Ukázková aplikace Bookstore je Vícevrstvá aplikace, která se skládá ze čtyř služeb, je bookbuyer, bookthief, Bookstore a bookwarehouse. Služba bookbuyer i bookthief komunikuje se službou Bookstore, aby získala knihy ze služby Bookstore. Služba Bookstore načítá do služby bookwarehouse Books, která poskytuje bookbuyer a bookthief. Toto je jednoduchá Vícevrstvá aplikace, která dobře funguje při zobrazení, jak se dá síť použít k ochraně a autorizaci komunikace mezi aplikačními službami. Jak pokračujeme v tomto návodu, povolíme a zakážete zásadám rozhraní sítě pro připojení k síti (SMI) možnost povolit a zakázat služby pro komunikaci přes OSM. Níže je diagram architektury toho, co je nainstalované pro aplikaci v knihkupectví.

![Architektura aplikace OSM bookbuyer](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Ověření aplikace v knihkupectví běžícím v rámci clusteru AKS

Od teď jsme nasadili aplikaci Bookstore Mulit-Container, ale je dostupná jenom v rámci clusteru AKS. Novější kurzy vám pomůžou při vystavení aplikace mimo cluster prostřednictvím řadiče příchozího přenosu dat. Teď budeme používat předávání portů pro přístup k aplikaci bookbuyer v clusteru AKS, abyste ověřili, že se kupuje knihy ze služby Bookstore.

Pokud chcete ověřit, jestli je aplikace spuštěná v rámci clusteru, použijeme k zobrazení uživatelského rozhraní komponent bookbuyer i bookthief port.

Nejprve získáme jméno bookbuyer pod.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Výstup by se měl podobat následujícímu. K vašemu bookbuyer pod se připojí jedinečný název.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Jakmile budeme mít název pod, teď k nastavení tunelu z našeho místního systému do aplikace v clusteru AKS můžete použít příkaz port-dopředně. Spusťte následující příkaz pro nastavení portu jako předávacího pro port místního systému 8080. Znovu použijte zadaný název bookbuyer pod.

> [!NOTE]
> Pro všechny příkazy předávání portů je nejvhodnější použít další terminál, abyste mohli pokračovat v práci s tímto návodem a nemusíte odpojit tunel. Je také nejlepší vytvořit tunelové propojení portů mimo Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Měl by se zobrazit výstup podobný tomuto.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

I když je relace předávání portů zavedena, přejděte v prohlížeči na následující adresu URL `http://localhost:8080` . Nyní byste měli být schopni zobrazit uživatelské rozhraní aplikace bookbuyer v prohlížeči podobně jako na obrázku níže.

![Obrázek uživatelského rozhraní aplikace OSM bookbuyer](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Také si všimnete, že celkový počet zakoupených knih se dál zvyšuje na službu Bookstore v1. Služba Bookstore v2 ještě není nasazená. Pokud předvádíme zásady rozdělení provozu SMI, nasadíme službu Bookstore v2.

Můžete také zaškrtnout stejné pro službu bookthief.

```azurecli-interactive
kubectl get pod -n bookthief
```

Výstup by se měl podobat následujícímu. K vašemu bookthief pod se připojí jedinečný název.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Port předaný na bookthief pod.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

V prohlížeči přejděte na následující adresu URL `http://localhost:8080` . Měli byste vidět, že bookthief aktuálně ukrást knihy ze služby Bookstore. Později budeme implementovat zásady provozu, které bookthief zastaví.

![Obrázek uživatelského rozhraní aplikace OSM bookthief](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Zakázat režim provozu s povoleným OSM pro síť

Jak bylo zmíněno dříve při zobrazení konfigurace clusteru OSM, konfigurace OSM standardně povoluje povolující zásady režimu provozu. V tomto režimu se vynucování zásad provozu vynechává a OSM automaticky zjišťuje služby, které jsou součástí pravidel zásad provozu sítě a programů pro každý zástupné proxy serveru, aby mohly komunikovat s těmito službami.

Nyní zakážeme zásadu Povolit režim provozu a OSM bude potřebovat explicitní zásady [SMI](https://smi-spec.io/) nasazené do clusteru, aby bylo možné povolit komunikaci v síti z každé služby. Chcete-li zakázat povolující režim provozu, spusťte následující příkaz, který aktualizuje vlastnost configmap, která mění hodnotu z `true` na `false` .

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

Výstup by se měl podobat následujícímu. K vašemu bookthief pod se připojí jedinečný název.

```Output
configmap/osm-config patched
```

Aby se ověřilo, že byl zakázán režim přenosu dat, přepošle se port zpátky na bookbuyer nebo bookthief pod a zobrazí se uživatelské rozhraní v prohlížeči a zjistí, jestli se už nezvyšuje platnost knih zakoupených v knihách Ujistěte se, že jste aktualizovali prohlížeč. Pokud se přírůstek zastavil, zásada se použila správně. Úspěšně jste zastavili bookthief z ukradení knih, ale ani bookbuyer si ho z Bookstore nemůžete koupit ani kniha Bookstore nemůže získat knihy z bookwarehouse. Dále budeme implementovat zásady [SMI](https://smi-spec.io/) , aby byly povoleny pouze služby v síti, na kterou chcete komunikovat.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Použití zásad přístupu k provozu rozhraní sítě SMI (Service)

Teď, když jsme zakázali veškerou komunikaci v síti, můžeme naší službě bookbuyer komunikovat se službou Bookstore pro nakupování knih a umožnit naší službě v rámci služby Bookstore komunikovat s naší službou bookwarehouse, aby mohla načítat knihy k prodeji.

Nasaďte následující zásady [SMI](https://smi-spec.io/) .

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

Výstup by se měl podobat následujícímu.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

Teď můžete nastavit relaci předávání portů v bookbuyer nebo v knihkupectvích a podívat se, jak se účtují i metriky, které se prodávají v knihách. To samé můžete provést i pro bookthief pod, abyste ověřili, že již nadále není schopen ukrást knihy.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Použití zásad rozdělení provozu rozhraní sítě SMI (Service)

Pro naši poslední ukázku vytvoříme zásadu rozdělení provozu [SMI](https://smi-spec.io/) pro konfiguraci váhy komunikace z jedné služby na více služeb jako back-endu. Funkce rozdělení provozu vám umožní postupně přesunout připojení do jedné služby na jinou a vyvážením provozu na škále od 0 do 100.

Níže uvedený obrázek je diagramem zásad rozdělení provozu [SMI](https://smi-spec.io/) , které se mají nasadit. Nasadíme další Bookstore verze 2 a potom rozdělíme příchozí provoz z bookbuyer, vážením 25% provozu do služby Bookstore V1 a 75% do služby Bookstore v2.

![Diagram rozděleného provozu OSM bookbuyer](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Nasaďte službu Bookstore v2.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
EOF
```

Měli byste vidět následující výstup.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

Teď nasaďte zásady rozdělení provozu a rozdělte bookbuyer provoz mezi dvě služby Bookstore V1 a v2.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

Měli byste vidět následující výstup.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Nastavte port pro přesměrovávání tunel na bookbuyer pod a teď byste měli vidět knihy zakoupené ze služby Bookstore v2. Pokud budete dál sledovat přírůstek nákupu, měli byste si všimnout rychlejšího zvýšení počtu nákupů, které probíhají prostřednictvím služby Bookstore v2.

![Uživatelské rozhraní OSM bookbuyer Books boough](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Spravovat existující nasazené aplikace, které se mají spravovat přes OSM (Open Service a AKS) doplněk Azure Kubernetes Service ()

### <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto návodu předpokládají, že jste předtím povolili doplněk OSM AKS pro váš cluster AKS. Pokud ne, přečtěte si část [Povolení osm (Open Service Grid) pro existující cluster AKS](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) a teprve potom pokračujte. Cluster AKS musí mít taky verzi Kubernetes `1.19+` a vyšší, musí mít povolený KUBERNETES RBAC a navázat `kubectl` spojení s clusterem (Pokud potřebujete pomáhat s kteroukoli z těchto položek, najdete je v článku [rychlý Start AKS](./kubernetes-walkthrough.md)a nainstalovali jste doplněk AKS osm.

Musíte mít nainstalované následující zdroje:

- Rozhraní příkazového řádku Azure, verze 2.20.0 nebo novější
- `aks-preview`Verze rozšíření 0.5.5 nebo novější
- OSM verze v 0.8.0 nebo novější
- apt – získat JQ instalace

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Ověření povolující zásady provozu v režimu Open Service (OSM)

Režim zásad pro řízení provozu OSM je režim, ve kterém se vynucuje vynucování zásad provozu [SMI](https://smi-spec.io/) . V tomto režimu OSM automaticky zjišťuje služby, které jsou součástí pravidel zásad provozu sítě a programů pro všechny servery proxy zástupné, aby mohly komunikovat s těmito službami.

Pokud chcete ověřit aktuální režim OSM provozu pro váš cluster, spusťte následující příkaz:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Výstup OSM configmap by měl vypadat takto:

```Output
{
  "egress": "true",
  "enable_debug_server": "true",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Pokud je **permissive_traffic_policy_mode** nakonfigurované na **hodnotu true**, můžete bezpečně připojit své obory názvů bez přerušení komunikace mezi službami. Pokud je **permissive_traffic_policy_mode** nakonfigurované na **hodnotu false**, budete muset zajistit, aby byly nasazeny správné manifesty zásad přístupu [SMI](https://smi-spec.io/) a zároveň aby se zajistilo, že máte účet služby reprezentující každou službu nasazenou v oboru názvů. Postupujte prosím podle pokynů pro připojení [existujících nasazených aplikací s využitím zásad povolujících přenosů otevřených sítí (osm) s nakonfigurovanou hodnotou false](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false) .

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Připojte existující nasazené aplikace s využitím zásad povolujících přenosů OSM (Open Service) nakonfigurovaných na hodnotu true.

První věc přidáme nasazené obory názvů aplikace do OSM ke správě.

```azurecli-interactive
osm namespace add bookstore
```

Měl by se zobrazit následující výstup:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

V dalším kroku se podíváme na aktuální nasazení pod oborem názvů. Spuštěním následujícího příkazu Zobrazte lusky v určeném oboru názvů.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Měl by se zobrazit následující podobný výstup:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Všimněte si, že **připravený** sloupec zobrazuje **1/1**, což znamená, že aplikace pod má pouze jeden kontejner. Dále bude nutné restartovat nasazení vaší aplikace, aby OSM mohli vložit kontejner proxy zástupné vozíku do vaší aplikace pod. Pojďme získat seznam nasazení v oboru názvů.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Měl by se zobrazit následující výstup:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

Teď znovu spustíme nasazení a vložíme kontejner proxy zástupné vozíku do vaší aplikace pod. Spusťte následující příkaz.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

Měl by se zobrazit následující výstup:

```Output
deployment.apps/bookbuyer restarted
```

Pokud se podíváme na lusky v oboru názvů znovu:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Nyní si všimnete, že ve sloupci **připraveno** se teď zobrazují **2/2** kontejnery připravené k vašemu poli pod. Druhý kontejner je proxy Zástupnéový postranný.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

Dále si můžete prohlédnout pod tím, že se zobrazí proxy zástupné spuštěním příkazu popis pro zobrazení konfigurace.

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

Ověřte, že je aplikace pořád funkční po injektáže proxy zástupné vozíku.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Připojte existující nasazené aplikace s využitím zásad povolujících provozování sítě (OSM) s nakonfigurovanou hodnotou false.

Když je konfigurace OSM pro zásady povolujících přenosů nastavená na hodnotu `false` , osm bude vyžadovat explicitní zásady přístupu k provozu [SMI](https://smi-spec.io/) nasazené pro komunikaci mezi službami a clusterem. V současné době používá OSM také účty služby Kubernetes v rámci autorizace komunikace mezi službami a službami. Aby se zajistilo, že existující nasazené aplikace budou komunikovat, když je spravuje síť OSM, musíme ověřit existenci účtu služby, který se má využít, aktualizovat nasazení aplikace pomocí informací o účtu služby, použít zásady přístupu [SMI](https://smi-spec.io/) pro přenos.

#### <a name="verify-kubernetes-service-accounts"></a>Ověření účtů služby Kubernetes

Ověřte, jestli máte účet služby Kubernetes v oboru názvů, do kterého jste aplikaci nasadili.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

V následujícím seznamu je k dispozici účet služby s názvem `bookbuyer` v oboru názvů bookbuyer.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Pokud nemáte účet služby uvedený na jiném než výchozím účtu, budete ho muset pro svoji aplikaci vytvořit. Použijte následující příkaz jako příklad pro vytvoření účtu služby v nasazeném oboru názvů aplikace.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>Zobrazit aktuální specifikaci nasazení vaší aplikace

Pokud jste museli vytvořit účet služby z předchozí části, je pravděpodobné, že vaše nasazení aplikace není nakonfigurováno s určitou `serviceAccountName` specifikací ve specifikaci nasazení. Specifikaci nasazení vaší aplikace si můžete prohlédnout pomocí následujících příkazů:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Ve výstupu bude uveden seznam nasazení.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Nyní popíšeme nasazení jako kontrolu a zjistíme, zda je v části pod šablonou pod uveden účet služby.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

V tomto konkrétním nasazení vidíte, že je k nasazení přidružen účet služby, který je uvedený v části šablona pod. Toto nasazení používá účet služby bookbuyer. Pokud se nezobrazí **účet službě:** vlastnost, vaše nasazení není nakonfigurované na používání účtu služby.

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

Existuje několik postupů, jak nasazení aktualizovat, aby bylo možné přidat účet služby Kubernetes. Přečtěte si dokumentaci k Kubernetes týkající se aktualizace vloženého [nasazení](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) nebo [Konfigurace účtů služeb pro lusky](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/). Po aktualizaci specifikace nasazení s účtem služby znovu nasaďte (kubectl použijte pro nasazení. yaml) do clusteru.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Nasazení nezbytných zásad SMI (Service pro rozhraní sítě)

Posledním krokem k povolení toku povoleného provozu v síti je nasazení potřebných zásad přístupu k provozu [SMI](https://smi-spec.io/) pro vaši aplikaci. Velikost konfigurace, kterou můžete dosáhnout pomocí zásad přístupu [SMI](https://smi-spec.io/) pro přístup k provozu, je nad rámec tohoto návodu, ale podrobně popisujeme některé společné komponenty specifikace a ukážeme, jak nakonfigurovat jednoduché zásady TrafficTarget a HTTPRouteGroup, aby se pro vaši aplikaci umožnila komunikace mezi službami.

Specifikace [](https://smi-spec.io/) [**Access Control přenosů**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) SMI umožňuje uživatelům definovat zásadu řízení přístupu pro své aplikace. Zaměřte se na prostředky rozhraní API pro **TrafficTarget** a **HTTPRoutGroup** .

Prostředek TrafficTarget se skládá ze tří míst cíle, pravidel a zdrojů nastavení konfigurace. Příklad TrafficTarget je uveden níže.

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

Ve výše uvedené specifikaci TrafficTarget `destination` označuje účet služby, který je nakonfigurovaný pro cílovou zdrojovou službu. Zapamatujte si, že účet služby, který byl přidán do nasazení dříve, bude použit k autorizaci přístupu k nasazení, ke kterému je připojen. `rules`Oddíl v tomto konkrétním příkladu definuje typ provozu http, který je povolen přes připojení. Můžete nakonfigurovat jemné vzory regulárního výrazu pro záhlaví HTTP, aby byly specifické pro to, jaký provoz je povolený přes HTTP. `sources`Oddíl je komunikace pocházející z provozu. Tato specifikace načte bookbuyer potřebuje komunikovat s Bookstore.

Prostředek HTTPRouteGroup se skládá z jednoho nebo pole shody s hlavičkou HTTP a je požadavkem specifikace TrafficTarget. V následujícím příkladu vidíte, že HTTPRouteGroup autorizuje tři akce HTTP, dvě GET a jedna POST.

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

Pokud nejste obeznámeni s typem provozu protokolu HTTP, aplikace front-end používá jiné úrovně aplikace, protože specifikace TrafficTarget vyžaduje pravidlo, můžete vytvořit ekvivalent pravidla Allow all s použitím níže uvedené specifikace pro HTTPRouteGroup.

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

Po konfiguraci specifikace TrafficTarget a HTTPRouteGroup je můžete umístit společně jako jednu YAML a nasadit. Níže je uveden příklad konfigurace Bookstore.

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

Podrobnější informace o specifikaci najdete na webu [SMI](https://smi-spec.io/) .

### <a name="manage-the-applications-namespace-with-osm"></a>Správa oboru názvů aplikace pomocí OSM

Dále nakonfigurujeme OSM pro správu oboru názvů a restartování nasazení za účelem získání proxy zástupné postranového vozíku vloženého do aplikace.

Spuštěním následujícího příkazu nakonfigurujte `azure-vote` obor názvů tak, aby byl spravovaný my osm.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Dále restartujte `azure-vote-front` `azure-vote-back` nasazení a pomocí následujících příkazů.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Pokud se zobrazí lusky pro `azure-vote` obor názvů, uvidíme **připravenou** fázi `azure-vote-front` a `azure-vote-back` jako 2/2, což znamená, že proxy server zástupnéového vozíku byl vložen společně s aplikací.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Kurz: nasazení aplikace spravované pomocí sítě OSM (Open Service) pomocí NGINX příchozího přenosu dat

Otevřená síť (OSM) je odlehčená a rozšiřitelná cloudová cloudová služba, která umožňuje uživatelům jednotně spravovat, zabezpečit a získávat funkce pozorování pro vysoce dynamická prostředí mikroslužeb.

V tomto kurzu:

> [!div class="checklist"]
>
> - Zobrazit aktuální konfiguraci clusteru OSM
> - Vytvořte obory názvů pro OSM pro správu nasazených aplikací v názvech.
> - Připojit obory názvů, které se mají spravovat pomocí OSM
> - Nasazení ukázkové aplikace
> - Ověření aplikace spuštěné v clusteru AKS
> - Vytvoření kontroleru NGINX příchozího přenosu dat použitého pro aplikaci
> - Zpřístupnění služby prostřednictvím Azure Application Gateway příchozí připojení k Internetu

### <a name="before-you-begin"></a>Než začnete

V krocích v tomto článku se předpokládá, že jste vytvořili cluster AKS (Kubernetes `1.19+` a vyšší s povoleným KUBERNETES RBAC), navázali jste `kubectl` připojení ke clusteru (Pokud potřebujete pomáhat s kteroukoli z těchto položek, najdete je v [rychlém startu AKS](./kubernetes-walkthrough.md)a nainstalovali jste doplněk AKS osm.

Musíte mít nainstalované následující zdroje:

- Rozhraní příkazového řádku Azure, verze 2.20.0 nebo novější
- `aks-preview`Verze rozšíření 0.5.5 nebo novější
- OSM verze v 0.8.0 nebo novější
- apt – získat JQ instalace

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Zobrazit a ověřit aktuální konfiguraci clusteru OSM

Po povolení doplňku OSM pro AKS v clusteru AKS můžete zobrazit aktuální parametry konfigurace v osm-config Kubernetes ConfigMap. Spusťte následující příkaz pro zobrazení vlastností ConfigMap:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Výstup zobrazuje aktuální konfiguraci OSM pro cluster.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Všimněte si, že **permissive_traffic_policy_mode** je nakonfigurován na **hodnotu true**. Režim zásad povolujících přenosů v OSM je režim, ve kterém se vynucuje vynucování zásad provozu [SMI](https://smi-spec.io/) . V tomto režimu OSM automaticky zjišťuje služby, které jsou součástí pravidel zásad provozu sítě a programů pro všechny servery proxy zástupné, aby mohly komunikovat s těmito službami.

### <a name="create-namespaces-for-the-application"></a>Vytvořit obory názvů pro aplikaci

V tomto kurzu použijeme aplikaci OSM Bookstore, která obsahuje následující součásti aplikace:

- bookbuyer
- bookthief
- knihkupectví
- bookwarehouse

Vytvořte obory názvů pro každou z těchto součástí aplikace.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Měl by se zobrazit následující výstup:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Připojit obory názvů, které se mají spravovat pomocí OSM

Přidání oborů názvů do sítě OSM umožní, aby řadič OSM automaticky vložil kontejnery proxy zástupné vozíku do vaší aplikace. Spuštěním následujícího příkazu připojte obory názvů aplikace OSM Bookstore.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Měl by se zobrazit následující výstup:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Nasazení aplikace Bookstore do clusteru AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Všechny výstupy nasazení jsou shrnuty níže.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Aktualizace služby Bookbuyer

Aktualizujte službu bookbuyer na správnou konfiguraci příchozího portu pomocí následujícího manifestu služby.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Ověření aplikace v knihkupectví běžícím v rámci clusteru AKS

Od teď jsme nasadili aplikaci Bookstore Mulit-Container, ale je dostupná jenom v rámci clusteru AKS. Později přidáme kontroler Azure Application Gateway příchozího přenosu dat, aby se aplikace vystavila mimo cluster AKS. Pokud chcete ověřit, jestli je aplikace spuštěná v rámci clusteru, použijeme pro zobrazení uživatelského rozhraní komponenty bookbuyer port.

Nejprve získáme jméno bookbuyer pod.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Výstup by se měl podobat následujícímu. K vašemu bookbuyer pod se připojí jedinečný název.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Jakmile budeme mít název pod, teď k nastavení tunelu z našeho místního systému do aplikace v clusteru AKS můžete použít příkaz port-dopředně. Spusťte následující příkaz pro nastavení portu jako předávacího pro port místního systému 8080. Znovu použijte zadaný název bookbuyer pod.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Měl by se zobrazit výstup podobný tomuto.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

I když je relace předávání portů zavedena, přejděte v prohlížeči na následující adresu URL `http://localhost:8080` . Nyní byste měli být schopni zobrazit uživatelské rozhraní aplikace bookbuyer v prohlížeči podobně jako na obrázku níže.

![OSM aplikace bookbuyer pro Image uživatelského rozhraní NGINX](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Vytvoření kontroleru NGINX příchozího přenosu dat ve službě Azure Kubernetes Service (AKS)

Kontroler příchozího přenosu dat je softwarový software, který poskytuje reverzní proxy, konfigurovatelné směrování provozu a ukončení protokolu TLS pro služby Kubernetes Services. Kubernetes příchozí prostředky se používají ke konfiguraci pravidel příchozího přenosu dat a tras pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat se dá jedna IP adresa používat ke směrování provozu do několika služeb v clusteru Kubernetes.

K zpřístupnění aplikace spravované serverem OSM do Internetu použijeme kontroler příchozího přenosu dat. Pokud chcete vytvořit kontroler příchozího přenosu dat, použijte Helm a nainstalujte Nginx – příchozí přenos dat. Pro přidání redundance se nasadí dvě repliky kontrolerů příchozího přenosu dat NGINX s parametrem `--set controller.replicaCount`. Pokud chcete mít v clusteru AKS k dispozici více než jeden uzel, zajistěte, aby bylo možné plně využít více uzlů.

Kontroler příchozího přenosu dat je potřeba naplánovat také v uzlu Linuxu. V uzlech Windows Serveru by se kontroler příchozího přenosu dat neměl spouštět. Selektor uzlů se specifikuje pomocí parametru `--set nodeSelector`, aby plánovači Kubernetes oznámil, že má spustit kontroler příchozího přenosu dat NGINX v uzlu Linuxu.

> [!TIP]
> Následující příklad vytvoří obor názvů Kubernetes pro prostředky příchozího přenosu dat s názvem příchozí _– Basic_. Podle potřeby zadejte obor názvů pro vlastní prostředí.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Když se pro kontroler příchozího přenosu NGINX vytvoří služba Vyrovnávání zatížení Kubernetes, přiřadí se dynamická veřejná IP adresa, jak je znázorněno v následujícím příkladu výstupu:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Zatím se nevytvořila žádná pravidla příchozího přenosu dat, takže pokud přejdete na interní IP adresu, zobrazí se 404 výchozí stránka NGINX adaptéru pro příjem dat. Pravidla příchozího přenosu dat jsou nakonfigurovaná v následujících krocích.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Zveřejnění služby bookbuyer na internetu

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Měl by se zobrazit následující výstup:

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

### <a name="view-the-nginx-logs"></a>Zobrazit protokoly NGINX

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

Výstup zobrazuje stav NGINX příchozího adaptéru, když se pravidlo příchozího přenosu úspěšně použije:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>Externí zobrazení služby NGINX Services a bookbuyer

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Vzhledem k tomu, že název hostitele v manifestu příchozího přenosu dat je psuedo název, který se používá pro testování, nebude název DNS k dispozici na internetu. K veřejné IP adrese NGINX můžeme alternativně použít oblý program a předchozí hlavičku názvu hostitele a získat 200 kód, který nám úspěšně propojuje službu bookbuyer.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

Měl by se zobrazit následující výstup:

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Kurz: nasazení aplikace spravované pomocí sítě OSM (Open Service) pomocí Azure Application Gateway AKS doplňku pro příchozí přenosy

Otevřená síť (OSM) je odlehčená a rozšiřitelná cloudová cloudová služba, která umožňuje uživatelům jednotně spravovat, zabezpečit a získávat funkce pozorování pro vysoce dynamická prostředí mikroslužeb.

V tomto kurzu:

> [!div class="checklist"]
>
> - Zobrazit aktuální konfiguraci clusteru OSM
> - Vytvořte obory názvů pro OSM pro správu nasazených aplikací v názvech.
> - Připojit obory názvů, které se mají spravovat pomocí OSM
> - Nasazení ukázkové aplikace
> - Ověření aplikace spuštěné v clusteru AKS
> - Vytvořte Application Gateway Azure, která se má použít jako kontroler příchozího přenosu pro aplikaci.
> - Zpřístupnění služby prostřednictvím Azure Application Gateway příchozí připojení k Internetu

### <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto článku předpokládají, že jste vytvořili cluster AKS (Kubernetes `1.19+` a vyšší s povoleným KUBERNETES RBAC), navázali jste `kubectl` připojení ke clusteru (Pokud potřebujete nápovědu k některé z těchto položek, pak si přečtěte článek [rychlý Start AKS](./kubernetes-walkthrough.md), který nainstaloval doplněk AKS osm a vytvoří novou službu Azure Application Gateway pro příchozí přenosy.

Musíte mít nainstalované následující zdroje:

- Rozhraní příkazového řádku Azure, verze 2.20.0 nebo novější
- `aks-preview`Verze rozšíření 0.5.5 nebo novější
- AKS clusteru verze 1.19 + pomocí sítě Azure CNI (připojené k virtuální síti Azure)
- OSM verze v 0.8.0 nebo novější
- apt – získat JQ instalace

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Zobrazit a ověřit aktuální konfiguraci clusteru OSM

Po povolení doplňku OSM pro AKS v clusteru AKS můžete zobrazit aktuální parametry konfigurace v osm-config Kubernetes ConfigMap. Spusťte následující příkaz pro zobrazení vlastností ConfigMap:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Výstup zobrazuje aktuální konfiguraci OSM pro cluster.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Všimněte si, že **permissive_traffic_policy_mode** je nakonfigurován na **hodnotu true**. Režim zásad povolujících přenosů v OSM je režim, ve kterém se vynucuje vynucování zásad provozu [SMI](https://smi-spec.io/) . V tomto režimu OSM automaticky zjišťuje služby, které jsou součástí pravidel zásad provozu sítě a programů pro všechny servery proxy zástupné, aby mohly komunikovat s těmito službami.

### <a name="create-namespaces-for-the-application"></a>Vytvořit obory názvů pro aplikaci

V tomto kurzu použijeme aplikaci OSM Bookstore, která obsahuje následující součásti aplikace:

- bookbuyer
- bookthief
- knihkupectví
- bookwarehouse

Vytvořte obory názvů pro každou z těchto součástí aplikace.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Měl by se zobrazit následující výstup:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Připojit obory názvů, které se mají spravovat pomocí OSM

Když přidáte obory názvů do sítě OSM, umožníte tak, aby řadič OSM automaticky vložil kontejnery proxy zástupné vozíku do vaší aplikace. Spuštěním následujícího příkazu připojte obory názvů aplikace OSM Bookstore.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Měl by se zobrazit následující výstup:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Nasazení aplikace Bookstore do clusteru AKS

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Všechny výstupy nasazení jsou shrnuty níže.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Aktualizace služby Bookbuyer

Aktualizujte službu bookbuyer na správnou konfiguraci příchozího portu pomocí následujícího manifestu služby.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Ověření aplikace v knihkupectví běžícím v rámci clusteru AKS

Od teď jsme nasadili aplikaci pro více kontejnerů v knihkupectvích, ale je dostupná jenom z clusteru AKS. Později přidáme kontroler Azure Application Gateway příchozího přenosu dat, aby se aplikace vystavila mimo cluster AKS. Pokud chcete ověřit, jestli je aplikace spuštěná v rámci clusteru, použijeme pro zobrazení uživatelského rozhraní komponenty bookbuyer port.

Nejprve získáme jméno bookbuyer pod.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Výstup by se měl podobat následujícímu. K vašemu bookbuyer pod se připojí jedinečný název.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Jakmile budeme mít název pod, teď k nastavení tunelu z našeho místního systému do aplikace v clusteru AKS můžete použít příkaz port-dopředně. Spusťte následující příkaz pro nastavení portu jako předávacího pro port místního systému 8080. Znovu použijte svůj konkrétní bookbuyer pod názvem.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Měl by se zobrazit výstup podobný tomuto.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

I když je relace předávání portů zavedena, přejděte v prohlížeči na následující adresu URL `http://localhost:8080` . Nyní byste měli být schopni zobrazit uživatelské rozhraní aplikace bookbuyer v prohlížeči podobně jako na obrázku níže.

![Obrázek uživatelského rozhraní aplikace OSM bookbuyer pro aplikaci App Gateway](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>Vytvoření Application Gateway Azure pro zpřístupnění aplikace bookbuyer mimo cluster AKS

> [!NOTE]
> Následující pokyny Vytvoří novou instanci Application Gateway Azure, která se má použít pro příchozí přenosy. Pokud máte existující Application Gateway Azure, kterou chcete použít, přejděte k části Povolení doplňku Application Gateway příchozího řadiče pro příchozí přenosy.

#### <a name="deploy-a-new-application-gateway"></a>Nasadit nový Application Gateway

> [!NOTE]
> Odkazujeme na stávající dokumentaci pro povolení doplňku Application Gateway příchozího adaptéru pro existující cluster AKS. Byly provedeny nějaké změny, které vyhovují OSM materiálům. Podrobnější dokumentaci k předmětu najdete [tady](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing).

Nyní nasadíte novou Application Gateway pro simulaci existující Application Gateway, kterou chcete použít k vyrovnávání zatížení provozu do clusteru AKS _myCluster_. Název Application Gateway bude _myApplicationGateway_, ale budete muset nejdřív vytvořit prostředek veřejné IP adresy s názvem _myPublicIp_ a novou virtuální síť s názvem _myVnet_ s adresním prostorem 11.0.0.0/8 a podsíť s adresním prostorem 11.1.0.0/16 nazvanou _MySubnet_ a nasadit Application Gateway v _mySubnet_ pomocí _myPublicIp_.

Pokud používáte cluster AKS a Application Gateway v samostatných virtuálních sítích, nesmí se adresní prostory těchto dvou virtuálních sítí překrývat. Výchozím adresním prostorem, ve kterém se nasazuje cluster AKS, je 10.0.0.0/8, takže nastavíme předponu adresy Application Gateway virtuální sítě na 11.0.0.0/8.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Doplněk Application Gateway AGIC (inWAF Controller) podporuje **pouze** SKU Application Gateway v2 (Standard a), **nikoli** SKU Application Gateway v1.

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Povolení doplňku AGIC pro existující cluster AKS prostřednictvím Azure CLI

Pokud chcete i nadále používat rozhraní příkazového řádku Azure CLI, můžete pokračovat ve povolování doplňku AGIC v clusteru AKS, který jste vytvořili, _myCluster_ a zadáním doplňku AGIC pro použití existujícího Application Gateway, který jste vytvořili, _myApplicationGateway_.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Pomocí následujícího příkazu můžete ověřit, že doplněk Azure Application Gateway AKS byl povolen.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

Tento příkaz by měl zobrazit výstup jako `true` .

#### <a name="peer-the-two-virtual-networks-together"></a>Partnerský vztah mezi dvěma virtuálními sítěmi

Vzhledem k tomu, že jsme cluster AKS nasadili ve vlastní virtuální síti a Application Gateway v jiné virtuální síti, budete muset vytvořit partnerský vztah mezi dvěma virtuálními sítěmi, aby přenos dat z Application Gateway do lusků v clusteru. Partnerský vztah dvou virtuálních sítí vyžaduje, aby se příkaz Azure CLI používal dvakrát, aby se zajistilo obousměrné připojení. První příkaz vytvoří připojení partnerského vztahu z Application Gateway virtuální sítě k virtuální síti AKS; Druhý příkaz vytvoří připojení partnerského vztahu v druhém směru.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Zveřejnění služby bookbuyer na internetu

Použijte následující manifest příchozího přenosu dat do clusteru AKS a vystavte službu bookbuyer pro Internet prostřednictvím Azure Application Gateway.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Měli byste vidět následující výstup.

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

Vzhledem k tomu, že název hostitele v manifestu příchozího přenosu dat je pseudo název, který se používá pro testování, nebude mít název DNS k dispozici na internetu. Můžete alternativně použít oblý program a za něj za hlavičku názvu hostitele přejít na veřejnou IP adresu Azure Application Gateway a získat 200 kód, který nám úspěšně propojíme se službou bookbuyer.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

Měli byste vidět následující výstup.

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

### <a name="troubleshooting"></a>Řešení potíží

- [Dokumentace k řešení potíží s AGIC](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [Další nástroje pro řešení potíží jsou k dispozici v úložišti GitHub AGIC](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>OSM (Open Service oky) pro monitorování a pozorování pomocí Azure Monitor a Application Insights

Azure Monitor i Azure Application Insights pomáhají maximalizovat dostupnost a výkon vašich aplikací a služeb tím, že poskytují ucelené řešení pro shromažďování, analýzu a jednání telemetrie z vašeho cloudového a místního prostředí.

Doplněk OSM AKS bude mít hlubokou integraci do obou těchto služeb Azure a poskytuje nezdánlivě dostupné prostředí Azure pro zobrazení a reakci na kritické klíčové ukazatele výkonu, které poskytuje metrika OSM. Další informace o tom, jak povolit a nakonfigurovat tyto služby pro doplněk OSM AKS, najdete na stránce [Azure monitor pro osm](https://aka.ms/azmon/osmpreview) , kde najdete další informace.

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Kurz: Ruční nasazení Prometheus, Grafana a Jaeger pro zobrazení metriky otevřené sítě (OSM) pro pozorování

> [!WARNING]
> Instalace Prometheus, Grafana a Jaeger jsou k dispozici jako obecné pokyny k zobrazování dat metriky OSM, jak se dají tyto nástroje používat. Pokyny k instalaci se nepoužívají pro produkční nastavení. Další informace o tom, jak nejlépe vyhovovat vašim potřebám, najdete v dokumentaci k jednotlivým nástrojům. Nejdůležitějším úložištěm bude nedostatečná trvalá úložiště, což znamená, že všechna data budou ztracena po Prometheus Grafana a/nebo Jaeger pod.

Otevřená síť služby (OSM) generuje podrobné metriky týkající se veškerého provozu v rámci sítě. Tyto metriky poskytují přehled o chování aplikací v síti, které pomáhají uživatelům řešit, udržovat a analyzovat své aplikace.

Od dnešního OSM shromažďuje metriky přímo z proxy vozíků (zástupné). OSM poskytuje bohatou metriku pro příchozí a odchozí provoz pro všechny služby v síti. S těmito metrikami může uživatel získat informace o celkovém objemu provozu, chybách v rámci provozu a době odezvy na požadavky.

OSM využívá Prometheus ke shromažďování a ukládání konzistentních metrik provozu a statistik pro všechny aplikace běžící v síti. Prometheus je open source sada nástrojů pro monitorování a upozorňování, která se běžně používá v Kubernetes a v prostředích sítí služby.

Každá aplikace, která je součástí sítě, běží v poli pod, které obsahuje postranní vozík zástupné, který zpřístupňuje metriky (metriky proxy serveru) ve formátu Prometheus. Každé pole, které je součástí sítě, navíc obsahuje poznámky Prometheus, díky čemuž je možné, aby server Prometheus mohl aplikaci vyřadit dynamicky. Tento mechanismus automaticky umožňuje vyřadit metriky pokaždé, když se do sítě přidá nový obor názvů/pod/službu.

Metriky OSM se dají zobrazit pomocí Grafana, což je open source vizualizace a analytický software. Umožňuje dotazování, vizualizaci, upozorňování a prozkoumání metrik.

V tomto kurzu:

> [!div class="checklist"]
>
> - Vytvoření a nasazení instance Prometheus
> - Nakonfigurujte OSM, aby povolovalo vynechání Prometheus.
> - Aktualizace Prometheus Configmap
> - Vytvoření a nasazení instance Grafana
> - Konfigurace Grafana pomocí zdroje dat Prometheus
> - Import řídicího panelu OSM pro Grafana
> - Vytvoření a nasazení instance Jaeger
> - Konfigurace trasování Jaeger pro OSM

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Nasazení a konfigurace instance Prometheus pro OSM

K nasazení instance Prometheus použijeme Helm. Spuštěním následujících příkazů nainstalujte Prometheus prostřednictvím Helm:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Podobný výstup by měl být zobrazen níže, pokud byla instalace úspěšná. Poznamenejte si port serveru Prometheus a název DNS clusteru. Tyto informace se použijí později ke konfiguraci Prometheus jako zdroje dat pro Grafana.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>Nakonfigurujte OSM, aby povolovalo vynechání Prometheus.

Chcete-li zajistit, aby byly komponenty OSM nakonfigurované pro Prometheusé šroty, doporučujeme zkontrolovat konfiguraci **prometheus_scraping** umístěnou v konfiguračním souboru osm-config. Prohlédněte si konfiguraci pomocí následujícího příkazu:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

Výstup předchozího příkazu by měl vracet, `true` Pokud je osm nakonfigurovaný pro vyřazení Prometheus. Pokud je vrácená hodnota `false` , bude nutné aktualizovat konfiguraci, aby byla `true` . Spusťte následující příkaz, kterým **zapnete** vyřazení osm Prometheus:

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

Měli byste vidět následující výstup.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>Aktualizace Prometheus Configmap

Výchozí instalace Prometheus bude obsahovat dvě Kubernetes configmaps. Seznam Prometheus configmaps můžete zobrazit pomocí následujícího příkazu.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

Budeme muset nahradit konfiguraci Prometheus. yml, která je umístěná v umístění **stabilní-Prometheus-Server** configmap s následující konfigurací osm. Tuto úlohu lze provést několika různými způsoby úprav souborů. Jednoduchým a bezpečným způsobem je exportovat configmap, vytvořit jeho kopii pro zálohování a pak ho upravit pomocí editoru, jako je Visual Studio Code.

> [!NOTE]
> Pokud nemáte nainstalovanou Visual Studio Code, můžete si ho stáhnout a nainstalovat [tady](https://code.visualstudio.com/Download).

Nejdřív vyexportujte **stabilní-Prometheus-Server** configmap a pak vytvořte kopii pro zálohování.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Teď soubor otevřete pomocí Visual Studio Code k úpravám.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Jakmile configmap otevřete v editoru Visual Studio Code, nahraďte soubor Prometheus. yml níže konfigurací OSM a soubor uložte.

> [!WARNING]
> Je velmi důležité zajistit, abyste zachovali strukturu odsazování souboru YAML. Změny ve struktuře souborů YAML by mohly způsobit, že configmap nebude možné znovu použít.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

Použijte aktualizovaný soubor configmap YAML pomocí následujícího příkazu.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Může se zobrazit zpráva o tom, že je potřeba anotace chybějícího Kubernetes. Tato možnost se dá pro teď ignorovat.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Ověřte, že je Prometheus nakonfigurované tak, aby vyodpadí OSM mřížku a koncové body rozhraní API

Pokud chcete ověřit, že je Prometheus správně nakonfigurovaný pro vyřazení OSM a koncových bodů rozhraní API, přesměrujte na Prometheus pod a zobrazte cílovou konfiguraci. Spusťte následující příkazy.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Otevření prohlížeče až do `http://localhost:9090/targets`

Pokud se posunete **dolů, měli** byste vidět všechny stavy koncových bodů SMI metrik a další osm metriky definované jako obrázek.

![Obrázek uživatelského rozhraní pro OSM Prometheus cílové metriky](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Nasazení a konfigurace instance Grafana pro OSM

K nasazení instance Grafana použijeme Helm. Spuštěním následujících příkazů nainstalujte Grafana prostřednictvím Helm:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

V dalším kroku načteme výchozí Grafana heslo pro přihlášení k webu Grafana.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Poznamenejte si heslo Grafana.

V dalším kroku načteme Grafana pod a předáte port do řídicího panelu Grafana pro přihlášení.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Otevření prohlížeče až do `http://localhost:3000`

Na obrazovce pro přihlášení zobrazené níže zadejte **správce** jako uživatelské jméno a použijte dříve zaznamenané heslo Grafana.

![Obrázek uživatelského rozhraní přihlašovací stránky Grafana OSM](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>Konfigurace zdroje dat Prometheus Grafana

Po úspěšném přihlášení k Grafana je dalším krokem přidání Prometheus jako zdrojů dat pro Grafana. Provedete to tak, že přejdete na ikonu konfigurace v levé nabídce a vyberete zdroje dat, jak je vidět níže.

![Obrázek uživatelského rozhraní stránky OSM Grafana DataSources](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Klikněte na tlačítko **Přidat zdroj dat** a v části databáze časových řad vyberte Prometheus.

![Obrázek uživatelského rozhraní stránky výběru OSM Grafana DataSources](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

Na stránce **Konfigurovat zdroj dat Prometheus** zadejte plně kvalifikovaný název domény clusteru Kubernetes pro službu Prometheus pro nastavení adresy URL protokolu HTTP. Výchozí plně kvalifikovaný název domény by měl být `stable-prometheus-server.default.svc.cluster.local` . Po zadání tohoto koncového bodu služby Prometheus se posuňte do dolní části stránky a vyberte **uložit & test**. Mělo by se zobrazit zelené zaškrtávací políčko, které určuje, že zdroj dat pracuje.

#### <a name="importing-osm-dashboards"></a>Import řídicích panelů OSM

Řídicí panely OSM jsou k dispozici prostřednictvím:

- [Naše úložiště](/charts/osm/grafana)a lze je importovat jako objekty blob JSON prostřednictvím portálu pro správu webu.
- nebo [online na adrese Grafana.com](https://grafana.com/grafana/dashboards/14145)

Pokud chcete importovat řídicí panel, vyhledejte v `+` levé nabídce možnost Sign (podepsat) a vyberte `import` .
Řídicí panel můžete přímo importovat podle jejich ID na `Grafana.com` . Například náš `OSM Mesh Details` řídicí panel používá ID `14145` , můžete použít ID přímo ve formuláři a vybrat `import` :

![Obrázek uživatelského rozhraní pro import stránky v řídicím panelu OSM Grafana](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

Jakmile vyberete importovat, automaticky se naimportuje do importovaného řídicího panelu.

![Obrázek uživatelského rozhraní stránky s podrobnostmi o velikosti OSM Grafana řídicího panelu](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Nasazení a konfigurace operátoru Jaeger v Kubernetes pro OSM

[Jaeger](https://www.jaegertracing.io/) je open source systém trasování, který slouží k monitorování a odstraňování potíží distribuovaných systémů. Dá se nasadit s OSM jako novou instancí nebo může přinést vlastní instanci. Následující pokyny nasadí novou instanci Jaeger do `jaeger` oboru názvů v clusteru AKS.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>Nasazení Jaeger do clusteru AKS

Použijte následující manifest pro instalaci Jaeger:

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

#### <a name="enable-tracing-for-the-osm-add-on"></a>Povolit trasování pro doplněk OSM

Dále bude nutné povolit trasování pro doplněk OSM.

> [!NOTE]
> Od této chvíle nejsou vlastnosti trasování viditelné v osm-config configmap v tomto okamžiku. Tím se viditelné v nové verzi doplňku OSM AKS.

Spusťte následující příkaz, který povolí trasování pro doplněk OSM:

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>Zobrazení uživatelského rozhraní Jaeger s předáváním portů

Uživatelské rozhraní Jaeger je spuštěno na portu 16686. Chcete-li zobrazit webové uživatelské rozhraní, můžete použít kubectl port-dopředné:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

V prohlížeči by se měl zobrazit rozevírací seznam služby, který umožňuje vybrat z různých aplikací nasazených v rámci aplikace Bookstore demo. Vyberte službu, ze které se mají zobrazit všechny rozsahy. Pokud například vyberete bookbuyer s lookback o jednu hodinu, uvidíte jeho interakce s Bookstore-V1 a Bookstore-v2 seřazenou podle času.

![Obrázek uživatelského rozhraní stránky trasování OSM Jaeger](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Vyberte jakoukoli položku a zobrazte ji podrobněji. Vyberte více položek pro porovnání trasování. Můžete například porovnat interakce bookbuyer s Bookstore a Bookstore-V2 v konkrétním okamžiku v čase.

Můžete také vybrat kartu architektura systému a zobrazit graf, jak jednotlivé aplikace pracují nebo komunikují. To poskytuje představu o tom, jak tok dat mezi aplikacemi probíhá.

![Obrázek uživatelského rozhraní architektury systému OSM Jaeger](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Průvodce odstraňováním potíží s OSM (Open Service) AKS doplňky pro řešení potíží

Když nasadíte doplněk OSM AKS, může občas docházet k potížím. Následující příručky vám pomohou vyřešit chyby a vyřešit běžné problémy.

### <a name="verifying-and-troubleshooting-osm-components"></a>Ověřování a odstraňování potíží s komponentami OSM

#### <a name="check-osm-controller-deployment"></a>Ověřit nasazení kontroleru OSM

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

Dobrý kontroler OSM by vypadal takto:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>Podívejte se na kontroler OSM pod

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Dobrý OSM pod by vypadal takto:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

I když v určitém okamžiku byl jeden kontroler vyřazený, máme ten, který je připravený 1/1 a spuštěný s 0 restarty. Pokud je sloupec připravený jiným způsobem než 1/1, síť sítě by byla v nefunkčním stavu.
Sloupec připravený s 0/1 indikuje, že došlo k chybě kontejneru řídicí roviny – musíme získat protokoly. Další informace najdete v části získání protokolů řadiče OSM z Azure Support Center níže. Sloupec připravený s číslem vyšším než 1, za který označuje, že je sajdkáry nainstalováno. Řadič OSM pravděpodobně nefunguje s jakýmkoli sajdkáry připojeným k němu.

> [!NOTE]
> Od verze v 0.8.2 kontroler OSM není v režimu HA a bude spuštěn v nasazeném s počtem replik 1-Single pod. V poli pod jsou sondy stavu a v případě potřeby bude v kubelet restartován.

#### <a name="check-osm-controller-service"></a>Ověřit službu kontroleru OSM

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

V pořádku by služba OSM Controller vypadala takto:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> CLUSTER-IP by se lišily. NÁZEV služby a PORT (y) musí být stejné jako v předchozím příkladu.

#### <a name="check-osm-controller-endpoints"></a>Kontrolovat koncové body kontroleru OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Dobrý koncový bod řadiče OSM by vypadal takto:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>Ověřit nasazení OSM pro vkládání

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Dobrým nasazením OSM pro vkládání by vypadalo takto:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>Ověřit OSM vstřikovacího zařízení pod

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Dobrý OSM vložený objekt pod by vypadal takto:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>Ověřit službu OSM pro vkládání

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

V pořádku by služba OSM injektor vypadala takto:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>Kontrolovat koncové body OSM

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Dobrý koncový bod OSM by vypadal takto:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Ověření a mutace webhooků

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Dobrý OSM ověřující Webhook by vypadal takto:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Dobrý OSMový Webhook by vypadal takto:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Vyhledat službu a sadu certifikačních autorit ověřující Webhook

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Správně nakonfigurované ověření konfigurace Webhooku by vypadalo přesně takto:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Vyhledat službu a sadu CA pro daný Webhook

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Správně nakonfigurovaná vhodná konfigurace Webhooku by vypadala přesně takto:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Ověřte, jestli se k OSM řadiči přistavila sada pro vyhodnocování (nebo mutace) Webhooku.

> [!NOTE]
> Od verze v 0.8.2 je důležité znát, že AKS RP nainstaluje ověření Webhooku, a tím zajišťuje, že existuje, ale řadič OSM je ten, který plní sadu certifikačních autorit.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Toto číslo indikuje počet bajtů nebo velikost sady prostředků certifikační autority. Pokud je toto pole prázdné, 0 nebo nějaké číslo pod 1000, znamená to, že sada certifikačních autorit není správně zřízená. Bez správné sady prostředků certifikační autority by se při ověřování Webhooku stala chyba a uživatel zakázal provádění změn v ConfigMap osm-config v oboru názvů Kube-System.

Ukázková chyba, pokud je sada certifikační autority nesprávná:

- Pokus o změnu osm-config ConfigMap:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Chyba:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

V případě, že má **ověřování** konfigurace Webhooku špatný certifikát, se můžete vyhnout takto:

- Možnost 1 – restartování OSM kontroleru – Tato akce restartuje kontroler OSM. V nabídce Start se přepíše sada certifikační autority pro obě tyto i ověřovací Webhooky.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- Možnost 2 – možnost 2 Odstranění Webhooku ověřování – odebráním Webhooku způsobí, že `osm-config` ConfigMap už neproběhne ověření. Všechny opravy budou procházet. Odsouhlasení AKS se v určitém okamžiku ověří, jestli existuje ověřovací Webhook, a vytvoří ho znovu. Aby bylo možné rychle přepsat sadu certifikačních autorit, může být nutné restartovat kontroler OSM.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- Možnost 3 – odstranění a Oprava: následující příkaz odstraní ověřovací Webhook, což nám umožní přidat jakékoli hodnoty a okamžitě se pokusí použít opravu. Nejpravděpodobnější, že AKS pro sjednocení nebude mít dost času na sjednocení a obnovení ověřovacího Webhooku, který nám dává možnost použít jako poslední možnost změnu:

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>Podívejte se na `osm-config` **ConfigMap**

> [!NOTE]
> Řadič OSM nevyžaduje, aby se `osm-config` ConfigMap nacházela v oboru názvů Kube-System. Kontroler má pro konfiguraci přiměřené výchozí hodnoty a může bez něj fungovat.

Kontrolovat existenci:

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

Ověřte obsah osm-config ConfigMap

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config -o json | jq '.data'
```

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.20.233",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

`osm-config` ConfigMap hodnoty:

| Klíč                              | Typ   | Povolené hodnoty                                          | Výchozí hodnota                          | Funkce                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| odchozí                           | bool   | true, false                                             | `"false"`                              | Povolí výstup do sítě.                                                                                                                                                                                                             |
| enable_debug_server              | bool   | true, false                                             | `"true"`                               | Povolí koncový bod ladění na osm pod tím, že zobrazí informace týkající se sítě, jako jsou připojení k proxy serveru, certifikáty a zásady SMI.                                                                                    |
| enable_privileged_init_container | bool   | true, false                                             | `"false"`                              | Povoluje privilegované kontejnery inicializace pro lusky v síti. V případě hodnoty false mají kontejnery init pouze NET_ADMIN.                                                                                                                                   |
| envoy_log_level                  | řetězec | trasování, ladění, informace, upozornění, varování, chyba, kritická, vypnutá | `"error"`                              | Nastaví podrobnost protokolování pro zástupné proxy, která se vztahuje pouze na nově vytvořené lusky spojující síť. Chcete-li aktualizovat úroveň protokolu pro existující lusky, restartujte nasazení s `kubectl rollout restart` .                            |
| outbound_ip_range_exclusion_list | řetězec | čárkami oddělený seznam rozsahů IP adres ve formátu a. b. c. d/x | `-`                                    | Globální seznam rozsahů IP adres, které se mají vyloučit z odposlouchávání odchozích přenosů prostřednictvím proxy vozíku.                                                                                                                                    |
| permissive_traffic_policy_mode   | bool   | true, false                                             | `"false"`                              | Nastavení na `true` Povolí režim povolit – vše v síti, tj. žádné vynucování zásad provozu v síti. Pokud je tato možnost nastavená na `false` , povolí možnost Odepřít – všechny zásady provozu v síti, tj. `SMI Traffic Target` je potřeba pro komunikaci služeb. |
| prometheus_scraping              | bool   | true, false                                             | `"true"`                               | Povolí vyřazení metrik Prometheus na proxy odnože.                                                                                                                                                                                 |
| service_cert_validity_duration   | řetězec | 24 hodin, 1h30m (jakákoli doba trvání)                          | `"24h"`                                | Nastaví dobu platnosti certifikátu služby reprezentovanou jako posloupnost desetinných čísel, z nichž každá má volitelný zlomek a příponu jednotky.                                                                                             |
| tracing_enable                   | bool   | true, false                                             | `"false"`                              | Povolí trasování Jaeger pro síť.                                                                                                                                                                                                    |
| tracing_address                  | řetězec | Jaeger. mesh-Namespace. svc. cluster. Local                 | `jaeger.kube-system.svc.cluster.local` | Adresa nasazení Jaeger, pokud je povoleno trasování.                                                                                                                                                                                |
| tracing_endpoint                 | řetězec | /api/v2/spans                                           | /api/v2/spans                          | Koncový bod pro data trasování, pokud je povoleno trasování.                                                                                                                                                                                          |
| tracing_port                     | int    | libovolná celočíselná hodnota, která není nula                              | `"9411"`                               | Port, na kterém je povoleno trasování.                                                                                                                                                                                                       |
| use_https_ingress                | bool   | true, false                                             | `"false"`                              | Povolí příchozí přenos HTTPS na síti.                                                                                                                                                                                                      |
| config_resync_interval           | řetězec | za 1 minutu Toto zakáže                            | 0 (zakázáno)                           | Když je k dispozici hodnota větší než 1 milion (60 s), OSM Controller pošle všechny dostupné konfigurace všem připojeným zástupné v daném intervalu.                                                                                                    |

#### <a name="check-namespaces"></a>Kontrolovat obory názvů

> [!NOTE]
> Obor názvů Kube-System se nikdy neúčastní v síti a nikdy nebude označený ani označen jako označený klíčem nebo hodnotami uvedenými níže.

Pomocí `osm namespace add` příkazu spojíte obory názvů s danou sítí služby.
Pokud je obor názvů k8s součástí sítě (nebo pokud je jeho součástí sítě), musí být splněny následující podmínky:

Zobrazit poznámky s

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

Musí být k dispozici následující Poznámka:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Zobrazit popisky s

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

Musí být k dispozici následující popisek:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Pokud obor názvů není označený `"openservicemesh.io/sidecar-injection": "enabled"` nebo není označený pomocí `"openservicemesh.io/monitored-by": "osm"` osm, nepřidá zástupné sajdkáry.

> Poznámka: po `osm namespace add` volání se bude na postranním vozíku zástupné vložit jenom **nové** lusky. Existující lusky se musí restartovat s `kubectl rollout restart deployment ...`

#### <a name="verify-the-smi-crds"></a>Ověřte CRDs SMI:

Ověřte, zda má cluster požadované CRDs:

```azurecli-interactive
kubectl get crds
```

V clusteru musí být nainstalované následující:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Získejte verze CRDs nainstalované pomocí tohoto příkazu:

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

Očekávaný výstup:

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

OSM Controller v 0.8.2 vyžaduje následující verze:

- traffictargets.access.smi-spec.io – [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io – [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io – [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io – nepodporováno
- trafficsplits.split.smi-spec.io – [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*. metrics.smi-spec.io – [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Pokud chybí CRDs, použijte k instalaci těchto v clusteru následující příkazy:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Zakázat doplněk OSM (Open Service oky) pro cluster AKS

Pokud chcete zakázat doplněk OSM, spusťte následující příkaz:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register