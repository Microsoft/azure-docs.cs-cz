---
title: Osvědčené postupy pro izolaci clusteru
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro izolaci ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: e51689d33711f127f775c63c9d7fc8ad4c901604
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105166"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro izolaci clusteru ve službě Azure Kubernetes (AKS)

Při správě clusterů ve službě Azure Kubernetes (AKS) je často potřeba izolovat týmy a úlohy. AKS poskytuje flexibilitu v tom, jak můžete spouštět clustery s více klienty a izolovat prostředky. Pokud chcete maximalizovat své investice do Kubernetes, nejdřív pochopíte a implementujte funkce AKS pro více tenantů a izolaci.

Tento článek o osvědčených postupech se zaměřuje na izolaci pro operátory clusteru. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Plánování clusterů s více klienty a oddělení prostředků
> * Použití logické nebo fyzické izolace v clusterech AKS

## <a name="design-clusters-for-multi-tenancy"></a>Návrh clusterů pro víceklientské prostředí

Kubernetes umožňuje logicky izolovat týmy a úlohy ve stejném clusteru. Cílem je poskytnout nejnižší počet oprávnění vymezených na prostředky, které každý tým potřebuje. [Obor názvů][k8s-namespaces] Kubernetes vytvoří hranici logické izolace. Další funkce Kubernetes a požadavky pro izolaci a víceklientské architektury zahrnují následující oblasti:

### <a name="scheduling"></a>Plánování

*Plánování* používá základní funkce, jako jsou kvóty prostředků nebo rozpočty přerušení pod. Další informace o těchto funkcích najdete v tématu [osvědčené postupy pro základní funkce plánovače v AKS][aks-best-practices-scheduler].

Mezi pokročilé funkce plánovače patří:
* Příchuti a tolerovánosti
* Selektory uzlů
* Spřažení node a pod nebo proti spřažení. 

Další informace o těchto funkcích najdete v tématu [osvědčené postupy pro pokročilé funkce plánovače v AKS][aks-best-practices-advanced-scheduler].

### <a name="networking"></a>Sítě

*Sítě* využívají zásady sítě k řízení toku provozu do lusků a mimo ně.

### <a name="authentication-and-authorization"></a>Ověřování a autorizace

*Ověřování a autorizace* používá:
* Řízení přístupu na základě role (RBAC)
* Integrace Azure Active Directory (AD)
* Identity pod
* Tajné kódy v Azure Key Vault 

Další informace o těchto funkcích najdete v tématu [osvědčené postupy pro ověřování a autorizaci v AKS][aks-best-practices-identity].

### <a name="containers"></a>Kontejnery
*Kontejnery* zahrnují:
* Azure Policy doplněk pro AKS, který vynutil zabezpečení pod.
* Použití kontextů pod zabezpečením.
* Probíhá skenování imagí a modulu runtime pro chyby zabezpečení. 
* Použití App obraně nebo Seccomp (Secure Computing) k omezení přístupu kontejneru k základnímu uzlu.

## <a name="logically-isolate-clusters"></a>Logicky izolujte clustery

> **Osvědčené postupy**
>
> Samostatné týmy a projekty, které používají *logickou izolaci*. Minimalizujte počet fyzických clusterů AKS, které jste nasadili k izolaci týmů nebo aplikací.

Při logické izolaci je možné použít jeden AKS cluster pro více úloh, týmů nebo prostředí. [Obory názvů][k8s-namespaces] Kubernetes tvoří logickou izolaci hranice pro úlohy a prostředky.

![Logická izolace clusteru Kubernetes v AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logické oddělení clusterů obvykle poskytuje vyšší hustotu pod, než fyzicky izolované clustery, s menším množstvím nevyužité výpočetní kapacity v clusteru. V kombinaci s nástrojem pro automatické škálování clusteru Kubernetes můžete škálovat počet uzlů nahoru nebo dolů, aby splňovaly požadavky. Tento osvědčený postup při přístupu k automatickému škálování minimalizuje náklady tím, že se spustí jenom požadovaný počet uzlů.

V současné době není prostředí Kubernetes zcela bezpečná pro nepřátelský využití více tenantů. V prostředí s více klienty pracuje více tenantů na společné sdílené infrastruktuře. Pokud nemůžete důvěřovat všem klientům, budete potřebovat dodatečné plánování, aby se předešlo tomu, že klienti nebudou mít vliv na zabezpečení a službu jiných uživatelů.

Další funkce zabezpečení, jako jsou například *zásady zabezpečení* nebo Kubernetes RBAC pro uzly, efektivně blokují zneužití. Pro skutečné zabezpečení při spouštění nepřátelských úloh s více klienty byste měli důvěřovat jenom hypervisoru. Doména zabezpečení pro Kubernetes se bude nacházet v celém clusteru, nikoli v jednotlivých uzlech. 

U těchto typů nepřátelských úloh s více klienty byste měli použít fyzicky izolované clustery.

## <a name="physically-isolate-clusters"></a>Fyzicky izolovat clustery

> **Osvědčené postupy** 
>
> Minimalizujte použití fyzické izolace pro každý samostatný tým nebo nasazení aplikace. Místo toho použijte *logickou* izolaci, jak je popsáno v předchozí části.

Fyzickým oddělením clusterů AKS je společný přístup ke izolaci clusteru. V tomto modelu izolace mají týmy nebo úlohy přiřazený svůj vlastní cluster AKS. Zatímco fyzická izolace může vypadat jako nejjednodušší způsob, jak izolovat úlohy nebo týmy, přináší správu a finanční režii. Nyní je třeba spravovat tyto více clusterů a jednotlivě poskytovat přístup a přiřazovat oprávnění. Budou se vám účtovat i jednotlivé uzly.

![Fyzická izolace jednotlivých Kubernetes clusterů v AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fyzicky oddělené clustery obvykle mají nízkou hustotu pod. Vzhledem k tomu, že každý tým nebo pracovní zatížení má svůj vlastní cluster AKS, cluster se často zřídí s výpočetními prostředky. Na těchto uzlech se často plánuje malý počet lusků. Nedeklarovaná kapacita uzlu se nedá použít pro aplikace nebo služby ve vývoji jinými týmy. Tyto nadbytečné prostředky přispívají k dodatečným nákladům v fyzicky oddělených clusterech.

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na izolaci clusteru. Další informace o operacích clusteru v AKS najdete v následujících osvědčených postupech:

* [Základní funkce plánovače Kubernetes][aks-best-practices-scheduler]
* [Pokročilé funkce plánovače Kubernetes][aks-best-practices-advanced-scheduler]
* [Ověřování a autorizace][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
