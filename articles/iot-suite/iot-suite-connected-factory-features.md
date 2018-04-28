---
title: Připojen objekt pro vytváření řešení funkce – Azure | Microsoft Docs
description: Přehled funkcí objektu pro vytváření připojení předkonfigurované řešení.
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: fea9ccc53bd019039cf1e989d72db7a218e4517c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-iot-suite-connected-factory"></a>Co je Azure IoT Suite připojené Factory?

Objekt Factory připojených je implementace referenční architektura IoT průmyslových Azure pomocí programu společnosti Microsoft, zabalené jako na řešení open source. Můžete ho použít jako výchozí bod pro komerční produkt. Předem připravené verzi připojen objekt pro vytváření řešení můžete nasadit do vašeho předplatného Azure z [Azure IoT Suite](https://www.azureiotsuite.com/#solutions/types/CF).

![Řídicí panel řešení připojených objekt pro vytváření](media/iot-suite-connected-factory-features/dashboard.png)

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
- Připojené factory se integruje s to služby umožňuje provádět analýzy v reálném čase, přímý vašich dat zařízení.

## <a name="rules-and-alerts"></a>Pravidla a oznámení

[Umožňuje konfigurovat pravidla na základě prahové hodnoty pro výstrahy](iot-suite-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Koncové zabezpečení

- Nakonfigurujte oprávnění zabezpečení pro uživatele, kteří používají řízení přístupu na základě Role (RBAC).
- Šifrování klient server je implementovaná pomocí OPC UA ověřování (pomocí certifikátů X.509) a také tokeny zabezpečení.

## <a name="customizability"></a>Možnosti přizpůsobení

- [Přizpůsobení](iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md) řešení podle konkrétních podnikových požadavků.
- Úplné řešení zdrojového kódu dostupná na Githubu. Najdete v článku [připojené vytváření předkonfigurovaného řešení](https://github.com/Azure/azure-iot-connected-factory) úložiště.

## <a name="next-steps"></a>Další postup

Další informace o připojené objekt pro vytváření předkonfigurovaného řešení přečíst v následujících článcích:

* [Návod připojené objekt pro vytváření předkonfigurovaného řešení](iot-suite-connected-factory-sample-walkthrough.md)
* [Nasazení brány pro připojené vytváření]( iot-suite-connected-factory-gateway-deployment.md)
