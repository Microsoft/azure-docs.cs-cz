---
title: Aplikace Azure Statistika Telemetrie datový Model - kontextu Telemetrie | Microsoft Docs
description: Application Insights telemetrie kontextu datový model
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/15/2017
ms.author: sergkanz
ms.openlocfilehash: 942a4bc09e632438e4a85280d723c72c4b09a015
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="telemetry-context-application-insights-data-model"></a>Kontext telemetrie: Application Insights datový model

Každá položka telemetrie může mít pole silného typu kontextu. Každé pole umožňuje určitého scénáře monitorování. Kolekce vlastních vlastností slouží k ukládání kontextové informace specifické pro aplikaci nebo vlastní.


## <a name="application-version"></a>Verze aplikace

Informace v kontextu pole aplikace je vždy o aplikaci, která odesílá telemetrii. Verze aplikace se používá k analýze trendů změny v chování aplikace a její korelace k nasazení.

Maximální délka: 1024


## <a name="client-ip-address"></a>IP adresa klienta

IP adresa klientského zařízení. IPv4 a IPv6 jsou podporovány. Při odesílání telemetrických dat ze služby umístění kontextu je o uživatele, který spustil operace ve službě. Application Insights extrahovat informace geografického umístění, z IP adresy klienta a pak ho zkrátit. IP adresa klienta samostatně proto nelze použít jako osobní údaje koncového uživatele. 

Maximální délka: 46


## <a name="device-type"></a>Typ zařízení

Toto pole byl původně slouží k určení typu zařízení, kterou používá koncový uživatel aplikace. Dnes používaná primárně k rozlišení telemetrie JavaScript s typem zařízení, prohlížeč, z telemetrických dat na straně serveru se zařízením zadejte "Počítač".

Maximální délka: 64


## <a name="operation-id"></a>Id operace

Jedinečný identifikátor kořenové operace. Tento identifikátor skupiny telemetrie umožňuje napříč více součástí. V tématu [telemetrie korelace](application-insights-correlation.md) podrobnosti. Id operace vytvoří žádost o nebo zobrazení stránky. Všechny další telemetrií nastaví toto pole na hodnotu obsahující zobrazení požadavku nebo stránky. 

Maximální délka: 128


## <a name="parent-operation-id"></a>ID nadřazené operace

Jedinečný identifikátor okamžitou nadřazené položce telemetrie. V tématu [telemetrie korelace](application-insights-correlation.md) podrobnosti.

Maximální délka: 128


## <a name="operation-name"></a>Název operace

Název (skupina) operaci. Název operace vytvoří žádost o nebo zobrazení stránky. Všechny ostatní položky telemetrie nastavte pole na hodnotu obsahující zobrazení požadavku nebo stránky. Název operace se používá pro hledání všech telemetrie položek pro skupinu operací (například "GET domovské/indexem"). Tato vlastnost kontextu se používá k odpovědi na otázky typu "jaké jsou typické výjimky vydané na této stránce."

Maximální délka: 1024


## <a name="synthetic-source-of-the-operation"></a>Syntetické zdroj operace

Název syntetické zdroje. Nějaké telemetrie z aplikace může představovat syntetické provoz. To může být webový prohledávací modul indexování webu, testy dostupnosti webu nebo trasování z diagnostiky knihoven jako Application Insights SDK sám sebe.

Maximální délka: 1024


## <a name="session-id"></a>Id relace

ID relace - instanci interakce uživatele s aplikací. Informace v polích kontextu relace je vždy o koncového uživatele. Při odesílání telemetrických dat ze služby kontextu relace je o uživatele, který spustil operace ve službě.

Maximální délka: 64


## <a name="anonymous-user-id"></a>Id anonymní uživatele

Id anonymního uživatele. Představuje koncový uživatel aplikace. Při odesílání telemetrických dat ze služby je kontext uživatele o uživatele, který spustil operace ve službě.

[Vzorkování](app-insights-sampling.md) je jedním z techniky, chcete-li minimalizovat objem shromážděných telemetrie. Algoritmus vzorkování pokusí buď ukázka příchozí nebo odchozí korelační telemetrie. Id anonymní uživatele se používá pro generování skóre vzorkování. Takže id anonymní uživatele by mělo být dostatečně náhodná hodnota. 

Pomocí id anonymního uživatele pro uložení uživatelské jméno je zneužití pole. Pomocí id uživatele ověřený.

Maximální délka: 128


## <a name="authenticated-user-id"></a>Id ověřeného uživatele

Id ověřeného uživatele. Opak id anonymního uživatele, toto pole představuje uživatele s popisným názvem. Od jeho PII informace nejsou shromažďovány ve výchozím nastavení většina SDK.

Maximální délka: 1024


## <a name="account-id"></a>ID účtu

V aplikacích víceklientské Toto je účet ID nebo název, který funguje s uživateli. Příklady může být ID předplatného pro Azure portal nebo blog název platforma blogu.

Maximální délka: 1024


## <a name="cloud-role"></a>Role cloudu

Název role aplikace je součástí. Mapuje přímo název role v azure. Můžete také použít k rozlišení malých služeb, které jsou součástí jedné aplikace.

Maximální délka: 256


## <a name="cloud-role-instance"></a>Instance role cloudu

Název instance, kde je aplikace spuštěna. Název počítače pro místní, název instance pro Azure.

Maximální délka: 256


## <a name="internal-sdk-version"></a>Interní: Verze sady SDK

Verze sady SDK. V tématu https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification informace.

Maximální délka: 64


## <a name="internal-node-name"></a>Interní: Název uzlu

Toto pole představuje název uzlu používá pro účely fakturace. Použijte ho k přepsání standardní detekce uzlů.

Maximální délka: 256


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [rozšířit a filtrovat telemetrie](app-insights-api-filtering-sampling.md).
- V tématu [datový model](application-insights-data-model.md) Application Insights typy a data modelu.
- Podívejte se na kolekci vlastností kontextu standardní [konfigurace](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).
