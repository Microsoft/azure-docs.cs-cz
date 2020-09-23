---
title: Architektura řešení
description: Přečtěte si o toku informací v Azure Defenderu pro službu IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3d26d9e3d686ad7c34e7493dc1413b7a9e7a2f6b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936895"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender pro architekturu IoT

Tento článek vysvětluje funkční architekturu řešení Defender for IoT.

## <a name="defender-for-iot-components"></a>Defender pro součásti IoT

Defender pro IoT se skládá z následujících součástí:

- Integrace IoT Hub
- Agenti zařízení (volitelné)
- Odeslat sadu SDK pro zprávy zabezpečení
- Analytický kanál

### <a name="defender-for-iot-workflows"></a>Defender pro pracovní postupy IoT

Defender pro IoT funguje v jednom ze dvou pracovních postupů: integrovaných a rozšířených

### <a name="built-in"></a>Integrované

V **integrovaném** režimu je program Defender pro IoT povolený, když se rozhodnete zapnout možnost **zabezpečení** v IoT Hub. Nabízí sledování, doporučení a výstrahy v reálném čase, integrovaný režim nabízí viditelnost zařízení s jedním krokem a nespárované zabezpečení. V režimu sestavení není nutné instalovat agenta na žádná zařízení a k analýze a ochraně zařízení pole používá pokročilou analýzu u protokolovaných aktivit.

### <a name="enhanced"></a>Rozšířené

V **rozšířeném** režimu po zapnutí možnosti **zabezpečení** ve vašem IoT Hub a instalaci programu Defender pro agenty zařízení IoT na vaše zařízení agenti shromáždí, agreguje a analyzuje nezpracované události zabezpečení z vašich zařízení. Nezpracované události zabezpečení můžou zahrnovat připojení IP, vytváření procesů, přihlášení uživatelů a další informace týkající se zabezpečení. Defender pro agenty zařízení IoT také zpracovává agregaci událostí, aby se předešlo vysoké propustnosti sítě. Agenti jsou vysoce přizpůsobitelní, což umožňuje jejich použití pro konkrétní úkoly, jako je například odesílání pouze důležitých informací na nejrychlejší smlouvu SLA nebo pro agregaci rozsáhlých informací o zabezpečení a kontextu do větších segmentů, což vyloučí vyšší náklady na službu.

![Defender pro architekturu IoT](./media/architecture/azure-iot-security-architecture.png)

Agenti zařízení a další aplikace používají k posílání informací o zabezpečení do Azure IoT Hub k dismailu **sadu SDK pro zprávy zabezpečení Azure** . IoT Hub tyto informace vybírá a předá je do programu Defender pro službu IoT.

Po povolení služby Defender for IoT se kromě předávaných dat IoT Hub také pošle veškerá interní data pro analýzu pomocí programu Defender pro IoT. Mezi tato data patří protokoly operací cloudového zařízení, identity zařízení a konfigurace centra. Všechny tyto informace pomáhají vytvořit kanál Defender for IoT Analytics.

V Defenderu pro kanál IoT Analytics se taky dostanou další datové proudy analýzy hrozeb z různých zdrojů v rámci partnerů Microsoftu a Microsoftu. Defender pro IoT celého analytického kanálu funguje se všemi zákaznickými konfiguracemi provedenými ve službě (například s vlastními výstrahami a s využitím sady SDK pro posílání zpráv zabezpečení).

Pomocí analytického kanálu sloučí Defender pro IoT všechny datové proudy s informacemi, aby vygenerovala doporučení a upozornění pro akce. Kanál obsahuje jak vlastní pravidla vytvořená výzkumnými pracovníky a odborníky z hlediska zabezpečení, tak i modely strojového učení, které hledají odchylky od standardních chování zařízení a analýzy rizik.

Doporučení a upozornění pro IoT (výstup kanálu analýz) se zapisuje do Log Analytics pracovního prostoru každého zákazníka. Včetně nezpracovaných událostí v pracovním prostoru a výstrah a doporučení umožňují důkladná podrobně vyšetřování a dotazy pomocí přesně podrobností o zjištěných podezřelých aktivitách.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o základní architektuře a pracovním postupu pro řešení pro IoT. Další informace o požadavcích, o tom, jak začít a povolit řešení zabezpečení v IoT Hub, najdete v následujících článcích:

- [Požadavky služeb](service-prerequisites.md)
- [Začínáme](getting-started.md)
- [Konfigurace vašeho řešení](quickstart-configure-your-solution.md)
- [Povolit zabezpečení v IoT Hub](quickstart-onboard-iot-hub.md)
- [Defender pro IoT – Nejčastější dotazy](resources-frequently-asked-questions.md)
- [Defender pro výstrahy zabezpečení IoT](concept-security-alerts.md)
