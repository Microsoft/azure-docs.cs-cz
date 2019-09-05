---
title: Co je Azure IoT Central | Microsoft Docs
description: Azure IoT Central je ucelené řešení SaaS, které můžete použít k vytvoření a správě vlastního řešení IoT. Tento článek obsahuje přehled funkcí Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: e1c206c329b491ec9f8d36a12e2b4bf8ed8107b3
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383017"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Co je Azure IoT Central (funkce verze Preview)?

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Možnosti [IoT technologie Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) v Azure IoT Central jsou momentálně ve verzi Public Preview. Nepoužívejte IoT Central aplikaci technologie Plug and Play IoT povolenou pro produkční úlohy. V produkčních prostředích se používá aplikace IoT Central vytvořená z aktuální, všeobecně dostupné šablony aplikace.

Azure IoT Central je plně spravované řešení IoT typu software jako služba, které umožňuje snadno vytvářet produkty, které spojují fyzické a digitální světů. Svoji vizi propojených produktů můžete přivést k životu následujícími kroky:

- Získávání nových informací z připojených zařízení, které vám pomohou poskytovat zákazníkům lepší produkty a prostředí
- Vytváření nových obchodních příležitostí pro vaši organizaci

Azure IoT Central v porovnání s typickým projektem IoT:

- Snižuje režijní náklady na správu.
- Snižuje provozní náklady a režijní náklady.
- Aplikace usnadňuje přizpůsobení aplikace při práci s těmito nástroji:
  - Špičkové technologie jako [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) a [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)
  - Funkce zabezpečení na podnikové úrovni, třeba komplexní šifrování

Následující video obsahuje přehled Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

Tento článek popisuje, jak IoT Central Azure:

- Typické osoby přidružené k projektu
- Postup vytvoření aplikace
- Postup připojení zařízení do aplikace
- Postup správy aplikace

## <a name="known-issues"></a>Známé problémy

> [!Note]
> Tyto známé problémy se vztahují pouze na aplikaci IoT Central Preview.

- Pravidla nepodporují všechny akce (jenom e-mail).
- Pro komplexní typy nejsou podporovaná pravidla, analýza a skupiny zařízení.
- Průběžný export dat nepodporuje formát Avro (nekompatibilní).
- Simulovaná zařízení nepodporují všechny komplexní typy.
- Pro injson není aktuálně podporováno.
- Dlaždice mapy není momentálně podporovaná.
- Úlohy nepodporují komplexní typy.
- Typy schématu pole nejsou podporovány.
- Export šablony aplikace a kopie aplikace nejsou podporovány.
- Podporovaná je jenom sada SDK zařízení C a sady SDK zařízení a služeb Node. js.
- Je dostupná jenom v oblastech Severní Evropa a střední USA.

## <a name="personas"></a>Osoby

Dokumentace ke službě Azure IoT Central odkazuje na čtyři osoby, kteří komunikují s aplikací Azure IoT Central:

- _Tvůrce_ zodpovídá za definování typů zařízení, která se připojují k aplikaci, a přizpůsobuje aplikaci pro operátora.
- _Operátor_ spravuje zařízení připojená k aplikaci.
- _Správce_ zodpovídá v aplikaci za úlohy správy, jako je třeba správa uživatelů a rolí.
- _Vývojář zařízení_ vytváří kód, který pak běží na zařízení připojeném k aplikaci.

## <a name="create-your-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT Central

Jako tvůrce vytvoříte pomocí Azure IoT Central pro svoji organizaci vlastní řešení IoT hostované v cloudu. Vlastní řešení IoT se obvykle skládá z těchto součástí:

- Cloudová aplikace, která přijímá ze zařízení telemetrická data a umožňuje vám tato zařízení spravovat
- Více zařízení připojených k vaší cloudové aplikaci, na kterých běží vlastní kód

Můžete rychle nasadit novou aplikaci IoT Central Azure a pak ji přizpůsobit konkrétním požadavkům v prohlížeči. Jako tvůrce použijete webové nástroje k vytvoření _šablony_ pro zařízení, která se připojují k vaší aplikaci. Šablona zařízení je plán, který definuje charakteristiky a chování typu zařízení, například:

- Telemetrii, kterou posílá.
- Obchodní vlastnosti, které můžete upravit operátor
- Vlastnosti zařízení nastavené zařízením, které jsou v aplikaci jen pro čtení
- Vlastnosti nastavené operátorem, který určuje chování zařízení.

Tato šablona zařízení zahrnuje:

- _Model schopností zařízení_ , který popisuje možnosti, které by zařízení mělo implementovat, jako je například telemetrie, kterou posílá, a vlastnosti, které vystavuje.
- Vlastnosti cloudu, které nejsou uloženy v zařízení.
- Vlastní nastavení, řídicí panely a formuláře, které jsou součástí vaší aplikace IoT Central.

### <a name="create-device-templates"></a>Vytvoření šablon zařízení

[IoT technologie Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) umožňuje IoT Central integrovat zařízení bez psaní jakéhokoli vloženého kódu zařízení. V jádru IoT technologie Plug and Play je schéma modelu schopností zařízení, které popisuje možnosti zařízení. V aplikaci IoT Central Preview používají šablony zařízení tyto modely schopností zařízení technologie Plug and Play IoT.

Jako tvůrce máte k dispozici několik možností pro vytváření šablon zařízení:

- Navrhněte šablonu zařízení v IoT Central a potom implementujte svůj model schopností zařízení do kódu zařízení.
- Naimportujte model schopností zařízení z [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat) a pak přidejte jakékoli vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše IoT Central aplikace potřebuje.
- Vytvořte model schopností zařízení pomocí Visual Studio Code. Implementujte kód zařízení z modelu a připojte své zařízení k aplikaci IoT Central. IoT Central najde model schopností zařízení z úložiště a vytvoří pro vás jednoduchou šablonu zařízení.
- Vytvořte model schopností zařízení pomocí Visual Studio Code. Implementujte kód zařízení z modelu. Model schopností zařízení naimportujte ručně do aplikace IoT Central a pak přidejte jakékoli vlastnosti cloudu, přizpůsobení a řídicí panely, které aplikace IoT Central potřebuje.

Jako tvůrce můžete použít IoT Central k vygenerování kódu pro testovací zařízení k ověření vašich šablon zařízení.

### <a name="customize-the-ui"></a>Přizpůsobení uživatelského rozhraní

Jako tvůrce můžete taky přizpůsobit uživatelské rozhraní aplikace Azure IoT Central pro operátory, kteří zodpovídají za každodenní použití aplikace. Tvůrce může provést třeba následující přizpůsobení:

- Definovat rozložení vlastností a nastavení v šabloně zařízení
- Nakonfigurovat vlastní řídicí panely, které operátorům pomůžou rychleji zjišťovat informace a řešit problémy
- Nakonfigurovat vlastní analýzy pro zkoumání časových řad dat z připojených zařízení

## <a name="connect-your-devices"></a>Připojení zařízení

Jakmile tvůrce definuje typy zařízení, které se můžou připojovat k aplikaci, vývojář zařízení vytvoří kód, který na zařízeních poběží. Jako vývojář zařízení vytváříte kód zařízení pomocí open source [sad SDK Azure IoT](https://github.com/Azure/azure-iot-sdks) od Microsoftu. Tyto sady SDK nabízejí širokou podporu jazyků, platforem a protokolů, aby vyhověly vašim požadavkům na připojení vašich zařízení k aplikaci Azure IoT Central. Sady SDK vám pomůžou implementovat následující možnosti zařízení:

- Vytvořit zabezpečené připojení
- Odesílat telemetrická data
- Hlásit stav
- Přijímat aktualizace konfigurace

Další informace najdete v příspěvku na blogu [Výhody používání sad SDK Azure IoT a nástrahy, kterým je potřeba se vyhnout v opačném případě](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Správa vaší aplikace

Aplikace Azure IoT Central jsou plně hostované Microsoftem, což snižuje režijní náklady na jejich správu.

Jako operátor používáte aplikaci Azure IoT Central ke správě zařízení v rámci svého řešení Azure IoT Central. Obsluha provede úkoly, jako například:

- Monitorovat zařízení připojená k aplikaci
- Řešit potíže a opravovat problémy se zařízeními
- Zřizovat nová zařízení

Jako tvůrce můžete definovat vlastní pravidla a akce, které provozují streamování dat z připojených zařízení. Operátor může tato pravidla povolit nebo zakázat na úrovni zařízení za účelem řízení a automatizace úloh v rámci aplikace.

Správci spravují přístup k vaší aplikaci pomocí [rolí a oprávnění uživatele](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Další postup

Přečetli jste si přehled Azure IoT Central a tady jsou doporučené další kroky:

- Pochopte rozdíly mezi [Azure IoT Central a akcelerátory řešení Azure IoT](overview-iot-options.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Seznamte se s [uživatelským rozhraním Azure IoT Central](overview-iot-central-tour-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Začněte [vytvořením aplikace Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
- Projděte si několik kurzů, kde se dozvíte, jak provést následující kroky:
  - [Vytvořit šablonu zařízení jako tvůrce](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Přidat pravidla pro automatizaci řešení jako tvůrce](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Monitorovat zařízení jako operátor](tutorial-monitor-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
  - [Jako operátor přidejte do řešení zařízení.](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- Další informace o [IoT technologie Plug and Play](../iot-pnp/overview-iot-plug-and-play.md)
