---
title: Telemetrický datový model Azure Application Insights – kontext telemetrie | Dokumenty společnosti Microsoft
description: Kontextový model telemetrie Application Insights
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8a2e3296643b13a54c4fceb11f044a2808cf2877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671859"
---
# <a name="telemetry-context-application-insights-data-model"></a>Kontext telemetrie: Datový model Application Insights

Každá položka telemetrie může mít pole kontextu silného typu. Každé pole umožňuje konkrétní scénář monitorování. Pomocí kolekce vlastních vlastností můžete ukládat vlastní kontextové informace nebo kontextové informace specifické pro aplikaci.


## <a name="application-version"></a>Verze aplikace

Informace v polích kontextu aplikace je vždy o aplikaci, která odesílá telemetrii. Verze aplikace se používá k analýze změn trendů v chování aplikace a její korelace s nasazeními.

Maximální délka: 1024


## <a name="client-ip-address"></a>IP adresa klienta

IP adresa klientského zařízení. Podporují se adresy IPv4 a IPv6. Při odeslání telemetrie ze služby, kontext umístění je o uživateli, který inicioval operaci ve službě. Application Insights extrahovat informace o geografickém umístění z IP klienta a pak zkrátit. Ip adresa klienta tedy sama o sobě nemůže být použita jako identifikovatelná informace koncového uživatele. 

Maximální délka: 46


## <a name="device-type"></a>Typ zařízení

Původně toto pole bylo použito k označení typu zařízení, které koncový uživatel aplikace používá. Dnes se používá především k rozlišení javascriptové telemetrie s typem zařízení "Prohlížeč" od telemetrie na straně serveru s typem zařízení "PC".

Maximální délka: 64


## <a name="operation-id"></a>Id operace

Jedinečný identifikátor kořenové operace. Tento identifikátor umožňuje seskupit telemetrická data mezi více součástmi. Podrobnosti naleznete [v tématu korelace telemetrie.](../../azure-monitor/app/correlation.md) ID operace je vytvořena požadavkem nebo zobrazením stránky. Všechny ostatní telemetrie nastaví toto pole na hodnotu obsahující požadavek nebo zobrazení stránky. 

Maximální délka: 128


## <a name="parent-operation-id"></a>ID nadřazené operace

Jedinečný identifikátor okamžitého nadřazeného položky telemetrie. Podrobnosti naleznete [v tématu korelace telemetrie.](../../azure-monitor/app/correlation.md)

Maximální délka: 128


## <a name="operation-name"></a>Název operace

Název (skupina) operace. Název operace je vytvořen požadavkem nebo zobrazením stránky. Všechny ostatní položky telemetrie nastavit toto pole na hodnotu pro obsahující požadavek nebo zobrazení stránky. Název operace se používá pro vyhledání všech položek telemetrie pro skupinu operací (například GET Home/Index). Tato vlastnost kontextu se používá k zodpovězení otázek, jako je "jaké jsou typické výjimky vyvolány na této stránce."

Maximální délka: 1024


## <a name="synthetic-source-of-the-operation"></a>Syntetický zdroj operace

Název syntetického zdroje. Některé telemetrie z aplikace může představovat syntetický provoz. Může to být webový prohledávač indexování webu, testy dostupnosti webu nebo trasování z diagnostických knihoven, jako je application insights SDK sám.

Maximální délka: 1024


## <a name="session-id"></a>ID relace

ID relace – instance interakce uživatele s aplikací. Informace v polích kontextu relace jsou vždy o koncovém uživateli. Při odeslání telemetrie ze služby je kontext relace o uživateli, který inicioval operaci ve službě.

Maximální délka: 64


## <a name="anonymous-user-id"></a>Anonymní id uživatele

Anonymní id uživatele. Představuje koncového uživatele aplikace. Při odeslání telemetrie ze služby, kontext uživatele je o uživateli, který inicioval operaci ve službě.

[Vzorkování](../../azure-monitor/app/sampling.md) je jedním z technik k minimalizaci množství shromážděných telemetrie. Algoritmus vzorkování se pokusí buď vzorkovat nebo out všechny korelované telemetrie. Anonymní id uživatele se používá pro generování skóre vzorkování. Takže anonymní id uživatele by měla být dostatečně náhodná hodnota. 

Použití anonymního id uživatele k uložení uživatelského jména je zneužití pole. Použijte ID ověřeného uživatele.

Maximální délka: 128


## <a name="authenticated-user-id"></a>Id ověřeného uživatele

Ověřené ID uživatele. Opak anonymního ID uživatele, toto pole představuje uživatele s popisným názvem. Vzhledem k tomu, že jeho informace PII nejsou shromažďovány ve výchozím nastavení většina SDK.

Maximální délka: 1024


## <a name="account-id"></a>ID účtu

V aplikacích s více klienty se jedná o ID nebo název účtu, se kterým uživatel jedná. Příkladem může být ID předplatného pro portál Azure nebo název blogu pro blogovací platformu.

Maximální délka: 1024


## <a name="cloud-role"></a>Role cloudu

Název role, jejíž je aplikace součástí. Mapuje přímo na název role v azure. Lze také použít k rozlišení mikroslužeb, které jsou součástí jedné aplikace.

Maximální délka: 256


## <a name="cloud-role-instance"></a>Instance role cloudu

Název instance, kde je aplikace spuštěna. Název počítače pro místní, název instance pro Azure.

Maximální délka: 256


## <a name="internal-sdk-version"></a>Interní: Verze sady SDK

Verze sady SDK. Informace naleznete v [tomto článku.](https://github.com/microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/SDK-VERSIONS.md)

Maximální délka: 64


## <a name="internal-node-name"></a>Interní: Název uzlu

Toto pole představuje název uzlu používaný pro účely fakturace. Slouží k přepsání standardní detekce uzlů.

Maximální délka: 256


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [rozšířit a filtrovat telemetrii](../../azure-monitor/app/api-filtering-sampling.md).
- Viz [datový model](data-model.md) pro typy application insights a datový model.
- Podívejte se na [konfiguraci](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)kolekce standardních kontextových vlastností .
