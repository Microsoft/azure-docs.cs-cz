---
title: Nejčastější dotazy k ASC pro náhled IoT | Dokumentace Microsoftu
description: Najdete odpovědi na nejčastější dotazy ohledně ASC funkce IoT a služby.
services: ASCforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.service: ASCforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 1eba32683883e60ae48f4d6fec9c434c27ce55da
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541929"
---
# <a name="asc-for-iot-frequently-asked-questions"></a>Nejčastější dotazy k ASC pro IoT  

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek obsahuje seznam nejčastějších dotazů a odpovědí týkajících ASC pro IoT. 

## <a name="does-azure-provide-support-for-iot-security"></a>Azure poskytuje podporu pro zabezpečení IoT?

Azure poskytuje integrované zobrazení pro monitorování a správu vašich IoT zabezpečení jako součást celkového řešení zabezpečení pomocí Azure Security Center. Pokud jste vývojář aplikací, můžete ke správě zabezpečení aplikace IoT zobrazení služby IoT Hub.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Co je Azure jedinečnou hodnotu návrh pro zabezpečení IoT?

ASC pro IoT umožňuje podnikům rozšířit svoje stávající zobrazení kybernetických zabezpečení a jejich kompletní řešení IoT. Azure poskytuje komplexní přehled obchodních řešení, můžete provést akce související s obchodní a rozhodnutí, která podle stavu zabezpečení organizace a shromažďují data. Kombinované zabezpečení pomocí Azure IoT, Azure IoT Edge, Azure Sphere, centrální Azure a Azure Security Center umožňují vytvářet řešení, se kterým chcete, aby se zabezpečením, které potřebujete.

## <a name="who-is-asc-for-iot-made-for"></a>Kdo je ASC stanovit IoT? 

ASC pro IoT je integrována v rámci Azure IoT Hub zabezpečení a poskytuje správu řešení business každodenních operací zabezpečení. ASC pro IoT je taky integrovaná do funkce služby Azure Security Center a poskytují ucelený pro monitorování a správu zabezpečení IoT jako součást celkového řešení zabezpečení.

## <a name="how-does-asc-for-iot-compare-to-the-competition"></a>Jak ASC pro IoT porovnání soutěže?

Jiná řešení nabízejí sadu funkcí, které umožňují zákazníkům vytvářet své vlastní řešení, ASC pro IoT poskytuje jedinečné řešení zabezpečení IoT začátku do konce, které poskytuje širokém zobrazení v rámci zabezpečení všech souvisejících prostředků Azure. Azure umožňuje rychlé nasazení a plnou integraci s dvojčaty modulů IoT Hub pro snadnou integraci se stávajícími nástroji pro správu zařízení.

## <a name="do-i-have-to-be-an-azure-security-center-asc-customer"></a>Je nutné mít zákazník Azure Security Center (ASC)?

Ne, ale doporučujeme ho. ASC pro IoT bez ASC, obdrží omezené připojených zdrojů dat a poskytuje omezenou analýzu potenciální prostor pro napadení, hrozby a potenciální útoky. 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Je nutné mít zákazník Azure IoT?

Ano. ASC pro IoT spoléhá na připojení k Azure IoT a infrastruktury.

## <a name="do-i-have-to-install-an-agent"></a>Je nutné instalovat agenta?

Instalace agenta na zařízeních IoT není povinné, pokud chcete povolit Microsoft ASC pro IoT. Můžete si vybrat mezi následující tři možnosti získání různé úrovně zabezpečení funkce správy a monitorování podle vašeho výběru:

1. Nainstalujte ASC pro agenta zabezpečení IoT s nebo bez úprav. Tato možnost poskytuje nejvyšší úroveň informace o chování zařízení a přístup k rozšířené zabezpečení.

2. Vytvoření vlastního agenta a implementaci Microsoft ASC pro schéma zprávu zabezpečení IoT. Tato možnost umožňuje využití Microsoft ASC pro IoT analytických nástrojů nad svým zástupcem zabezpečení zařízení.

3. Bez instalace agenta zabezpečení na zařízeních IoT. Tato možnost povolí, IoT Hub komunikace monitorování pomocí funkce monitorování a správa nižší zabezpečení. 

## <a name="what-does-the-asc-for-iot-agent-do"></a>Co dělá ASC pro agenta IoT?

ASC pro agenta IoT poskytuje pokryta všechna zařízení úrovně hrozby pro konfiguraci zařízení, chování a přístup (tím, že kontroluje konfigurace), proces a připojení. ASC pro agenta zabezpečení IoT nekontroluje obchodní data nebo aktivity.

## <a name="where-i-can-get-the-asc-for-iot-security-agent"></a>Kde můžu získat ASC pro agenta zabezpečení IoT?

ASC pro agenta zabezpečení IoT je open source a je k dispozici na Githubu v 32bitové a 64bitové verze Windows a Linux: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="where-does-the-asc-for-iot-agent-get-installed"></a>Kde se budou instalovány ASC pro agenta IoT? 

Podrobné informace o instalaci a agenta nasazení najdete v Githubu: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Co jsou závislosti a požadavky agenta?

ASC pro IoT podporuje celou řadu platforem. Zobrazit [platformy zařízení podporovány](select-deploy-agent.md) ověřit podporu pro vaše konkrétní zařízení. 

## <a name="which-data-is-collected-by-the-agent"></a>Jaká data se shromažďují pomocí agenta?

Připojení, access, konfigurace brány firewall, seznam procesů a směrným plánem operačního systému se shromažďují pomocí agenta.

## <a name="how-much-data-will-the-agent-generate"></a>Kolik dat bude generovat agenta?

Agent generování dat vychází zařízení, aplikace, typu připojení a konfigurace agenta zákazníka. Z důvodu velice proměnlivé mezi zařízeními a řešení IoT doporučujeme prvního nasazení agenta do testovacího prostředí nebo nastavení sledovat, přečtěte si a konkrétní konfigurace, která nejlépe vyhovuje vašim potřebám, při měření objem dat generovaný sady testu. Po spuštění služby ASC pro agenta IoT poskytuje provozní doporučení pro optimalizaci propustnosti agenta vám pomůže při procesu konfigurace a vlastního nastavení.

## <a name="how-can-i-control-my-billing"></a>Jak lze řídit Moje vyúčtování?

ASC pro IoT poskytuje konfigurovatelné agenta kontrol, vyrovnávací paměti dat a možnost vytvářet vlastní výstrahy, které zvýšit nebo snížit množství dat vytvářené agentem.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Nepoužívejte agenta zprávy do kvóty ze služby IoT Hub?

Ano. Agent přenášených dat se počítá v rámci svojí kvóty služby IoT Hub. 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Co dál? Můžu nainstalujete agenta a nezobrazují žádné aktivity nebo protokoly

1. Zkontrolujte, [typ agenta odpovídá určené platforma operačního systému vašeho zařízení](select-deploy-agent.md)

1. Potvrďte [agent běží na zařízení](concept-agent-configuration.md).

2. Zkontrolujte [služby se úspěšně povolila](quickstart-onboard-iot-hub.md) k **zabezpečení** ve službě IoT Hub. 

3. Zkontrolujte, jestli je zařízení [nakonfigurované ve službě IoT Hub s ASC pro modul IoT](quickstart-create-security-twin.md).  

Pokud aktivity nebo protokoly jsou stále k dispozici, obraťte se na vaše ASC IoT partnera pro další pomoc.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Co se stane, když přestane fungovat připojení k Internetu?

Agent nadále provozuje a ukládat data tak dlouho, dokud je na zařízení spuštěný. Data uložená v mezipaměti zabezpečení zprávy podle konfigurace velikosti. Když zařízení znovu získal připojení, zprávy o zabezpečení pokračovat v odesílání. 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Pokud se zařízení nerestartuje, bude agent zabezpečení samoobslužné obnovení?

Zabezpečení agenta je navržen pro znovu spustit automaticky s každou restartování zařízení.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Agent může ovlivnit výkon zařízení nebo jiný software nainstalovaný?

Agent jako jakékoli jiné aplikace nebo proces využívá prostředky počítače a nesmí přerušit aktivit normální zařízení. Spotřeba prostředků na zařízení, který je spuštěn agent je párována s jeho nastavení a konfigurace. Doporučujeme otestovat konfiguraci agenta v prostředí s omezením, spolu s vzájemná funkční spolupráce s vaší aplikace IoT a funkce, která je před pokusem o nasazení v produkčním prostředí.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Některé údržbu zadávám na zařízení. Můžete vypnout agenta?

Agenta nelze vypnout.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Existuje způsob, který testuje, jestli správně funguje agent? 

Pokud agent přestane komunikovat nebo selhání k odeslání zprávy o zabezpečení, **zařízení mlčí** , je vygenerována výstraha.

## <a name="can-i-create-my-own-alerts"></a>Můžete vytvořit vlastní oznámení?

Ano. Vlastní upozornění můžete nastavit na předem určenou sadu chování, jako je například IP adres a portů. Zobrazit [vytvářet vlastní výstrahy](quickstart-create-custom-alerts.md) získat další informace o vlastních výstrah a jak je. 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Kde vidí protokolů? Můžete přizpůsobit protokoly?

- Zobrazení výstrah a doporučení pomocí připojený pracovní prostor Log Analytics. Nakonfigurujte velikost úložiště a doba trvání v pracovním prostoru.

- Nezpracovaná data z vašeho agenta zabezpečení mohou být také uloženy ve vašem účtu Log Analytics. Před změnou konfigurace této možnosti zvažte velikost, doba trvání, požadavky na úložiště a související náklady. 

## <a name="why-should-i-add-asc-for-iot-to-the-module-identity-what-is-it-used-for"></a>Proč je třeba přidat ASC pro IoT na identitu modulu? Co se používá pro?

ASC pro IoT modul se používá pro správu a konfiguraci agenta.


## <a name="next-steps"></a>Další postup

Další informace o tom, jak začít pracovat s ASC pro IoT, najdete v následujících článcích:


- Přečtěte si ASC pro IoT [– přehled](overview.md)
- Ověřte, [služby požadavky](service-prerequisites.md)
- Další informace o tom, jak [Začínáme](getting-started.md)
- Vysvětlení [ASC pro výstrahy zabezpečení IoT](concept-security-alerts.md)

