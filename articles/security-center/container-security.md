---
title: Zabezpečení kontejneru v Azure Security Center | Microsoft Docs
description: Přečtěte si o funkcích zabezpečení kontejneru Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: c01ed6dbbd6e1f7febfb99df11d2ee67cb1e5465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800595"
---
# <a name="container-security-in-security-center"></a>Zabezpečení kontejneru v Security Center

Azure Security Center je řešení Azure Native pro zabezpečení vašich kontejnerů. Security Center může chránit následující typy prostředků kontejneru:



|Prostředek |Name  |Podrobnosti  |
|:---------:|---------|---------|
|![Hostitel kontejneru](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)|Hostitelé kontejnerů (virtuální počítače, na kterých běží Docker)|Security Center kontroluje konfigurace Dockeru a poskytuje vám vhled do nesprávných konfigurací tím, že poskytuje seznam všech neúspěšných pravidel, která byla posouzena. Security Center poskytuje pokyny, které vám pomůžou tyto problémy rychle vyřešit a ušetřit čas. Security Center nepřetržitě posuzuje konfigurace Dockeru a poskytuje vám jejich nejnovější stav.|
|![Služba Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)|Clustery Azure Kubernetes Service (AKS)|Pro uživatele úrovně Standard můžete získat hlubší přehled o AKSch uzlech, cloudovém provozu a ovládacích prvcích zabezpečení s [volitelným AKS Security Center sadou prostředků](azure-kubernetes-service-integration.md) .|
|![Registr kontejneru](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)|Registry Azure Container Registry (ACR)|Získejte hlubší přehled o chybách zabezpečení imagí v registrech ACR založených na ARM, a [to Security Center pomocí volitelného ACR sady prostředků](azure-kubernetes-service-integration.md) pro uživatele úrovně Standard.|
||||


Tento článek popisuje, jak můžete tyto sady využít ke zlepšení, monitorování a údržbě zabezpečení vašich kontejnerů a jejich aplikací. Naučíte se, jak Security Center pomáhá s těmito základními aspekty zabezpečení kontejneru:

- [Správa ohrožení zabezpečení – kontrola imagí kontejnerů](#vulnerability-management---scanning-container-images)
- [Posílení zabezpečení prostředí – nepřetržité monitorování konfigurace Docker a clusterů Kubernetes](#environment-hardening)
- [Ochrana v době běhu – detekce hrozeb v reálném čase](#run-time-protection---real-time-threat-detection)

[![Karta zabezpečení kontejneru Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Pokyny k použití těchto funkcí najdete v tématu [monitorování zabezpečení kontejnerů](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images"></a>Správa ohrožení zabezpečení – kontrola imagí kontejnerů
Pokud chcete monitorovat Azure Container Registry na bázi ARM, ujistěte se, že jste na úrovni Standard Security Center (viz [ceny](/azure/security-center/security-center-pricing)). Pak povolte volitelnou sadu registrů kontejnerů. Když se doručí nový obrázek, Security Center zkontroluje Image pomocí skeneru z špičkového dodavatele kontroly ohrožení zabezpečení, Qualys.

Po nalezení problémů – podle Qualys nebo Security Center – na řídicím panelu Security Center se zobrazí oznámení. U každé chyby zabezpečení Security Center poskytuje užitečná doporučení spolu se klasifikací závažnosti a pokyny, jak problém vyřešit. Podrobnosti o doporučeních Security Center pro kontejnery najdete v [referenčním seznamu doporučení](recommendations-reference.md#recs-containers).

Security Center filtruje a klasifikuje závěry ze skeneru. Když je obrázek v pořádku, Security Center ho označí jako takový. Security Center generuje doporučení zabezpečení pouze pro image, které mají problémy, které mají být vyřešeny. Tím, že se upozorní jenom na problémy, Security Center snižuje potenciální informativní výstrahy.

## <a name="environment-hardening"></a>Posílení zabezpečení prostředí

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Nepřetržité monitorování konfigurace Docker
Azure Security Center identifikuje nespravované kontejnery hostované na virtuálních počítačích s IaaS Linux nebo jiné počítače se systémem Linux s kontejnery Docker. Security Center nepřetržitě vyhodnocuje konfiguraci těchto kontejnerů. Pak je porovná s [centrem testování v Docker pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/).

Security Center zahrnuje celou RuleSet srovnávacího testu služby CI Docker a upozorní vás, pokud vaše kontejnery nevyhovují žádnému z ovládacích prvků. Když nalezne chybnou konfiguraci, Security Center vygeneruje doporučení zabezpečení. Na **stránce doporučení** si můžete zobrazit doporučení a opravit problémy. Na kartě **kontejnery** se zobrazí také doporučení se všemi virtuálními počítači nasazenými pomocí Docker. 

Podrobnosti o relevantních doporučeních Security Center, která se můžou zobrazit pro tuto funkci, najdete v [části Container](recommendations-reference.md#recs-containers) referenční tabulky doporučení.

Když prozkoumáte problémy se zabezpečením virtuálního počítače, Security Center poskytne další informace o kontejnerech v počítači. Tyto informace zahrnují verzi Docker a počet imagí spuštěných na hostiteli. 

>[!NOTE]
> Tyto kontroly srovnávacích testů služby ci se nespouštějí na AKS instancích nebo virtuálních počítačích spravovaných datacihly.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Nepřetržité monitorování clusterů Kubernetes
Security Center spolupracuje s Azure Kubernetes Service (AKS), což je spravovaná služba orchestrace kontejnerů Microsoftu pro vývoj, nasazování a správu kontejnerových aplikací.

AKS poskytuje bezpečnostní mechanismy a přehled o stavech zabezpečení vašich clusterů. Security Center tyto funkce využívají k těmto akcím:
* Nepřetržité monitorování konfigurace clusterů AKS
* Generování doporučení zabezpečení zarovnaného k oborovým standardům

Podrobnosti o relevantních doporučeních Security Center, která se můžou zobrazit pro tuto funkci, najdete v [části Container](recommendations-reference.md#recs-containers) referenční tabulky doporučení.

## <a name="run-time-protection---real-time-threat-detection"></a>Ochrana v době běhu – detekce hrozeb v reálném čase

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]




## <a name="next-steps"></a>Další kroky

V tomto přehledu jste se dozvěděli o základních prvcích zabezpečení kontejneru v Azure Security Center. Pokračujte [postupem, jak monitorovat zabezpečení vašich kontejnerů](monitor-container-security.md).
> [!div class="nextstepaction"]
> [Monitorování zabezpečení kontejnerů](monitor-container-security.md)