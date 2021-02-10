---
title: Azure Defender pro Kubernetes – výhody a funkce
description: Přečtěte si o výhodách a funkcích Azure Defenderu pro Kubernetes.
author: memildin
ms.author: memildin
ms.date: 02/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1f013f22b482c1e1d093f106bd786be870103f3d
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008498"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Seznámení s Azure Defenderem pro Kubernetes

Služba Azure Kubernetes Service (AKS) je spravovaná služba Microsoftu pro vývoj, nasazování a správu kontejnerových aplikací.

Azure Security Center a AKS tvoří Kubernetes cloudovou nabídku zabezpečení s posílením zabezpečení prostředí, ochranou zatížení a ochranou za běhu, jak je popsáno v [zabezpečení kontejneru v Security Center](container-security.md).

Pro detekci hrozeb pro clustery Kubernetes povolte **Azure Defender pro Kubernetes**.

Detekce hrozeb na úrovni hostitele pro uzly se systémem Linux AKS je k dispozici, pokud povolíte [Azure Defender pro servery](defender-for-servers-introduction.md) a jeho agenta Log Analytics. Pokud je ale cluster AKS nasazený v sadě škálování virtuálního počítače, agent Log Analytics se momentálně nepodporuje.

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Obecná dostupnost (GA)|
|Stanov|**Azure Defender pro Kubernetes** se účtuje tak, jak je znázorněno na [stránce s cenami](security-center-pricing.md) .|
|Požadované role a oprávnění:|**Správce zabezpečení** může zrušit výstrahy.<br>**Čtenář zabezpečení** může zobrazit zjištění.|
|Cloud|![Ano](./media/icons/yes-icon.png) Komerční cloudy<br>![Ano](./media/icons/yes-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Jaké jsou výhody Azure Defenderu pro Kubernetes?

Azure Defender pro Kubernetes poskytuje **ochranu před hrozbami na úrovni clusteru** monitorováním služeb spravovaných AKS prostřednictvím protokolů načtených službou Azure Kubernetes Service (AKS).

Příklady událostí zabezpečení, které Azure Defender pro Kubernetes monitoruje, jsou vystavené řídicí panely Kubernetes, vytváření vysoce privilegovaných rolí a vytváření citlivých připojení. Úplný seznam výstrah na úrovni clusteru AKS najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-akscluster).

> [!TIP]
> Pomocí pokynů v [tomto blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)můžete simulovat výstrahy kontejneru.

Náš globální tým výzkumných pracovníků pro zabezpečení nepřetržitě monitoruje i na šířku hrozby. Přidávají výstrahy a chyby specifické pro kontejner při jejich zjištění.

>[!NOTE]
> Security Center vygeneruje výstrahy zabezpečení pro akce a nasazení služby Azure Kubernetes, ke kterým dochází **po** povolení Azure Defenderu pro Kubernetes.




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender pro Kubernetes – Nejčastější dotazy

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Můžu I nadále získávat AKS ochranu bez agenta Log Analytics?

Plán **Azure Defender pro Kubernetes** poskytuje ochranu na úrovni clusteru. Pokud nasadíte i agenta Log Analytics **služby Azure Defender pro servery**, získáte ochranu před hrozbami pro vaše uzly, které jsou k dispozici s tímto plánem. Další informace najdete v [úvodu k Azure Defenderu pro servery](defender-for-servers-introduction.md).

Pro nejúplnější možnou ochranu doporučujeme nasazení obou.

Pokud se rozhodnete neinstalovat agenta na hostitele, obdržíte pouze podmnožinu výhod ochrany před hrozbami a výstrahy zabezpečení. Pořád budete dostávat výstrahy týkající se analýzy sítě a komunikace se škodlivými servery.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Umožňuje AKS instalovat vlastní rozšíření virtuálních počítačů na mých uzlech AKS?
Aby mohl Azure Defender monitorovat uzly AKS, musí používat agenta Log Analytics. 

AKS je spravovaná služba a vzhledem k tomu, že agent Log Analytics je rozšíření spravované Microsoftem, je podporované i v clusterech AKS.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Pokud můj cluster už používá Azure Monitor pro agenta kontejnerů, potřebuji agenta Log Analytics?
Aby mohl Azure Defender monitorovat uzly AKS, musí používat agenta Log Analytics.

Pokud již máte v clusterech spuštěný agent Azure Monitor for Containers, můžete také agenta Log Analytics nainstalovat a dva agenti mohou společně navzájem pracovat bez jakýchkoli problémů.

[Přečtěte si další informace o agentovi Azure monitor for Containers](../azure-monitor/insights/container-insights-manage-agent.md).


## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o ochraně Security Center Kubernetes, včetně Azure Defenderu pro Kubernetes. 

> [!div class="nextstepaction"]
> [Povolení Azure Defenderu](security-center-pricing.md#enable-azure-defender)

Související materiály najdete v následujících článcích: 

- [Streamování výstrah do řešení pro správu služeb SIEM, společnosti nebo IT](export-to-siem.md)
- [Referenční tabulka výstrah](alerts-reference.md)
