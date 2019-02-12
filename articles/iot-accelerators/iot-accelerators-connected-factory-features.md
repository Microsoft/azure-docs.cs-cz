---
title: Připojený objekt pro vytváření řešení funkce – Azure | Dokumentace Microsoftu
description: Přehled funkcí připojené továrny v předkonfigurovaném řešení.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: dobett
ms.openlocfilehash: b54331e644d55497a7c7d33344cf29a82404847e
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56096908"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Co je akcelerátor řešení připojené továrny IoT?

Propojené továrny je implementace společnosti Microsoft Azure průmyslové referenční architektura IoT v, zabalit jako na open-source řešení. Můžete ho použít jako výchozí bod pro komerční produktu. Můžete nasadit předem sestavené verzi řešení připojená továrna do vašeho předplatného Azure z [akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Řídicí panel řešení propojené továrny](./media/iot-accelerators-connected-factory-features/dashboard.png)

Akcelerátor řešení připojená továrna [kód je k dispozici na Githubu](https://github.com/Azure/azure-iot-connected-factory).

Propojená továrna zahrnuje následující funkce:

## <a name="industrial-device-interoperability"></a>Vzájemná funkční spolupráce průmyslových zařízení

- Připojte se k průmyslovým prostředkům pomocí rozhraní OPC UA.
- Použití simulovaných výrobních linek (servery OPC UA v kontejnerech Dockeru) zobrazíte živou telemetrii z nich.
- Procházejte model informace o OPC UA serverů OPC UA z řídicího panelu cloud.

## <a name="remote-management"></a>Vzdálená správa

- Prostředky OPC UA můžete konfigurovat na řídicím panelu cloudu (volat metody, číst a zapisovat data).
- Na řídicím panelu cloudu můžete publikovat telemetrická data z prostředků OPC UA nebo jejich publikování zrušit.

## <a name="cloud-dashboard"></a>Řídicí panel cloudu

- Zobrazit náhledy telemetrie přímo na řídicím panelu cloud.
- Prohlédněte si trendy v telemetrických datech a vytvářejte korelace s využitím řídicího panelu průzkumníka Time Series Insights.
- Z řídicího panelu cloud, najdete v článku vypočtené celkové efektivity zařízení (OEE) a klíčových ukazatelů výkonu (KPI).
- Zobrazení asset průmyslové hierarchie v topologii stromu i na interaktivní mapu.
- Zobrazení, potvrzení a zavírání výstrah z řídicího panelu cloud.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) je sestaven pro ukládání, vizualizaci a dotazování velkých objemů dat časových řad. Připojené továrny využívá tuto službu.
- Propojená továrna se integruje s Tato služba umožňuje provádět hloubkové, v reálném čase analýzy údajů zařízení.

## <a name="rules-and-alerts"></a>Pravidel a výstrah

[Konfigurace pravidel založené na prahových hodnotách pro výstrahy](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Komplexní zabezpečení

- Konfigurujte pro uživatele oprávnění zabezpečení s využitím řízení přístupu na základě role (RBAC).
- Koncové šifrování je implementováno pomocí OPC UA ověřování (pomocí certifikátů X.509), stejně jako tokeny zabezpečení.

## <a name="customizability"></a>Přizpůsobitelnost

- Přizpůsobení řešení pro splnění konkrétních podnikových požadavků.
- Úplné řešení zdrojového kódu k dispozici na Githubu. Zobrazit [předkonfigurované řešení připojené továrny](https://github.com/Azure/azure-iot-connected-factory) úložiště.

## <a name="next-steps"></a>Další postup

Další informace o řešení připojené továrny v předkonfigurovaném v následujících článcích:

* [Propojená továrna návod pro předkonfigurované řešení](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Nasazení brány pro propojenou továrnu]( iot-accelerators-connected-factory-gateway-deployment.md)
