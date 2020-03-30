---
title: Doporučené postupy pro službu Azure Kubernetes Service (AKS)
description: Kolekce osvědčených postupů pro operátora clusteru a vývojáře pro vytváření a správu aplikací ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: 627eeffd18a479486e5a682da06bf89cd5f8f0e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596313"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Osvědčené postupy pro operátora clusteru a vývojáře pro vytváření a správu aplikací ve službě Azure Kubernetes Service (AKS)

Chcete-li úspěšně vytvářet a spouštět aplikace ve službě Azure Kubernetes Service (AKS), existují některé klíčové aspekty, které je třeba pochopit a implementovat. Mezi tyto oblasti patří funkce víceklientské a plánovače, zabezpečení clusteru a podu nebo kontinuita provozu a zotavení po havárii. Následující osvědčené postupy jsou seskupeny, aby pomohly operátorům clusteru a vývojářům porozumět aspektům pro každou z těchto oblastí a implementovat příslušné funkce.

Tyto osvědčené postupy a koncepční články byly napsány ve spolupráci se skupinou produktů AKS, technickými týmy a týmy v terénu, včetně globálních černých pásů (GBBs).

## <a name="cluster-operator-best-practices"></a>Osvědčené postupy operátora clusteru

Jako operátor clusteru spolupracujte s vlastníky aplikací a vývojáři, abyste porozuměli jejich potřebám. Potom můžete použít následující osvědčené postupy ke konfiguraci clusterů AKS podle potřeby.

**Víceklientská architektura**

* [Osvědčené postupy pro izolaci clusteru](operator-best-practices-cluster-isolation.md)
    * Zahrnuje součásti jádra více klienta a logickou izolaci s obory názvů.
* [Osvědčené postupy pro základní funkce plánovače](operator-best-practices-scheduler.md)
    * Zahrnuje použití kvót zdrojů a rozpočtů přerušení podu.
* [Osvědčené postupy pro pokročilé funkce plánovače](operator-best-practices-advanced-scheduler.md)
    * Zahrnuje použití karamelů a tolerance, uzlů a afinity a spřažení mezi pody a anti-afinity.
* [Osvědčené postupy pro ověřování a autorizaci](operator-best-practices-identity.md)
    * Zahrnuje integraci s Azure Active Directory pomocí ovládacích prvků přístupu na základě rolí (RBAC) a identit pod.

**Zabezpečení**

* [Osvědčené postupy pro zabezpečení clusteru a upgrady](operator-best-practices-cluster-security.md)
    * Zahrnuje zabezpečení přístupu k serveru rozhraní API, omezení přístupu ke kontejnerům a správu upgradů a restartování uzlů.
* [Doporučené postupy pro správu a zabezpečení obrázků kontejnerů](operator-best-practices-container-image-management.md)
    * Zahrnuje zabezpečení bitové kopie a runtimes a automatické sestavení na základní aktualizace bitové kopie.
* [Doporučené postupy pro zabezpečení podu](developer-best-practices-pod-security.md)
    * Zahrnuje zabezpečení přístupu k prostředkům, omezení expozice pověření a použití identit podu a trezorů digitálních klíčů.

**Síť a úložiště**

* [Doporučené postupy pro připojení k síti](operator-best-practices-network.md)
    * Zahrnuje různé síťové modely, pomocí příchozího přenosu dat a brány firewall webových aplikací (WAF) a zabezpečení přístupu uzlu SSH.
* [Osvědčené postupy pro ukládání a zálohování](operator-best-practices-storage.md)
    * Zahrnuje výběr vhodného typu úložiště a velikosti uzlu, dynamicky zřizovacích svazků a zálohování dat.

**Spuštění úloh připravených pro podniky**

* [Osvědčené postupy pro kontinuitu provozu a zotavení po havárii](operator-best-practices-multi-region.md)
    * Zahrnuje použití dvojice oblastí, více clusterů s Azure Traffic Manager a geografické replikace ibi kontejnerů.

## <a name="developer-best-practices"></a>Doporučené postupy pro vývojáře

Jako vývojář nebo vlastník aplikace můžete zjednodušit vývojové prostředí a definovat potřeby výkonu aplikace.

* [Osvědčené postupy pro vývojáře aplikací ke správě prostředků](developer-best-practices-resource-management.md)
    * Zahrnuje definování požadavků a omezení prostředků podu, konfiguraci vývojových nástrojů a kontrolu problémů s aplikací.
* [Doporučené postupy pro zabezpečení podu](developer-best-practices-pod-security.md)
    * Zahrnuje zabezpečení přístupu k prostředkům, omezení expozice pověření a použití identit podu a trezorů digitálních klíčů.

## <a name="kubernetes--aks-concepts"></a>Kubernetes / AKS koncepty

Chcete-li porozumět některým funkcím a součástem těchto doporučených postupů, můžete také zobrazit následující koncepční články pro clustery ve službě Azure Kubernetes Service (AKS):

* [Kubernetes základní pojmy](concepts-clusters-workloads.md)
* [Přístup a identita](concepts-identity.md)
* [Koncepty zabezpečení](concepts-security.md)
* [Síťové koncepty](concepts-network.md)
* [Možnosti úložiště](concepts-storage.md)
* [Volby změny velikosti](concepts-scale.md)

## <a name="next-steps"></a>Další kroky

Pokud potřebujete začít s AKS, postupujte podle jednoho z rychlých startů k nasazení clusteru služby Azure Kubernetes Service (AKS) pomocí [azure cli](kubernetes-walkthrough.md) nebo [portálu Azure](kubernetes-walkthrough-portal.md).
