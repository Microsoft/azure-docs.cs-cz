---
title: Výstrahy zabezpečení v Azure Security Center | Microsoft Docs
description: Toto téma vysvětluje, jaké jsou výstrahy zabezpečení, a různé typy, které jsou k dispozici v Azure Security Center.
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
ms.openlocfilehash: d905096432c6e05e5386631480e5cc45b5c890c5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013300"
---
# <a name="security-alerts-in-azure-security-center"></a>Výstrahy zabezpečení ve službě Azure Security Center

V Azure Security Center existuje celá řada výstrah pro mnoho různých typů prostředků. Security Center generuje výstrahy pro prostředky nasazené v Azure a také pro prostředky nasazené v místních i hybridních cloudových prostředích. 

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?

Výstrahy jsou oznámení, která Security Center generují při detekci hrozeb na vašich prostředcích. Nastaví prioritu a zobrazí seznam výstrah spolu s informacemi potřebnými k rychlému prozkoumání problému. Security Center také poskytuje doporučení, jak můžete útok napravit.

## <a name="how-does-security-center-detect-threats"></a>Jak Security Center detekuje hrozby?

Security Center shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure a sítě, aby zjistila skutečné hrozby a snížila falešně pozitivní výsledky. Funguje taky s připojenými partnerskými řešeními, jako jsou brány firewall a řešení ochrany koncových bodů. Security Center analyzuje tyto informace a často koreluje informace z různých zdrojů k identifikaci hrozeb.

Security Center sleduje prostředky v jakémkoli prostředí, které může být. Další informace o detekci a reakci na hrozby najdete v tématu [jak Security Center detekuje hrozby a reaguje na](security-center-detection-capabilities.md#asc-detects)ně.

## <a name="security-alert-types"></a>Typy výstrah zabezpečení

Následující témata vás provedou různými výstrahami v závislosti na typech prostředků:

* [Výstrahy IaaS virtuální počítače a servery](security-center-alerts-iaas.md)
* [Nativní výpočetní výstrahy](security-center-alerts-compute.md)
* [Výstrahy datových služeb](security-center-alerts-data-services.md)

Následující témata vysvětlují, jak Security Center používá jinou telemetrii, kterou shromažďuje z integrace s infrastrukturou Azure, aby bylo možné použít další vrstvy ochrany pro prostředky nasazené v Azure:

* [Výstrahy vrstvy služeb](security-center-alerts-service-layer.md)
* [Integrace s produkty zabezpečení Azure](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Co jsou incidenty zabezpečení?

Incident zabezpečení je kolekce souvisejících výstrah, místo aby se jednotlivé výstrahy zobrazovaly jednotlivě. Security Center používá [korelaci cloudových inteligentních výstrah](security-center-alerts-cloud-smart.md) ke korelaci různých výstrah a signálů s nízkou věrností do incidentů zabezpečení.

Pomocí incidentů vám Security Center poskytuje jediné zobrazení kampaně útoku a všech souvisejících výstrah. Toto zobrazení vám umožní rychle pochopit, jaké akce útočníka trvala, a jaké prostředky byly ovlivněny. Další informace najdete v tématu [korelace cloudové inteligentní výstrahy](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Začínáme s výstrahami

Tady jsou články, které vám pomohou lépe pochopit prostředky monitorované nástrojem Security Center a poskytnout pokyny, jak reagovat na zobrazené výstrahy.

* [Platformy a funkce podporované službou Azure Security Center](security-center-os-coverage.md)  
* [Postup zpracování incidentů zabezpečení v Azure Security Center](security-center-incident.md) 
* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Ověřování výstrah ve službě Azure Security Center](security-center-alert-validation.md)  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Upgrade na úroveň Standard pro rozšířené zjišťování

Pokud chcete nastavit rozšířené detekce, upgradujte na Azure Security Center Standard. 

1. V nabídce **Security Center** vyberte **zásady zabezpečení**.
2. U předplatných, která chcete přesunout na úroveň Standard, vyberte **Upravit nastavení**. 
3. Na stránce nastavení vyberte cenová **úroveň**. 
   K dispozici je bezplatná zkušební verze za měsíc. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o různých typech výstrah, které jsou k dispozici v Security Center. Další informace naleznete v tématu:

* [Průvodce plánováním a provozem služby Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center – nejčastější dotazy](https://docs.microsoft.com/azure/security-center/security-center-faq)

