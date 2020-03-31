---
title: Osvědčené postupy operátora – izolace clusteru ve službách Azure Kubernetes Services (AKS)
description: Seznamte se s doporučenými postupy pro operátorclusteru pro izolaci ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 228b856d5c5ffa2bfac7df12094667e02f797690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594851"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Doporučené postupy pro izolaci clusteru ve službě Azure Kubernetes Service (AKS)

Při správě clusterů ve službě Azure Kubernetes Service (AKS) často potřebujete izolovat týmy a úlohy. AKS poskytuje flexibilitu v tom, jak můžete spouštět clustery s více tenanty a izolovat prostředky. Chcete-li maximalizovat své investice do Kubernetes, tyto multi-nájem a izolace funkce by měly být chápány a implementovány.

Tento článek osvědčených postupů se zaměřuje na izolaci pro operátory clusteru. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Plánování clusterů s více klienty a oddělení prostředků
> * Použití logické nebo fyzické izolace v clusterech AKS

## <a name="design-clusters-for-multi-tenancy"></a>Navrhujte clustery pro více klienty

Kubernetes poskytuje funkce, které umožňují logicky izolovat týmy a úlohy ve stejném clusteru. Cílem by mělo být poskytnutí nejnižšího počtu oprávnění vymezených na prostředky, které každý tým potřebuje. [Obor názvů][k8s-namespaces] v Kubernetes vytvoří hranici logické izolace. Další funkce Kubernetes a důležité informace pro izolaci a více-nájem patří následující oblasti:

* **Plánování** zahrnuje použití základních funkcí, jako jsou kvóty zdrojů a rozpočty přerušení podu. Další informace o těchto funkcích naleznete [v tématu Doporučené postupy pro základní funkce plánovače v AKS][aks-best-practices-scheduler].
  * Pokročilejší funkce plánovače zahrnují zkažení a tolerance, voliče uzlů a spřažení uzlů a podnebo spřažení nebo spřažení. Další informace o těchto funkcích naleznete [v tématu Doporučené postupy pro pokročilé funkce plánovače v AKS][aks-best-practices-advanced-scheduler].
* **Síť** zahrnuje použití zásad sítě pro řízení toku provozu do a z podů.
* **Ověřování a autorizace** zahrnují uživatele řízení přístupu na základě rolí (RBAC) a integrace služby Azure Active Directory (AD), identity podů a tajné kódy v úložišti azure key vault. Další informace o těchto funkcích naleznete [v tématu Doporučené postupy pro ověřování a autorizaci v AKS][aks-best-practices-identity].
* **Kontejnery** obsahují zásady zabezpečení podu, kontexty zabezpečení pod, skenování obrázků a moduly runtimes pro chyby zabezpečení. Zahrnuje také použití aplikace Armor nebo Seccomp (Secure Computing) k omezení přístupu kontejneru k základnímu uzlu.

## <a name="logically-isolate-clusters"></a>Logicky izolovat clustery

**Pokyny pro osvědčené postupy** – k oddělení týmů a projektů použijte logickou izolaci. Pokuste se minimalizovat počet fyzických clusterů AKS, které nasadíte k izolování týmů nebo aplikací.

S logickou izolací lze jeden cluster AKS použít pro více úloh, týmů nebo prostředí. Kubernetes [Obory názvů][k8s-namespaces] tvoří hranice logické izolace pro úlohy a prostředky.

![Logická izolace clusteru Kubernetes v AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logické oddělení clusterů obvykle poskytuje vyšší hustotu podu než fyzicky izolované clustery. Je menší nadbytečná výpočetní kapacita, která je v clusteru nečinná. V kombinaci s automatickým škálovačem clusteru Kubernetes můžete škálovat počet uzlů nahoru nebo dolů, aby splňovaly požadavky. Tento přístup osvědčených postupů k automatickému škálování umožňuje spustit pouze počet uzlů požadovaných a minimalizuje náklady.

Prostředí Kubernetes v AKS nebo jinde nejsou zcela bezpečná pro nepřátelské použití s více tenanty. V prostředí s více tenanty pracuje více klientů na společné sdílené infrastruktuře. V důsledku toho, pokud nelze důvěřovat všem klientům, je třeba provést další plánování, aby se zabránilo jednomu klientovi, který má vliv na zabezpečení a službu jiného. Další funkce zabezpečení, jako jsou *zásady zabezpečení podu* a podrobnější ovládací prvky přístupu založené na rolích (RBAC) pro uzly ztěžují zneužití. Pro skutečné zabezpečení při spuštění nepřátelských úloh s více klienty je však hypervisor jedinou úrovní zabezpečení, které byste měli důvěřovat. Doména zabezpečení pro Kubernetes se stane celým clusterem, nikoli jednotlivým uzlem. Pro tyto typy nepřátelských víceklientských úloh byste měli použít fyzicky izolované clustery.

## <a name="physically-isolate-clusters"></a>Fyzicky izolovat clustery

**Pokyny pro osvědčené postupy** – minimalizujte použití fyzické izolace pro každý samostatný tým nebo nasazení aplikace. Místo toho použijte *logickou* izolaci, jak je popsáno v předchozí části.

Běžným přístupem k izolaci clusteru je použití fyzicky oddělených clusterů AKS. V tomto modelu izolace týmy nebo úlohy jsou přiřazeny vlastní cluster AKS. Tento přístup často vypadá jako nejjednodušší způsob, jak izolovat úlohy nebo týmy, ale přidává další správu a finanční režii. Nyní je třeba udržovat tyto více clusterů a musí jednotlivě poskytnout přístup a přiřadit oprávnění. Účtují se vám také všechny jednotlivé uzly.

![Fyzická izolace jednotlivých Kubernetesových klastrů v AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fyzicky oddělené clustery mají obvykle nízkou hustotu podu. Jako každý tým nebo zatížení má svůj vlastní cluster AKS, cluster je často nadměrně zřízené s výpočetními prostředky. Často je na těchto uzlech naplánován malý počet podů. Nevyužitou kapacitu na uzlech nelze použít pro aplikace nebo služby ve vývoji jinými týmy. Tyto nadbytečné prostředky přispívají k dodatečným nákladům ve fyzicky oddělených clusterech.

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřil na izolaci clusteru. Další informace o operacích clusteru v AKS naleznete v následujících doporučených postupech:

* [Základní funkce plánovače Kubernetes][aks-best-practices-scheduler]
* [Pokročilé funkce plánovače Kubernetes][aks-best-practices-advanced-scheduler]
* [Ověřování a autorizace][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
