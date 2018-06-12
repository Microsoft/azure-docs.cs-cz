---
title: Vytvoření nového prostředku Azure Application Insights | Microsoft Docs
description: Ručně nastavte Application Insights monitorování nové aplikace za provozu.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: mbullwin
ms.openlocfilehash: 59bb8564613e9a0cebda00c2c847283ff218b882
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294713"
---
# <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights
Azure Application Insights zobrazí data o vaší aplikaci v Microsoft Azure *prostředků*. Vytvoření nového prostředku, proto je součástí [nastavení Application Insights pro monitorování nové aplikace][start]. V mnoha případech vytvoření prostředku můžete provést automaticky rozhraní IDE. Ale v některých případech můžete vytvořit prostředek ručně – například mít samostatné prostředky pro vývoj a produkční sestavení vaší aplikace.

Po vytvoření prostředku, můžete získat svůj klíč instrumentace a použít ke konfiguraci SDK v aplikaci. Klíč prostředku propojí telemetrie do prostředku.

## <a name="sign-up-to-microsoft-azure"></a>Zaregistrujte si Microsoft Azure
Pokud jste to ještě získali [Microsoft account, pořiďte si ho](http://live.com). (Pokud používáte služby jako Outlook.com, OneDrive, Windows Phone nebo XBox Live, už máte účet Microsoft.)

Potřebujete předplatné [Microsoft Azure](http://azure.com). Pokud váš tým nebo společnost má předplatné Azure, vlastník můžete přidat můžete, pomocí účtu Windows Live ID. Se účtují poplatky se používá. Základní plán výchozí umožňuje určité množství experimentální použití zdarma.

Pokud máte k dispozici přístup do předplatného, přihlaste se k Application Insights na [ http://portal.azure.com ](https://portal.azure.com)a používat svůj Live ID k přihlášení.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights
V [portal.azure.com](https://portal.azure.com), přidejte prostředek Application Insights:

![Klikněte na tlačítko Nový, Application Insights](./media/app-insights-create-new-resource/01-new.png)

* **Typ aplikace** ovlivňuje, co se zobrazí v okně Přehled a vlastnosti, které jsou k dispozici v [metriky explorer][metrics]. Pokud nevidíte vašeho typu aplikace, vyberte Obecné.
* **Předplatné** je váš účet platebních v Azure.
* **Skupina prostředků** je užitečný pro správu vlastnosti jako řízení přístupu. Pokud jste již vytvořili ostatní prostředky služby Azure, je možné uvést tento nový prostředek do stejné skupiny.
* **Umístění** je, kde společnost Microsoft uchovávat data.
* **Připnout na řídicí panel** vloží dlaždici rychlý přístup pro prostředek na Azure domovské stránky. Nedoporučuje.

Po vytvoření aplikace, otevře se nové okno. Toto okno je, kde uvidíte data o využití a výkonu o vaší aplikaci. 

Chcete-li se vrátit k němu při příštím přihlášení do Azure, vyhledejte dlaždice úvodní vaší aplikace na Tabule start (domovskou obrazovku). Nebo klikněte na tlačítko Procházet a najít.

## <a name="copy-the-instrumentation-key"></a>Zkopírovat klíč instrumentace
Klíč instrumentace identifikuje prostředek, který jste vytvořili. Je nutné ji poskytnout k sadě SDK.

![Klikněte na tlačítko Essentials, klikněte na klíč instrumentace, CTRL + C](./media/app-insights-create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Instalace sady SDK v aplikaci
Nainstalujte službu Application Insights SDK ve vaší aplikaci. Tento krok výraznou závisí na typu aplikace. 

Klíč instrumentace použít ke konfiguraci [sady SDK, který nainstalujete v aplikaci][start].

Sada SDK zahrnuje standardní moduly, které odesílat telemetrická data bez nutnosti psaní jakéhokoli kódu. Sledování akcí uživatele nebo diagnostikovat problémy podrobněji, [použít rozhraní API] [ api] k odeslání vlastní telemetrie.

## <a name="monitor"></a>Zobrazit data telemetrie
Zavřete okno rychlý start se vraťte do okna vaší aplikací na portálu Azure.

Kliknutím na dlaždici hledání najdete v části [diagnostické vyhledávání][diagnostic], kde se zobrazí první události. 

Pokud očekáváte více dat, klikněte na tlačítko **aktualizovat** za několik sekund.

## <a name="creating-a-resource-automatically"></a>Vytvoření prostředku automaticky
Můžete napsat [skript prostředí PowerShell](app-insights-powershell.md) automaticky vytvoření prostředku.

## <a name="next-steps"></a>Další postup
* [Vytvoření řídicího panelu](app-insights-dashboards.md)
* [Diagnostické vyhledávání](app-insights-diagnostic-search.md)
* [Zkoumání metrik](app-insights-metrics-explorer.md)
* [Psaní analytických dotazů](app-insights-analytics.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md

