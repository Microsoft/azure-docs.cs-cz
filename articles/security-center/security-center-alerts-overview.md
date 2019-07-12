---
title: Výstrahy zabezpečení ve službě Azure Security Center | Dokumentace Microsoftu
description: Toto téma vysvětluje, co jsou výstrahy zabezpečení a různé typy dostupných ve službě Azure Security Center.
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
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 06f41bbfd97d5deb59e7bfd08615b2f28e256070
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571670"
---
# <a name="security-alerts-in-azure-security-center"></a>Výstrahy zabezpečení ve službě Azure Security Center

Tento článek představuje různé druhy výstrah zabezpečení dostupným v Azure Security Center (ASC). Existuje řada různých výstrah pro mnoho typů jiný prostředek. ASC generuje výstrahy pro oba prostředky nasazené v Azure a prostředky nasazené v místní a hybridní Cloudová prostředí. 

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?

Upozornění jsou typem oznámení, které Security Center se vygeneruje při zjištění hrozeb pro vaše prostředky. Upřednostňuje a obsahuje seznam upozornění společně s informacemi potřebnými pro vás k rychlému prozkoumání problému. Security Center také poskytuje doporučení, jak můžete nápravě útoku.

## <a name="how-does-security-center-detect-threats"></a>Jak Security Center detekuje hrozby?

Zjistily skutečné hrozby a snížil počet falešných poplachů, Security Center shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a řešení ochrany koncových bodů. Security Center tyto informace analyzuje a často přitom koreluje data z více zdrojů, a identifikovat hrozby.

ASC monitoruje prostředky nasazené v Azure nebo nasadit na jiné místní a hybridní Cloudová prostředí. Další informace o zjišťování a reakce na hrozby, naleznete v tématu [jak Security Center detekuje hrozby a reaguje na ně](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Typy výstrah zabezpečení

Následující témata vás provedou u jednotlivých výstrah ASC podle typů prostředků:

* [Virtuální počítače IaaS a servery výstrahy](security-center-alerts-iaas.md)
* [Upozornění nativní výpočetní prostředí](security-center-alerts-compute.md)
* [Data services – oznámení](security-center-alerts-data-services.md)

Následující témata popisují, jak využívá jiné telemetrie shromažďující z integraci s vaší stávající infrastrukturou Azure k použití vrstvy zvýšenou ochranu pro prostředky nasazené v Azure Security Center:

* [Výstrahy služeb vrstev](security-center-alerts-service-layer.md)
* [Integrace s produkty zabezpečení Azure](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>Co jsou upozornění incidenty?

Incident zabezpečení je kolekce souvisejících výstrah, namísto vypisování každého upozornění jednotlivě. Security Center používá fusion korelovat různé výstrahy a s nízkou spolehlivostí signály na incidenty zabezpečení.

Pomocí incidenty, Security Center vám poskytne ucelený přehled o útočné kampani a všech souvisejících výstrahách. Toto zobrazení umožňuje rychle pochopit, jaké akce útočník podniká a které prostředky byly zasaženy. Další informace najdete v tématu [korelací výstrah inteligentní Cloud](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Začínáme s výstrahami

V následujících tématech, chcete-li zjistit prostředky, které monitoruje služba ASC a pokyny o tom, jak reagovat na výstrahy předložený ASC.

* Pokud chcete zobrazit, které platformy a funkce jsou chráněny ASC, naleznete v tématu [platformy a funkcí podporovaných službou Azure Security Center](security-center-os-coverage.md).  
* Co jsou incidenty zabezpečení a jak ASC reaguje na ně najdete v tématu [jak k řešení bezpečnostních incidentů v Azure Security Center](security-center-incident.md). 
* Zjistěte, jak spravovat upozornění se zobrazí, najdete v článku [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md).
* Informace o ověření, že je správně nakonfigurované Centrum zabezpečení a podporovat testovací upozornění, najdete v tématu [ověřování výstrah ve službě Azure Security Center](security-center-alert-validation.md).  


## <a name="upgrade-to-standard-for-advanced-detections"></a>Upgrade na úroveň Standard pro rozšířené detekce

Pokud chcete nastavit rozšířené detekce, upgradujte na Azure Security Center Standard. 

1. V nabídce služby Security Center, vyberte **zásady zabezpečení**.
2. Odběry, které chcete přesunout na úroveň Standard, klikněte na tlačítko **upravit nastavení**. 
3. Na stránce Nastavení vyberte **cenová úroveň**. 
   Bezplatná zkušební verze je k dispozici po dobu jednoho měsíce. Další informace najdete [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, co jsou výstrahy zabezpečení a různých typech výstrah ve službě Security Center. Další informace naleznete v následujících tématech:

* [Průvodce plánováním a provozem služby Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Nejčastější dotazy k Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq): Přečtěte si nejčastější dotazy o použití této služby.

