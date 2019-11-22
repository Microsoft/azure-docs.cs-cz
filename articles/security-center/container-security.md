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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 2a40680b45522d8a8e49a3f01a50a748e1c71b53
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274067"
---
# <a name="container-security-in-security-center"></a>Zabezpečení kontejneru v Security Center

Azure Security Center je řešení zabezpečení kontejneru pro Azure Native. Security Center je také optimální jediné podokno prostředí pro zabezpečení vašich cloudových úloh, virtuálních počítačů, serverů a kontejnerů.

Tento článek popisuje, jak můžete vylepšit, monitorovat a udržovat zabezpečení vašich kontejnerů a jejich aplikací. Naučíte se, jak Security Center pomáhá s těmito základními aspekty zabezpečení kontejneru:

* Správa ohrožení zabezpečení
* Posílení zabezpečení prostředí kontejneru
* Ochrana za běhu

[karta zabezpečení kontejneru ![Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

## <a name="vulnerability-management---scanning-container-images-preview"></a>Správa ohrožení zabezpečení – kontrola imagí kontejneru (Preview)
Pokud chcete monitorovat svůj Azure Container Registry, ujistěte se, že jste na úrovni Standard Security Center (viz [ceny](https://docs.microsoft.com/azure/security-center/security-center-pricing.md)). Pak povolte volitelnou sadu registrů kontejnerů. Když se doručí nový obrázek, Security Center zkontroluje Image pomocí skeneru z špičkového dodavatele kontroly ohrožení zabezpečení, Qualys.

Po nalezení problémů – podle Qualys nebo Security Center – na řídicím panelu Security Center se zobrazí oznámení. U každé chyby zabezpečení Security Center poskytuje užitečná doporučení spolu se klasifikací závažnosti a pokyny, jak problém vyřešit. Podrobnosti o doporučeních Security Center najdete v seznamu doporučení v části [ochrana virtuálního počítače](security-center-virtual-machine-protection.md##compute-and-app-recs).

## <a name="environment-hardening"></a>Posílení zabezpečení prostředí

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Nepřetržité monitorování konfigurace Docker
Azure Security Center identifikuje nespravované kontejnery hostované na virtuálních počítačích s IaaS Linux nebo jiné počítače se systémem Linux s kontejnery Docker. Security Center nepřetržitě vyhodnocuje konfiguraci těchto kontejnerů. Pak je porovná s [centrem pro Internet Security (CIS) Docker test](https://www.cisecurity.org/benchmark/docker/). 

Security Center zahrnuje celou RuleSet srovnávacího testu služby CI Docker a upozorní vás, pokud vaše kontejnery nevyhovují žádnému z ovládacích prvků. Když nalezne chybnou konfiguraci, Security Center vygeneruje doporučení zabezpečení. Na **stránce doporučení** si můžete zobrazit doporučení a opravit problémy. Na kartě **kontejnery** se zobrazí také doporučení se všemi virtuálními počítači nasazenými pomocí Docker. Když prozkoumáte problémy zabezpečení na virtuálním počítači, Security Center poskytne další informace o kontejnerech v počítači. Tyto informace zahrnují verzi Docker a počet imagí spuštěných na hostiteli. Podrobnosti o doporučeních najdete [tady](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-protection). 

>[!NOTE]
> Tyto kontroly srovnávacích testů služby ci se nespouštějí na AKS instancích nebo virtuálních počítačích spravovaných datacihly.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Nepřetržité monitorování clusterů Kubernetes (Preview)
Security Center spolupracuje s Azure Kubernetes Service (AKS), což je spravovaná služba orchestrace kontejnerů Microsoftu pro vývoj, nasazování a správu kontejnerových aplikací.

AKS poskytuje bezpečnostní mechanismy a přehled o stavech zabezpečení vašich clusterů. Security Center tyto funkce využívají k těmto akcím:
* Nepřetržité monitorování konfigurace clusterů AKS
* Generování doporučení zabezpečení zarovnaného k oborovým standardům

Podrobnosti o doporučeních Security Center najdete v tématu [ochrana virtuálního počítače](security-center-virtual-machine-protection.md).

## <a name="run-time-protection---real-time-threat-detection"></a>Ochrana v době běhu – detekce hrozeb v reálném čase

Security Center poskytuje detekci hrozeb v reálném čase pro vaše kontejnerová prostředí a generuje výstrahy pro podezřelé aktivity. Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů.

Zjistili jsme hrozby na úrovni hostitele a AKS clusteru. Úplné podrobnosti najdete v tématu [Azure Container Service](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-).


## <a name="to-view-the-security-posture-of-your-container-related-resources"></a>Zobrazení stav zabezpečení prostředků souvisejících s kontejnerem
1.  Otevřete stránku **compute & aplikace** Security Center.
2.  Klikněte na kartu **kontejnery** . Zobrazí se stav clustery AKS, Registry ACR a virtuální počítače se spuštěným Docker.

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení kontejneru v Azure Security Center najdete v tématech:
* Podrobnosti o [integraci se službou Azure Kubernetes](azure-kubernetes-service-integration.md)

* Podrobnosti o [integraci s Azure Container Registry](azure-container-registry-integration.md)