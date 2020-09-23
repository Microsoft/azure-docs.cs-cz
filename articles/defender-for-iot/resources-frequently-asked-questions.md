---
title: Defender pro často kladené otázky pro IoT
description: Získejte odpovědi na nejčastější dotazy týkající se Azure Defenderu pro funkce a službu IoT.
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 97dd4e13754175e9c81ae308b86faae811e4d554
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936371"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Nejčastější dotazy k Azure Defenderu pro IoT

Tento článek obsahuje seznam nejčastějších dotazů a odpovědí k programu Defender pro IoT.

## <a name="does-azure-provide-support-for-iot-security"></a>Poskytuje Azure podporu pro zabezpečení IoT?

Azure poskytuje integrované zobrazení pro monitorování a správu zabezpečení IoT v rámci celkového řešení zabezpečení prostřednictvím Azure Security Center. Pokud jste vývojářem aplikací, můžete použít IoT Hub zobrazení ke správě zabezpečení aplikace IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Co je u jedinečného umístění Azure pro zabezpečení IoT k dispozici?

Defender pro IoT umožňuje podnikům rozšiřování stávajícího zobrazení zabezpečení na Internetu do celého řešení IoT. Azure poskytuje ucelený pohled na vaše obchodní řešení, což vám umožní vzít v úvahu akce a rozhodnutí související s podnikáním na základě stav podnikového zabezpečení a shromážděných dat. Kombinované zabezpečení pomocí Azure IoT, Azure IoT Edge a Azure Security Center vám umožní vytvořit požadované řešení s potřebným zabezpečením.

## <a name="who-is-defender-for-iot-made-for"></a>Kdo je program Defender pro IoT vytvořen?

Defender for IoT je integrovaný v rámci služby Azure IoT Hub Security a poskytuje správu pro každodenní operace zabezpečení obchodních řešení. Defender pro IoT je také integrovaný do funkcí Azure Security Center a poskytuje integrované zobrazení pro monitorování a správu zabezpečení IoT v rámci vašeho celkového řešení zabezpečení.

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Jak program Defender pro IoT porovnává soutěž s konkurencí?

Zatímco jiná řešení poskytují sadu funkcí, které zákazníkům umožňují vytvářet vlastní řešení, Defender pro IoT poskytuje jedinečné komplexní řešení zabezpečení IoT, které poskytuje nejrůznější přehled o zabezpečení všech souvisejících prostředků Azure. Azure umožňuje rychlé nasazení a úplnou integraci s IoT Hubmi moduly, které umožňují snadnou integraci s existujícími nástroji pro správu zařízení.

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>Musím být Azure Security Center zákazníkem, abyste mohli používat tuto službu?

Ne, ale doporučuje se. Bez Azure Security Center Defender pro IoT přijímá jenom data připojených prostředků a poskytuje omezené analýzy potenciálního prostoru pro útoky, hrozeb a potenciálních útoků.

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Musím být zákazníkem Azure IoT?

Ano. Defender pro IoT spoléhá na konektivitu a infrastrukturu Azure IoT.

## <a name="do-i-have-to-install-an-agent"></a>Musím instalovat agenta?

Instalace agenta na zařízeních IoT není povinná, aby bylo možné povolit Defender pro IoT. Můžete si vybrat z následujících tří možností a získat na základě vašeho výběru různé úrovně monitorování zabezpečení a možnosti správy:

1. Nainstalujte agenta zabezpečení Defender pro IoT s úpravami nebo beze změn. Tato možnost poskytuje nejvyšší úroveň rozšířených přehledů zabezpečení pro chování zařízení a přístup.

1. Vytvořte vlastního agenta a implementujte schéma zprávy zabezpečení programu Defender for IoT. Tato možnost povolí použití programu Defender pro analytické nástroje pro IoT nad vaším agentem zabezpečení zařízení.

1. Žádná instalace agenta zabezpečení na vaše zařízení IoT. Tato možnost umožňuje IoT Hub monitorování komunikace se sníženými možnostmi pro monitorování a správu zabezpečení.

## <a name="what-does-the-defender-for-iot-agent-do"></a>K čemu se používá Defender pro IoT Agent?

Defender pro službu IoT agent poskytuje pro konfiguraci zařízení, chování a přístup (pomocí kontroly konfigurace) pokrytí hrozeb na úrovni zařízení, proces & připojení. Agent zabezpečení programu Defender for IoT nekontroluje data ani aktivity související s obchodem.

## <a name="where-can-i-get-the-defender-for-iot-security-agent"></a>Kde můžu získat agenta zabezpečení v programu Defender for IoT?

Agent zabezpečení pro IoT for IoT je open source a dostupný na GitHubu v 32 bitových a 64 verzích Windows a Linux: https://github.com/Azure/Azure-IoT-Security .

## <a name="where-does-the-defender-for-iot-agent-get-installed"></a>Kde se agent Defender pro IoT nainstaluje?

Podrobné informace o instalaci a nasazení agenta najdete na webu GitHub: https://github.com/Azure/Azure-IoT-Security .

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Jaké jsou závislosti a požadavky agenta?

Defender pro IoT podporuje širokou škálu platforem. Podívejte se na [podporované platformy zařízení](how-to-deploy-agent.md) a ověřte podporu pro konkrétní zařízení.

## <a name="which-data-is-collected-by-the-agent"></a>Která data shromažďuje Agent?

Agentem se shromažďují možnosti připojení, přístup, konfigurace brány firewall, seznam procesů & standardních hodnot operačního systému.

## <a name="how-much-data-will-the-agent-generate"></a>Kolik dat bude agent generovat?

Generování dat agenta je založené na zařízení, aplikaci, typu připojení a konfiguraci zákaznického agenta. Vzhledem k vysoké variabilitě mezi zařízeními a řešeními IoT doporučujeme, abyste agenta nasadili v testovacím prostředí nebo nastavení testu, abyste mohli sledovat, učit a nastavovat konkrétní konfiguraci, která vyhovuje vašim potřebám, a současně měřit množství vygenerovaných dat. Po spuštění služby poskytuje Defender pro IoT agent provozní doporučení pro optimalizaci propustnosti agenta, které vám pomůžou s procesem konfigurace a přizpůsobení.

## <a name="how-can-i-control-my-billing"></a>Jak můžu řídit svou fakturaci?

Defender pro IoT poskytuje konfigurovatelné kontroly agenta, vyrovnávací paměti dat a možnost vytvářet vlastní výstrahy, které zvyšují nebo snižují množství dat generovaných agentem.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Využívají zprávy agenta kvótu od IoT Hub?

Ano. Data přenášená agentem se počítají ve vaší kvótě IoT Hub.

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Co dál? Nainstaloval (a) jsem agenta a nevidí žádné aktivity ani protokoly...

1. Ověřte, jestli [typ agenta odpovídá určené platformě operačního systému vašeho zařízení](how-to-deploy-agent.md) .

1. Potvrďte, že [je agent na zařízení spuštěný](how-to-agent-configuration.md).

1. Ověřte, že se [služba úspěšně aktivovala](quickstart-onboard-iot-hub.md) pro **zabezpečení** v IoT Hub.

1. Ověřte, že je zařízení [nakonfigurované v IoT Hub s modulem Defender for IoT](quickstart-create-security-twin.md).

Pokud jsou aktivity nebo protokoly stále nedostupné, požádejte o další nápovědu svého Defenderu pro IoT partner.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Co se stane, když připojení k Internetu přestane fungovat?

Agent pokračuje v běhu a ukládá data, pokud je zařízení spuštěné. Data se ukládají do mezipaměti zpráv zabezpečení podle konfigurace velikosti. Když zařízení znovu získá připojení, zprávy o zabezpečení budou pokračovat v posílání.

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Pokud se zařízení restartuje, bude se Agent zabezpečení automaticky obnovovat?

Agent zabezpečení je navržen pro automatické opětovné spuštění při každém restartování zařízení.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Může agent ovlivnit výkon zařízení nebo jiného nainstalovaného softwaru?

Agent spotřebovává prostředky počítače jako jakoukoli jinou aplikaci nebo proces a neměl by rušit normální aktivitu zařízení. Spotřeba prostředků na zařízení, na kterém je spuštěný agent, je spojená s její instalací a konfigurací. Před pokusem o nasazení v produkčním prostředí doporučujeme otestovat konfiguraci vašeho agenta v prostředí s omezením a vzájemnou funkční spolupráci s vašimi aplikacemi a funkcemi aplikace IoT.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>V zařízení se provádí nějaká údržba. Můžu agenta vypnout?

Agenta nelze vypnout.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Existuje způsob, jak otestovat, jestli agent funguje správně?

Pokud agent přestane komunikovat nebo se mu nepovede odeslat zprávy o zabezpečení, dojde k vygenerování upozornění na **tichou** výstrahu zařízení.

## <a name="can-i-create-my-own-alerts"></a>Můžu vytvořit vlastní výstrahy?

Ano. Přizpůsobenou výstrahu můžete nastavit na předem stanovenou sadu chování, jako je například IP adresa a otevřené porty. Další informace o vlastních výstrahách a o tom, jak je udělat, najdete v tématu [Vytvoření vlastních výstrah](quickstart-create-custom-alerts.md) .

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Kde můžu zobrazit protokoly? Můžu přizpůsobovat protokoly?

- Pomocí vašeho připojeného pracovního prostoru Log Analytics zobrazovat výstrahy a doporučení. Nakonfigurujte velikost úložiště a dobu trvání v pracovním prostoru.

- Nezpracovaná data z vašeho agenta zabezpečení taky můžete ukládat do svého účtu Log Analytics. Před změnou konfigurace této možnosti zvažte možnost změnit velikost, dobu trvání, požadavky na úložiště a související náklady.

## <a name="why-should-i-add-defender-for-iot-to-the-module-identity-what-is-it-used-for"></a>Proč je vhodné přidat do identity modulu Defender pro IoT? K čemu slouží?

Modul Defender for IoT se používá pro správu a konfiguraci agenta.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak začít s Defenderem pro IoT, najdete v následujících článcích:

- Přečtěte si [Přehled](overview.md) programu Defender for IoT.
- Ověření [požadovaných součástí služby](service-prerequisites.md)
- Další informace o [tom, jak začít](getting-started.md)
- Vysvětlení [výstrah zabezpečení pro program Defender pro IoT](concept-security-alerts.md)
