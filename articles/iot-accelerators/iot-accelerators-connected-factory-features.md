---
title: Připojen objekt pro vytváření řešení funkce – Azure | Microsoft Docs
description: Přehled funkcí objektu pro vytváření připojení předkonfigurované řešení.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: 3478217771418ab31772d6a42a7ed8d8a2e8069a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626492"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Co je připojen objekt pro vytváření IoT řešení akcelerátoru?

Objekt Factory připojených je implementace referenční architektura IoT průmyslových Azure pomocí programu společnosti Microsoft, zabalené jako na řešení open source. Můžete ho použít jako výchozí bod pro komerční produkt. Předem připravené verzi připojen objekt pro vytváření řešení můžete nasadit do vašeho předplatného Azure z [akcelerátorů řešení Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Řídicí panel řešení připojených objekt pro vytváření](./media/iot-accelerators-connected-factory-features/dashboard.png)

Připojené Factory zahrnuje následující funkce:

## <a name="industrial-device-interoperability"></a>Vzájemná funkční spolupráce průmyslových zařízení

- Připojte k průmyslových prostředky s rozhraním OPC UA.
- Simulované řádky výrobní (se OPC UA servery v kontejnerech Docker) slouží k zobrazení za provozu telemetrie z nich.
- Vyhledejte informace o modelu OPC UA OPC UA serverů z řídicí panel cloud.

## <a name="remote-management"></a>Vzdálená správa

- Nakonfigurujte vaše prostředky OPC UA z řídicího panelu cloudu (volání metody, čtení a zápis dat).
- Publikování a zrušení publikování telemetrická data z vaše prostředky OPC UA z řídicí panel cloud.

## <a name="cloud-dashboard"></a>Řídicí panel cloud

- Zobrazení telemetrie náhledy přímo na řídicím panelu cloudu.
- Zobrazení trendů v telemetrii dat a vytvořte korelací časové řady Statistika Explorer řídicího panelu.
- Z řídicího panelu cloudu najdete v části Počítané celkové vybavení efektivitu (OEE) a klíčových ukazatelů výkonu (KPI).
- Zobrazení průmyslových asset hierarchií v topologii stromu, a také na interaktivní mapu.
- Zobrazení, vědomí a uzavřít výstrahy z řídicí panel cloud.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Statistiky Azure řady čas](../time-series-insights/time-series-insights-overview.md) určené pro ukládání, vizualizace a dotazování velké objemy dat časové řady. Připojen objekt pro vytváření využívá tuto službu.
- Připojené Factory se integruje s to služby umožňuje provádět analýzy v reálném čase, přímý vašich dat zařízení.

## <a name="rules-and-alerts"></a>Pravidla a oznámení

[Umožňuje konfigurovat pravidla na základě prahové hodnoty pro výstrahy](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Koncové zabezpečení

- Nakonfigurujte oprávnění zabezpečení pro uživatele, kteří používají řízení přístupu na základě Role (RBAC).
- Šifrování klient server je implementovaná pomocí OPC UA ověřování (pomocí certifikátů X.509) a také tokeny zabezpečení.

## <a name="customizability"></a>Možnosti přizpůsobení

- Přizpůsobení řešení podle konkrétních podnikových požadavků.
- Úplné řešení zdrojového kódu dostupná na Githubu. Najdete v článku [připojen objekt pro vytváření předkonfigurovaného řešení](https://github.com/Azure/azure-iot-connected-factory) úložiště.

## <a name="next-steps"></a>Další postup

Další informace o vytváření připojení předkonfigurované řešení přečíst v následujících článcích:

* [Připojené Factory návod pro předkonfigurované řešení](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Nasazení brány pro propojenou továrnu]( iot-accelerators-connected-factory-gateway-deployment.md)
