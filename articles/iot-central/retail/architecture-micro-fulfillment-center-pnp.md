---
title: Centrum mikroplnění Azure IoT Central | Dokumenty společnosti Microsoft
description: Naučte se vytvářet aplikaci centra mikroplnění pomocí naší šablony aplikace Centra pro mikroplnění v IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: f752c77a6a62b9b259a8bb1869ca03ff6a19b1f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77020875"
---
# <a name="micro-fulfillment-center-architecture"></a>Architektura centra mikroplnění

Řešení centra pro mikroplnění umožňují digitální připojení, monitorování a správu všech aspektů plně automatizovaného centra plnění, abyste snížili náklady tím, že eliminují prostoje a současně zvyšují zabezpečení a celkovou efektivitu. Tato řešení lze sestavit pomocí jedné ze šablon aplikací v rámci IoT Central a architektury níže jako vodítko.

![Azure IoT Central Store Analytics](./media/architecture/micro-fulfillment-center-architecture-frame.png)

- Sada ioT senzorů odesílajících telemetrická data do zařízení brány
- Zařízení brány odesílající telemetrii a agregované přehledy do IoT Central
- Nepřetržitý export dat do požadované služby Azure pro manipulaci
- Data mohou být strukturována v požadovaném formátu a odeslána do služby úložiště
- Obchodní aplikace mohou dotazovat data a generovat přehledy, které pohánějí maloobchodní provoz
 
Podívejme se na klíčové komponenty, které obvykle hrají roli v řešení centra mikroplnění.

## <a name="robotic-carriers"></a>Robotické nosiče

Řešení centra mikroplnění bude pravděpodobně mít velkou sadu robotických nosičů generujících různé druhy telemetrických signálů. Tyto signály mohou být ingestovány zařízením brány, agregovány a pak odeslány do IoT Central, jak se odráží na levé straně diagramu architektury.  

## <a name="condition-monitoring-sensors"></a>Čidla pro sledování stavu

Řešení IoT začíná sadou senzorů zachycujících smysluplné signály z vašeho centra plnění. Odráží se to různými druhy senzorů zcela vlevo od schématu architektury výše.

## <a name="gateway-devices"></a>Zařízení brány

Mnoho senzorů IoT může podávat nezpracované signály přímo do cloudu nebo do zařízení brány umístěného v jejich blízkosti. Zařízení brány provádí agregaci dat na okraji před odesláním souhrnné přehledy do aplikace IoT Central. Zařízení brány jsou také zodpovědní za předávání řídicích a řídicích operací do senzorových zařízení, pokud je to možné. 

## <a name="iot-central-application"></a>Aplikace IoT Central

Aplikace Azure IoT Central ingestuje data z různých druhů senzorů IoT, robotů a také gateway zařízení v prostředí centra plnění a generuje sadu smysluplných přehledů.

Azure IoT Central také poskytuje přizpůsobené prostředí pro operátora obchodu, které jim umožňuje vzdáleně monitorovat a spravovat zařízení infrastruktury.

## <a name="data-transform"></a>Transformace dat
Aplikaci Azure IoT Central v rámci řešení lze nakonfigurovat tak, aby exportovala nezpracované nebo agregované přehledy do sady služeb Azure PaaS (Platforma jako služba), které můžou provádět manipulaci s daty a obohatit tyto poznatky před jejich přistáním v podniku. Aplikace. 

## <a name="business-application"></a>Obchodní aplikace
Data IoT lze použít k napájení různých druhů obchodních aplikací nasazených v maloobchodním prostředí. Manažer nebo zaměstnanec centra plnění může tyto aplikace použít k vizualizaci obchodních přehledů a k významným akcím v reálném čase. Pokud se chcete dozvědět, jak vytvořit řídicí panel Power BI v reálném čase pro váš maloobchodní tým, postupujte podle [kurzu](./tutorial-in-store-analytics-create-app-pnp.md).

## <a name="next-steps"></a>Další kroky
* Začínáme se šablonou aplikace [Centrum pro mikroplnění.](https://aka.ms/checkouttemplate) 
* Podívejte se na [kurz,](https://aka.ms/mfc-tutorial) který vás provede vytvořením řešení pomocí šablony aplikace Centrum pro mikroplnění.
