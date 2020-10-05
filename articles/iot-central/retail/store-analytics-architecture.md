---
title: Analýza architektury pro Store
description: Naučte se vytvářet analytické aplikace v obchodě pomocí šablony aplikace pro rezervaci v IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: f1f83fdd73816e6e30c5cac7d193719591bb8dc1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "80999023"
---
# <a name="in-store-analytics-architecture"></a>Architektura analýzy v úložišti



Analytická řešení v obchodě umožňují sledovat různé podmínky v rámci prostředí maloobchodního obchodu. Tato řešení lze sestavit pomocí jedné z šablon aplikací v rámci IoT Central a níže uvedenou architekturou jako doprovodné materiály.


![Azure IoT Central Store Analytics](./media/architecture/store-analytics-architecture-frame.png)

- Sada senzorů IoT odesílajících data telemetrie na zařízení brány
- Zařízení brány odesílající telemetrii a agregované přehledy pro IoT Central
- Průběžný export dat do požadované služby Azure za účelem manipulace
- Data mohou být strukturovaná v požadovaném formátu a odeslána do služby úložiště.
- Obchodní aplikace se mohou dotazovat na data a generovat přehledy, které maloobchodní operace spotřeby
 
Pojďme se podívat na klíčové komponenty, které obecně hrají součást v analytickém řešení v obchodě.

## <a name="condition-monitoring-sensors"></a>Senzory monitorování podmínek

Řešení IoT začíná sadou senzorů, které zachytí smysluplné signály z maloobchodního prostředí v obchodě. Projeví se v různých druzích senzorů úplně vlevo od diagramu architektury výše.

## <a name="gateway-devices"></a>Zařízení brány

Mnoho senzorů IoT může zacházet s nezpracovanými signály přímo do cloudu nebo na zařízení brány, které se nachází poblíž. Zařízení brány provádí agregaci dat na hranici před odesláním souhrnných přehledů do aplikace IoT Central. Zařízení brány jsou také zodpovědná za přenos příkazů a operací řízení na zařízení snímače, pokud jsou k dispozici. 

## <a name="iot-central-application"></a>IoT Central aplikace

Aplikace IoT Central v Azure ingestuje data z různých druhů IoT snímačů, a to i zařízení brány v rámci maloobchodního prostředí v prodejnách a generuje sadu smysluplných přehledů.

Azure IoT Central také poskytuje přizpůsobené prostředí pro operátora Store, které umožňuje vzdáleně monitorovat a spravovat zařízení infrastruktury.

## <a name="data-transform"></a>Transformace dat
Aplikace Azure IoT Central v rámci řešení se dá nakonfigurovat tak, aby exportovali nezpracované nebo agregované poznatky do sady služeb Azure PaaS (platforma jako služba), které můžou provádět manipulaci s daty a rozšiřovat tyto přehledy před jejich vyvoláním do obchodní aplikace. 

## <a name="business-application"></a>Obchodní aplikace
Data IoT se dají použít k napájení různých druhů podnikových aplikací nasazených v rámci maloobchodního prostředí. Manažer maloobchodního obchodu nebo zaměstnanec může tyto aplikace využívat k vizualizaci obchodních přehledů a provádět smysluplné akce v reálném čase. Pokud chcete zjistit, jak vytvořit řídicí panel Power BI v reálném čase pro maloobchodní tým, postupujte podle tohoto [kurzu](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Další kroky
* Začněte s [rezervací analýz v rámci obchodu](https://aka.ms/checkouttemplate) a v šablonách aplikací [monitorování podmínek analýzy v úložišti](https://aka.ms/conditiontemplate) . 
* Podívejte se na úvodní [kurz](https://aka.ms/storeanalytics-tutorial) , který vás provede vytvořením řešení pomocí jedné z šablon aplikací pro analýzu v úložišti.
