---
title: Úvod do služby Azure Kubernetes Service
description: Poznejte funkce a výhody služby Azure Kubernetes Service, při nasazování a správě aplikací založených na kontejnerech v Azure.
services: container-service
ms.topic: overview
ms.date: 02/09/2021
ms.custom: mvc
ms.openlocfilehash: 58a467d697e782b3e21e7b488b7db4c9b8951b2a
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102616838"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Služba Azure Kubernetes Service (AKS) zjednodušuje nasazení spravovaného clusteru Kubernetes v Azure tím, že převede do Azure přesměrování na velkou složitost a provozní režii. Jako hostovaná služba Kubernetes zpracovává Azure kritické úkoly, jako je monitorování stavu a údržba.  

Vzhledem k tomu, že hlavní servery Kubernetes spravuje Azure, spravujete a spravujete jenom uzly agentů. Proto je jako spravovaná služba Kubernetes AKS volná; platíte jenom za uzly agenta v rámci svých clusterů, a ne pro hlavní servery.  

Cluster AKS můžete vytvořit pomocí Azure Portal, rozhraní příkazového řádku Azure, Azure PowerShell nebo pomocí možností nasazení na základě šablon, jako jsou například šablony Správce prostředků a Terraformu. Při nasazování clusteru AKS se hlavní uzel Kubernetes i všechny ostatní uzly nasadí a nakonfigurují za vás. Během procesu nasazení je možné nakonfigurovat také další funkce, jako jsou pokročilé síťové funkce, integrace s Azure Active Directory a monitorování. Kontejnery Windows serveru jsou podporované v AKS.

Další informace o základech Kubernetes najdete v tématu [Kubernetes Core koncepty pro AKS][concepts-clusters-workloads].

Začněte tím, že [v Azure Portal][aks-portal] nebo [pomocí Azure CLI][aks-cli]dokončíte rychlý Start AKS.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="access-security-and-monitoring"></a>Přístup, zabezpečení a sledování

Pro lepší zabezpečení a správu umožňuje AKS integraci s Azure Active Directory (Azure AD) a:
* Použijte řízení přístupu na základě role (Kubernetes RBAC) Kubernetes. 
* Monitorujte stav svého clusteru a prostředků.

### <a name="identity-and-security-management"></a>Správa identit a zabezpečení

Aby AKS omezil přístup k prostředkům clusteru, podporuje [KUBERNETES RBAC][kubernetes-rbac]. Kubernetes RBAC umožňuje řídit přístup a oprávnění k Kubernetes prostředkům a oborům názvů.  

Můžete také nakonfigurovat cluster AKS pro integraci se službou Azure AD. S integrací služby Azure AD můžete nakonfigurovat přístup Kubernetes na základě stávající identity a členství ve skupinách. Stávající uživatele a skupiny Azure AD je možné poskytnout s integrovaným přihlašováním a přístupem k prostředkům AKS.  

Další informace o identitě najdete v tématu [Možnosti přístupu a identit pro AKS][concepts-identity].

Informace, jak zabezpečit váš cluster AKS, najdete v tématu [Integrace Azure Active Directory s AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Integrované protokolování a monitorování

Azure Monitor pro stav kontejnerů shromažďuje metriky výkonu paměti a procesoru z kontejnerů, uzlů a řadičů v rámci clusteru AKS a nasazených aplikací. Můžete zkontrolovat protokoly kontejneru i [hlavní protokoly Kubernetes][aks-master-logs]. Tato data monitorování se ukládají v pracovním prostoru Azure Log Analytics a jsou dostupná prostřednictvím Azure Portal, Azure CLI nebo koncového bodu REST.

Další informace najdete v tématu [Monitorování stavu kontejneru služby Azure Kubernetes Service][container-health].

## <a name="clusters-and-nodes"></a>Clustery a uzly

Uzly AKS běží na virtuálních počítačích Azure (VM). Pomocí uzlů AKS můžete připojit úložiště k uzlům a luskům, upgradovat součásti clusteru a používat GPU. AKS podporuje clustery Kubernetes, které spouštějí více fondů uzlů pro podporu smíšených operačních systémů a kontejnerů Windows serveru.  

Další informace týkající se možností clusteru Kubernetes, uzlu a fondu uzlů najdete v tématu [Kubernetes Core koncepty pro AKS][concepts-clusters-workloads].

### <a name="cluster-node-and-pod-scaling"></a>Uzel clusteru a škálování podů

Jako požadavek na změnu prostředků můžete počet uzlů clusteru nebo podů, na kterých běží vaše služby, automaticky škálovat směrem nahoru nebo dolů. Můžete použít jak horizontálního automatického škálování podů, tak automatického škálování clusteru. Díky tomuto přístupu ke škálování je možné cluster AKS automaticky upravit podle požadavků a spouštět jenom potřebné prostředky.

Další informace najdete v tématu [Škálování clusteru Azure Kubernetes Service (AKS)][aks-scale].

### <a name="cluster-node-upgrades"></a>Upgrady uzlů clusteru

AKS nabízí několik verzí Kubernetes. Jakmile budou v AKS k dispozici nové verze, můžete cluster upgradovat pomocí webu Azure Portal nebo Azure CLI. Během procesu upgradu jsou uzly pečlivě uzavřené a vyprázdněné, aby se minimalizovalo přerušení spuštěných aplikací.  

Další informace o verzích životního cyklu, naleznete v tématu [Podporované verze Kubernetes v AKS][aks-supported versions]. Další postup, jak upgradovat, najdete v tématu [Upgrade clusteru Azure Kubernetes Service (AKS)][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Uzly s podporou GPU

AKS podporuje vytváření fondů uzlů s podporou GPU. Azure v současné době poskytuje jeden nebo víc virtuálních počítačů s podporou GPU. Virtuální počítače s podporou GPU jsou navržené pro úlohy náročné na výpočetní výkon, náročné na grafiku a vizualizaci.

Další informace najdete v tématu [Použití grafických procesorů v AKS][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Důvěrné výpočetní uzly (Public Preview)

AKS podporuje vytváření fondů pro důvěrné výpočetní uzly založené na Intel SGX (virtuální počítače DCSv2). V případě důvěrných výpočetních uzlů je možné kontejnery spouštět v prostředí enclaves (Trusted Execution Environment). Izolace mezi kontejnery v kombinaci s integritou kódu prostřednictvím ověření identity vám může pomáhat s vaší ochrannou strategií zabezpečení kontejneru v rámci důkladné ochrany. Důvěrné výpočetní uzly podporují důvěrné kontejnery (existující aplikace Docker) i kontejnery s podporou enklávy.

Další informace najdete v tématu [důvěrné výpočetní uzly v AKS][conf-com-node].

### <a name="storage-volume-support"></a>Podpora svazků úložiště

Můžete připojit svazky úložiště pro trvalý přístup dat, abyste podpořili úlohy aplikací. Můžete použít statické i dynamické svazky. V závislosti na počtu připojení, které očekáváte ke sdílení svazků úložiště, můžete využít úložiště zajištěné buď disky Azure pro přístup k jednomu z nich, nebo soubory Azure pro více souběžných přístupů.

Další informace najdete v tématu [Možnosti úložiště pro aplikace v AKS][concepts-storage].

Začněte s dynamickými trvalými svazky na [discích Azure][azure-disk] nebo v [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Virtuální sítě a příchozí přenos dat

Cluster AKS je možné nasadit do stávající virtuální sítě. V této konfiguraci je každé z nich v clusteru přiřazená IP adresa ve virtuální síti a může přímo komunikovat s dalšími lusky v clusteru a dalšími uzly ve virtuální síti. Lusky se taky můžou připojit k ostatním službám v partnerské virtuální síti a k místním sítím přes ExpressRoute nebo připojení VPN typu Site-to-Site (S2S).  

Další informace najdete v tématu [Koncepty sítě pro aplikace v AKS][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Příchozí přenos dat se směrováním aplikace HTTP

Doplněk směrování aplikace HTTP usnadňuje přístup aplikace nasazené na clusteru AKS. Když je povolené, řešení směrování aplikace HTTP nakonfiguruje v clusteru AKS kontroler příchozího přenosu dat.  

Při nasazení aplikací jsou všechny veřejně dostupné názvy DNS nakonfigurované. Směrování aplikace HTTP nastaví zónu DNS a integruje ji do clusteru AKS. Pak můžete nasadit prostředky příchozího přenosu dat Kubernetes jako za běžných okolností.  

Jak začít s přenosem příchozích dat, najdete v článku [Směrování aplikace HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Integrace nástrojů pro vývoj

Kubernetes má bohatou ekosystém nástrojů pro vývoj a správu, které bez problémů fungují s AKS. Mezi tyto nástroje patří Helm a rozšíření Kubernetes pro Visual Studio Code. Tyto nástroje bez problémů fungují s AKS.  

Azure navíc poskytuje několik nástrojů, které vám pomůžou zjednodušit Kubernetes, jako je DevOps Starter.  

DevOps Starter poskytuje jednoduché řešení pro vložení stávajícího kódu a úložišť Git do Azure. DevOps Starter automaticky:
* Vytvoří prostředky Azure (například AKS); 
* Nakonfiguruje kanál vydání v Azure DevOps Services, který obsahuje kanál sestavení pro CI; 
* Nastaví pro CD kanál verze; ani 
* Vygeneruje prostředek Azure Application Insights pro monitorování. 

Další informace najdete v tématu [DevOps Starter][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Podpora image dockeru a privátní registr kontejnerů

AKS podporuje formátu image dockeru. Pro soukromé úložiště vašich imagí dockeru můžete AKS provést integraci se službou Azure Container Registry (ACR).

Pokud chcete vytvořit úložiště privátních imagí, přečtěte si téma [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Certifikace Kubernetes

AKS je CNCF – certifikováno jako Kubernetesické podmínky.

## <a name="regulatory-compliance"></a>Dodržování legislativní předpisů

AKS je kompatibilní s SOC, ISO, PCI DSS a HIPAA. Další informace najdete v tématu [Přehled dodržování předpisů Microsoft Azure][compliance-doc].

## <a name="next-steps"></a>Další kroky

Další informace o nasazení a správě AKS najdete v rychlém startu Azure CLI.

> [!div class="nextstepaction"]
> [Rychlý start pro AKS][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://azure.microsoft.com/en-us/overview/trusted-cloud/compliance/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: ../dev-spaces/index.yml
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/containers/container-insights-overview.md
[aks-master-logs]: ./view-control-plane-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
