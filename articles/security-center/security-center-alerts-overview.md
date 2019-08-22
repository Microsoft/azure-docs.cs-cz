---
title: Výstrahy zabezpečení v Azure Security Center | Microsoft Docs
description: Toto téma vysvětluje, co jsou výstrahy zabezpečení a různé typy, které jsou k dispozici v Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/20/2019
ms.author: v-mohabe
ms.openlocfilehash: 25fb94afdfe68510c66cff130a3abc8b74d7311d
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647308"
---
# <a name="security-alerts-in-azure-security-center"></a>Výstrahy zabezpečení v Azure Security Center

V tomto článku jsou uvedeny různé typy výstrah zabezpečení, které jsou k dispozici v Azure Security Center (ASC). Existuje řada výstrah pro mnoho různých typů prostředků. ASC generuje výstrahy pro prostředky nasazené v Azure a prostředky nasazené v místních i hybridních cloudových prostředích. 

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?

Výstrahy jsou oznámení, která Security Center generují při detekci hrozeb na vašich prostředcích. Nastaví prioritu a vypíše výstrahy spolu s informacemi, které jsou potřeba k rychlému prozkoumání problému. Security Center také poskytuje doporučení, jak můžete útok napravit.

## <a name="how-does-security-center-detect-threats"></a>Jak Security Center detekuje hrozby?

Security Center shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a řešení ochrany koncových bodů, ke zjištění skutečných hrozeb a omezení falešně pozitivních hodnot. Security Center analyzuje tyto informace a často koreluje informace z různých zdrojů k identifikaci hrozeb.

ASC monitoruje prostředky bez ohledu na to, jestli jsou nasazené v Azure nebo nasazené na jiných místních a hybridních cloudových prostředích. Další informace o detekci a reakci na hrozby najdete v tématu [jak Security Center detekuje hrozby a reaguje na](security-center-detection-capabilities.md#asc-detects)ně.

## <a name="security-alert-types"></a>Typy výstrah zabezpečení

Následující témata vás provedou různými výstrahami ASC v závislosti na typech prostředků:

* [Výstrahy &ch IaaSch serverů virtuálních počítačů](security-center-alerts-iaas.md)
* [Nativní výpočetní výstrahy](security-center-alerts-compute.md)
* [Výstrahy datových služeb](security-center-alerts-data-services.md)

Následující témata vysvětlují, jak Security Center využívá různé telemetrie, které shromažďuje z integrace s infrastrukturou Azure, aby bylo možné použít další vrstvy ochrany pro prostředky nasazené v Azure:

* [Výstrahy vrstvy služeb](security-center-alerts-service-layer.md)
* [Integrace s produkty zabezpečení Azure](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>Co jsou incidenty výstrah?

Incident zabezpečení je kolekce souvisejících výstrah, místo aby se jednotlivé výstrahy zobrazovaly jednotlivě. Security Center používá [korelaci cloudových inteligentních výstrah](security-center-alerts-cloud-smart.md) ke korelaci různých výstrah a signálů s nízkou věrností do incidentů zabezpečení.

Pomocí incidentů vám Security Center poskytuje jediné zobrazení kampaně útoku a všech souvisejících výstrah. Toto zobrazení vám umožní rychle pochopit, jaké akce útočník prováděl a jaké prostředky byly ovlivněny. Další informace najdete v tématu [korelace cloudové inteligentní výstrahy](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Začínáme s výstrahami

V následujících tématech najdete další informace o prostředcích monitorovaných pomocí ASC a pokyny k tomu, jak reagovat na výstrahy prezentované pomocí ASC.

* Chcete-li zjistit, které platformy a funkce jsou chráněny pomocí funkce ASC, přečtěte si téma [platformy a funkce podporované nástrojem Azure Security Center](security-center-os-coverage.md).  
* Chcete-li zjistit, co jsou incidenty zabezpečení a jak je ASC reaguje, přečtěte si téma [jak zpracovávat incidenty zabezpečení v Azure Security Center](security-center-incident.md). 
* Informace o tom, jak spravovat výstrahy, které obdržíte, najdete v tématu [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md).
* Informace o tom, jak ověřit, jestli je Security Center správně nakonfigurovaná a že se má povzbuzovat testovací upozornění, najdete [v tématu ověřování výstrah v Azure Security Center](security-center-alert-validation.md).  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Upgrade na úroveň Standard pro rozšířené zjišťování

Pokud chcete nastavit rozšířené detekce, upgradujte na Azure Security Center Standard. 

1. V nabídce Security Center vyberte **zásady zabezpečení**.
2. U předplatných, která byste chtěli přesunout na úroveň Standard, klikněte na **Upravit nastavení**. 
3. Na stránce nastavení vyberte cenová **úroveň**. 
   K dispozici je bezplatná zkušební verze za měsíc. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, co jsou výstrahy zabezpečení, a různé typy výstrah, které jsou k dispozici v Security Center. Další informace naleznete v následujících tématech:

* [Průvodce plánováním a provozem služby Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Nejčastější dotazy k Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq): Přečtěte si nejčastější dotazy o použití této služby.

