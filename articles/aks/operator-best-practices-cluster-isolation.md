---
title: Osvědčené postupy pro izolaci clusteru
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro izolaci ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cdeecabf569e3c6f9b280e6b0179e5378f5b1c95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011366"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro izolaci clusteru ve službě Azure Kubernetes (AKS)

Při správě clusterů ve službě Azure Kubernetes (AKS) je často potřeba izolovat týmy a úlohy. AKS poskytuje flexibilitu v tom, jak můžete spouštět clustery s více klienty a izolovat prostředky. Chcete-li maximalizovat své investice do Kubernetes, je třeba pochopit a implementovat tyto funkce pro více tenantů a izolaci.

Tento článek o osvědčených postupech se zaměřuje na izolaci pro operátory clusteru. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Plánování clusterů s více klienty a oddělení prostředků
> * Použití logické nebo fyzické izolace v clusterech AKS

## <a name="design-clusters-for-multi-tenancy"></a>Návrh clusterů pro víceklientské prostředí

Kubernetes poskytuje funkce, které umožňují logicky izolovat týmy a úlohy ve stejném clusteru. Cílem by mělo být poskytnutí nejnižšího počtu oprávnění vymezených na prostředky, které každý tým potřebuje. [Obor názvů][k8s-namespaces] v Kubernetes vytvoří logickou izolaci hranice. Další funkce Kubernetes a požadavky pro izolaci a víceklientské architektury zahrnují následující oblasti:

* **Plánování** zahrnuje použití základních funkcí, jako jsou kvóty prostředků nebo rozpočty přerušení provozu pod. Další informace o těchto funkcích najdete v tématu [osvědčené postupy pro základní funkce plánovače v AKS][aks-best-practices-scheduler].
  * Pokročilejší funkce plánovače zahrnují příchuti a tolerovánost, selektory uzlů a spřažení uzlů nebo spřažení nebo proti spřažení. Další informace o těchto funkcích najdete v tématu [osvědčené postupy pro pokročilé funkce plánovače v AKS][aks-best-practices-advanced-scheduler].
* **Sítě** zahrnují použití zásad sítě k řízení toku provozu v části a z lusků.
* **Ověřování a autorizace** zahrnují uživatele řízení přístupu na základě role (RBAC) a Azure Active Directory (AD) Integration, identity pod a tajné klíče v Azure Key Vault. Další informace o těchto funkcích najdete v tématu [osvědčené postupy pro ověřování a autorizaci v AKS][aks-best-practices-identity].
* **Kontejnery** obsahují Azure Policy doplněk pro AKS, který vynutil zabezpečení, používá v kontextu zabezpečení a kontroluje jak image, tak i modul runtime pro zabezpečení. Zahrnuje také použití aplikace App obraně nebo Seccomp (Secure Computing) k omezení přístupu k kontejneru do podkladového uzlu.

## <a name="logically-isolate-clusters"></a>Logicky izolujte clustery

**Doprovodné materiály k osvědčeným postupům** – k oddělení týmů a projektů použijte logickou izolaci. Pokuste se minimalizovat počet fyzických clusterů AKS, které jste nasadili k izolaci týmů nebo aplikací.

Při logické izolaci je možné použít jeden AKS cluster pro více úloh, týmů nebo prostředí. [Obory názvů][k8s-namespaces] Kubernetes tvoří logickou izolaci hranice pro úlohy a prostředky.

![Logická izolace clusteru Kubernetes v AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logické oddělení clusterů obvykle poskytuje vyšší hustotu pod, než fyzicky izolované clustery. V clusteru je méně nadbytečná výpočetní kapacita, která se nachází v nečinnosti. V kombinaci s nástrojem pro automatické škálování clusteru Kubernetes můžete škálovat počet uzlů nahoru nebo dolů, aby splňovaly požadavky. Tento doporučený postup pro automatické škálování umožňuje spustit jenom požadovaný počet uzlů a minimalizuje náklady.

Prostředí Kubernetes, v AKS nebo jinde, nejsou zcela bezpečná pro nepřátelský využití více tenantů. V prostředí s více klienty pracuje více tenantů na společné sdílené infrastruktuře. V důsledku toho, že všichni klienti nemůžou být důvěryhodní, musíte provést další plánování, aby se předešlo tomu, že jeden tenant bude mít vliv na zabezpečení a službu jiného. Další funkce zabezpečení, jako jsou *zásady zabezpečení* a pokročilejší řízení přístupu na základě role (RBAC) pro uzly, se obtížně využívají. Pro skutečné zabezpečení při spouštění nepřátelských úloh s více klienty však je hypervisor jedinou úrovní zabezpečení, které byste měli důvěřovat. Doména zabezpečení pro Kubernetes se bude nacházet v celém clusteru, nikoli v jednotlivých uzlech. U těchto typů nepřátelských úloh s více klienty byste měli použít fyzicky izolované clustery.

## <a name="physically-isolate-clusters"></a>Fyzicky izolovat clustery

**Doprovodné materiály k osvědčeným postupům** – minimalizace použití fyzické izolace pro každý samostatný tým nebo nasazení aplikace. Místo toho použijte *logickou* izolaci, jak je popsáno v předchozí části.

Běžným přístupem k izolaci clusteru je použití fyzicky oddělených clusterů AKS. V tomto modelu izolace mají týmy nebo úlohy přiřazený svůj vlastní cluster AKS. Tento přístup často vypadá jako nejjednodušší způsob, jak izolovat úlohy nebo týmy, ale přidává další správu a finanční režii. Nyní musíte spravovat několik clusterů a přiřazovat k nim individuální přístup a oprávnění. Účtují se i všechny jednotlivé uzly.

![Fyzická izolace jednotlivých Kubernetes clusterů v AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fyzicky oddělené clustery obvykle mají nízkou hustotu pod. Jelikož má každý tým nebo pracovní postup svůj vlastní cluster AKS, cluster se často zřídí s výpočetními prostředky. Na těchto uzlech se často plánuje malý počet lusků. Nevyužitou kapacitu uzlů nelze použít pro aplikace nebo služby ve vývoji jinými týmy. Tyto nadbytečné prostředky přispívají k dodatečným nákladům v fyzicky oddělených clusterech.

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
