---
title: Osvědčené postupy pro službu Azure Kubernetes (AKS)
description: Kolekce operátoru clusteru a osvědčené postupy pro vývojáře při sestavování a správě aplikací ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: e8f9c1946567ac3c3ea1045a216d6b0486fd959a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94686255"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Doporučené postupy pro vytváření a správu aplikací ve službě Azure Kubernetes (AKS) pro operátora clusteru a vývojáře

K úspěšnému sestavování a spouštění aplikací ve službě Azure Kubernetes Service (AKS) jsou k dispozici některé klíčové důležité informace, které je potřeba pochopit a implementovat. Mezi tyto oblasti patří víceklientské a funkce Scheduleru, cluster a zabezpečení, nebo provozní kontinuita a zotavení po havárii. Následující osvědčené postupy jsou seskupené, které usnadňují operátorům clusteru a vývojářům pochopit důležité informace pro každou z těchto oblastí a implementují příslušné funkce.

Tyto osvědčené postupy a koncepční články byly napsány ve spojení se skupinou produktů AKS, technickými týmy a týmy pro pole, včetně globálních černých pásů (GBBs).

## <a name="cluster-operator-best-practices"></a>Doporučené postupy pro operátora clusteru

Jako operátor clusteru můžete spolupracovat s vlastníky aplikací a vývojáři a pochopit jejich potřeby. Pak můžete pomocí následujících osvědčených postupů nakonfigurovat clustery AKS podle potřeby.

**Architektura s více tenanty**

* [Osvědčené postupy pro izolaci clusteru](operator-best-practices-cluster-isolation.md)
    * Zahrnuje základní komponenty víceklientské architektury a logickou izolaci s obory názvů.
* [Osvědčené postupy pro základní funkce plánovače](operator-best-practices-scheduler.md)
    * Zahrnuje použití kvót prostředků a rozpočtů přerušení pod.
* [Osvědčené postupy pro pokročilé funkce plánovače](operator-best-practices-advanced-scheduler.md)
    * Zahrnuje použití chuti a tolerování, selektorů uzlů a spřažení a spřažení a proti spřažení.
* [Osvědčené postupy pro ověřování a autorizaci](operator-best-practices-identity.md)
    * Zahrnuje integraci s Azure Active Directory s využitím Kubernetes řízení přístupu na základě role (Kubernetes RBAC), pomocí Azure RBAC a identity pod.

**Zabezpečení**

* [Osvědčené postupy pro zabezpečení clusteru a upgrady](operator-best-practices-cluster-security.md)
    * Zahrnuje zabezpečení přístupu k serveru rozhraní API, omezení přístupu k kontejneru a správu upgradů a restartování uzlů.
* [Osvědčené postupy pro správu a zabezpečení imagí kontejneru](operator-best-practices-container-image-management.md)
    * Zahrnuje zabezpečení image a běhových prostředí a automatických sestavení na základních aktualizacích imagí.
* [Osvědčené postupy pro zabezpečení pod](developer-best-practices-pod-security.md)
    * Zahrnuje zabezpečení přístupu k prostředkům, omezování vystavování přihlašovacích údajů a používání identit pod a digitálních trezorů klíčů.

**Síť a úložiště**

* [Osvědčené postupy pro připojení k síti](operator-best-practices-network.md)
    * Zahrnuje různé síťové modely, použití bran firewall pro příchozí a webové aplikace (WAF) a zabezpečení přístupu k uzlu pomocí protokolu SSH.
* [Osvědčené postupy pro ukládání a zálohování](operator-best-practices-storage.md)
    * Zahrnuje výběr vhodného typu úložiště a velikosti uzlu, dynamické zřizování svazků a zálohování dat.

**Spuštění úloh připravených pro podniky**

* [Osvědčené postupy pro zajištění kontinuity podnikových procesů a zotavení po havárii](operator-best-practices-multi-region.md)
    * Zahrnuje použití párů oblastí, více clusterů s Azure Traffic Manager a geografickou replikaci imagí kontejneru.

## <a name="developer-best-practices"></a>Osvědčené postupy pro vývojáře

Jako vlastníkem vývojářů nebo aplikací můžete zjednodušit vývojové prostředí a definovat požadavky na výkon aplikací.

* [Osvědčené postupy pro vývojáře aplikací ke správě prostředků](developer-best-practices-resource-management.md)
    * Zahrnuje definování požadavků a omezení prostředků pod, konfiguraci vývojářských nástrojů a kontrolu potíží s aplikacemi.
* [Osvědčené postupy pro zabezpečení pod](developer-best-practices-pod-security.md)
    * Zahrnuje zabezpečení přístupu k prostředkům, omezování vystavování přihlašovacích údajů a používání identit pod a digitálních trezorů klíčů.

## <a name="kubernetes--aks-concepts"></a>Koncepty Kubernetes/AKS

Pro pochopení některých funkcí a součástí těchto osvědčených postupů můžete také zobrazit následující koncepční články pro clustery ve službě Azure Kubernetes (AKS):

* [Základní koncepty Kubernetes](concepts-clusters-workloads.md)
* [Přístup a identita](concepts-identity.md)
* [Koncepce zabezpečení](concepts-security.md)
* [Koncepty sítě](concepts-network.md)
* [Možnosti úložiště](concepts-storage.md)
* [Možnosti škálování](concepts-scale.md)

## <a name="next-steps"></a>Další kroky

Pokud potřebujete začít s AKS, postupujte podle jednoho z rychlých startů a nasaďte cluster Azure Kubernetes Service (AKS) pomocí rozhraní příkazového [řádku Azure CLI](kubernetes-walkthrough.md) nebo [Azure Portal](kubernetes-walkthrough-portal.md).
