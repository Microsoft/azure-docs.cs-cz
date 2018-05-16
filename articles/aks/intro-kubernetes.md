---
title: Úvod do služby Azure Kubernetes Service
description: Azure Kubernetes Service zjednodušuje nasazování a správu aplikací založených na kontejnerech v Azure.
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 4a36809b0f6a041ac4d9250624495aeaf1e397e9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="introduction-to-azure-kubernetes-service-aks-preview"></a>Úvod do služby Azure Kubernetes Service (AKS) ve verzi Preview

Azure Kubernetes Service (AKS) zjednodušuje vytvoření, konfiguraci a správu clusteru virtuálních počítačů, ve kterých je předem nakonfigurované spouštění kontejnerizovaných aplikací. Díky tomu můžete při nasazování a správě kontejnerových aplikací v Microsoft Azure využívat své stávající dovednosti nebo stavět na rozsáhlých a stále se rozšiřujících odborných znalostech komunity.

Díky používání AKS můžete využívat výhody funkcí Azure na podnikové úrovni, a přitom zachovávat přenositelnost aplikací prostřednictvím Kubernetes a formátu image Dockeru.

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) je aktuálně ve verzi **Preview**. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.
>

## <a name="managed-kubernetes-in-azure"></a>Managed Kubernetes v Azure

AKS zjednodušuje správu clusteru Kubernetes a snižuje provozní režii s tím spojenou díky přenášení většiny zodpovědnosti na Azure. Jako hostovaná služba Kubernetes se za vás Azure stará o důležité úlohy, jako je monitorování stavu a údržba. Navíc platíte pouze za agentské uzly v rámci vašich clusterů, a ne za hlavní uzly. AKS jako spravovaná služba Kubernetes poskytuje:

> [!div class="checklist"]
> * Automatické upgrady verzí Kubernetes a opravy chyb
> * Snadné škálování clusterů
> * Hostovanou rovinu řízení s automatickými opravami (hlavní uzly)
> * Úspory nákladů – platíte pouze za spuštěné uzly fondu agentů

Díky tomu, že se Azure stará o správu uzlů ve vašem clusteru AKS, už řadu úloh, například upgrady clusteru, nemusíte provádět ručně. Vzhledem k tomu, že se Azure stará o tyto důležité úlohy údržby za vás, neposkytuje AKS přímý přístup (například pomocí SSH) ke clusteru.

## <a name="using-azure-kubernetes-service-aks"></a>Použití služby Azure Kubernetes Service (AKS)
Cílem AKS je poskytnout hostitelské prostředí pro kontejnery díky používání nástrojů a technologií open source, které jsou dnes mezi zákazníky oblíbené. Za tímto účelem zveřejňujeme standardní koncové body rozhraní Kubernetes API. S použitím těchto standardních koncových bodů můžete využívat veškerý software, který dokáže komunikovat s clusterem Kubernetes. Můžete například zvolit [kubectl][kubectl-overview], [helm][helm] nebo [draft][draft].

## <a name="creating-a-kubernetes-cluster-using-azure-kubernetes-service-aks"></a>Vytvoření clusteru Kubernetes pomocí služby Azure Kubernetes Service (AKS)
Pokud chcete začít používat AKS, nasaďte cluster AKS pomocí [Azure CLI][aks-quickstart] nebo přes portál (na Marketplace vyhledejte **Azure Kubernetes Service**). Pokud jste pokročilý uživatel a potřebujete větší kontrolu nad šablonami Azure Resource Manageru, použijte open source projektu [acs-engine][acs-engine] k sestavení vlastního clusteru Kubernetes a nasaďte ho přes CLI `az`.

### <a name="using-kubernetes"></a>S použitím Kubernetes
Kubernetes automatizuje nasazení, škálování a správu kontejnerizovaných aplikací. Má bohatou výbavu funkcí, například:
* Automatické balení do kontejnerů
* Samoopravení
* Horizontální škálování
* Zjišťování služeb a vyrovnávání zatížení
* Automatická uvádění a vracení zpět
* Správa tajných kódů a konfigurací
* Orchestrace úložiště
* Spouštění dávek

## <a name="videos"></a>Videa

Azure Kubernetes Service (AKS) – Azure Friday, říjen 2017:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Nástroje pro vývoj a nasazování aplikací v Kubernetes – Azure OpenDev, červen 2017:

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

Další informace o nasazení a správě AKS najdete v rychlých startech pro AKS.

> [!div class="nextstepaction"]
> [Kurz AKS][aks-quickstart]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md

