---
title: Nejčastější dotazy ke službě Azure Defender pro IoT Agent
description: Získejte odpovědi na nejčastější dotazy týkající se Azure Defenderu pro agenta IoT.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 4bd7d3c9b76dfb37e53cc51e5e16b660545cb7f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778657"
---
# <a name="azure-defender-for-iot-agent-frequently-asked-questions"></a>Nejčastější dotazy ke službě Azure Defender pro IoT Agent

Tento článek obsahuje seznam nejčastějších dotazů a odpovědí k programu Defender pro IoT agent.

## <a name="do-i-have-to-install-an-embedded-security-agent"></a>Musím instalovat vloženého agenta zabezpečení?

Instalace agenta na zařízeních IoT není povinná, aby bylo možné povolit Defender pro IoT. Můžete si vybrat z následujících tří možností a získat na základě vašeho výběru různé úrovně monitorování zabezpečení a možnosti správy:

- Pasivní a neinvazivní nasazení (bez agentů), které využívá senzory NTA (Analýza provozu sítě) k monitorování a poskytování hloubkového rizika IoT/OT s nulovým dopadem na výkon sítě a zařízení.
- Nainstalujte agenta zabezpečení Defender pro IoT Embedded s úpravami nebo beze změn. Tato možnost poskytuje nejvyšší úroveň rozšířených přehledů zabezpečení pro chování zařízení a přístup.

- Vytvořte vlastního agenta a implementujte schéma zprávy zabezpečení programu Defender for IoT. Tato možnost povolí použití programu Defender pro analytické nástroje pro IoT nad vaším agentem zabezpečení zařízení.

- Žádná instalace agenta zabezpečení na vaše zařízení IoT. Tato možnost umožňuje IoT Hub monitorování komunikace se sníženými možnostmi pro monitorování a správu zabezpečení.

## <a name="what-does-the-defender-for-iot-agent-do"></a>K čemu se používá Defender pro IoT Agent?

Defender pro službu IoT agent poskytuje pro konfiguraci zařízení, chování a přístup (pomocí kontroly konfigurace) pokrytí hrozeb na úrovni zařízení, proces & připojení. Agent zabezpečení programu Defender for IoT nekontroluje data ani aktivity související s obchodem.

Agent zabezpečení pro IoT for IoT je open source a dostupný na GitHubu v 32 bitových a 64 verzích Windows a Linux: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Jaké jsou závislosti a požadavky agenta?

Defender pro IoT podporuje širokou škálu platforem. Podívejte se na [podporované platformy zařízení](how-to-deploy-agent.md) a ověřte podporu pro konkrétní zařízení.

## <a name="which-data-is-collected-by-the-agent"></a>Která data shromažďuje Agent?

Agentem se shromažďují možnosti připojení, přístup, konfigurace brány firewall, seznam procesů & standardních hodnot operačního systému.

## <a name="how-much-data-will-the-agent-generate"></a>Kolik dat bude agent generovat?

Generování dat agenta je založené na zařízení, aplikaci, typu připojení a konfiguraci zákaznického agenta. Vzhledem k vysoké variabilitě mezi zařízeními a řešeními IoT doporučujeme, abyste agenta nasadili v testovacím prostředí nebo nastavení testu, abyste mohli sledovat, učit a nastavovat konkrétní konfiguraci, která vyhovuje vašim potřebám, a současně měřit množství vygenerovaných dat. Po spuštění služby poskytuje Defender pro IoT agent provozní doporučení pro optimalizaci propustnosti agenta, které vám pomůžou s procesem konfigurace a přizpůsobení.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Využívají zprávy agenta kvótu od IoT Hub?

Ano. Data přenášená agentem se počítají ve vaší kvótě IoT Hub.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Co dál? Nainstaloval (a) jsem agenta a nevidí žádné aktivity ani protokoly...

1. Ověřte, jestli [typ agenta odpovídá určené platformě operačního systému vašeho zařízení](how-to-deploy-agent.md) .

1. Potvrďte, že [je agent na zařízení spuštěný](how-to-agent-configuration.md).

1. Ověřte, že se [služba úspěšně aktivovala](quickstart-onboard-iot-hub.md) pro **zabezpečení** v IoT Hub.

1. Ověřte, že je zařízení [nakonfigurované v IoT Hub s modulem Defender for IoT](quickstart-create-security-twin.md).

Pokud jsou aktivity nebo protokoly stále nedostupné, požádejte o další nápovědu svého Defenderu pro IoT partner.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Co se stane, když připojení k Internetu přestane fungovat?

Senzory a agenti pokračují v běhu a ukládají data, pokud je zařízení spuštěné. Data se ukládají do mezipaměti zpráv zabezpečení podle konfigurace velikosti. Když zařízení znovu získá připojení, zprávy o zabezpečení budou pokračovat v posílání.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Může agent ovlivnit výkon zařízení nebo jiného nainstalovaného softwaru?

Agent spotřebovává prostředky počítače jako jakoukoli jinou aplikaci nebo proces a neměl by rušit normální aktivitu zařízení. Spotřeba prostředků na zařízení, na kterém je spuštěný agent, je spojená s její instalací a konfigurací. Před pokusem o nasazení v produkčním prostředí doporučujeme otestovat konfiguraci vašeho agenta v prostředí s omezením a vzájemnou funkční spolupráci s vašimi aplikacemi a funkcemi aplikace IoT.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>V zařízení se provádí nějaká údržba. Můžu agenta vypnout?

Agenta nelze vypnout.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Existuje způsob, jak otestovat, jestli agent funguje správně?

Pokud agent přestane komunikovat nebo se mu nepovede odeslat zprávy o zabezpečení, dojde k vygenerování upozornění na **tichou** výstrahu zařízení.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak začít s Defenderem pro IoT, najdete v následujících článcích:

- Přečtěte si [Přehled](overview.md) programu Defender for IoT.
- Ověření [systémových požadavků](quickstart-system-prerequisites.md)
- Další informace o tom, jak [začít s Defenderem pro IoT](getting-started.md)
- Vysvětlení [výstrah zabezpečení pro program Defender pro IoT](concept-security-alerts.md)
