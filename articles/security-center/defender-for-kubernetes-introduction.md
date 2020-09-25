---
title: Azure Defender pro Kubernetes – výhody a funkce
description: Přečtěte si o výhodách a funkcích Azure Defenderu pro Kubernetes.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a7fb97f1036515f82fa3e61c18f95eadf0af704d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301699"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Seznámení s Azure Defenderem pro Kubernetes

Služba Azure Kubernetes Service (AKS) je spravovaná služba Microsoftu pro vývoj, nasazování a správu kontejnerových aplikací.

Azure Security Center a AKS tvoří nejlepší nabídku Kubernetes zabezpečení cloudu a společně poskytují posílení zabezpečení prostředí, ochranu úloh a ochranu za běhu, jak je uvedeno níže.

Pro detekci hrozeb pro clustery Kubernetes povolte **Azure Defender pro Kubernetes**.

Detekce hrozeb na úrovni hostitele pro uzly se systémem Linux AKS je k dispozici, pokud povolíte [Azure Defender pro servery](defender-for-servers-introduction.md).

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|**Azure Defender pro Kubernetes** se účtuje tak, jak je znázorněno na [stránce s cenami](security-center-pricing.md) .|
|Požadované role a oprávnění:|**Správce zabezpečení** může zrušit výstrahy.<br>**Čtenář zabezpečení** může zobrazit zjištění.|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Jaké jsou výhody Azure Defenderu pro Kubernetes?

### <a name="run-time-protection"></a>Ochrana za běhu

Díky průběžné analýze následujících zdrojů AKS Security Center poskytuje ochranu před internetovými útoky v reálném čase pro vaše kontejnerová prostředí a generuje výstrahy na úrovni hostitele *a* AKS na úrovni clusteru a škodlivá aktivita. Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů.

Security Center poskytuje ochranu před hrozbami na různých úrovních: 

- **Úroveň hostitele (poskytovaná pomocí Azure Defenderu pro servery)** – pomocí stejného log Analyticsho agenta, který Security Center používá na jiných virtuálních počítačích, Azure Defender monitoruje své uzly Linux AKS na podezřelé aktivity, jako je detekce webového prostředí a připojení se známými podezřelými IP adresami. Agent také monitoruje analýzy specifické pro kontejner, jako je například vytvoření privilegovaného kontejneru, podezřelý přístup k serverům rozhraní API a servery Secure Shell (SSH) běžící uvnitř kontejneru Docker.

    Seznam výstrah na úrovni hostitele AKS najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-containerhost).

    >[!IMPORTANT]
    > Pokud se rozhodnete neinstalovat agenty na hostitele, obdržíte pouze podmnožinu výhod ochrany před hrozbami a výstrahy zabezpečení. Pořád budete dostávat výstrahy týkající se analýzy sítě a komunikace se škodlivými servery.

- **Úroveň clusteru AKS (poskytovaná službou Azure Defender pro Kubernetes)** – na úrovni clusteru je ochrana před hrozbami založená na analýze protokolů auditu Kubernetes. Pokud chcete povolit toto monitorování bez **agentů** , povolte Azure Defender. Pokud chcete na této úrovni generovat výstrahy, Security Center monitorovat služby spravované AKS pomocí protokolů načtených pomocí AKS. Mezi příklady událostí na této úrovni patří exponované řídicí panely Kubernetes, vytváření vysoce privilegovaných rolí a vytváření citlivých připojení.

    Seznam výstrah na úrovni clusteru AKS najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-akscluster).

    >[!NOTE]
    > Security Center vygeneruje výstrahy zabezpečení pro akce a nasazení služby Azure Kubernetes, ke kterým dochází po povolení možnosti Kubernetes v nastaveních předplatného. 

Náš globální tým výzkumných pracovníků pro zabezpečení nepřetržitě monitoruje i na šířku hrozby. Přidávají výstrahy a chyby specifické pro kontejner při jejich zjištění.

> [!TIP]
> Pomocí pokynů v [tomto blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)můžete simulovat výstrahy kontejneru.



## <a name="how-does-security-centers-kubernetes-protection-work"></a>Jak funguje ochrana Security Center Kubernetes?

Níže je uvedený diagram vysoké úrovně interakce mezi Azure Security Center, službou Azure Kubernetes a Azure Policy.

Můžete vidět, že položky přijaté a analyzované pomocí Security Center zahrnují:

- protokoly auditu ze serveru rozhraní API
- nezpracované události zabezpečení od agenta Log Analytics
- informace o konfiguraci clusteru z clusteru AKS
- Konfigurace úlohy z Azure Policy (prostřednictvím **Azure Policy doplňku pro Kubernetes**). [Další informace o osvědčených postupech ochrany úloh pomocí řízení přístupu Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Architektura interakce mezi Azure Security Center, službou Azure Kubernetes a Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender pro Kubernetes – Nejčastější dotazy

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Můžu I nadále získávat AKS ochranu bez agenta Log Analytics?

Jak je uvedeno výše, volitelný plán **Azure Defender for Kubernetes** poskytuje ochranu na úrovni clusteru. Agent Log Analytics **Azure Defenderu pro servery** chrání vaše uzly. 

Pro nejúplnější možnou ochranu doporučujeme nasazení obou.

Pokud se rozhodnete neinstalovat agenta na hostitele, obdržíte pouze podmnožinu výhod ochrany před hrozbami a výstrahy zabezpečení. Pořád budete dostávat výstrahy týkající se analýzy sítě a komunikace se škodlivými servery.


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o ochraně Security Center Kubernetes, včetně Azure Defenderu pro Kubernetes. 

Související materiály najdete v následujících článcích: 

- [Povolit Azure Defender](security-center-pricing.md)
- [Exportovat výstrahy do Azure Sentinel nebo SIEM třetí strany](continuous-export.md)
- [Referenční tabulka výstrah](alerts-reference.md)