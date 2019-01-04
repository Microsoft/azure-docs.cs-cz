---
title: Azure Application Insights Telemetrie datový Model - kontextu Telemetrie | Dokumentace Microsoftu
description: Application Insights telemetrie kontextu datového modelu
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: fc2b5a5badcf38f7bc94a876f9f0a21af4f558b7
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811925"
---
# <a name="telemetry-context-application-insights-data-model"></a>Kontext telemetrická data: Datový model Application Insights

Každé položce telemetrie může obsahovat pole silného typu kontextu. Každé pole umožňuje určitého scénáře monitorování. Kolekce vlastních vlastností slouží k ukládání kontextové informace specifické pro aplikaci nebo vlastní.


## <a name="application-version"></a>Verze aplikace

Informace v polích kontext aplikace je vždy o aplikaci, která odesílá telemetrická data. Verze aplikace se používá k analýze trendů změny v chování aplikace a její spojitost s míněním nasazení.

Maximální délka: 1024


## <a name="client-ip-address"></a>IP adresa klienta

IP adresa klientského zařízení. Protokoly IPv4 a IPv6 jsou podporovány. Při odesílání telemetrických dat ze služby umístění kontext je informace o uživateli, který inicioval operaci služby. Application Insights extrahovat informace geografického umístění, z IP adresy klienta a pak ji zkrátit. IP adresa klienta samostatně proto nelze použít jako identifikovatelné informace pro koncové uživatele. 

Maximální délka: 46


## <a name="device-type"></a>Typ zařízení

Toto pole bylo původně slouží k označení typu zařízení, kterou používá koncový uživatel aplikace. Dnes používají především pro rozlišení JavaScript telemetrická data typu zařízení: prohlížeč"z telemetrických dat na straně serveru se zařízením zadejte"Počítač".

Maximální délka: 64


## <a name="operation-id"></a>ID operace

Jedinečný identifikátor kořenovou operaci. Tento identifikátor skupiny telemetrie umožňuje napříč více komponent. Zobrazit [korelace telemetrie](../../azure-monitor/app/correlation.md) podrobnosti. Id operace je vytvořen pomocí požadavku nebo zobrazení stránky. Všechny další telemetrie nastaví toto pole na hodnotu nadřazeného žádosti nebo zobrazení stránky. 

Maximální délka: 128


## <a name="parent-operation-id"></a>ID nadřazené operace

Jedinečný identifikátor položky telemetrie bezprostřední nadřazený objekt. Zobrazit [korelace telemetrie](../../azure-monitor/app/correlation.md) podrobnosti.

Maximální délka: 128


## <a name="operation-name"></a>Název operace

Název (skupina) operace. Název operace vytvoří požadavek nebo zobrazení stránky. Všechny ostatní položky telemetrie nastavte pole na hodnotu nadřazeného žádosti nebo zobrazení stránky. Název operace se používá pro vyhledání všech položkách telemetrie pro skupinu operací (například "GET Home/Index"). Tato vlastnost kontextu se používá zodpovědět otázky typu "jaké jsou typické výjimky vyvolané na této stránce."

Maximální délka: 1024


## <a name="synthetic-source-of-the-operation"></a>Zdroj syntetického operace

Název syntetické zdroje. Některé telemetrická data z aplikace může představovat syntetického provozu. Může být webový prohledávací modul indexování webu, testy dostupnosti webu nebo trasování z diagnostické knihoven, jako jsou Application Insights SDK.

Maximální délka: 1024


## <a name="session-id"></a>ID relace

ID relace – instance interakce uživatele s aplikací. Informace v polích kontextu relace je vždy o koncového uživatele. Při odesílání telemetrických dat ze služby, spočívá v kontextu relace uživatele, který inicioval operaci ve službě.

Maximální délka: 64


## <a name="anonymous-user-id"></a>Id anonymního uživatele

Id anonymního uživatele. Představuje koncový uživatel aplikace. Při odesílání telemetrických dat ze služby je kontext uživatele o uživateli, který inicioval operaci služby.

[Vzorkování](../../application-insights/app-insights-sampling.md) je jeden z postupů, chcete-li minimalizovat množství shromážděných telemetrie. Algoritmus vzorkování, pokusí se buď ukázka snížení nebo navýšení kapacity korelační telemetrická data. Id anonymního uživatele se používá pro generování skóre vzorkování. Id anonymního uživatele by tak měly být dostatečně náhodnou hodnotu. 

Uložení uživatelské jméno pomocí id anonymního uživatele je nesprávné použití pole. Pomocí id uživatele ověřený.

Maximální délka: 128


## <a name="authenticated-user-id"></a>Id ověřeného uživatele

Id ověřeného uživatele. Opak id anonymního uživatele, toto pole představuje uživatele s popisným názvem. Od jeho informace PII není shromažďují ve výchozím nastavení většina sady SDK.

Maximální délka: 1024


## <a name="account-id"></a>ID účtu

Ve víceklientských aplikacích jedná se o účet ID nebo název, který uživatel pracuje s. Příklady může být ID předplatného pro Azure portal nebo blogu název blogovací platforma.

Maximální délka: 1024


## <a name="cloud-role"></a>Role v cloudových procesech

Název role aplikace je součástí. Mapuje přímo na název role v azure. Můžete také použít k rozlišení mikroslužby, které jsou součástí jedné aplikace.

Maximální délka: 256


## <a name="cloud-role-instance"></a>Instance cloudové role

Název skupiny prostředků, ve kterém je spuštěná aplikace. Název počítače pro místní, název instance pro Azure.

Maximální délka: 256


## <a name="internal-sdk-version"></a>Interní: SDK version (Verze sady SDK)

Verze sady SDK. Zobrazit https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification informace.

Maximální délka: 64


## <a name="internal-node-name"></a>Interní: Název uzlu

Toto pole představuje název uzlu použije pro účely fakturace. Použijte k přepsání standardních detekce uzly.

Maximální délka: 256


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [rozšířit a filtrování telemetrie](../../azure-monitor/app/api-filtering-sampling.md).
- Zobrazit [datový model](data-model.md) pro typy a datový model Application Insights.
- Prohlédněte si kolekci properties standardní kontextu [konfigurace](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet).
