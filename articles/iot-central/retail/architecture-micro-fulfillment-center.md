---
title: Centrum pro Azure IoT Central Micro-doplňování | Microsoft Docs
description: Naučte se vytvářet aplikace centra pro mikroplnění pomocí naší šablony aplikace pro vyřizování softwaru v IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 23e1880a3abac6ebda71eaa36276d2bf5815048e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833775"
---
# <a name="micro-fulfillment-center-architecture"></a>Architektura Micro-splního centra

Řešení pro vyplňování vám umožňují digitálně propojit, monitorovat a spravovat všechny aspekty plně automatizovaného centra plnění a snížit tak náklady tím, že se při zvyšování zabezpečení a celkové efektivity sníží prostoje. Tato řešení lze sestavit pomocí jedné z šablon aplikací v rámci IoT Central a níže uvedenou architekturou jako doprovodné materiály.

![Azure IoT Central Store Analytics](./media/architecture/micro-fulfillment-center-architecture-frame.png)

1. Sada senzorů IoT odesílajících data telemetrie na zařízení brány
2. Zařízení brány odesílající telemetrii a agregované přehledy pro IoT Central
3. Průběžný export dat do požadované služby Azure za účelem manipulace
4. Data mohou být strukturovaná v požadovaném formátu a odeslána do služby úložiště.
5. Obchodní aplikace se mohou dotazovat na data a generovat přehledy, které maloobchodní operace spotřeby
 
Pojďme se podívat na klíčové komponenty, které obvykle hrají součást v rámci řešení pro doplňování v centru.

## <a name="robotic-carriers"></a>Robotní operátoři

Řešení pro mikroplnění do středu bude pravděpodobně mít velkou sadu automatických dopravců generujících různé druhy signálů telemetrie. Tyto signály můžou být ingestované zařízením brány, agregované a pak odesílány IoT Central tak, jak se projeví na levé straně diagramu architektury.  

## <a name="condition-monitoring-sensors"></a>Senzory monitorování podmínek

Řešení IoT začíná sadou senzorů, které zachytí smysluplné signály v rámci vašeho centra pro splnění. Odrazí se v různých druzích senzorů úplně vlevo od diagramu architektury výše.

## <a name="gateway-devices"></a>Zařízení brány

Mnoho senzorů IoT může zacházet s nezpracovanými signály přímo do cloudu nebo na zařízení brány, které se nachází poblíž. Zařízení brány provádí agregaci dat na hranici před odesláním souhrnných přehledů do aplikace IoT Central. Zařízení brány jsou také zodpovědná za přenos příkazů a operací řízení na zařízení snímače, pokud jsou k dispozici. 

## <a name="iot-central-application"></a>IoT Central aplikace

Aplikace IoT Central v Azure ingestuje data z různých druhů IoT snímačů, robotů, stejně jako zařízení brány v rámci prostředí pro splnění a vygeneruje sadu smysluplných přehledů.

Azure IoT Central také poskytuje přizpůsobené prostředí pro operátora Store, které umožňuje vzdáleně monitorovat a spravovat zařízení infrastruktury.

## <a name="data-transform"></a>Transformace dat
Aplikace Azure IoT Central v rámci řešení se dá nakonfigurovat tak, aby se vyexportoval nezpracované nebo agregované poznatky do sady služeb Azure PaaS (platforma jako služba), které můžou provádět manipulaci s daty a rozšiřovat tyto přehledy před jejich vyvoláním do obchodní aplikace. 

## <a name="business-application"></a>Obchodní aplikace
Data IoT se dají použít k napájení různých druhů podnikových aplikací nasazených v rámci maloobchodního prostředí. Správce plnění nebo zaměstnanec může využít tyto aplikace k vizualizaci obchodních přehledů a provádět smysluplné akce v reálném čase. Pokud chcete zjistit, jak vytvořit řídicí panel Power BI v reálném čase pro maloobchodní tým, postupujte podle tohoto [kurzu](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Další kroky
* Začněte s šablonou aplikace [centra pro vyplňování](https://aka.ms/checkouttemplate) . 
* Podívejte se na [kurz](https://aka.ms/mfc-tutorial) , který vás provede vytvořením řešení pomocí šablony aplikace pro vyplňování.
