---
title: Principy ASC pro architekturu řešení IoT ve verzi Preview | Dokumentace Microsoftu
description: Další informace o toku informací v ASC pro služby Azure IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: ee81d9543525ba1187fc6c078391559929b9bf96
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541899"
---
# <a name="asc-for-iot-architecture"></a>ASC architektury IoT

Tento článek popisuje architekturu funkčnosti systému služby Azure Security Center pro řešení IoT. 

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="asc-for-iot-components"></a>ASC pro součásti IoT

ASC pro IoT se skládá z následujících součástí:
- Agenti zařízení
- Odeslat zprávu zabezpečení sady SDK
- Integrace služby IoT Hub
- Analytický kanál
 
### <a name="asc-for-iot-workflow"></a>ASC pro pracovní postup IoT

ASC pro agenty zařízení IoT umožňuje snadno shromažďovat události zabezpečení nezpracovaná z vašich zařízení. Nezpracovaná bezpečnostních událostí může obsahovat připojení IP, proces vytvoření, přihlášení uživatelů a dalších informací týkajících se zabezpečení. ASC pro agenty zařízení IoT také zpracovávat události agregaci, která pomáhá zabránit sítě vysokou propustnost. Agenti jsou vysoce přizpůsobitelné, umožňují použít je pro konkrétní úlohy, jako je odeslání pouze důležité informace na nejrychlejší smlouvou SLA, nebo pro agregaci rozsáhlé zabezpečení informací a kontext do větší segmentů, jak se vyhnout vyšší náklady na službu.
 
Zařízení agenty a jiné použití aplikace **Azure ASC odeslat zprávu zabezpečení sady SDK** odesílat informace o zabezpečení do služby Azure IoT Hub. IoT Hub převezme tyto informace a předá jej do ASC pro služby Azure IoT.

Po povolení ASC pro služby Azure IoT, kromě předané dat služby IoT Hub rovněž odesílá všechny svoje vnitřní data pro analýzu, ASC pro IoT. Tato data zahrnují protokoly operací typu zařízení cloud, identit zařízení a konfiguraci rozbočovače. Všechny tyto informace pomáhá vytvořit ASC pro IoT analytics kanálu.
 
ASC pro IoT analytics kanál také přijímat další threat intelligence datové proudy z různých zdrojů v rámci společnosti Microsoft a Microsoft partnery. ASC pro IoT analytics celý kanál funguje s každou zákazníka konfigurace služby (například vlastní výstrahy a využívání odeslat zprávu zabezpečení sady SDK).
 
Použití kanálu analýzy, ASC pro IoT je kombinací všech datových proudů informace pro generování užitečná doporučení a výstrahy. Kanál obsahuje i vlastní pravidla vytvořená výzkumníci a odborníky, jakož i hledání odchylky od analýzy chování a riziko zařízení standard modelů strojového učení.
 
ASC pro IoT doporučení a výstrahy (analytický kanál výstupu) se zapíše do pracovního prostoru Log Analytics z každého zákazníka. Včetně nezpracovaných událostí v pracovním prostoru a upozornění a doporučení umožňuje podrobné informace o vyšetřování a dotazy, které používají přesné údaje o podezřelých aktivitách zjištěna.  

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o základní architektuře a pracovní postup ASC pro řešení IoT. Další informace o požadavcích, jak začít pracovat a povolit řešení zabezpečení ve službě IoT Hub, najdete v následujících článcích:

- [Požadavky na služby](service-prerequisites.md)
- [Začínáme](getting-started.md)
- [Konfigurace řešení](quickstart-configure-your-solution.md)
- [Povolit zabezpečení ve službě IoT Hub](quickstart-onboard-iot-hub.md)
- [ASC pro IoT – nejčastější dotazy](resources-frequently-asked-questions.md)
- [ASC pro výstrahy zabezpečení IoT](concept-security-alerts.md)

