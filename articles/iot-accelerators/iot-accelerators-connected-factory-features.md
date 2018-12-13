---
title: Připojený objekt pro vytváření řešení funkce – Azure | Dokumentace Microsoftu
description: Přehled funkcí připojené továrny v předkonfigurovaném řešení.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: af2a2c84f9eb420a7ca9a8bd5909cbf856d29a5e
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309191"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Co je akcelerátor řešení připojené továrny IoT?

Propojené továrny je implementace společnosti Microsoft Azure průmyslové referenční architektura IoT v, zabalit jako na open-source řešení. Můžete ho použít jako výchozí bod pro komerční produktu. Můžete nasadit předem sestavené verzi řešení připojená továrna do vašeho předplatného Azure z [akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Řídicí panel řešení propojené továrny](./media/iot-accelerators-connected-factory-features/dashboard.png)

Akcelerátor řešení připojená továrna [kód je k dispozici na Githubu](https://github.com/Azure/azure-iot-connected-factory).

Propojená továrna zahrnuje následující funkce:

## <a name="industrial-device-interoperability"></a>Vzájemná funkční spolupráce průmyslová zařízení

- Připojte se k průmyslových prostředků pomocí rozhraní OPC UA.
- Použití simulovaných výrobních linek (servery OPC UA v kontejnerech Dockeru) zobrazíte živou telemetrii z nich.
- Procházejte model informace o OPC UA serverů OPC UA z řídicího panelu cloud.

## <a name="remote-management"></a>Vzdálená správa

- Konfigurujte vaše prostředky OPC UA na řídicím panelu cloud (volání metody, číst a zapisovat data).
- Publikování a zrušení publikování telemetrická data z vašich prostředků OPC UA z řídicího panelu cloud.

## <a name="cloud-dashboard"></a>Řídicí panel cloud

- Zobrazit náhledy telemetrie přímo na řídicím panelu cloud.
- Zobrazit trendy v telemetrických dat a vytvoření korelace na řídicím panelu Průzkumník Time Series Insights.
- Z řídicího panelu cloud, najdete v článku vypočtené celkové efektivity zařízení (OEE) a klíčových ukazatelů výkonu (KPI).
- Zobrazení asset průmyslové hierarchie v topologii stromu i na interaktivní mapu.
- Zobrazení, potvrzení a zavírání výstrah z řídicího panelu cloud.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) je sestaven pro ukládání, vizualizaci a dotazování velkých objemů dat časových řad. Připojené továrny využívá tuto službu.
- Propojená továrna se integruje s Tato služba umožňuje provádět hloubkové, v reálném čase analýzy údajů zařízení.

## <a name="rules-and-alerts"></a>Pravidel a výstrah

[Konfigurace pravidel založené na prahových hodnotách pro výstrahy](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Koncové zabezpečení

- Konfigurace oprávnění zabezpečení pro uživatele, kteří používají řízení přístupu na základě Role (RBAC).
- Koncové šifrování je implementováno pomocí OPC UA ověřování (pomocí certifikátů X.509), stejně jako tokeny zabezpečení.

## <a name="customizability"></a>Přizpůsobitelnost

- Přizpůsobení řešení pro splnění konkrétních podnikových požadavků.
- Úplné řešení zdrojového kódu k dispozici na Githubu. Zobrazit [předkonfigurované řešení připojené továrny](https://github.com/Azure/azure-iot-connected-factory) úložiště.

## <a name="next-steps"></a>Další postup

Další informace o řešení připojené továrny v předkonfigurovaném v následujících článcích:

* [Propojená továrna návod pro předkonfigurované řešení](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Nasazení brány pro propojenou továrnu]( iot-accelerators-connected-factory-gateway-deployment.md)
