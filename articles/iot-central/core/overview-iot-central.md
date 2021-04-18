---
title: Co je Azure IoT Central | Microsoft Docs
description: IoT Central je hostovaná aplikační platforma IoT, která je zabezpečená, škáluje se s vámi, jak vaše podnikání roste, a integruje se s vašimi stávajícími obchodními aplikacemi. Tento článek obsahuje přehled funkcí Azure IoT Central.
author: vishwam
ms.author: vishwams
ms.date: 04/16/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: 1ff8243f9f4d070592ae6dc99e7c33472a6a90de
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600438"
---
# <a name="what-is-azure-iot-central"></a>Co je Azure IoT Central?

IoT Central je hostovaná aplikační platforma IoT, která je zabezpečená, škáluje se s vámi, jak vaše podnikání roste, a integruje se s vašimi stávajícími obchodními aplikacemi. Volba sestavování pomocí IoT Central vám umožní soustředit se na čas, peníze a energii při transformaci vaší firmy na data IoT, nikoli jenom na údržbu a aktualizaci složitých a nepřetržitě vyvíjejících se infrastruktury IoT.

IoT Central vám umožní rychle připojit zařízení, monitorovat požadavky na zařízení, vytvářet pravidla a spravovat miliony zařízení a jejich data v průběhu svého životního cyklu. Kromě toho vám umožňuje reagovat na přehledy zařízení díky rozšíření IoT Intelligence do obchodních aplikací.

## <a name="create-your-iot-central-application"></a>Vytvoření aplikace IoT Central

Můžete rychle vytvořit novou aplikaci IoT Central a pak ji přizpůsobit jedinečným požadavkům. Můžete buď začít se _šablonou obecné aplikace_ , nebo s jednou ze šablon aplikací zaměřených na obor pro [maloobchodní prodej](../retail/overview-iot-central-retail.md), [energii](../energy/overview-iot-central-energy.md), [státní](../government/overview-iot-central-government.md)správu nebo [zdravotní péči](../healthcare/overview-iot-central-healthcare.md).

Návod, jak vytvořit první aplikaci, najdete v rychlém startu pro [Vytvoření nové aplikace](quick-deploy-iot-central.md) .

## <a name="connect-your-devices"></a>Připojení zařízení
Po vytvoření aplikace je prvním krokem připojení vašich zařízení. Úvod k připojení zařízení k aplikaci IoT Central najdete v tématu [Přehled vývoje zařízení](./overview-iot-central-developer.md) .

### <a name="device-templates"></a>Šablony zařízení

Zařízení v IoT Central jsou přidružena k _šabloně zařízení_. Šablona zařízení je jako podrobný plán: definuje charakteristiky a chování vašich zařízení, například:

- Telemetrií, která představuje měření ze senzorů, například teploty nebo vlhkosti.
- Vlastnosti, které reprezentují trvalý stav zařízení. Příklady zahrnují stav pumpy chladicích zařízení nebo cílovou teplotu pro zařízení. Vlastnosti můžete deklarovat jako jen pro čtení nebo zapisovatelné. Pouze zařízení mohou aktualizovat hodnotu vlastnosti jen pro čtení. Operátor může nastavit hodnotu vlastnosti s možností zápisu, která se má odeslat do zařízení.
- Příkazy, které se můžou aktivovat na zařízení, například příkaz pro vzdálené restartování zařízení.
- Vlastnosti cloudu, což jsou metadata zařízení, která se mají ukládat v aplikaci IoT Central, například adresa zákazníka nebo poslední datum služby.

Další informace najdete v článku [Vytvoření šablony zařízení](howto-set-up-template.md) .

### <a name="customize-the-ui"></a>Přizpůsobení uživatelského rozhraní

IoT Central aplikaci můžete přizpůsobit pro operátory, kteří zodpovídají za každodenní použití aplikace, například:

- Nakonfigurovat vlastní řídicí panely, které operátorům pomůžou rychleji zjišťovat informace a řešit problémy


## <a name="manage-your-devices"></a>správu zařízení


U všech řešení IoT navržených pro provoz ve velkém měřítku je důležité strukturovaný přístup ke správě zařízení. Nestačí, abyste svá zařízení připojili ke cloudu, je potřeba zajistit, aby byla vaše zařízení připojená a v pořádku.

Zařízení můžete [Spravovat](howto-manage-devices.md) pomocí aplikace IoT Central, abyste mohli provádět následující úlohy:

- Monitorování zařízení.
- Řešit potíže a opravovat problémy se zařízeními
- Provede hromadnou aktualizaci na zařízeních.

### <a name="dashboards"></a>Řídicí panely

Vestavěné [řídicí panely](./howto-set-up-template.md#generate-default-views) poskytují přizpůsobitelné uživatelské rozhraní pro monitorování stavu a telemetrie zařízení. Začněte s předem vytvořeným řídicím panelem v [šabloně aplikace](howto-use-app-templates.md) nebo vytvořte vlastní řídicí panely přizpůsobené potřebám vašich operátorů. Řídicí panely můžete sdílet se všemi uživateli aplikace nebo je chránit jako soukromé.

### <a name="rules-and-actions"></a>Pravidla a akce

Sestavujte [vlastní pravidla](tutorial-create-telemetry-rules.md) na základě stavu zařízení a telemetrie a Identifikujte zařízení, která potřebují pozornost. Nakonfigurujte akce pro oznamování správných lidí a zajistěte, aby byla včas zajištěna nápravná opatření.

### <a name="jobs"></a>Úlohy

[Úlohy](howto-run-a-job.md) umožňují použít jednotlivé nebo hromadné aktualizace zařízení nastavením vlastností nebo volání příkazů.

### <a name="analytics"></a>Analýzy
[Analýza](howto-create-analytics.md) zpřístupňuje bohatou možnost analýzy historických trendů a koreluje různé telemetrií ze svých zařízení.

## <a name="integrate-with-other-services"></a>Integrace s ostatními službami

Jako aplikační platforma IoT Central umožňuje transformovat data IoT do obchodních přehledů, které mají za úkol reagovat. Příklady, jak můžete integrovat IoT Central s obchodními aplikacemi, najdete v tématu [pravidla](./tutorial-create-telemetry-rules.md), [Export dat](./howto-export-data.md)a [veřejné REST API](/learn/modules/manage-iot-central-apps-with-rest-api/) :

![Jak IoT Central můžou transformovat data IoT](media/overview-iot-central/transform.png)

Můžete vygenerovat obchodní přehledy, jako je určení trendů efektivity počítače nebo předpověď budoucí spotřeby energie v produkčním prostředí, a to vytvořením vlastních analytických kanálů pro zpracování telemetrie z vašich zařízení a uložení výsledků. Nakonfigurujte exporty dat ve vaší aplikaci IoT Central, abyste mohli exportovat data do jiných služeb, kde je můžete analyzovat, ukládat a vizualizovat pomocí preferovaných nástrojů.

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
- Aplikace, které vytvoříte pomocí plánu *Standard* , se účtují podle jednotlivých zařízení. Můžete zvolit Cenový tarif **Standard 0**, **Standard 1** nebo **Standard 2** s prvními dvěma zařízeními, která jsou zdarma. Přečtěte si další informace o [cenách IoT Central](https://aka.ms/iotcentral-pricing).

## <a name="next-steps"></a>Další kroky

Teď, když máte přehled o IoT Central, tady je několik navrhovaných dalších kroků:

- Začněte [vytvořením aplikace Azure IoT Central](quick-deploy-iot-central.md).
- Seznamte se s [uživatelským rozhraním Azure IoT Central](overview-iot-central-tour.md).
- Pokud jste vývojář zařízení a chcete podrobně do nějakého kódu, [vytvořte a připojte klientskou aplikaci k vaší aplikaci Azure IoT Central](./tutorial-connect-device.md).
