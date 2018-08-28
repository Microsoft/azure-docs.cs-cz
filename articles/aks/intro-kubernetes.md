---
title: Úvod do služby Azure Kubernetes Service
description: Azure Kubernetes Service zjednodušuje nasazování a správu aplikací založených na kontejnerech v Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e6f67c8900a7ee1728b474651b47d278cdfa51eb
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42023046"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) usnadňuje nasazení spravovaného clusteru Kubernetes v Azure. AKS zjednodušuje správu Kubernetes a snižuje provozní režii s tím spojenou díky přenášení většiny zodpovědnosti na Azure. Jako hostovaná služba Kubernetes se za vás Azure stará o důležité úlohy, jako je monitorování stavu a údržba. Služba je navíc bezplatná a platíte pouze za uzly agentů v rámci vašich clusterů, a ne za hlavní uzly.

Tento dokument obsahuje přehled funkcí služby Azure Kubernetes Service (AKS).

## <a name="flexible-deployment-options"></a>Flexibilní možnosti nasazení

Azure Kubernetes Service nabízí možnosti nasazení pomocí portálu, příkazového řádku a šablony (šablony Resource Manageru a Terraform). Při nasazování clusteru AKS se hlavní uzel Kubernetes i všechny ostatní uzly nasadí a nakonfigurují z vás. Během procesu nasazení je možné nakonfigurovat také další funkce, jako jsou pokročilé síťové funkce, integrace s Azure Active Directory a monitorování.

Další informace najdete v [rychlém startu pro portál AKS][aks-portal] a [rychlém startu pro AKS CLI][aks-cli].

## <a name="identity-and-security-management"></a>Správa identit a zabezpečení

Clustery AKS podporují [řízení přístupu na základě role (RBAC)][kubernetes-rbac]. Cluster AKS je také možné nakonfigurovat tak, aby se integroval s Azure Active Directory. V této konfiguraci je možné nakonfigurovat přístup ke Kubernetes na základě členství ve skupinách a identit Azure Active Directory.

Další informace najdete v tématu [Integrace Azure Active Directory s AKS][aks-aad].

## <a name="integrated-logging-and-monitoring"></a>Integrované protokolování a monitorování

Stav kontejneru poskytuje přehled o výkonu díky tomu, že shromažďuje metriky paměti a procesoru z kontejnerů, uzlů a kontrolerů. Shromažďují se také protokoly kontejnerů. Tato data se ukládají do pracovního prostoru Log Analytics a jsou dostupná prostřednictvím webu Azure Portal, Azure CLI nebo koncového bodu REST.

Další informace najdete v tématu [Monitorování stavu kontejneru služby Azure Kubernetes Service][container-health].

## <a name="cluster-node-scaling"></a>Škálování uzlů clusteru

Se zvyšujícími se požadavky na prostředky je možné horizontálně navýšit kapacitu uzlů clusteru AKS, aby jim vyhovovaly. Pokud se požadavky na prostředky sníží, uzly je možné odebrat horizontálním snížením kapacity clusteru. Operace škálování služby AKS je možné provádět pomocí webu Azure Portal nebo Azure CLI.

Další informace najdete v tématu [Škálování clusteru Azure Kubernetes Service (AKS)][aks-scale].

## <a name="cluster-node-upgrades"></a>Upgrady uzlů clusteru

Azure Kubernetes Service nabízí několik verzí Kubernetes. Jakmile budou v AKS k dispozici nové verze, můžete cluster upgradovat pomocí webu Azure Portal nebo Azure CLI. Během procesu upgradu jsou uzly pečlivě uzavřené a vyprázdněné, aby se minimalizovalo přerušení spuštěných aplikací.

Další informace najdete v tématu [Upgrade clusteru Azure Kubernetes Service (AKS)][aks-upgrade].

## <a name="http-application-routing"></a>Směrování aplikace HTTP

Řešení směrování aplikace HTTP usnadňuje přístup k aplikacím nasazeným do clusteru AKS. Když je povolené, řešení směrování aplikace HTTP nakonfiguruje v clusteru AKS kontroler příchozího přenosu dat. Při nasazování aplikací se automaticky konfigurují veřejně přístupné názvy DNS.

Další informace najdete v tématu [Směrování aplikace HTTP][aks-http-routing].

## <a name="gpu-enabled-nodes"></a>Uzly s podporou grafického procesoru

AKS podporuje vytváření fondů uzlů s podporou grafického procesoru. Azure v současné době nabízí virtuální počítače s podporou jednoho nebo více grafických procesorů. Virtuální počítače s podporou grafického procesoru jsou určené pro úlohy náročné na výpočetní nebo grafický výkon a úlohy vizualizace.

Další informace najdete v tématu [Použití grafických procesorů v AKS][aks-gpu].

## <a name="development-tooling-integration"></a>Integrace nástrojů pro vývoj

Kubernetes má bohatý ekosystém nástrojů pro vývoj a správu, jako je Helm, Draft a rozšíření Kubernetes pro Visual Studio Code. Tyto nástroje bez problémů fungují se službou Azure Kubernetes Service.

Služba Azure Dev Spaces navíc poskytuje rychlé a iterativní vývojové prostředí Kubernetes pro týmy. S minimální konfigurací můžete spouštět a ladit kontejnery přímo ve službě Azure Kubernetes Service (AKS).

Další informace najdete v tématu [Azure Dev Spaces][azure-dev-spaces].

Projekt Azure DevOps poskytuje jednoduché řešení pro přenos stávajícího kódu a úložiště Git do Azure. Projekt Azure DevOps automaticky vytvoří prostředky Azure, jako je služba AKS, kanál verze ve VSTS obsahující definici sestavení pro průběžnou integraci, nastaví definici verze pro průběžné nasazování a pak vytvoří prostředek Azure Application Insights pro účely monitorování.

Další informace najdete v tématu věnovaném [projektu Azure DevOps][azure-devops].

## <a name="virtual-network-integration"></a>Integrace virtuální sítě

Cluster AKS je možné nasadit do stávající virtuální sítě. V této konfiguraci se ke každému podu v clusteru přiřadí IP adresa ve virtuální síti a pody tak můžou komunikovat přímo s ostatními pody v clusteru a dalšími uzly ve virtuální síti. Pody se můžou připojit také k jiným službám v partnerské virtuální síti a k místním sítím přes ExpressRoute nebo připojení VPN typu Site-to-Site.

Další informace najdete v tématu [Přehled sítí AKS][aks-networking].

## <a name="private-container-registry"></a>Privátní registr kontejneru

Pokud chcete privátní úložiště pro své image Dockeru, proveďte integraci se službou Azure Container Registry (ACR).

Další informace najdete v tématu [Azure Container Registry (ACR)][acr-docs].

## <a name="storage-volume-support"></a>Podpora svazků úložiště

Azure Kubernetes Service (AKS) podporuje připojování svazků úložiště pro trvalá data. Clustery AKS se vytváří s podporou služby Soubory Azure a Disky Azure.

Další informace najdete v tématech [Soubory Azure][azure-files] a [Disky Azure][azure-disk].

## <a name="docker-image-support"></a>Podpora imagí Dockeru

Azure Kubernetes Service (AKS) podporuje formát image Dockeru.

## <a name="kubernetes-certification"></a>Certifikace Kubernetes

Azure Kubernetes Service (AKS) má certifikaci CNFC a je tedy v souladu s Kubernetes.

## <a name="regulatory-compliance"></a>Dodržování legislativní předpisů

Azure Kubernetes Service (AKS) dodržuje předpisy SOC a ISO.

## <a name="next-steps"></a>Další kroky

Další informace o nasazení a správě AKS najdete v rychlých startech pro AKS.

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
[aks-networking]: ./networking-overview.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./scale-cluster.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/en-us/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/en-us/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md

