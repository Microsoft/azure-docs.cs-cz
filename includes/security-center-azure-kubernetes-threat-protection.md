---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: 4c09057f606423dc92b3364e502e632a385bf83f
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007804"
---
Security Center poskytuje ochranu před internetovými útoky v reálném čase pro vaše kontejnerová prostředí a generuje výstrahy pro podezřelé aktivity. Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů.

Security Center poskytuje ochranu před hrozbami na různých úrovních: 

* **Úroveň hostitele (poskytovaná pomocí Azure Defenderu pro servery)** – pomocí stejného log Analyticsho agenta, který Security Center používá na jiných virtuálních počítačích, Azure Defender monitoruje své uzly Linux AKS na podezřelé aktivity, jako je detekce webového prostředí a připojení se známými podezřelými IP adresami. Agent také monitoruje analýzy specifické pro kontejner, jako je například vytvoření privilegovaného kontejneru, podezřelý přístup k serverům rozhraní API a servery Secure Shell (SSH) běžící uvnitř kontejneru Docker.

    Pokud se rozhodnete neinstalovat agenty na hostitele, obdržíte pouze podmnožinu výhod ochrany před hrozbami a výstrahy zabezpečení. Pořád budete dostávat výstrahy týkající se analýzy sítě a komunikace se škodlivými servery.

    >[!IMPORTANT]
    > V tuto chvíli nepodporujeme instalaci agenta Log Analytics v clusterech Azure Kubernetes, které běží na virtuálních počítačích služby Virtual Machine Scale Sets.

    Seznam výstrah na úrovni hostitele AKS najdete v [referenční tabulce výstrah](../articles/security-center/alerts-reference.md#alerts-containerhost).


* **Úroveň clusteru AKS (poskytovaná službou Azure Defender pro Kubernetes)** – na úrovni clusteru je ochrana před hrozbami založená na analýze protokolů auditu Kubernetes. Pokud chcete povolit toto monitorování bez **agentů** , povolte Azure Defender. Pokud chcete na této úrovni generovat výstrahy, Security Center monitorovat služby spravované AKS pomocí protokolů načtených pomocí AKS. Mezi příklady událostí na této úrovni patří exponované řídicí panely Kubernetes, vytváření vysoce privilegovaných rolí a vytváření citlivých připojení.

    >[!NOTE]
    > Security Center vygeneruje výstrahy zabezpečení pro akce a nasazení služby Azure Kubernetes, ke kterým dochází po povolení možnosti Kubernetes v nastaveních předplatného. 

    Seznam výstrah na úrovni clusteru AKS najdete v [referenční tabulce výstrah](../articles/security-center/alerts-reference.md#alerts-akscluster).

Náš globální tým výzkumných pracovníků pro zabezpečení nepřetržitě monitoruje i na šířku hrozby. Přidávají výstrahy a chyby specifické pro kontejner při jejich zjištění.

> [!TIP]
> Pomocí pokynů v [tomto blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)můžete simulovat výstrahy kontejneru.