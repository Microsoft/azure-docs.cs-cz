---
title: Úvod do služby Azure Kubernetes Service
description: Poznejte funkce a výhody služby Azure Kubernetes Service, při nasazování a správě aplikací založených na kontejnerech v Azure.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: overview
ms.date: 09/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ac18b8099b14f29579b35eb8dd2124d2d63f80d7
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49390070"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) usnadňuje nasazení spravovaného clusteru Kubernetes v Azure. AKS zjednodušuje správu Kubernetes a snižuje provozní režii s tím spojenou díky přenášení většiny zodpovědnosti na Azure. Jako hostovaná služba Kubernetes se za vás Azure stará o důležité úlohy, jako je monitorování stavu a údržba. Hlavní servery Kubernetes spravuje Azure. Pouze spravujete a udržujete uzly agentů. Jako spravované prostředí Kubernetes je AKS navíc zdarmaa platíte pouze za uzly agentů v rámci vašich clusterů, a ne za hlavní uzly.

Cluster AKS na portálu Azure můžete vytvořit pomocí Azure CLI nebo šablony řízené možnostmi nasazení, jako jsou šablony Resource Manager a Terraform. Při nasazování clusteru AKS se hlavní uzel Kubernetes i všechny ostatní uzly nasadí a nakonfigurují za vás. Během procesu nasazení je možné nakonfigurovat také další funkce, jako jsou pokročilé síťové funkce, integrace s Azure Active Directory a monitorování.

Abyste mohli začít, projděte si rychlé spuštění AKS [na portálu Azure][aks-portal] nebo [pomocí Azure CLI][aks-cli].

## <a name="access-security-and-monitoring"></a>Přístup, zabezpečení a sledování

Pro lepší zabezpečení a správu vám AKS umožní integraci s Azure Active Directory a použití řízení přístupu na základě rolí Kubernetes. Můžete také monitorovat stav vašeho clusteru a prostředků.

### <a name="identity-and-security-management"></a>Správa identit a zabezpečení

Chcete-li omezit přístup k prostředkům clusteru, AKS podporuje [řízení přístupu na základě rolí (RBAC) Kubernetes][kubernetes-rbac]. RBAC umožňuje řídit přístup k prostředkům a oborům názvů Kubernetes a oprávnění k těmto prostředkům. Cluster AKS můžete také nakonfigurovat tak, aby se integroval s Azure Active Directory (AD). S integrací Azure AD je možné nakonfigurovat přístup ke Kubernetes na základě členství ve skupinách a identit. Vašim stávajícím uživatelům Azure AD a skupinám lze poskytnout přístup k prostředkům AKS a s integrovanou možností přihlašování.

Informace, jak zabezpečit váš cluster AKS, najdete v tématu [Integrace Azure Active Directory s AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Integrované protokolování a monitorování

Abyste pochopili, jak pracuje váš cluster AKS a nasazené aplikace, Azure Monitor bude sledovat stav kontejneru a shromažďovat metriky paměti a procesoru z kontejnerů, uzlů a kontrolerů. Protokoly kontejneru jsou k dispozici a můžete také [zkontrolovat hlavní protokoly Kubernetes][aks-master-logs]. Tato data ze sledování se ukládají do pracovního prostoru Azure Log Analytics a jsou dostupná prostřednictvím webu Azure Portal, Azure CLI nebo koncového bodu REST.

Další informace najdete v tématu [Monitorování stavu kontejneru služby Azure Kubernetes Service][container-health].

## <a name="cluster-and-node"></a>Clustery a uzly

Uzly AKS jsou spuštěny na virtuálních počítačích Azure. Můžete úložiště připojit k uzlům a podům, upgradovat komponenty clusteru a použít GPU.

### <a name="cluster-node-and-pod-scaling"></a>Uzel clusteru a škálování podů

Jako požadavek na změnu prostředků můžete počet uzlů clusteru nebo podů, na kterých běží vaše služby, automaticky škálovat směrem nahoru nebo dolů. Můžete použít jak horizontálního automatického škálování podů, tak automatického škálování clusteru. Díky tomuto přístupu ke škálování je možné cluster AKS automaticky upravit podle požadavků a spouštět jenom potřebné prostředky.

Další informace najdete v tématu [Škálování clusteru Azure Kubernetes Service (AKS)][aks-scale].

### <a name="cluster-node-upgrades"></a>Upgrady uzlů clusteru

Azure Kubernetes Service nabízí několik verzí Kubernetes. Jakmile budou v AKS k dispozici nové verze, můžete cluster upgradovat pomocí webu Azure Portal nebo Azure CLI. Během procesu upgradu jsou uzly pečlivě uzavřené a vyprázdněné, aby se minimalizovalo přerušení spuštěných aplikací.

Další informace o verzích životního cyklu, naleznete v tématu [Podporované verze Kubernetes v AKS][aks-supported versions]. Další postup, jak upgradovat, najdete v tématu [Upgrade clusteru Azure Kubernetes Service (AKS)][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Uzly s podporou grafického procesoru

AKS podporuje vytváření fondů uzlů s podporou grafického procesoru. Azure v současné době nabízí virtuální počítače s podporou jednoho nebo více grafických procesorů. Virtuální počítače s podporou grafického procesoru jsou určené pro úlohy náročné na výpočetní nebo grafický výkon a úlohy vizualizace.

Další informace najdete v tématu [Použití grafických procesorů v AKS][aks-gpu].

### <a name="storage-volume-support"></a>Podpora svazků úložiště

Můžete připojit svazky úložiště pro trvalý přístup dat, abyste podpořili úlohy aplikací. Je možné použít jak statické, tak dynamické svazky. V závislosti na tom, kolik připojených podů sdílí úložiště, můžete použít úložiště s podporu disků Azure pro přístup jednoho podu nebo soubory Azure pro přístup více souběžných podů.

Začínáme s dynamicky trvalými svazky s [disky Azure][azure-disk] nebo [soubory Azure][azure-files].

## <a name="virtual-networks-and-ingress"></a>Virtuální sítě a příchozí přenos dat

Cluster AKS je možné nasadit do stávající virtuální sítě. V této konfiguraci se ke každému podu v clusteru přiřadí IP adresa ve virtuální síti a pody tak můžou komunikovat přímo s ostatními pody v clusteru a dalšími uzly ve virtuální síti. Pody se můžou připojit také k jiným službám v partnerské virtuální síti a k místním sítím přes ExpressRoute nebo připojení VPN typu Site-to-Site (S2S).

Další informace najdete v tématu [Přehled sítí AKS][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Příchozí přenos dat se směrováním aplikace HTTP

Doplněk směrování aplikace HTTP usnadňuje přístup aplikace nasazené na clusteru AKS. Když je povolené, řešení směrování aplikace HTTP nakonfiguruje v clusteru AKS kontroler příchozího přenosu dat. Při nasazování aplikací se automaticky konfigurují veřejně přístupné názvy DNS. Směrování aplikace HTTP nakonfiguruje zónu DNS a integruje ji do clusteru AKS. Pak můžete nasadit prostředky příchozího přenosu dat Kubernetes jako za běžných okolností.

Jak začít s přenosem příchozích dat, najdete v článku [Směrování aplikace HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Integrace nástrojů pro vývoj

Kubernetes má bohatý ekosystém nástrojů pro vývoj a správu, jako je Helm, Draft a rozšíření Kubernetes pro Visual Studio Code. Tyto nástroje bez problémů fungují s AKS.

Služba Azure Dev Spaces navíc poskytuje rychlé a iterativní vývojové prostředí Kubernetes pro týmy. S minimální konfigurací můžete spouštět a ladit kontejnery přímo v AKS. Jak začít, najdete v článku [Azure Dev Spaces][azure-dev-spaces].

Projekt Azure DevOps poskytuje jednoduché řešení pro přenos stávajícího kódu a úložiště Git do Azure. Projekt Azure DevOps automaticky vytvoří prostředky Azure, jako je služba AKS, kanál verze ve Azure DevOps Services obsahující kanály buildu pro průběžnou integraci, nastaví kanály verze pro průběžné nasazování a pak vytvoří prostředek Azure Application Insights pro účely monitorování.

Další informace najdete v tématu věnovaném [projektu Azure DevOps][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Podpora image dockeru a privátní registr kontejnerů

AKS podporuje formátu image dockeru. Pro soukromé úložiště vašich imagí dockeru můžete AKS provést integraci se službou Azure Container Registry (ACR).

Chcete-li vytvořit úložiště bitových kopií, viz téma [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Certifikace Kubernetes

Azure Kubernetes Service (AKS) má certifikaci CNFC a je tedy v souladu s Kubernetes.

## <a name="regulatory-compliance"></a>Dodržování legislativní předpisů

Azure Kubernetes Service (AKS) je kompatibilní s normami SOC, ISO a PCI DSS.

## <a name="next-steps"></a>Další kroky

Další informace o nasazení a správě AKS najdete v rychlých startech pro Azure CLI.

> [!div class="nextstepaction"]
> [Rychlý start pro AKS][aks-cli]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./aad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
