---
title: Funkce řešení Connected Factory – Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje přehled funkcí předkonfigurovaného řešení připojené továrny, jako je cloudový řídicí panel, pravidla a výstrahy.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820113"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Co je akcelerátor řešení Connected Factory IoT?

Connected Factory je implementace referenční architektury Azure Industrial IoT od Microsoftu, která je zabalená jako řešení s otevřeným zdrojovým kódem. Můžete jej použít jako výchozí bod pro komerční produkt. Do předplatného Azure můžete nasadit předem vytvořenou verzi řešení Connected Factory z [akcelerátorů řešení Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Řídicí panel řešení připojené továrny](./media/iot-accelerators-connected-factory-features/dashboard.png)

Kód akcelerátoru řešení Připojené továrny [je k dispozici na GitHubu](https://github.com/Azure/azure-iot-connected-factory).

Připojená továrna obsahuje následující funkce:

## <a name="industrial-device-interoperability"></a>Interoperabilita průmyslových zařízení

- Připojte se k průmyslovým aktivům pomocí rozhraní OPC UA.
- Pomocí simulovaných výrobních linek (se systémem OPC UA serverů v kontejnerech Dockeru) znějí živá telemetrická metrie z nich.
- Prohlédněte si informační model OPC UA serverů OPC UA z cloudového řídicího panelu.

## <a name="remote-management"></a>Vzdálená správa

- Nakonfigurujte prostředky OPC UA z řídicího panelu cloudu (metody volání, čtení a zápis dat).
- Publikujte a odivujte telemetrická data z prostředků OPC UA z cloudového řídicího panelu.

## <a name="cloud-dashboard"></a>Řídicí panel cloudu

- Zobrazení náhledů telemetrie přímo na cloudovém řídicím panelu.
- Zobrazení trendů v telemetrických datech a vytváření korelací pomocí řídicího panelu Průzkumníka přehledů časové řady.
- Podívejte se na vypočítané celkové účinnosti zařízení (OEE) a klíčové ukazatele výkonu (KU) z cloudového řídicího panelu.
- Zobrazení hierarchií průmyslových aktiv v topologii stromu i na interaktivní mapě.
- Zobrazení, potvrzení a zavření výstrah z cloudového řídicího panelu.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) je vytvořený pro ukládání, vizualizaci a dotazování velkého množství dat časových řad. Connected Factory využívá tuto službu.
- Connected Factory se integruje s touto službou a umožňuje provádět hloubkovou analýzu dat vašeho zařízení v reálném čase.

## <a name="rules-and-alerts"></a>Pravidla a výstrahy

[Konfigurace pravidel založených na prahových hodnotách pro výstrahy](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Komplexní zabezpečení

- Nakonfigurujte oprávnění zabezpečení pro uživatele pomocí řízení přístupu na základě rolí (RBAC).
- End-to-end šifrování je implementováno pomocí ověřování OPC UA (pomocí certifikátů X.509) a tokenů zabezpečení.

## <a name="customizability"></a>Přizpůsobitelnost

- Přizpůsobte si řešení tak, aby splňovalo specifické obchodní požadavky.
- Kompletní zdrojový kód řešení dostupný na GitHubu. Podívejte se na [předkonfigurované](https://github.com/Azure/azure-iot-connected-factory) úložiště řešení Connected Factory.

## <a name="next-steps"></a>Další kroky

Další informace o akcelerátoru řešení Připojené továrny najdete v tématu Rychlý start [Vyzkoušejte cloudové řešení pro správu průmyslových zařízení IoT](quickstart-connected-factory-deploy.md).
