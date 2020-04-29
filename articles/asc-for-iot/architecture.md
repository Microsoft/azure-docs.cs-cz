---
title: Architektura řešení
description: Přečtěte si o toku informací Azure Security Center pro službu IoT.
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
ms.openlocfilehash: 4cc7d1982555f058a4ea23f7d8a6fdc2d83e484d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311756"
---
# <a name="azure-security-center-for-iot-architecture"></a>Architektura služby Azure Security Center pro IoT

Tento článek vysvětluje funkční architekturu Azure Security Center pro řešení IoT.

## <a name="azure-security-center-for-iot-components"></a>Azure Security Center pro komponenty IoT

Azure Security Center pro IoT se skládá z následujících součástí:

- Integrace IoT Hub
- Agenti zařízení (volitelné)
- Odeslat sadu SDK pro zprávy zabezpečení
- Analytický kanál

### <a name="azure-security-center-for-iot-workflows"></a>Azure Security Center pro pracovní postupy IoT

Azure Security Center pro IoT funguje v jednom ze dvou pracovních postupů: integrovaných a rozšířených

### <a name="built-in"></a>Integrované

V **integrovaném** režimu je Azure Security Center pro IoT povolený, když se rozhodnete zapnout možnost **zabezpečení** v IoT Hub. Nabízí sledování, doporučení a výstrahy v reálném čase, integrovaný režim nabízí viditelnost zařízení s jedním krokem a nespárované zabezpečení. V režimu sestavení není nutné instalovat agenta na žádná zařízení a k analýze a ochraně zařízení pole používá pokročilou analýzu u protokolovaných aktivit.

### <a name="enhanced"></a>Rozšířené

Když v **rozšířeném** režimu zapnete možnost **zabezpečení** ve vašem IoT Hub a nainstalujete Azure Security Center pro agenty zařízení IoT na vaše zařízení, budou agenti shromažďovat, agregovat a analyzovat nezpracované události zabezpečení z vašich zařízení. Nezpracované události zabezpečení můžou zahrnovat připojení IP, vytváření procesů, přihlášení uživatelů a další informace týkající se zabezpečení. Azure Security Center pro agenty zařízení IoT také zpracovávají agregaci událostí, aby se zabránilo vysoké propustnosti sítě. Agenti jsou vysoce přizpůsobitelní, což umožňuje jejich použití pro konkrétní úkoly, jako je například odesílání pouze důležitých informací na nejrychlejší smlouvu SLA nebo pro agregaci rozsáhlých informací o zabezpečení a kontextu do větších segmentů, což vyloučí vyšší náklady na službu.

![Architektura služby Azure Security Center pro IoT](./media/architecture/azure-iot-security-architecture.png)

Agenti zařízení a další aplikace používají k posílání informací o zabezpečení do Azure IoT Hub k dismailu **sadu SDK pro zprávy zabezpečení Azure** . IoT Hub tyto informace přenese a přepošle ji do Azure Security Center pro službu IoT.

Jakmile je služba Azure Security Center for IoT povolena, kromě předaných IoT Hub dat také pošle veškerá interní data pro analýzu Azure Security Center pro IoT. Mezi tato data patří protokoly operací cloudového zařízení, identity zařízení a konfigurace centra. Všechny tyto informace pomáhají vytvořit Azure Security Center pro kanál IoT Analytics.

Azure Security Center pro kanál IoT Analytics taky přijímá další datové proudy pro analýzu hrozeb z různých zdrojů v rámci partnerů Microsoftu a Microsoftu. Azure Security Center pro IoT pro celé účely analytického kanálu funguje se všemi zákaznickými konfiguracemi provedenými ve službě (například s vlastními výstrahami a s využitím sady SDK pro posílání zpráv zabezpečení).

Použití kanálu analýzy Azure Security Center pro IoT kombinuje všechny datové proudy s informacemi, aby vygenerovala doporučení a upozornění pro akce. Kanál obsahuje jak vlastní pravidla vytvořená výzkumnými pracovníky a odborníky z hlediska zabezpečení, tak i modely strojového učení, které hledají odchylky od standardních chování zařízení a analýzy rizik.

Azure Security Center doporučení a upozornění IoT (výstup kanálu analýz) se zapisuje do pracovního prostoru Log Analytics každého zákazníka. Včetně nezpracovaných událostí v pracovním prostoru a výstrah a doporučení umožňují důkladná podrobně vyšetřování a dotazy pomocí přesně podrobností o zjištěných podezřelých aktivitách.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o základní architektuře a pracovním postupu Azure Security Center pro řešení IoT. Další informace o požadavcích, o tom, jak začít a povolit řešení zabezpečení v IoT Hub, najdete v následujících článcích:

- [Požadavky služeb](service-prerequisites.md)
- [Začínáme](getting-started.md)
- [Konfigurace vašeho řešení](quickstart-configure-your-solution.md)
- [Povolit zabezpečení v IoT Hub](quickstart-onboard-iot-hub.md)
- [Nejčastější dotazy k Azure Security Center pro IoT](resources-frequently-asked-questions.md)
- [Výstrahy zabezpečení ve službě Azure Security Center pro IoT](concept-security-alerts.md)
