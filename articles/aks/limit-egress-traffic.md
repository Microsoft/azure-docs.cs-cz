---
title: Omezení provozu odchozího přenosu dat ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jaké porty a adresy jsou potřeba ovládací prvek výchozí přenos ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: iainfou
ms.openlocfilehash: 52a9ba20b60e8ef6cdb743546cd842e4ee24b3fd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441925"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Ve verzi Preview - omezení odchozí provoz pro uzly clusteru a řízení přístupu k požadované porty a služby ve službě Azure Kubernetes Service (AKS)

Ve výchozím nastavení clustery AKS mít stejně neomezený přístup k Internetu odchozí (odchozí). Tato úroveň přístupu k síti umožňuje uzly a služeb, které můžete spustit pro přístup k externím prostředkům podle potřeby. Pokud chcete omezit výchozí přenos, musí být omezený počet portů a adres přístupné Udržovat úlohy údržby v dobrém stavu clusteru. Cluster se pak nakonfiguruje k použít pouze základní systém Image kontejneru z registru kontejneru Microsoft (MCR) nebo Azure Container Registry (ACR), není externí veřejných úložištích. Je nutné nakonfigurovat upřednostňované pravidla brány firewall a zabezpečení na tyto požadované porty a adresy.

Tento článek podrobně popisuje, jaké síťové porty a plně kvalifikované názvy domény (FQDN) jsou povinné a nepovinné-li omezit výchozí přenos v clusteru AKS.  Tato funkce je aktuálně ve verzi Preview.

> [!IMPORTANT]
> Funkce AKS ve verzi preview jsou samoobslužných služeb, vyjádřit výslovný souhlas. Jsou poskytovány shromažďovat zpětnou vazbu a chyb z naší komunitě. Ve verzi preview nejsou tyto funkce určené k použití v produkčním prostředí. Funkce ve verzi public preview spadají pod "co možná nejlepší" podporu. Pomoc od týmů AKS technická podpora je k dispozici během pracovní doby tichomořské časové pásmo (PST) pouze. Další informace najdete v tématu následující články o podpoře:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="before-you-begin"></a>Než začnete

Musí mít Azure CLI verze 2.0.66 nebo později nainstalována a nakonfigurována. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

K vytvoření clusteru AKS, které omezuje výchozí přenos, nejprve povolte příznak funkce v rámci předplatného. Tato funkce registrace nakonfiguruje jakémkoli AKS vytvoříte pomocí systému základní Image kontejneru z MCR nebo služby ACR. K registraci *AKSLockingDownEgressPreview* příznak funkce, použijte [az funkce register][az-feature-register] příkaz, jak je znázorněno v následujícím příkladu:

> [!CAUTION]
> Při registraci funkce v rámci předplatného nelze nyní zrušit registraci této funkce. Po povolení některé funkce ve verzi preview se výchozí hodnoty lze pro všechny clustery AKS, pak jste vytvořili v rámci předplatného. Nepovolí funkce ve verzi preview na předplatná pro produkční prostředí. Testování funkce ve verzi preview a shromažďování zpětné vazby pomocí samostatné předplatné.

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Trvá několik minut, než se stav zobrazíte *registrované*. Můžete zkontrolovat stav registrace pomocí [seznam funkcí az][az-feature-list] příkaz:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Až to budete mít, aktualizujte registraci *Microsoft.ContainerService* poskytovatele prostředků pomocí [az provider register][az-provider-register] příkaz:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Přehled provozu odchozího přenosu dat

Pro účely provozní a správy uzly v clusteru AKS potřebují přístup k určité porty a plně kvalifikované názvy domény (FQDN). Tato akce může být ke komunikaci se serverem rozhraní API nebo stáhněte a nainstalujte základní součásti clusteru Kubernetes a aktualizace zabezpečení uzlu. Ve výchozím nastavení se výchozí přenos dat (odchozí) internetový provoz neomezuje pro uzly v clusteru AKS. Cluster může přetáhněte základní systém Image kontejneru z externích úložišť.

Pokud chcete zvýšit zabezpečení vašeho clusteru AKS, můžete chtít omezit výchozí přenos. Cluster je nakonfigurovaný k přetahování imagí kontejneru z MCR nebo ACR základního systému. Pokud uzamknout výchozí přenos tímto způsobem, je nutné definovat konkrétní porty a plně kvalifikovaných názvů domén povolit uzlů AKS správně komunikovat s vyžaduje externí služby. Bez těchto oprávnění portů a plně kvalifikované názvy domény nelze uzly AKS komunikaci se serverem pro rozhraní API nebo nainstalujte základní součásti.

Můžete použít [Brána Firewall služby Azure][azure-firewall] nebo zařízení brány firewall 3. stran k zabezpečení provozu odchozího přenosu dat a definovat tyto požadované porty a adresy. AKS neprovádí automatické vytváření těchto pravidel pro vás. Následující porty a adresy jsou pro referenci při vytváření příslušných pravidel brány firewall vaší sítě.

Ve službě AKS existují dvě sady porty a adresy:

* [Požadované porty a adresy pro AKS clustery](#required-ports-and-addresses-for-aks-clusters) podrobně popisuje minimální požadavky na oprávnění výchozí přenos.
* [Volitelné doporučené adresy a porty pro AKS clustery](#optional-recommended-addresses-and-ports-for-aks-clusters) se nevyžadují pro všechny scénáře, ale integrace s dalšími službami, jako je Azure Monitor nebude fungovat správně. Projděte si seznam volitelné porty a plně kvalifikované názvy domény a povolit všechny služby a komponenty použité ve vašem clusteru AKS.

> [!NOTE]
> Omezení výchozí přenos funguje pouze na nové AKS clustery, které jsou vytvořené po povolení registrace příznak funkce. Pro existující clustery [provést operaci upgradu clusteru][aks-upgrade] pomocí `az aks upgrade` příkazu před omezit výchozí přenos.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Požadované porty a adresy pro clustery AKS

Těchto odchozích portech / pravidla sítě jsou požadovány pro AKS cluster:

* TCP port *443*
* TCP port *9000* a TCP port *22* pro front-pod tunel ke komunikaci s koncovým tunelového propojení na serveru rozhraní API.
    * Chcete-li si to trochu přiblížit, přečtěte si téma * *.hcp.\< umístění\>. azmk8s.io* a * *. tun.\< umístění\>. azmk8s.io* adresy v následující tabulce.

Následující plně kvalifikovaný název domény / pravidla aplikací jsou požadovány:

| PLNĚ KVALIFIKOVANÝ NÁZEV DOMÉNY                       | Port      | Použití      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Tato adresa je koncový bod rozhraní API serveru. Nahraďte *\<umístění\>* s oblastí, které se nasadí AKS cluster. |
| *. tun. \<umístění\>. azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Tato adresa je koncový bod rozhraní API serveru. Nahraďte *\<umístění\>* s oblastí, které se nasadí AKS cluster. |
| aksrepos.azurecr.io        | HTTPS:443 | Tato adresa je potřeba přístup imagí v Azure Container Registry (ACR). |
| *.blob.core.windows.net    | HTTPS:443 | Tato adresa je back-endu úložiště pro imagí uložených v ACR. |
| mcr.microsoft.com          | HTTPS:443 | Tato adresa je potřeba přístup imagí v Microsoft Container Registry (MCR). |
| *.cdn.mscr.io              | HTTPS:443 | Tato adresa je povinná pro MCR úložiště zajišťuje podporu Azure content delivery network (CDN). |
| management.azure.com       | HTTPS:443 | Tato adresa je povinná pro Kubernetes GET/PUT operace. |
| login.microsoftonline.com  | HTTPS:443 | Tato adresa je povinná pro ověřování Azure Active Directory. |
| api.snapcraft.io           | HTTPS:443, HTTP:80 | Tato adresa je potřeba nainstalovat balíčky Snap na uzly s Linuxem. |
| ntp.ubuntu.com             | UDP:123   | Tato adresa je povinná pro synchronizaci času NTP na uzly s Linuxem. |
| *.docker.io                | HTTPS:443 | Tato adresa je potřeba o přijetí změn imagí kontejnerů požadované pro tunelové propojení front. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Volitelné doporučené adresy a porty pro clustery AKS

* UDP port *53* pro DNS

Následující plně kvalifikovaný název domény / pravidla aplikací se doporučují pro AKS clustery fungoval správně:

| PLNĚ KVALIFIKOVANÝ NÁZEV DOMÉNY                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | Tato adresa umožňuje stáhnout požadované zabezpečení oprav a aktualizací uzlech clusteru s Linuxem. |
| packages.microsoft.com                  | HTTPS:443 | Tato adresa se používá úložiště balíčky společnosti Microsoft pro uložené v mezipaměti *apt-get* operace. |
| dc.services.visualstudio.com            | HTTPS:443 | Doporučeno pro správné metriky a monitorování pomocí Azure monitoru. |
| *.opinsights.azure.com                  | HTTPS:443 | Doporučeno pro správné metriky a monitorování pomocí Azure monitoru. |
| *.monitoring.azure.com                  | HTTPS:443 | Doporučeno pro správné metriky a monitorování pomocí Azure monitoru. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Tato adresa se používá pro správné fungování Azure Policy (aktuálně ve verzi preview ve službě AKS). |
| apt.dockerproject.org                   | HTTPS:443 | Tato adresa se používá k instalaci správné ovladače a operace na uzlech založený na grafickém procesoru. |
| nvidia.github.io                        | HTTPS:443 | Tato adresa se používá k instalaci správné ovladače a operace na uzlech založený na grafickém procesoru. |

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jaké porty a adresy, které povolí, pokud omezíte výchozí přenos pro cluster. Můžete také definovat, jak můžete sami podů komunikovat a jaká omezení mají v rámci clusteru. Další informace najdete v tématu [zabezpečení přenosu mezi pody pomocí zásady sítě ve službě AKS][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
