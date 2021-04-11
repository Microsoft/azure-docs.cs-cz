---
title: Zabezpečení kontejneru pomocí Azure Security Center a Azure Defenderu
description: Další informace o funkcích zabezpečení kontejneru Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 9fddb27ee6a1139fa8b07c6c19dd4fdf1a20096e
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029130"
---
# <a name="container-security-in-security-center"></a>Zabezpečení kontejnerů ve službě Security Center

Azure Security Center je řešení Azure Native pro zabezpečení vašich kontejnerů.

Security Center může chránit následující typy prostředků kontejneru:

| Typ prostředku | Ochrana nabízená nástrojem Security Center |
|:--------------------:|-----------|
| ![Služba Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Clustery Kubernetes** | Průběžné posuzování vašich clusterů, které poskytují přehled o nepravidelných konfiguracích a pokyny, které vám pomohou zmírnit zjištěné hrozby. Přečtěte si další informace o [posílení zabezpečení prostředí prostřednictvím doporučení zabezpečení](#environment-hardening).<br><br>Ochrana před hrozbami pro clustery a uzly Linux. Upozornění na podezřelé aktivity poskytuje [Azure Defender pro Kubernetes](defender-for-kubernetes-introduction.md). Tento plán Azure Defenderu chrání clustery Kubernetes bez ohledu na to, jestli jsou hostované ve službě Azure Kubernetes (AKS), v místním prostředí nebo na jiných poskytovatelích cloudu. existující. <br>Přečtěte si další informace o [ochraně za běhu pro uzly a clustery Kubernetes](#run-time-protection-for-kubernetes-nodes-and-clusters).|
| ![Hostitel kontejneru](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Hostitelé kontejneru**<br>(Virtuální počítače s Docker) | Průběžné hodnocení vašich prostředí Docker, které poskytuje přehled o nepravidelných konfiguracích a pokyny, které vám pomohou zmírnit hrozby identifikované volitelným programem [Azure Defender pro servery](defender-for-servers-introduction.md).<br>Přečtěte si další informace o [posílení zabezpečení prostředí prostřednictvím doporučení zabezpečení](#environment-hardening).|
| ![Registr kontejneru](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Registry Azure Container Registry (ACR)** | Nástroje pro posouzení a správu ohrožení zabezpečení pro image v ACR Registry založené na Azure Resource Manager s volitelným [Azure Defenderem pro Registry kontejnerů](defender-for-container-registries-introduction.md).<br>Přečtěte si další informace o [kontrole chyb zabezpečení v imagích kontejnerů](#vulnerability-management---scanning-container-images). |
|||

Tento článek popisuje, jak můžete použít Security Center společně s nepovinnými plány Azure Defender pro Registry kontejnerů, servery a Kubernetes, ke zlepšení, monitorování a údržbě zabezpečení vašich kontejnerů a jejich aplikací.

Naučíte se, jak Security Center pomáhá s těmito základními aspekty zabezpečení kontejneru:

- [Správa ohrožení zabezpečení – kontrola imagí kontejnerů](#vulnerability-management---scanning-container-images)
- [Posílení zabezpečení prostředí](#environment-hardening)
- [Ochrana za běhu pro uzly a clustery Kubernetes](#run-time-protection-for-kubernetes-nodes-and-clusters)

Na následujícím snímku obrazovky vidíte stránku inventáře prostředků a různé typy prostředků kontejneru chráněné Security Center.

:::image type="content" source="./media/container-security/inventory-container-resources.png" alt-text="Prostředky související s kontejnerem na stránce inventáře prostředků Security Center" lightbox="./media/container-security/inventory-container-resources.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Správa ohrožení zabezpečení – kontrola imagí kontejnerů

Pokud chcete monitorovat image v registrech kontejnerů Azure založených na Azure Resource Manager, povolte v [Azure Defenderu Registry kontejnerů](defender-for-container-registries-introduction.md). Security Center prohledává všechny image za posledních 30 dnů, vloženy do registru nebo naimportovaly. Integrovaný skener je poskytován od špičkové kontroly ohrožení zabezpečení od dodavatele, Qualys.

Po nalezení problémů – podle Qualys nebo Security Center – obdržíte oznámení na [řídicím panelu Azure Defender](azure-defender-dashboard.md). U každé chyby zabezpečení Security Center poskytuje užitečná doporučení spolu se klasifikací závažnosti a pokyny, jak problém vyřešit. Podrobnosti o doporučeních Security Center pro kontejnery najdete v [referenčním seznamu doporučení](recommendations-reference.md#recs-compute).

Security Center filtruje a klasifikuje závěry ze skeneru. Když je obrázek v pořádku, Security Center ho označí jako takový. Security Center generuje doporučení zabezpečení pouze pro image, které mají problémy, které mají být vyřešeny. Tím, že se upozorní jenom na problémy, Security Center snižuje potenciální informativní výstrahy.

## <a name="environment-hardening"></a>Posílení zabezpečení prostředí

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Nepřetržité monitorování konfigurace Docker

Azure Security Center identifikuje nespravované kontejnery hostované na virtuálních počítačích s IaaS Linux nebo jiné počítače se systémem Linux s kontejnery Docker. Security Center nepřetržitě vyhodnocuje konfiguraci těchto kontejnerů. Pak je porovná s [centrem testování v Docker pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/).

Security Center zahrnuje celou RuleSet srovnávacího testu služby CI Docker a upozorní vás, pokud vaše kontejnery nevyhovují žádnému z ovládacích prvků. Když nalezne chybnou konfiguraci, Security Center vygeneruje doporučení zabezpečení. Pomocí **stránky doporučení** Security Center můžete zobrazit doporučení a opravit problémy. Kontroly srovnávacích testů CIS neběží na AKS instancích nebo virtuálních počítačích spravovaných datacihly.

Podrobnosti o relevantních doporučeních Security Center, která se můžou zobrazit pro tuto funkci, najdete v [části COMPUTE](recommendations-reference.md#recs-compute) referenční tabulky doporučení.

Když prozkoumáte problémy se zabezpečením virtuálního počítače, Security Center poskytne další informace o kontejnerech v počítači. Tyto informace zahrnují verzi Docker a počet imagí spuštěných na hostiteli. 

Pro monitorování nespravovaných kontejnerů hostovaných na virtuálních počítačích s IaaS Linux povolte [pro servery volitelné prostředí Azure Defender](defender-for-servers-introduction.md).


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Nepřetržité monitorování clusterů Kubernetes
Security Center spolupracuje s Azure Kubernetes Service (AKS), což je spravovaná služba orchestrace kontejnerů Microsoftu pro vývoj, nasazování a správu kontejnerových aplikací.

AKS poskytuje bezpečnostní mechanismy a přehled o stavech zabezpečení vašich clusterů. Security Center tyto funkce používá k nepřetržitému monitorování konfigurace clusterů AKS a generování doporučení zabezpečení, která jsou v souladu s oborem standardů.

Toto je diagram vysoké úrovně interakce mezi Azure Security Center, službou Azure Kubernetes a Azure Policy:

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Architektura interakce mezi Azure Security Center, službou Azure Kubernetes a Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

Můžete vidět, že položky přijaté a analyzované pomocí Security Center zahrnují:

- protokoly auditu ze serveru rozhraní API
- nezpracované události zabezpečení od agenta Log Analytics

    > [!NOTE]
    > V tuto chvíli nepodporujeme instalaci agenta Log Analytics v clusterech Azure Kubernetes, které běží na virtuálních počítačích služby Virtual Machine Scale Sets.

- informace o konfiguraci clusteru z clusteru AKS
- Konfigurace úlohy z Azure Policy (pomocí **Azure Policy doplňku pro Kubernetes**)

Podrobnosti o relevantních doporučeních Security Center, která se můžou zobrazit pro tuto funkci, najdete v [části COMPUTE](recommendations-reference.md#recs-compute) referenční tabulky doporučení.


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Osvědčené postupy ochrany úloh pomocí řízení přístupu Kubernetes

Pro sadu doporučení pro ochranu úloh Kubernetes kontejnerů nainstalujte  **doplněk Azure Policy pro Kubernetes**. Tento doplněk můžete také automaticky nasadit, jak je vysvětleno v tématu [Povolení automatického zřizování Log Analytics agenta a rozšíření](security-center-enable-data-collection.md#auto-provision-mma). Pokud je Automatické zřizování pro doplněk nastavené na zapnuto, rozšíření je ve výchozím nastavení povolené ve všech stávajících i budoucích clusterech (které splňují požadavky na instalaci doplňku).

Jak je vysvětleno na [tomto Azure Policy stránce pro Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), doplněk rozšiřuje Webhook open-source [gatekeeper V3](https://github.com/open-policy-agent/gatekeeper)   Admission Controller pro [Open Policy agent](https://www.openpolicyagent.org/). Řadiče pro přijímání Kubernetes jsou moduly plug-in, které vynutily používání clusterů. Doplněk se registruje jako Webhook pro Kubernetes Admission Control a umožňuje použít v centralizovaném, konzistentním způsobem vynucování a ochranu vašich clusterů v rámci škálování. 

Díky doplňku v clusteru AKS se všechny požadavky na server rozhraní Kubernetes API monitorují proti předdefinované sadě osvědčených postupů předtím, než se trvale uloží do clusteru. Pak můžete nakonfigurovat, aby **vynutil** osvědčené postupy a nakonfigurovaly je pro budoucí úlohy. 

Můžete například stanovit, že by se neměly vytvářet privilegované kontejnery a všechny budoucí požadavky tak budou zablokovány.

Další informace najdete v informacích o [ochraně úloh Kubernetes](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-kubernetes-nodes-and-clusters"></a>Ochrana za běhu pro uzly a clustery Kubernetes

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Další kroky

V tomto přehledu jste se dozvěděli o základních prvcích zabezpečení kontejneru v Azure Security Center. Související materiál najdete v těchto tématech:

- [Seznámení s Azure Defenderem pro Kubernetes](defender-for-kubernetes-introduction.md)
- [Úvod do Azure Defenderu pro registry kontejnerů](defender-for-container-registries-introduction.md)