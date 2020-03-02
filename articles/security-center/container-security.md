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
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: 45ce8a808efc5b882c90f99875fdde661e292774
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205972"
---
# <a name="container-security-in-security-center"></a>Zabezpečení kontejneru v Security Center

Azure Security Center je řešení zabezpečení kontejneru pro Azure Native. Security Center je také optimální jediné podokno prostředí pro zabezpečení vašich cloudových úloh, virtuálních počítačů, serverů a kontejnerů.

Tento článek popisuje, jak Security Center pomáhá vylepšit, monitorovat a udržovat zabezpečení vašich kontejnerů a jejich aplikací. Naučíte se, jak Security Center pomáhá s těmito základními aspekty zabezpečení kontejneru:

* Správa ohrožení zabezpečení
* Posílení zabezpečení prostředí kontejneru
* Ochrana za běhu

[karta zabezpečení kontejneru ![Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Pokyny k použití těchto funkcí najdete v tématu [monitorování zabezpečení kontejnerů](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images"></a>Správa ohrožení zabezpečení – kontrola imagí kontejnerů
Pokud chcete monitorovat Azure Container Registry na bázi ARM, ujistěte se, že jste na úrovni Standard Security Center (viz [ceny](/azure/security-center/security-center-pricing)). Pak povolte volitelnou sadu registrů kontejnerů. Když se doručí nový obrázek, Security Center zkontroluje Image pomocí skeneru z špičkového dodavatele kontroly ohrožení zabezpečení, Qualys.

Po nalezení problémů – podle Qualys nebo Security Center – na řídicím panelu Security Center se zobrazí oznámení. U každé chyby zabezpečení Security Center poskytuje užitečná doporučení spolu se klasifikací závažnosti a pokyny, jak problém vyřešit. Podrobnosti o doporučeních Security Center pro kontejnery najdete v [referenčním seznamu doporučení](recommendations-reference.md#recs-containers).

## <a name="environment-hardening"></a>Posílení zabezpečení prostředí

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Nepřetržité monitorování konfigurace Docker
Azure Security Center identifikuje nespravované kontejnery hostované na virtuálních počítačích s IaaS Linux nebo jiné počítače se systémem Linux s kontejnery Docker. Security Center nepřetržitě vyhodnocuje konfiguraci těchto kontejnerů. Pak je porovná s [centrem testování v Docker pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/docker/).

Security Center zahrnuje celou RuleSet srovnávacího testu služby CI Docker a upozorní vás, pokud vaše kontejnery nevyhovují žádnému z ovládacích prvků. Když nalezne chybnou konfiguraci, Security Center vygeneruje doporučení zabezpečení. Na **stránce doporučení** si můžete zobrazit doporučení a opravit problémy. Na kartě **kontejnery** se zobrazí také doporučení se všemi virtuálními počítači nasazenými pomocí Docker. 

Podrobnosti o relevantních doporučeních Security Center, která se můžou zobrazit pro tuto funkci, najdete v [části Container](recommendations-reference.md#recs-containers) referenční tabulky doporučení.

Když prozkoumáte problémy se zabezpečením virtuálního počítače, Security Center poskytne další informace o kontejnerech v počítači. Tyto informace zahrnují verzi Docker a počet imagí spuštěných na hostiteli. 

>[!NOTE]
> Tyto kontroly srovnávacích testů služby ci se nespouštějí na AKS instancích nebo virtuálních počítačích spravovaných datacihly.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Nepřetržité monitorování clusterů Kubernetes (Preview)
Security Center spolupracuje s Azure Kubernetes Service (AKS), což je spravovaná služba orchestrace kontejnerů Microsoftu pro vývoj, nasazování a správu kontejnerových aplikací.

AKS poskytuje bezpečnostní mechanismy a přehled o stavech zabezpečení vašich clusterů. Security Center tyto funkce využívají k těmto akcím:
* Nepřetržité monitorování konfigurace clusterů AKS
* Generování doporučení zabezpečení zarovnaného k oborovým standardům

Podrobnosti o relevantních doporučeních Security Center, která se můžou zobrazit pro tuto funkci, najdete v [části Container](recommendations-reference.md#recs-containers) referenční tabulky doporučení.

## <a name="run-time-protection---real-time-threat-protection"></a>Ochrana za běhu – ochrana před hrozbami v reálném čase

Security Center poskytuje ochranu před internetovými útoky v reálném čase pro vaše kontejnerová prostředí a generuje výstrahy pro podezřelé aktivity. Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů.

Zjistili jsme hrozby na úrovni hostitele a AKS clusteru. Úplné podrobnosti najdete v tématu [Ochrana před hrozbami pro kontejnery Azure](threat-protection.md#azure-containers).


## <a name="container-security-faq"></a>Nejčastější dotazy týkající se zabezpečení kontejneru

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Jaké typy imagí mohou Azure Security Center prohledávat?
Security Center prohledává image založené na operačním systému Linux, které poskytují přístup k prostředí. 

Qualys skener nepodporuje image Super minimalist, jako jsou [pomocné obrázky Docker](https://hub.docker.com/_/scratch/) , nebo Image Distroless, které obsahují jenom vaši aplikaci a její závislosti za běhu (bez správce balíčků, prostředí nebo operačního systému).

### <a name="how-does-we-scan-azure-security-center-scan-an-image"></a>Jak Prohledávám Azure Security Center naskenováním obrázku?
Bitová kopie se extrahuje z registru. Pak se spustí v izolovaném izolovaném prostoru (sandbox) se skenerem Qualys, který extrahuje seznam známých chyb zabezpečení.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Jak často Azure Security Center kontrolovat obrázky?
Při každém nabízení se spouštějí kontroly imagí.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Můžu získat výsledky kontroly prostřednictvím REST API?
Ano. Výsledky se nacházejí v rámci [dílčích posouzení rozhraní REST API](/rest/api/securitycenter/subassessments/list/). Kromě toho můžete použít Azure Resource Graph (ARG), Kusto rozhraní API pro všechny vaše prostředky: dotaz může načíst konkrétní kontrolu.
 

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení kontejneru v Azure Security Center najdete v těchto souvisejících článcích:

* Pokud chcete zobrazit stav zabezpečení prostředků souvisejících s kontejnerem, přečtěte si část kontejnery v tématu [Ochrana vašich počítačů a aplikací](security-center-virtual-machine-protection.md#containers).

* Podrobnosti o [integraci se službou Azure Kubernetes](azure-kubernetes-service-integration.md)

* Podrobnosti o [integraci s Azure Container Registry](azure-container-registry-integration.md)