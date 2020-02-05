---
title: Co je Azure IoT Central | Microsoft Docs
description: Azure IoT Central je aplikační platforma IoT, která zjednodušuje vytváření řešení IoT. Tento článek obsahuje přehled funkcí Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/26/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 6c62b247da9447138bd7e5d84dfe49fdf75a06fd
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990755"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Co je Azure IoT Central?

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Azure IoT Central je aplikační platforma IoT, která snižuje zatížení a náklady spojené s vývojem, správou a údržbou řešení IoT na podnikové úrovni. Když zvolíte sestavení pomocí Azure IoT Central, získáte možnost zaměřit se na vaše podnikání, peníze a energii při transformaci vaší firmy na data IoT, a ne jenom zachovat a aktualizovat složitou a nepřetržitou vývojovou infrastrukturu IoT.

Snadno použitelné rozhraní usnadňuje monitorování podmínek zařízení, vytváření pravidel a správu milionů zařízení a jejich dat během svého životního cyklu. Kromě toho vám umožňuje reagovat na přehledy zařízení díky rozšíření IoT Intelligence do obchodních aplikací.

Tento článek popisuje, jak IoT Central Azure:

- Typické osoby přidružené k projektu
- Postup vytvoření aplikace
- Postup připojení zařízení do aplikace
- Postup správy aplikace

## <a name="personas"></a>Osoby

Dokumentace ke službě Azure IoT Central odkazuje na čtyři osoby, kteří komunikují s aplikací Azure IoT Central:

- _Tvůrce_ zodpovídá za definování typů zařízení, která se připojují k aplikaci, a přizpůsobuje aplikaci pro operátora.
- _Operátor_ spravuje zařízení připojená k aplikaci.
- _Správce_ zodpovídá za úkoly, jako je například Správa [rolí uživatelů a oprávnění](howto-administer.md) v rámci aplikace.
- _Vývojář zařízení_ vytváří kód, který pak běží na zařízení připojeném k aplikaci.

## <a name="create-your-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT Central

Jako tvůrce vytvoříte pomocí Azure IoT Central pro svoji organizaci vlastní řešení IoT hostované v cloudu. Vlastní řešení IoT se obvykle skládá z těchto součástí:

- Cloudová aplikace, která přijímá ze zařízení telemetrická data a umožňuje vám tato zařízení spravovat
- Více zařízení připojených k vaší cloudové aplikaci, na kterých běží vlastní kód

Můžete rychle nasadit novou aplikaci IoT Central Azure a pak ji přizpůsobit konkrétním požadavkům v prohlížeči. Jako tvůrce použijete webové nástroje k vytvoření _šablony_ pro zařízení, která se připojují k vaší aplikaci. Šablona zařízení je plán, který definuje charakteristiky a chování typu zařízení, například:

- Telemetrii, kterou posílá.
- Obchodní vlastnosti, které můžete upravit operátor
- Vlastnosti zařízení nastavené zařízením, které jsou v aplikaci jen pro čtení
- Prahové hodnoty, na které aplikace reaguje.
- Nastavení určující chování zařízení

Šablony zařízení a aplikaci můžete okamžitě otestovat s využitím simulovaných dat, která vám Azure IoT Central vygeneruje.

Jako tvůrce můžete taky přizpůsobit uživatelské rozhraní aplikace Azure IoT Central pro operátory, kteří zodpovídají za každodenní použití aplikace. Tvůrce může provést třeba následující přizpůsobení:

- Definovat rozložení vlastností a nastavení v šabloně zařízení
- Nakonfigurovat vlastní řídicí panely, které operátorům pomůžou rychleji zjišťovat informace a řešit problémy
- Nakonfigurovat vlastní analýzy pro zkoumání časových řad dat z připojených zařízení

## <a name="pricing"></a>Ceny

IoT Central aplikaci můžete vytvořit pomocí bezplatné zkušební verze 7 dní nebo použít Cenový tarif Standard.

- Aplikace vytvořené pomocí *bezplatného* plánu jsou po dobu sedmi dnů zdarma a podporují až pět zařízení. Můžete je kdykoli převést na používání standardního cenového plánu, než vyprší jejich platnost.
- Aplikace, které vytvoříte pomocí plánu *Standard* , se účtují podle jednotlivých zařízení. můžete zvolit Cenový tarif **Standard 1** nebo **Standard 2** s prvními dvěma zařízeními, která jsou zdarma. Přečtěte si další informace o [cenách IoT Central](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Připojit zařízení

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

Správci spravují přístup k vaší aplikaci pomocí [rolí a oprávnění uživatele](howto-administer.md).

## <a name="next-steps"></a>Další kroky

Přečetli jste si přehled Azure IoT Central a tady jsou doporučené další kroky:

- Seznamte se s dostupnými [technologiemi a službami Azure pro vytváření řešení IoT](../../iot-fundamentals/iot-services-and-technologies.md).
- Seznamte se s [uživatelským rozhraním Azure IoT Central](overview-iot-central-tour.md).
- Začněte [vytvořením aplikace Azure IoT Central](quick-deploy-iot-central.md).
- Projděte si několik kurzů, kde se dozvíte, jak provést následující kroky:
  - [Vytvořit šablonu zařízení jako tvůrce](tutorial-define-device-type.md)
  - [Přidat pravidla pro automatizaci řešení jako tvůrce](tutorial-configure-rules.md)
  - [Přizpůsobit aplikaci pro operátory jako tvůrce](tutorial-customize-operator.md)
  - [Monitorovat zařízení jako operátor](tutorial-monitor-devices.md)
  - [Přidat do řešení skutečné zařízení jako operátor](tutorial-add-device.md)
  - [Vytvořit kód pro zařízení jako vývojář zařízení](tutorial-add-device.md#prepare-the-client-code)
