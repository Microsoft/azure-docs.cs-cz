---
title: Principy architektury řešení Azure Security Center for IoT| Dokumenty společnosti Microsoft
description: Další informace o toku informací v Centru zabezpečení Azure pro službu IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 2e55f98f8c7b6ddbc21f7ea8633467461ea5be29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922164"
---
# <a name="azure-security-center-for-iot-architecture"></a>Architektura služby Azure Security Center pro IoT

Tento článek vysvětluje architekturu funkčního systému řešení Azure Security Center for IoT. 

## <a name="azure-security-center-for-iot-components"></a>Azure Security Center pro součásti IoT

Azure Security Center pro IoT se skládá z následujících součástí:
- Integrace IoT Hubu
- Agenti zařízení (volitelné)
- Odeslat bezpečnostní zprávu SDK
- Kanál Analytics
 
### <a name="azure-security-center-for-iot-workflows"></a>Pracovní postupy Azure Security Center pro IoT

Azure Security Center pro IoT funguje v jednom ze dvou pracovních postupů: integrované a vylepšené  

### <a name="built-in"></a>Integrované
V **integrovaném** režimu azure security center pro IoT je povolena, když se rozhodnete zapnout možnost **zabezpečení** ve vašem IoT Hub. Vestavěný režim nabízí monitorování v reálném čase, doporučení a výstrahy a nabízí jednostupňovou viditelnost zařízení a bezkonkurenční zabezpečení. Vestavěný režim nevyžaduje instalaci agenta na všech zařízeních a používá pokročilé analýzy na přihlášených aktivitách k analýze a ochraně vašeho terénního zařízení. 

### <a name="enhanced"></a>Rozšířené 
V **rozšířeném** režimu po zapnutí možnosti **Zabezpečení** ve službě IoT Hub a instalaci Azure Security Center pro agenty zařízení IoT na vaše zařízení agenti shromažďují, agregují a analyzují nezpracované události zabezpečení z vašich zařízení. Nezpracované události zabezpečení mohou zahrnovat připojení IP, vytváření procesů, přihlášení uživatelů a další informace týkající se zabezpečení. Azure Security Center pro agenty zařízení IoT také zpracovávat agregace událostí, aby se zabránilo vysoké propustnost sítě. Agenti jsou vysoce přizpůsobitelné, což vám umožňuje je používat pro konkrétní úkoly, jako je odesílání pouze důležitých informací v nejrychlejší sla nebo pro agregaci rozsáhlých informací o zabezpečení a kontextu do větších segmentů, aby se zabránilo vyšším nákladům na služby.

![Architektura služby Azure Security Center pro IoT](./media/architecture/azure-iot-security-architecture.png)
 
Agenti zařízení a další aplikace používají azure **odeslat zprávu zabezpečení SDK** k odeslání informací o zabezpečení do služby Azure IoT Hub. Služba IoT Hub tyto informace vyzvedne a předá je službě Azure Security Center for IoT.

Jakmile je služba Azure Security Center for IoT povolena, kromě předávaných dat služba IoT Hub také odešle všechna svá interní data k analýze pomocí Centra zabezpečení Azure pro IoT. Tato data zahrnují protokoly operací cloudu zařízení, identity zařízení a konfiguraci centra. Všechny tyto informace pomáhají vytvořit kanál Azure Security Center for IoT analytics.
 
Azure Security Center pro analytics IoT analytiky kanálu také přijímá další streamy analýzy hrozeb z různých zdrojů v rámci Microsoft a Microsoft partnerů. Celý analytický kanál Centra zabezpečení Azure pro IoT funguje s každou konfigurací zákazníků provedenou ve službě (například s vlastními výstrahami a použitím sady SDK zasílejte zprávu zabezpečení).
 
Pomocí kanálu analýzy Azure Security Center pro IoT kombinuje všechny datové proudy informací ke generování doporučení a výstrah s využitím informací. Kanál obsahuje jak vlastní pravidla vytvořená výzkumníky zabezpečení a odborníky, tak modely strojového učení, které hledají odchylku od standardního chování zařízení a analýzy rizik.
 
Azure Security Center pro ioT doporučení a výstrahy (výstup kanálu analýzy) se zapíše do pracovního prostoru Log Analytics každého zákazníka. Zahrnutí nezpracovaných událostí v pracovním prostoru, stejně jako výstrahy a doporučení umožňuje podrobné podrobné šetření a dotazy pomocí přesné podrobnosti o zjištěných podezřelých aktivit.  

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o základní architektuře a pracovním postupu Řešení Azure Security Center pro IoT. Další informace o požadavcích, postup, jak začít a povolit řešení zabezpečení v centru IoT Hub, najdete v následujících článcích:

- [Požadavky služeb](service-prerequisites.md)
- [Začínáme](getting-started.md)
- [Konfigurace vašeho řešení](quickstart-configure-your-solution.md)
- [Povolení zabezpečení v centru IoT Hub](quickstart-onboard-iot-hub.md)
- [Nejčastější dotazy k Centru zabezpečení Azure pro IoT](resources-frequently-asked-questions.md)
- [Výstrahy zabezpečení ve službě Azure Security Center pro IoT](concept-security-alerts.md)
