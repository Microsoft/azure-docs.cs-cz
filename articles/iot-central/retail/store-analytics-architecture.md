---
title: Architektura analýzy obchodu
description: Naučte se vytvářet analytickou aplikaci v obchodě pomocí šablony aplikace Pokladna v IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: f1f83fdd73816e6e30c5cac7d193719591bb8dc1
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80999023"
---
# <a name="in-store-analytics-architecture"></a>Architektura analýz y v obchodě



Analytická řešení v obchodě umožňují sledovat různé podmínky v prostředí maloobchodu. Tato řešení lze sestavit pomocí jedné ze šablon aplikací v rámci IoT Central a architektury níže jako vodítko.


![Azure IoT Central Store Analytics](./media/architecture/store-analytics-architecture-frame.png)

- Sada ioT senzorů odesílajících telemetrická data do zařízení brány
- Zařízení brány odesílající telemetrii a agregované přehledy do IoT Central
- Nepřetržitý export dat do požadované služby Azure pro manipulaci
- Data mohou být strukturována v požadovaném formátu a odeslána do služby úložiště
- Obchodní aplikace mohou dotazovat data a generovat přehledy, které pohánějí maloobchodní provoz
 
Podívejme se na klíčové součásti, které obecně hrají roli v analytickém řešení v obchodě.

## <a name="condition-monitoring-sensors"></a>Čidla pro sledování stavu

Řešení IoT začíná sadou senzorů zachycujících smysluplné signály z prostředí maloobchodu. Odráží se v různých typech senzorů zcela vlevo od výše uvedeného diagramu architektury.

## <a name="gateway-devices"></a>Zařízení brány

Mnoho senzorů IoT může podávat nezpracované signály přímo do cloudu nebo do zařízení brány umístěného v jejich blízkosti. Zařízení brány provádí agregaci dat na okraji před odesláním souhrnné přehledy do aplikace IoT Central. Zařízení brány jsou také zodpovědní za předávání řídicích a řídicích operací do senzorových zařízení, pokud je to možné. 

## <a name="iot-central-application"></a>Aplikace IoT Central

Aplikace Azure IoT Central ingestuje data z různých druhů senzorů IoT a také brány zařízení v prostředí maloobchodu a generuje sadu smysluplné přehledy.

Azure IoT Central také poskytuje přizpůsobené prostředí pro operátora obchodu, které jim umožňuje vzdáleně monitorovat a spravovat zařízení infrastruktury.

## <a name="data-transform"></a>Transformace dat
Aplikace Azure IoT Central v rámci řešení můžete nakonfigurovat pro export nezpracovaných nebo agregovaných přehledů do sady služeb Azure PaaS (platforma jako služba), které můžou provádět manipulaci s daty a obohatit tyto poznatky před jejich přistáním v obchodní aplikaci. 

## <a name="business-application"></a>Obchodní aplikace
Data IoT lze použít k napájení různých druhů obchodních aplikací nasazených v maloobchodním prostředí. Manažer maloobchodu nebo zaměstnanec může tyto aplikace použít k vizualizaci obchodních přehledů a k významným akcím v reálném čase. Pokud se chcete dozvědět, jak vytvořit řídicí panel Power BI v reálném čase pro váš maloobchodní tým, postupujte podle [kurzu](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Další kroky
* Začínáme se šablonami aplikací [pro kontrolu analýzy v obchodě a](https://aka.ms/checkouttemplate) v [úložišti.](https://aka.ms/conditiontemplate) 
* Podívejte se na [kurz od konce do konce,](https://aka.ms/storeanalytics-tutorial) který vás provede vytvořením řešení pomocí jedné ze šablon aplikací In-Store Analytics.
