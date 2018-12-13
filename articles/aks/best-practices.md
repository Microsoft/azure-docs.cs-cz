---
title: Osvědčené postupy pro Azure Kubernetes Service (AKS)
description: Kolekce clusteru operátor a developer osvědčené postupy při vytváření a správě aplikací ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 12/07/18
ms.author: iainfou
ms.openlocfilehash: ef7dabc8bf792893380b80458d5fb20aa15cd909
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256464"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Osvědčené postupy pro vytváření a správě aplikací ve službě Azure Kubernetes Service (AKS) – operátor clusteru a pro vývojáře

Chcete-li vytvářet a spouštět aplikace úspěšně ve službě Azure Kubernetes Service (AKS), jsou některé klíčové faktory pochopit a implementovat. Patří sem více tenantů a funkce plánovače, clusteru a pod zabezpečení nebo obchodní kontinuity podnikových procesů a zotavení po havárii. Následující osvědčené postupy jsou seskupené do clusteru Operátoři a vývojáři pochopit, jaké jsou požadavky na každou z těchto oblastí a implementovat odpovídající funkce.

Tyto osvědčené postupy a koncepčních článků být napsán ve spojení s AKS produktovou skupinou a technické týmy a týmy pole, včetně globální pásů černé (GBBs).

## <a name="cluster-operator-best-practices"></a>Osvědčené postupy clusteru – operátor

Jako operátor clusteru fungují společně s počet vlastníků aplikace a vývojářům k pochopení jejich potřeb. Následující osvědčené postupy pak můžete podle potřeby nakonfigurujte své clustery AKS.

**Víceklientská architektura**

* [Osvědčené postupy pro zajištění izolace clusteru](operator-best-practices-cluster-isolation.md)
    * Zahrnuje více tenantů základní komponenty a logické izolace s obory názvů.
* [Osvědčené postupy pro Plánovač základní funkce](operator-best-practices-scheduler.md)
    * Zahrnuje použití kvóty prostředků a rozpočet pod přerušení.
* [Osvědčené postupy pro Plánovač pokročilé funkce](operator-best-practices-advanced-scheduler.md)
    * Zahrnuje použití poskvrnění a tolerations, uzel selektory a vztahů a vztahů mezi pod a proti spřažení.
* [Osvědčené postupy pro ověřování a autorizace](operator-best-practices-identity.md)
    * Zahrnuje integraci s Azure Active Directory, pomocí řízení přístupu na základě rolí (RBAC) a pod identit.

**Zabezpečení**

* [Osvědčené postupy pro zabezpečení clusteru a upgrady](operator-best-practices-cluster-security.md)
    * Zahrnuje zabezpečení přístupu k rozhraní API serveru, omezení přístupu ke kontejneru a Správa aktualizací a restartování uzlu.
* [Osvědčené postupy pro správu image kontejnerů a zabezpečení](operator-best-practices-container-image-management.md)
    * Zahrnuje zabezpečení image a moduly runtime, s využitím důvěryhodné registry a automatizované buildy při aktualizacích základní image...
* [Osvědčené postupy pro zabezpečení pod](developer-best-practices-pod-security.md)
    * Zahrnuje zabezpečení přístupu k prostředkům, omezení odhalení přihlašovacích údajů a používání pod identity a digitální trezorům klíčů.

**Síť a úložiště**

* [Osvědčené postupy pro připojení k síti](operator-best-practices-network.md)
    * Zahrnuje modely jinou síť, pomocí firewallů příchozího přenosu dat a webových aplikací (WAF) a zabezpečení přístup k uzlu SSH.
* [Osvědčené postupy pro ukládání a zálohování](operator-best-practices-storage.md)
    * Zahrnuje zvolíte odpovídající typ a uzel velikost úložiště, dynamické zřizování svazků a záloh.

**Spouštění úloh v připraveno pro podniky**

* [Osvědčené postupy pro obchodní kontinuity podnikových procesů a zotavení po havárii](operator-best-practices-multi-region.md)
    * Zahrnuje použití párování oblastí, víc clusterů pomocí Azure Traffic Manageru a geografickou replikaci imagí kontejnerů.

## <a name="developer-best-practices"></a>Osvědčené postupy pro vývojáře

Jako vývojář nebo vlastníka aplikace, můžete zjednodušit vaše zkušenosti s vývojem a definovat vyžadují požadavkům na výkon aplikace.

* [Osvědčené postupy pro vývojáře aplikací ke správě prostředků](developer-best-practices-resource-management.md)
    * Zahrnuje definování pod prostředků požadavky a omezení, konfigurace nástrojů pro vývoj a kontrola problémů s aplikací.
* [Osvědčené postupy pro zabezpečení pod](developer-best-practices-pod-security.md)
    * Zahrnuje zabezpečení přístupu k prostředkům, omezení odhalení přihlašovacích údajů a používání pod identity a digitální trezorům klíčů.

## <a name="kubernetes--aks-concepts"></a>Kubernetes / koncepty AKS

Abychom pomohli pochopit některé funkce a součásti tyto osvědčené postupy, můžete také zobrazit následující koncepční články pro clustery ve službě Azure Kubernetes Service (AKS):

* [Základní koncepty Kubernetes](concepts-clusters-workloads.md)
* [Přístup a identita](concepts-identity.md)
* [Koncepty zabezpečení](concepts-security.md)
* [Koncepty sítě](concepts-network.md)
* [Možnosti úložiště](concepts-storage.md)
* [Možnosti škálování.](concepts-scale.md)

## <a name="next-steps"></a>Další postup

Pokud chcete začít pracovat s AKS, postupujte podle jednoho z postupů rychlý start k nasazení clusteru Azure Kubernetes Service (AKS) pomocí [rozhraní příkazového řádku Azure](kubernetes-walkthrough.md) nebo [webu Azure portal](kubernetes-walkthrough-portal.md).
