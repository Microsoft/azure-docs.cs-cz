---
title: Co je Azure IoT Central | Microsoft Docs
description: Azure IoT Central je kompletní řešení SaaS, pomocí kterého můžete sestavit a spravovat vlastní řešení IoT. Tento článek obsahuje přehled funkcí Azure IoT Central.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 41eb9049b45c6835dd866c988af486a4975c4831
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201162"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Co je Azure IoT Central?

Microsoft Azure IoT Central je plně spravované řešení IoT typu SaaS (software jako služba), které usnadňuje vytváření produktů propojujících fyzický svět s tím digitálním. Svoji vizi propojených produktů můžete přivést k životu následujícími kroky:

- Získávání nových informací z připojených zařízení, které vám pomohou poskytovat zákazníkům lepší produkty a prostředí
- Vytváření nových obchodních příležitostí pro vaši organizaci

Azure IoT Central na rozdíl od typického projektu IoT zjednodušuje správu řešení IoT v těchto ohledech:

- Snížení náročnosti správy
- Snížení provozních a režijních nákladů
- Snazší přizpůsobení aplikace prostřednictvím těchto prvků:
  - Špičkové technologie jako [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) a [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)
  - Funkce zabezpečení na podnikové úrovni, třeba komplexní šifrování

Následující video obsahuje přehled Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

Zbývající část tohoto článku popisuje tyto aspekty Azure IoT Central:

- Typické osoby přidružené k projektu
- Postup vytvoření aplikace
- Postup připojení zařízení do aplikace
- Postup správy aplikace

## <a name="personas"></a>Osoby

Dokumentace Azure IoT Central odkazuje na čtyři osoby, které obvykle pracují s aplikací Azure IoT Central:

- _Tvůrce_ zodpovídá za definování typů zařízení, která se připojují k aplikaci, a přizpůsobuje aplikaci pro operátora.
- _Operátor_ spravuje zařízení připojená k aplikaci.
- _Správce_ zodpovídá v aplikaci za úlohy správy, jako je třeba správa uživatelů a rolí.
- _Vývojář zařízení_ vytváří kód, který pak běží na zařízení připojeném k aplikaci.

## <a name="create-your-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT Central

Jako tvůrce vytvoříte pomocí Azure IoT Central pro svoji organizaci vlastní řešení IoT hostované v cloudu. Vlastní řešení IoT se obvykle skládá z těchto součástí:

- Cloudová aplikace, která přijímá ze zařízení telemetrická data a umožňuje vám tato zařízení spravovat
- Více zařízení připojených k vaší cloudové aplikaci, na kterých běží vlastní kód

Novou aplikaci Azure IoT Central můžete rychle nasadit a přizpůsobit konkrétním požadavkům přímo v prohlížeči. Jako tvůrce Azure IoT Central můžete pomocí webových nástrojů vytvořit _šablonu zařízení_ pro zařízení, která se připojují k vaší aplikaci. Šablona zařízení je podrobný plán modelu zařízení, který sdílí všechna zařízení vytvořená ze šablony zařízení. Šablona zařízení definuje vlastnosti a chování určitého typu zařízení, třeba v těchto ohledech:

- Telemetrická data, která odesílá
- Obchodní vlastnosti, které můžete upravit operátor
- Vlastnosti zařízení nastavené zařízením, které jsou v aplikaci jen pro čtení
- Prahové hodnoty, na které aplikace reaguje
- Nastavení určující chování zařízení

Šablony zařízení a aplikaci můžete okamžitě otestovat s využitím simulovaných dat, která vám Azure IoT Central vygeneruje.

Jako tvůrce můžete taky přizpůsobit uživatelské rozhraní aplikace Azure IoT Central pro operátory, kteří zodpovídají za každodenní použití aplikace. Tvůrce může provést třeba následující přizpůsobení:

- Definovat rozložení vlastností a nastavení v šabloně zařízení
- Nakonfigurovat vlastní řídicí panely, které operátorům pomůžou rychleji zjišťovat informace a řešit problémy
- Nakonfigurovat vlastní analýzy pro zkoumání časových řad dat z připojených zařízení

## <a name="connect-your-devices"></a>Připojení zařízení

Jakmile tvůrce definuje typy zařízení, které se můžou připojovat k aplikaci, vývojář zařízení vytvoří kód, který na zařízeních poběží. Jako vývojář zařízení vytváříte kód zařízení pomocí open source [sad SDK Azure IoT](https://github.com/Azure/azure-iot-sdks) od Microsoftu. Tyto sady SDK nabízejí širokou podporu jazyků, platforem a protokolů, aby vyhověly vašim požadavkům na připojení vašich zařízení k aplikaci Azure IoT Central. Sady SDK vám umožňují provádět na zařízení připojeném k Azure IoT Central následující úkoly:

- Vytvořit zabezpečené připojení
- Odesílat telemetrická data
- Hlásit stav
- Přijímat aktualizace konfigurace

Další informace najdete v příspěvku na blogu [Výhody používání sad SDK Azure IoT a nástrahy, kterým je potřeba se vyhnout v opačném případě](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Správa vaší aplikace

Aplikace Azure IoT Central jsou plně hostované Microsoftem, což snižuje režijní náklady na jejich správu.

Jako operátor používáte aplikaci Azure IoT Central ke správě zařízení v rámci svého řešení Azure IoT Central. Operátoři můžou provádět třeba tyto úlohy:

- Monitorovat zařízení připojená k aplikaci
- Řešit potíže a opravovat problémy se zařízeními
- Zřizovat nová zařízení

Tvůrce může vytvořit vlastní pravidla a akce řídící streamování dat na úrovni šablony zařízení. Operátor může tato pravidla povolit nebo zakázat na úrovni zařízení za účelem řízení a automatizace úloh v rámci aplikace.

Správci můžou spravovat přístup k aplikaci pomocí [uživatelských rolí a oprávnění](howto-administer.md).

## <a name="next-steps"></a>Další kroky

Přečetli jste si přehled Azure IoT Central a tady jsou doporučené další kroky:

- Pochopte rozdíly mezi [Azure IoT Central a Azure IoT Suite](overview-iot-options.md).
- Seznamte se s [uživatelským rozhraním Azure IoT Central](overview-iot-central-tour.md).
- Začněte [vytvořením aplikace Azure IoT Central](quick-deploy-iot-central.md).
- Projděte si několik kurzů, kde se dozvíte, jak provést následující kroky:
  - [Vytvořit šablonu zařízení jako tvůrce](tutorial-define-device-type.md)
  - [Přidat pravidla pro automatizaci řešení jako tvůrce](tutorial-configure-rules.md)
  - [Přizpůsobit aplikaci pro operátory jako tvůrce](tutorial-customize-operator.md)
  - [Monitorovat zařízení jako operátor](tutorial-monitor-devices.md)
  - [Přidat do řešení skutečné zařízení jako operátor](tutorial-add-device.md)
  - [Vytvořit kód pro zařízení jako vývojář zařízení](tutorial-add-device.md#prepare-the-client-code)
