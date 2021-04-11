---
title: Co je architektura řešení založená na agentech
description: Přečtěte si o Azure Defenderu pro architekturu a informační tok založené na agentech IoT.
ms.topic: overview
ms.date: 1/25/2021
ms.openlocfilehash: e08c3f151c3d3bc4fe08e61d960874b07f5b63e8
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383520"
---
# <a name="what-is-agent-based-solution-for-device-builders"></a>Co je řešení založené na agentech pro sestavovatele zařízení

Tento článek popisuje funkční architekturu řešení Defender pro řešení založená na agentovi IoT. Azure Defender pro IoT nabízí dvě sady možností, které vyhovují potřebám vašeho prostředí, řešení bez agentů pro organizace a řešení založené na agentech pro sestavovatele zařízení.

## <a name="iot-hub-built-in-security"></a>Integrované zabezpečení centra IoT

V každém novém IoT Hub, který je vytvořen, je Defender pro IoT ve výchozím nastavení povolen. Defender pro IoT poskytuje monitorování, doporučení a výstrahy v reálném čase, aniž by bylo potřeba instalovat agenta na všech zařízeních a pomocí pokročilých analýz na základě protokolovaných IoT Hub meta data analyzovat a chránit vaše zařízení polí a centra IoT. 

## <a name="defender-for-iot-micro-agent"></a>Defender pro agenta IoT Micro 

Defender pro IoT Micro agent poskytuje hloubkovou ochranu zabezpečení a přehled o chování zařízení. shromažďuje, agreguje a analyzuje nezpracované události zabezpečení z vašich zařízení. Nezpracované události zabezpečení můžou zahrnovat připojení IP, vytváření procesů, přihlášení uživatelů a další informace týkající se zabezpečení. Defender pro agenty zařízení IoT také zpracovává agregaci událostí, aby se zabránilo vysoké propustnosti sítě. Agenti jsou vysoce přizpůsobitelní, což umožňuje jejich použití pro konkrétní úkoly, jako je například odesílání pouze důležitých informací na nejrychlejší smlouvu SLA nebo pro agregaci rozsáhlých informací o zabezpečení a kontextu do větších segmentů, což vyloučí vyšší náklady na službu.

Agenti zařízení a další aplikace používají k posílání informací o zabezpečení do služby Azure IoT Hub **sadu SDK pro zprávy zabezpečení Azure** . Služba IoT Hub získá tyto informace a předá ji do programu Defender pro službu IoT.

Po povolení služby Defender pro IoT se kromě předávaných dat taky služba IoT Hub pošle veškerá interní data pro analýzu pomocí programu Defender pro IoT. Mezi tato data patří protokoly operací cloudového zařízení, identity zařízení a konfigurace centra. Všechny tyto informace pomáhají vytvořit kanál Defender for IoT Analytics.

V Defenderu pro kanál IoT Analytics se taky v rámci partnerů Microsoftu a Microsoftu dostává jiné datové proudy analýzy hrozeb z různých zdrojů. Defender pro IoT celého analytického kanálu funguje se všemi zákaznickými konfiguracemi provedenými ve službě (například s vlastními výstrahami a s využitím sady SDK pro posílání zpráv zabezpečení).

Pomocí analytického kanálu sloučí Defender pro IoT všechny datové proudy s informacemi, aby vygenerovala doporučení a upozornění pro akce. Kanál obsahuje jak vlastní pravidla vytvořená výzkumnými pracovníky a odborníky z hlediska zabezpečení, tak i modely strojového učení, které hledají odchylky od standardních chování zařízení a analýzy rizik.

Doporučení a upozornění pro IoT (výstup kanálu analýz) se zapisuje do Log Analytics pracovního prostoru každého zákazníka. Včetně nezpracovaných událostí v pracovním prostoru a výstrah a doporučení umožňují důkladné podrobně vyšetřování a dotazy pomocí přesně podrobností o zjištěných podezřelých aktivitách.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="Architektura mikroagentů.":::

## <a name="next-steps"></a>Další kroky

[Defender pro IoT – Nejčastější dotazy](resources-frequently-asked-questions.md)

[Systémové požadavky](quickstart-system-prerequisites.md)
