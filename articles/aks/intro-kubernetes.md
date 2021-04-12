---
title: Úvod do služby Azure Kubernetes Service
description: Poznejte funkce a výhody služby Azure Kubernetes Service, při nasazování a správě aplikací založených na kontejnerech v Azure.
services: container-service
ms.topic: overview
ms.date: 02/24/2021
ms.custom: mvc
ms.openlocfilehash: 1cddd39d0b95e021478235fcdafbacd40eb4097c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105251"
---
# <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Služba Azure Kubernetes Service (AKS) zjednodušuje nasazení spravovaného clusteru Kubernetes v Azure díky přesměrování provozní režie do Azure. Jako hostovaná služba Kubernetes zpracovává Azure kritické úkoly, jako je monitorování stavu a údržba. Vzhledem k tomu, že hlavní servery Kubernetes spravuje Azure, spravujete a spravujete jenom uzly agentů. Proto je AKS zdarma; platíte jenom za uzly agenta v rámci svých clusterů, a ne pro hlavní servery.  

Cluster AKS můžete vytvořit pomocí:
* [Azure CLI](kubernetes-walkthrough.md)
* [Azure Portal](kubernetes-walkthrough-portal.md)
* [Azure PowerShell](kubernetes-walkthrough-powershell.md)
* Použití možností nasazení založených na šablonách, například [Azure Resource Manager šablon](kubernetes-walkthrough-rm-template.md) a terraformu 

Při nasazování clusteru AKS se hlavní uzel Kubernetes i všechny ostatní uzly nasadí a nakonfigurují za vás. Pokročilé sítě, Azure Active Directory (Azure AD) Integration, monitoring a další funkce, se dají nakonfigurovat během procesu nasazení. 

Další informace o základech Kubernetes najdete v tématu [Kubernetes Core koncepty pro AKS][concepts-clusters-workloads].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]
> AKS podporuje i kontejnery Windows serveru.

## <a name="access-security-and-monitoring"></a>Přístup, zabezpečení a sledování

Pro zlepšení zabezpečení a správy vám AKS umožňuje integrovat s Azure AD do těchto součástí:
* Použijte řízení přístupu na základě role (Kubernetes RBAC) Kubernetes. 
* Monitorujte stav svého clusteru a prostředků.

### <a name="identity-and-security-management"></a>Správa identit a zabezpečení

#### <a name="kubernetes-rbac"></a>Kubernetes RBAC

Aby AKS omezil přístup k prostředkům clusteru, podporuje [KUBERNETES RBAC][kubernetes-rbac]. Kubernetes RBAC řídí přístup a oprávnění pro Kubernetes prostředky a obory názvů.  

#### <a name="azure-ad"></a>Azure AD

Cluster AKS můžete nakonfigurovat pro integraci se službou Azure AD. S integrací služby Azure AD můžete nastavit přístup Kubernetes na základě stávající identity a členství ve skupinách. Stávající uživatele a skupiny Azure AD je možné poskytnout s integrovaným přihlašováním a přístupem k prostředkům AKS.  

Další informace o identitě najdete v tématu [Možnosti přístupu a identit pro AKS][concepts-identity].

Informace, jak zabezpečit váš cluster AKS, najdete v tématu [Integrace Azure Active Directory s AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Integrované protokolování a monitorování

Azure Monitor pro stav kontejnerů shromažďuje metriky výkonu paměti a procesoru z kontejnerů, uzlů a řadičů v rámci clusteru AKS a nasazených aplikací. Můžete zkontrolovat protokoly kontejneru i [hlavní protokoly Kubernetes][aks-master-logs], které jsou:
* Uložené v pracovním prostoru Azure Log Analytics.
* K dispozici prostřednictvím Azure Portal, Azure CLI nebo koncového bodu REST.

Další informace najdete v tématu [Monitorování stavu kontejneru služby Azure Kubernetes Service][container-health].

## <a name="clusters-and-nodes"></a>Clustery a uzly

Uzly AKS běží na virtuálních počítačích Azure (VM). Pomocí uzlů AKS můžete připojit úložiště k uzlům a luskům, upgradovat součásti clusteru a používat GPU. AKS podporuje clustery Kubernetes, které spouštějí více fondů uzlů pro podporu smíšených operačních systémů a kontejnerů Windows serveru.  

Další informace o možnostech clusteru Kubernetes, uzlu a fondu uzlů najdete v tématu [Kubernetes Core koncepty pro AKS][concepts-clusters-workloads].

### <a name="cluster-node-and-pod-scaling"></a>Uzel clusteru a škálování podů

Jako požadavek na změnu prostředků se počet uzlů clusteru nebo lusky, které spouštějí vaše služby, automaticky škálují směrem nahoru nebo dolů. Můžete upravit jak horizontální, tak i automatické škálování clusteru, aby se nastavily na požadavky a běžely jenom nezbytné prostředky.

Další informace najdete v tématu [Škálování clusteru Azure Kubernetes Service (AKS)][aks-scale].

### <a name="cluster-node-upgrades"></a>Upgrady uzlů clusteru

AKS nabízí několik verzí Kubernetes. Když budou nové verze dostupné v AKS, můžete upgradovat cluster pomocí Azure Portal nebo Azure CLI. Během procesu upgradu jsou uzly pečlivě uzavřené a vyprázdněné, aby se minimalizovalo přerušení spuštěných aplikací.  

Další informace o verzích životního cyklu, naleznete v tématu [Podporované verze Kubernetes v AKS][aks-supported versions]. Další postup, jak upgradovat, najdete v tématu [Upgrade clusteru Azure Kubernetes Service (AKS)][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Uzly s podporou GPU

AKS podporuje vytváření fondů uzlů s podporou GPU. Azure v současné době poskytuje jeden nebo víc virtuálních počítačů s podporou GPU. Virtuální počítače s podporou GPU jsou navržené pro úlohy náročné na výpočetní výkon, náročné na grafiku a vizualizaci.

Další informace najdete v tématu [Použití grafických procesorů v AKS][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Důvěrné výpočetní uzly (Public Preview)

AKS podporuje vytváření fondů pro důvěrné výpočetní uzly založené na Intel SGX (virtuální počítače DCSv2). V případě důvěrných výpočetních uzlů je možné kontejnery spouštět v prostředí enclaves (Trusted Execution Environment). Izolace mezi kontejnery v kombinaci s integritou kódu prostřednictvím ověření identity vám může pomáhat s vaší ochrannou strategií zabezpečení kontejneru v rámci důkladné ochrany. Důvěrné výpočetní uzly podporují důvěrné kontejnery (existující aplikace Docker) i kontejnery s podporou enklávy.

Další informace najdete v tématu [důvěrné výpočetní uzly v AKS][conf-com-node].

### <a name="storage-volume-support"></a>Podpora svazků úložiště

Aby bylo možné podporovat aplikační úlohy, můžete pro trvalá data připojit statické nebo dynamické svazky úložiště. V závislosti na počtu připojení, které očekáváte ke sdílení svazků úložiště, můžete využít úložiště, na které se zálohuje:
* Disky Azure pro přístup k jednomu pod nebo 
* Soubory Azure pro více souběžných přístupů pod.

Další informace najdete v tématu [Možnosti úložiště pro aplikace v AKS][concepts-storage].

Začněte s dynamickými trvalými svazky na [discích Azure][azure-disk] nebo v [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Virtuální sítě a příchozí přenos dat

Cluster AKS je možné nasadit do stávající virtuální sítě. V této konfiguraci je každé z nich v clusteru přiřazená IP adresa ve virtuální síti a může přímo komunikovat s těmito službami:
* Jiné lusky v clusteru 
* Další uzly ve virtuální síti. 

Lusky se taky můžou připojit k ostatním službám v partnerské virtuální síti a k místním sítím přes ExpressRoute nebo připojení VPN typu Site-to-Site (S2S).  

Další informace najdete v tématu [Koncepty sítě pro aplikace v AKS][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Příchozí přenos dat se směrováním aplikace HTTP

Doplněk pro směrování aplikací HTTP vám pomůže snadno získat přístup k aplikacím nasazeným do clusteru AKS. Když je povolené, řešení směrování aplikace HTTP nakonfiguruje v clusteru AKS kontroler příchozího přenosu dat.  

Při nasazení aplikací jsou všechny veřejně dostupné názvy DNS nakonfigurované. Směrování aplikace HTTP nastaví zónu DNS a integruje ji do clusteru AKS. Pak můžete nasadit prostředky příchozího přenosu dat Kubernetes jako za běžných okolností.  

Jak začít s přenosem příchozích dat, najdete v článku [Směrování aplikace HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Integrace nástrojů pro vývoj

Kubernetes má bohatou ekosystém nástrojů pro vývoj a správu, které bez problémů fungují s AKS. Mezi tyto nástroje patří Helm a rozšíření Kubernetes pro Visual Studio Code.   

Azure poskytuje několik nástrojů, které zjednodušují Kubernetes, například Azure Dev Spaces a DevOps Starter.  

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Služba Azure Dev Spaces poskytuje rychlé a iterativní vývojové prostředí Kubernetes pro týmy. S minimální konfigurací můžete spouštět a ladit kontejnery přímo v AKS. Jak začít, najdete v článku [Azure Dev Spaces][azure-dev-spaces].

### <a name="devops-starter"></a>DevOps Starter

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
> [Nasazení clusteru AKS pomocí Azure CLI][aks-cli]

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
[kubernetes-rbac]: concepts-identity.md#kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
