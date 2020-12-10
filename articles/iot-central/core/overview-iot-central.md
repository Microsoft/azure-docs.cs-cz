---
title: Co je Azure IoT Central | Microsoft Docs
description: Azure IoT Central je aplikační platforma IoT, která zjednodušuje vytváření řešení IoT a pomáhá snižovat zatížení a náklady na operace správy IoT a vývoj. Tento článek obsahuje přehled funkcí Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: b5f4b6d42d67b69bd8b6be1ada6f5651d0cb8675
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029834"
---
# <a name="what-is-azure-iot-central"></a>Co je Azure IoT Central?

IoT Central je platforma aplikací IoT, která snižuje nároky a náklady na vývoj, správu a údržbu řešení IoT na podnikové úrovni. Volba sestavování pomocí IoT Central vám umožní soustředit se na čas, peníze a energii při transformaci vaší firmy na data IoT, nikoli jenom na údržbu a aktualizaci složitých a nepřetržitě vyvíjejících se infrastruktury IoT.

Webové uživatelské rozhraní umožňuje monitorovat podmínky zařízení, vytvářet pravidla a spravovat miliony zařízení a jejich data v průběhu svého životního cyklu. Kromě toho vám umožňuje reagovat na přehledy zařízení díky rozšíření IoT Intelligence do obchodních aplikací.

Tento článek popisuje, jak IoT Central:

- Typické osoby přidružené k projektu
- Postup vytvoření aplikace
- Postup připojení zařízení do aplikace
- Postup správy aplikace
- Možnosti Azure IoT Edge v IoT Central.
- Jak připojit zařízení s Azure IoT Edge modulem runtime do vaší aplikace

## <a name="personas"></a>Osoby

Dokumentace IoT Central odkazuje na čtyři osobyy, které komunikují s aplikací IoT Central:

- _Tvůrce řešení_ zodpovídá za [Vytvoření aplikace](quick-deploy-iot-central.md), [konfiguraci pravidel a akcí](quick-configure-rules.md), [Definování integrace s jinými službami](howto-export-data.md)a další přizpůsobení aplikace pro operátory a vývojáře zařízení.
- _Operátor_ [spravuje zařízení](howto-manage-devices.md) připojená k aplikaci.
- _Správce_ zodpovídá za úlohy správy, jako je například Správa [rolí uživatelů a oprávnění](howto-administer.md) v rámci aplikace.
- _Vývojář zařízení_ [vytvoří kód, který běží na zařízení](concepts-telemetry-properties-commands.md) nebo [IoT Edge modul](concepts-iot-edge.md) připojený k vaší aplikaci.

## <a name="create-your-iot-central-application"></a>Vytvoření aplikace IoT Central

Jako tvůrce řešení můžete pomocí IoT Central vytvořit vlastní řešení IoT hostované v cloudu pro vaši organizaci. Vlastní řešení IoT se obvykle skládá z těchto součástí:

- Cloudová aplikace, která přijímá ze zařízení telemetrická data a umožňuje vám tato zařízení spravovat
- Více zařízení připojených k vaší cloudové aplikaci, na kterých běží vlastní kód

Můžete rychle nasadit novou aplikaci IoT Central a pak ji přizpůsobit konkrétním požadavkům v prohlížeči. Můžete začít se _šablonou obecné aplikace_ nebo s jednou ze šablon aplikací zaměřených na odvětví pro [maloobchodní prodej](../retail/overview-iot-central-retail.md), [energii](../energy/overview-iot-central-energy.md), [státní](../government/overview-iot-central-government.md)správu nebo [zdravotní péči](../healthcare/overview-iot-central-healthcare.md).

Jako tvůrce řešení můžete pomocí webových nástrojů vytvořit _šablonu zařízení_ pro zařízení, která se připojují k vaší aplikaci. Šablona zařízení je plán, který definuje charakteristiky a chování typu zařízení, například:

- Telemetrii, kterou posílá. Mezi příklady patří teplota a vlhkost. Telemetrie jsou streamovaná data.
- Obchodní vlastnosti, které můžete upravit operátor Mezi příklady patří adresa zákazníka a datum poslední služby.
- Vlastnosti zařízení nastavené zařízením, které jsou v aplikaci jen pro čtení Například stav ventilu buď otevřený, nebo vypnutý.
- Vlastnosti, které jsou nastaveny jako operátor, které určují chování zařízení. Například cílovou teplotu pro zařízení.
- Příkazy, které může operátor volat, které se spouštějí na zařízení. Například příkaz pro vzdálené restartování zařízení.

Tato [Šablona zařízení](howto-set-up-template.md) zahrnuje:

- _Model zařízení_ , který popisuje možnosti, které zařízení má implementovat. Mezi možnosti zařízení patří:

  - Telemetrie IT, která se má IoT Central.
  - Vlastnosti jen pro čtení, které používá k hlášení stavu do IoT Central.
  - Vlastnosti s možností zápisu, které obdrží z IoT Central pro nastavení stavu zařízení.
  - Příkazy volané z IoT Central.

- Vlastnosti cloudu, které nejsou uložené na zařízení.
- Vlastní nastavení, řídicí panely a formuláře, které jsou součástí vaší aplikace IoT Central.

### <a name="create-device-templates"></a>Vytvoření šablon zařízení

Jako tvůrce řešení máte k dispozici několik možností pro vytváření šablon zařízení:

- Navrhněte šablonu zařízení v IoT Central a potom v kódu zařízení implementujte svůj model zařízení.
- Vytvořte model zařízení pomocí nástroje Visual Studio Code a publikujte model do úložiště. Implementujte kód zařízení z modelu a připojte své zařízení k aplikaci IoT Central. IoT Central najde model zařízení z úložiště a vytvoří pro vás jednoduchou šablonu zařízení.
- Vytvořte model zařízení pomocí Visual Studio Code. Implementujte kód zařízení z modelu. Ručně importujte model zařízení do aplikace IoT Central a pak přidejte jakékoli vlastnosti cloudu, vlastní nastavení a řídicí panely, které aplikace IoT Central potřebuje.

Jako tvůrce řešení můžete použít IoT Central k vygenerování kódu pro testovací zařízení pro ověření šablon zařízení.

Pokud jste vývojář zařízení, přečtěte si téma [Přehled vývoje IoT Central zařízení](./overview-iot-central-developer.md) , kde najdete Úvod k implementaci zařízení, která používají tyto šablony zařízení.

### <a name="customize-the-ui"></a>Přizpůsobení uživatelského rozhraní

Jako tvůrce řešení můžete také přizpůsobit uživatelské rozhraní aplikace IoT Central pro operátory, kteří zodpovídají za každodenní použití aplikace. Vlastní nastavení, které může tvůrce řešení provést, zahrnují:

- Definovat rozložení vlastností a nastavení v šabloně zařízení
- Nakonfigurovat vlastní řídicí panely, které operátorům pomůžou rychleji zjišťovat informace a řešit problémy
- Nakonfigurovat vlastní analýzy pro zkoumání časových řad dat z připojených zařízení

## <a name="manage-your-devices"></a>správu zařízení

Jako operátor můžete použít aplikaci IoT Central ke [správě zařízení](howto-manage-devices.md) v řešení IoT Central. Obsluha provede úkoly, jako například:

- Monitorovat zařízení připojená k aplikaci
- Řešit potíže a opravovat problémy se zařízeními
- Zřizovat nová zařízení

Jako tvůrce řešení můžete [definovat vlastní pravidla a akce](howto-configure-rules.md) , které provozují streamování dat z připojených zařízení. Operátor může tato pravidla povolit nebo zakázat na úrovni zařízení za účelem řízení a automatizace úloh v rámci aplikace.

U všech řešení IoT navržených pro provoz ve velkém měřítku je důležité strukturovaný přístup ke správě zařízení. Nestačí, abyste svá zařízení připojili ke cloudu, je potřeba zajistit, aby byla vaše zařízení připojená a v pořádku. Operátor může pomocí následujících možností IoT Central spravovat zařízení během životního cyklu aplikace:

### <a name="dashboards"></a>Řídicí panely

Vestavěné [řídicí panely](./howto-set-up-template.md#generate-default-views) poskytují přizpůsobitelné uživatelské rozhraní pro monitorování stavu a telemetrie zařízení. Začněte s předem vytvořeným řídicím panelem v [šabloně aplikace](howto-use-app-templates.md) nebo vytvořte vlastní řídicí panely přizpůsobené potřebám vašich operátorů. Řídicí panely můžete sdílet se všemi uživateli aplikace nebo je chránit jako soukromé.

### <a name="rules-and-actions"></a>Pravidla a akce

Sestavujte [vlastní pravidla](tutorial-create-telemetry-rules.md) na základě stavu zařízení a telemetrie a Identifikujte zařízení, která potřebují pozornost. Nakonfigurujte akce pro oznamování správných lidí a zajistěte, aby byla včas zajištěna nápravná opatření.

### <a name="jobs"></a>Úlohy

[Úlohy](howto-run-a-job.md) umožňují použít jednotlivé nebo hromadné aktualizace zařízení nastavením vlastností nebo volání příkazů.

## <a name="integrate-with-other-services"></a>Integrace s ostatními službami

Jako aplikační platforma IoT Central umožňuje transformovat data IoT do obchodních přehledů, které mají za úkol reagovat. Příklady, jak můžete integrovat IoT Central s obchodními aplikacemi, najdete v tématu [pravidla](./tutorial-create-telemetry-rules.md), [Export dat](./howto-export-data.md)a [veřejné REST API](/learn/modules/manage-iot-central-apps-with-rest-api/) :

![Jak IoT Central můžou transformovat data IoT](media/overview-iot-central/transform.png)

Můžete vygenerovat obchodní přehledy, jako je určení trendů efektivity počítače nebo předpověď budoucí spotřeby energie v produkčním prostředí, a to vytvořením vlastních analytických kanálů pro zpracování telemetrie z vašich zařízení a uložení výsledků. Nakonfigurujte exporty dat ve vaší aplikaci IoT Central, abyste mohli exportovat telemetrie, změny vlastností zařízení a změny šablon zařízení do jiných služeb, kde můžete analyzovat, ukládat a vizualizovat data pomocí preferovaných nástrojů.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Vytváření vlastních řešení IoT a integrace s rozhraními REST API

Sestavujte řešení IoT, jako je:

- Mobilní doprovodné aplikace, které můžou vzdáleně nastavovat a řídit zařízení.
- Vlastní integrace, které umožňují stávajícím podnikovým aplikacím pracovat s vašimi zařízeními a daty IoT.
- Aplikace pro správu zařízení pro modelování zařízení, připojování, správu a přístup k datům.

## <a name="administer-your-application"></a>Správa vaší aplikace

Aplikace IoT Central plně hostují Microsoft, což snižuje nároky na správu při správě vašich aplikací. Správci spravují přístup k vaší aplikaci pomocí [rolí a oprávnění uživatele](howto-administer.md).

## <a name="pricing"></a>Ceny

IoT Central aplikaci můžete vytvořit pomocí bezplatné zkušební verze 7 dní nebo použít Cenový tarif Standard.

- Aplikace vytvořené pomocí *bezplatného* plánu jsou po dobu sedmi dnů zdarma a podporují až pět zařízení. Můžete je kdykoli převést na používání standardního cenového plánu, než vyprší jejich platnost.
- Aplikace, které vytvoříte pomocí plánu *Standard* , se účtují podle jednotlivých zařízení. v prvním ze dvou zařízení, která jsou zdarma, můžete zvolit Cenový tarif **Standard 0**, **Standard 1** nebo **Standard 2** . Přečtěte si další informace o [cenách IoT Central](https://aka.ms/iotcentral-pricing).

## <a name="quotas"></a>Kvóty

Každé předplatné Azure má výchozí kvóty, které by mohly mít vliv na rozsah vašeho řešení IoT. V současné době IoT Central omezuje počet aplikací, které můžete nasadit v rámci předplatného na 10. Pokud potřebujete tento limit zvýšit, obraťte se na [podporu Microsoftu](https://azure.microsoft.com/support/options/).

## <a name="known-issues"></a>Známé problémy

- Průběžný export dat nepodporuje formát Avro (nekompatibilní).
- Pro injson není aktuálně podporováno.
- Dlaždice mapy není momentálně podporovaná.
- Typy schématu pole nejsou podporovány.
- Podporovaná je jenom sada SDK pro zařízení C a sady SDK pro Node.js zařízení a služeb.
- IoT Central je v současné době k dispozici v místech USA, Evropa, Asie a Tichomoří, Austrálie, Spojené království a Japonsko.

## <a name="next-steps"></a>Další kroky

Teď, když máte přehled o IoT Central, tady je několik navrhovaných dalších kroků:

- Seznamte se s dostupnými [technologiemi a službami Azure pro vytváření řešení IoT](../../iot-fundamentals/iot-services-and-technologies.md).
- Pokud jste vývojářem zařízení a chcete podrobně do nějakého kódu, je navržený další krok [Vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central](./tutorial-connect-device.md).
- Seznamte se s [uživatelským rozhraním Azure IoT Central](overview-iot-central-tour.md).
- Začněte [vytvořením aplikace Azure IoT Central](quick-deploy-iot-central.md).
- Naučte se [připojit zařízení Azure IoT Edge](./tutorial-add-edge-as-leaf-device.md).
- Přečtěte si další informace o [technologiích a službách Azure IoT](../../iot-fundamentals/iot-services-and-technologies.md).