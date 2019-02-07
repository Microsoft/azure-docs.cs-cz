---
title: Principy – škálování aplikace ve službě Azure Kubernetes služby (AKS)
description: Další informace o škálování ve službě Azure Kubernetes Service (AKS), včetně automatického škálování podů vodorovné, automatického škálování clusteru a konektoru Azure Container Instances.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: f464b6411e1375fa005980454a9b61d2d6c05b0b
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819106"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Škálování možností pro aplikace ve službě Azure Kubernetes Service (AKS)

Při spouštění aplikací ve službě Azure Kubernetes Service (AKS), budete muset zvýšit nebo snížit množství výpočetních prostředků. Jako počet instancí aplikace, které budete potřebovat změnit počet uzlů může být také nutné změnit základní Kubernetes. Také můžete potřebovat k rychlému zřízení velký počet instancí další aplikace.

Tento článek představuje základní koncepty, které vám pomůžou škálovat aplikace ve službě AKS:

- [Ruční škálování](#manually-scale-pods-or-nodes)
- [Vodorovné pod automatického škálování (HPA)](#horizontal-pod-autoscaler)
- [Automatického škálování clusteru](#cluster-autoscaler)
- Integrace služby Azure Container Instance (ACI) s AKS

## <a name="manually-scale-pods-or-nodes"></a>Ruční škálování podů nebo uzly

Můžete ručně škálovat repliky (podů) a uzly pro testování, jak vaše aplikace reaguje na změnu dostupných prostředků a stavu. Ruční škálování prostředků také umožňuje definovat nastavené množství prostředků, který používají k údržbě hradit fixní částku, jako je počet uzlů. Ručně škálovat, definujete repliky nebo počtu uzlů a rozhraní Kubernetes API plány vytváření dalších podů nebo vyprazdňování uzlů.

Abyste mohli začít s ruční škálování podů a uzly [škálování aplikací ve službě AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Vodorovné pod automatického škálování

Kubernetes pomocí automatického škálování podů vodorovné (HPA) monitorovat požadavky na prostředky a automaticky škálovat počet replik. Ve výchozím nastavení kontroluje vodorovné pod automatického škálování metriky rozhraní API k nějakým nezbytným změnám v počet replik každých 30 sekund. Pokud nejsou nutné nějaké změny, počet replik zvýší nebo sníží odpovídajícím způsobem. Automatického škálování podů vodorovné pracuje s clustery AKS, které jste nasadili Server metriky pro Kubernetes 1.8 +.

![Kubernetes horizontální automatické škálování podů](media/concepts-scale/horizontal-pod-autoscaling.png)

Při konfiguraci automatického škálování podů vodorovné pro konkrétní nasazení, můžete definovat minimální a maximální počet replik, které můžou běžet. Můžete také definovat metriky pro monitorování a škálování rozhodnutích založit na, jako je například využití procesoru.

Začínáme s vodorovné pod automatického škálování ve službě AKS najdete v článku [automatické škálování podů ve službě AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Která škálování události

Jak automatického škálování podů vodorovné kontroly rozhraní API metrik každých 30 sekund, naleznete v předchozích událostech škálování nemusí byly úspěšně dokončeny před provedením další kontrolu. Toto chování může způsobit automatického škálování podů vodorovné Chcete-li změnit počet replik, než předchozí událost škálování byl schopný přijímat úlohy aplikace a požadavky na prostředky a odpovídajícím způsobem upravte.

Chcete-li minimalizovat tyto závodu události, která nebo zpoždění hodnoty lze nastavit. Tyto hodnoty definovat, jak dlouho musí čekat automatického škálování podů vodorovné po škálovací události, před další událost škálování se dá spouštět. Toto chování umožňuje novou repliku počítat se projeví a rozhraní API pro metriky odrážejí úlohách distribuovaných mezi. Ve výchozím nastavení zpoždění v měřítku události je 3 minuty a zpoždění o vertikální snížení kapacity události je 5 minut

Budete muset vyladit tyto hodnoty která. Výchozí hodnoty která může mít dojem, že není automatického škálování podů horizontální škálování počet replik dostatečně rychle. Například rychlejší zvýšit počet replik používá, snížit `--horizontal-pod-autoscaler-upscale-delay` při vytváření vodorovné podu definice automatického škálování pomocí `kubectl`.

## <a name="cluster-autoscaler"></a>Automatického škálování clusteru

Reagovat na měnící se požadavky podů Kubernetes má přizpůsobí počet uzlů, které jsou založené na požadované výpočetní prostředky ve fondu uzlu clusteru bylo. Ve výchozím nastavení kontroluje automatického škálování clusteru serveru rozhraní API každých 10 sekund k nějakým nezbytným změnám v počtu uzlů. Pokud automatické škálování clusteru zjistí, že je vyžadována změna, počet uzlů v clusteru AKS zvýší nebo sníží odpovídajícím způsobem. Funguje automatického škálování clusteru s podporou RBAC AKS clustery se spuštěnou technologií Kubernetes 1.10.x nebo vyšší.

![Automatického škálování clusteru Kubernetes](media/concepts-scale/cluster-autoscaler.png)

Automatického škálování clusteru se obvykle používá podél vodorovné pod automatického škálování. V kombinaci, automatického škálování podů horizontální zvýšení nebo snížení počtu podů založené na požadavcích aplikace a automatického škálování clusteru přizpůsobí počet uzlů podle potřeby ke spuštění těchto dalších podů odpovídajícím způsobem.

Začínáme s automatického škálování clusteru ve službě AKS najdete v článku [automatického škálování clusteru v AKS][aks-cluster-autoscaler].

### <a name="scale-up-events"></a>Vertikálně navýšit kapacitu události

Pokud uzel nemá dostatečný výpočetní prostředky, které spustit požadovaný pod, nemůžete tohoto podu průběhu procesu plánování. Pokud chcete pod nelze spustit, dokud další výpočetní prostředky, které jsou k dispozici v rámci fondu uzlů.

Při oznámení automatického škálování clusteru podů, které kvůli omezením prostředku fondu uzel se nedá naplánovat, se zvýší počet uzlů v rámci fondu uzel k poskytnutí dalších výpočetních prostředků. Pokud tyto další uzly jsou úspěšně nasazen a dostupný pro použití v rámci uzlu fondu, jsou podů pak naplánovány ke spuštění na ně.

Pokud vaše aplikace potřebuje rychle škálovat, může několik podů zůstanou ve stavu čekání na naplánované, dokud další uzly, které jsou nasazeny pomocí automatického škálování clusteru můžete přijímat plánované pody. Pro aplikace, které mají vysokou burst požadavky je možné škálovat s virtuálními uzly a Azure Container Instances.

### <a name="scale-down-events"></a>Vertikálně snížit kapacitu události

Automatického škálování clusteru také sleduje pod plánování stav pro uzly, které nedávno neobdrželi nové žádosti o plánování. Tento scénář označuje, že fond uzlů má víc výpočetních prostředků, než jsou povinné a, můžete snížit počet uzlů.

Uzel, který předává prahovou hodnotu pro už je nepotřebujete po dobu 10 minut ve výchozím nastavení je naplánováno odstranění. Pokud dojde k této situaci, podů jsou naplánovány ke spuštění na jiných uzlech v rámci fondu uzlů a automatického škálování clusteru snižuje počet uzlů.

Vaše aplikace může docházet některých přerušení, jako podů naplánováno na různých uzlech clusteru automatického škálování snižuje počet uzlů. Pokud chcete přerušení minimalizovat, vyhněte se aplikace, které používají jeden pod instance.

## <a name="burst-to-azure-container-instances"></a>Přejít na Azure Container Instances

Rychle škálovat AKS cluster, můžete integrovat s Azure Container Instances (ACI). Kubernetes má integrované komponenty škálovat počet replik a uzel. Ale pokud vaše aplikace potřebuje rychle škálovat, automatického škálování podů vodorovné může naplánovat další pody, než se můžou poskytovat pomocí existujících výpočetních prostředků na fond uzlů. Pokud nakonfigurované, tento scénář znamenalo automatického škálování clusteru k nasazení dalších uzlů ve fondu uzlu, ale může trvat několik minut, než ty uzly k úspěšné zřízení a povolit Kubernetes plánovač spustit podů na ně.

![Kubernetes burst škálování do služby ACI](media/concepts-scale/burst-scaling.png)

ACI vám umožní rychle nasadit kontejner instancí bez režie další infrastrukturu. Když se připojíte pomocí AKS, stane ACI zabezpečené, logické rozšíření ve vašem clusteru AKS. V clusteru AKS, který představuje ACI jako virtuální uzel Kubernetes je nainstalovaná komponenta Virtual Kubelet. Kubernetes pak můžete plánovat podů, které běží jako instance ACI prostřednictvím virtuální uzly, nikoli jako podů na uzlech virtuálních počítačů přímo ve vašem clusteru AKS.

Vaše aplikace vyžaduje žádné změny použít virtuální uzly. Nasazení můžete škálovat napříč AKS a ACI a bez zpoždění jako cluster automatického škálování nasadí nové uzly v clusteru AKS.

Virtuální uzly jsou nasazené do další podsítě ve stejné virtuální síti jako clusteru AKS. Tato konfigurace virtuální sítě umožňuje přenos dat mezi ACI a AKS zabezpečit. Podobně jako AKS cluster je ACI instance zabezpečené a logické výpočetní prostředek, který je izolovaný od ostatních uživatelů.

## <a name="next-steps"></a>Další postup

Abyste mohli začít s škálování aplikací, nejdřív postupujte [rychlý start k vytvoření clusteru AKS pomocí Azure CLI][aks-quickstart]. Potom můžete spustit ručně nebo automaticky škálovat aplikace ve vašem clusteru AKS:

- Ruční škálování [podů] [ aks-manually-scale-pods] nebo [uzly][aks-manually-scale-nodes]
- Použití [vodorovné pod automatického škálování][aks-hpa]
- Použití [automatického škálování clusteru][aks-cluster-autoscaler]

Další informace o Kubernetes základní informace a koncepty AKS naleznete v následujících článcích:

- [Kubernetes / AKS clustery a úlohy][aks-concepts-clusters-workloads]
- [Kubernetes / AKS přístup a identita][aks-concepts-identity]
- [Kubernetes / zabezpečení AKS][aks-concepts-security]
- [Kubernetes / virtuální sítě AKS][aks-concepts-network]
- [Kubernetes / AKS storage][aks-concepts-storage]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
