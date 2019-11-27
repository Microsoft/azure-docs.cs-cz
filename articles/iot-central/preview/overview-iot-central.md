---
title: Co je Azure IoT Central | Microsoft Docs
description: Azure IoT Central je aplikační platforma IoT, která zjednodušuje vytváření řešení IoT a pomáhá snižovat zatížení a náklady na operace správy IoT a vývoj. Tento článek obsahuje přehled funkcí Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/12/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 8cfcbddfc8d50855860af655847f997fb2a01711
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479714"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Co je Azure IoT Central (funkce verze Preview)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Možnosti [IoT technologie Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) v Azure IoT Central jsou momentálně ve verzi Public Preview. Nepoužívejte IoT Central [šablonu aplikace](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) technologie Plug and Play IoT povolenou pro produkční úlohy. V produkčních prostředích se používá aplikace IoT Central vytvořená z aktuální, všeobecně dostupné [šablony aplikace](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).

IoT Central je aplikační platforma IoT, která snižuje zatížení a náklady na vývoj, správu a údržbu řešení IoT na podnikové úrovni. Volba sestavování pomocí IoT Central vám umožní soustředit se na čas, peníze a energii při transformaci vaší firmy na data IoT, nikoli jenom na údržbu a aktualizaci složitých a nepřetržitě vyvíjejících se infrastruktury IoT.

Webové uživatelské rozhraní umožňuje monitorovat podmínky zařízení, vytvářet pravidla a spravovat miliony zařízení a jejich data v průběhu svého životního cyklu. Kromě toho vám umožňuje reagovat na přehledy zařízení díky rozšíření IoT Intelligence do obchodních aplikací.

Tento článek popisuje, jak IoT Central:

- Typické osoby přidružené k projektu
- Postup vytvoření aplikace
- Postup připojení zařízení do aplikace
- Postup správy aplikace
- Možnosti Azure IoT Edge v IoT Central.
- Jak připojit zařízení s Azure IoT Edge modulem runtime do vaší aplikace

## <a name="known-issues"></a>Známé problémy

> [!Note]
> Tyto známé problémy se vztahují pouze na aplikace IoT Central Preview.

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
- Je k dispozici pouze v umístěních USA a Evropa.
- Modely schopností zařízení musí mít všechna rozhraní definovaná vložené do stejného souboru.

## <a name="personas"></a>Osoby

Dokumentace IoT Central odkazuje na čtyři osobyy, které komunikují s aplikací IoT Central:

- _Tvůrce řešení_ zodpovídá za definování typů zařízení, která se připojují k aplikaci, a přizpůsobení aplikace pro operátor.
- _Operátor_ spravuje zařízení připojená k aplikaci.
- _Správce_ zodpovídá za úlohy správy, jako je například Správa [rolí uživatelů a oprávnění](howto-administer.md) v rámci aplikace.
- _Vývojář zařízení_ vytvoří kód, který běží na zařízení nebo IoT Edge modul připojený k vaší aplikaci.

## <a name="create-your-iot-central-application"></a>Vytvoření aplikace IoT Central

Jako tvůrce řešení můžete pomocí IoT Central vytvořit vlastní řešení IoT hostované v cloudu pro vaši organizaci. Vlastní řešení IoT se obvykle skládá z těchto součástí:

- Cloudová aplikace, která přijímá ze zařízení telemetrická data a umožňuje vám tato zařízení spravovat
- Více zařízení připojených k vaší cloudové aplikaci, na kterých běží vlastní kód

Můžete rychle nasadit novou aplikaci IoT Central a pak ji přizpůsobit konkrétním požadavkům v prohlížeči. Jako tvůrce řešení můžete pomocí webových nástrojů vytvořit _šablonu zařízení_ pro zařízení, která se připojují k vaší aplikaci. Šablona zařízení je plán, který definuje charakteristiky a chování typu zařízení, například:

- Telemetrii, kterou posílá.
- Obchodní vlastnosti, které můžete upravit operátor
- Vlastnosti zařízení nastavené zařízením, které jsou v aplikaci jen pro čtení
- Vlastnosti, které jsou nastaveny jako operátor, které určují chování zařízení.

Tato šablona zařízení zahrnuje:

- _Model schopností zařízení_ , který popisuje možnosti, které by zařízení mělo implementovat, jako je například telemetrie, kterou posílá, a vlastnosti, které vystavuje.
- Vlastnosti cloudu, které nejsou uložené na zařízení.
- Vlastní nastavení, řídicí panely a formuláře, které jsou součástí vaší aplikace IoT Central.

### <a name="create-device-templates"></a>Vytvoření šablon zařízení

[IoT technologie Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) umožňuje IoT Central integrovat zařízení bez psaní jakéhokoli vloženého kódu zařízení. V jádru IoT technologie Plug and Play je schéma modelu schopností zařízení, které popisuje možnosti zařízení. V aplikaci IoT Central Preview používají šablony zařízení tyto modely schopností zařízení technologie Plug and Play IoT.

Jako tvůrce řešení máte k dispozici několik možností pro vytváření šablon zařízení:

- Navrhněte šablonu zařízení v IoT Central a potom implementujte svůj model schopností zařízení do kódu zařízení.
- Naimportujte model schopností zařízení z [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat) a pak přidejte jakékoli vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše IoT Central aplikace potřebuje.
- Vytvořte model schopností zařízení pomocí Visual Studio Code. Implementujte kód zařízení z modelu a připojte své zařízení k aplikaci IoT Central. IoT Central najde model schopností zařízení z úložiště a vytvoří pro vás jednoduchou šablonu zařízení.
- Vytvořte model schopností zařízení pomocí Visual Studio Code. Implementujte kód zařízení z modelu. Model schopností zařízení naimportujte ručně do aplikace IoT Central a pak přidejte jakékoli vlastnosti cloudu, přizpůsobení a řídicí panely, které aplikace IoT Central potřebuje.

Jako tvůrce řešení můžete použít IoT Central k vygenerování kódu pro testovací zařízení pro ověření šablon zařízení.

### <a name="customize-the-ui"></a>Přizpůsobení uživatelského rozhraní

Jako tvůrce řešení můžete také přizpůsobit uživatelské rozhraní aplikace IoT Central pro operátory, kteří zodpovídají za každodenní použití aplikace. Vlastní nastavení, které může tvůrce řešení provést, zahrnují:

- Definovat rozložení vlastností a nastavení v šabloně zařízení
- Nakonfigurovat vlastní řídicí panely, které operátorům pomůžou rychleji zjišťovat informace a řešit problémy
- Nakonfigurovat vlastní analýzy pro zkoumání časových řad dat z připojených zařízení

## <a name="connect-your-devices"></a>Připojení zařízení

Jakmile tvůrce definuje typy zařízení, které se můžou připojovat k aplikaci, vývojář zařízení vytvoří kód, který na zařízeních poběží. Jako vývojář zařízení vytváříte kód zařízení pomocí open source [sad SDK Azure IoT](https://github.com/Azure/azure-iot-sdks) od Microsoftu. Tyto sady SDK mají širokou škálu jazyků, platforem a protokolů, které splňují vaše požadavky na připojení vašich zařízení k vaší IoT Central aplikaci. Sady SDK vám pomůžou implementovat následující možnosti zařízení:

- Vytvořit zabezpečené připojení
- Odesílat telemetrická data
- Hlásit stav
- Přijímat aktualizace konfigurace

Další informace najdete v příspěvku na blogu [Výhody používání sad SDK Azure IoT a nástrahy, kterým je potřeba se vyhnout v opačném případě](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

### <a name="azure-iot-edge-devices"></a>Zařízení Azure IoT Edge

I zařízení vytvořená pomocí [sad SDK služby Azure IoT](https://github.com/Azure/azure-iot-sdks)můžete také připojit [zařízení Azure IoT Edge](../../iot-edge/about-iot-edge.md) k aplikaci IoT Central. Azure IoT Edge umožňuje spustit cloudovou logiku a vlastní logiku přímo na zařízeních IoT spravovaných pomocí IoT Central. Modul runtime IoT Edge umožňuje:

- Instalace a aktualizace úloh na zařízení.
- Udržujte na zařízení standardy zabezpečení Azure IoT Edge.
- Ujistěte se, že moduly IoT Edge jsou vždycky spuštěné.
- Oznamte stav modulu v cloudu pro vzdálené monitorování.
- Spravujte komunikaci mezi moduly pro příjem a IoT Edge zařízení, mezi moduly v zařízení IoT Edge a mezi IoT Edgem zařízením a cloudem.

Další informace najdete v tématu [Azure IoT Edge zařízení a IoT Central](./concepts-architecture.md#azure-iot-edge-devices).

## <a name="manage-your-application"></a>Správa vaší aplikace

Aplikace IoT Central plně hostují Microsoft, což snižuje nároky na správu při správě vašich aplikací.

Jako operátor můžete použít aplikaci IoT Central ke správě zařízení v řešení IoT Central. Obsluha provede úkoly, jako například:

- Monitorovat zařízení připojená k aplikaci
- Řešit potíže a opravovat problémy se zařízeními
- Zřizovat nová zařízení

Jako tvůrce řešení můžete definovat vlastní pravidla a akce, které provozují streamování dat z připojených zařízení. Operátor může tato pravidla povolit nebo zakázat na úrovni zařízení za účelem řízení a automatizace úloh v rámci aplikace.

Správci spravují přístup k vaší aplikaci pomocí [rolí a oprávnění uživatele](howto-administer.md).

## <a name="quotas"></a>Kvóty

Každé předplatné Azure má výchozí kvóty, které by mohly mít vliv na rozsah vašeho řešení IoT. V současné době IoT Central omezuje počet aplikací, které můžete nasadit v rámci předplatného na 10. Pokud potřebujete tento limit zvýšit, obraťte se na [podporu Microsoftu](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Další kroky

Teď, když máte přehled o IoT Central, tady jsou doporučené další kroky:

- Pochopte rozdíly mezi [IoT Central a akcelerátory řešení Azure IoT](../core/overview-iot-options.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).
- Seznamte se s [uživatelským rozhraním Azure IoT Central](overview-iot-central-tour.md).
- Začněte [vytvořením aplikace Azure IoT Central](quick-deploy-iot-central.md).
- Další informace o [IoT technologie Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Informace o tom, jak [vytvořit šablonu zařízení Azure IoT Edge](./tutorial-define-edge-device-type.md)
