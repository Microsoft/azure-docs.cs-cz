---
title: Vytvořit nový prostředek služby Azure Application Insights | Dokumentace Microsoftu
description: Ručně nastavte monitorování pomocí Application Insights pro nové živé aplikace.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbullwin
ms.openlocfilehash: 9da52e5a9dfa3b55431d66ed3162172226f71a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073316"
---
# <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Azure Application Insights zobrazí data o vaší aplikaci v Microsoft Azure *prostředků*. Vytváří se nový prostředek je proto součástí [nastavení Application Insights pro monitorování nové aplikace][start]. Po vytvoření nového prostředku, můžete mít svůj Instrumentační klíč a použít ke konfiguraci Application Insights SDK. Instrumentační klíč odkazuje na zdroj telemetrie.

## <a name="sign-in-to-microsoft-azure"></a>Přihlaste se k Microsoft Azure

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Přihlaste se k [webu Azure portal](https://portal.azure.com)a vytvořte prostředek Application Insights:

![Klikněte na tlačítko znaménka '+' v levém horním rohu. Vyberte nástroje pro vývojáře, za nímž následuje Application Insights](./media/create-new-resource/new-app-insights.png)

   | Nastavení        |  Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název identifikující aplikaci, kterou monitorujete. |
   | **Skupina prostředků**     | myResourceGroup      | Název nové nebo existující prostředek skupiny hostovat data App Insights. |
   | **Umístění** | USA – východ | Vyberte umístění ve vaší blízkosti nebo v blízkosti je hostitelem vaší aplikace. |

Zadejte odpovídající hodnoty do požadovaných polí a potom vyberte **revize + vytvořit**.

![Zadejte hodnoty do požadovaných polí a potom vyberte "zkontrolovat a vytvořit".](./media/create-new-resource/review-create.png)

Po vytvoření aplikace, otevře se nové podokno. Toto podokno je, kde se zobrazí data o využití a výkonu o monitorované aplikaci. 

## <a name="copy-the-instrumentation-key"></a>Zkopírujte klíč instrumentace

Instrumentační klíč identifikuje prostředek, který chcete přidružit vaše telemetrická data s. Budete potřebovat kopii do kódu vaší aplikace přidat Instrumentační klíč.

![Klikněte na tlačítko a zkopírujte klíč instrumentace](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Instalace sady SDK do vaší aplikace

Nainstalujte službu Application Insights SDK ve vaší aplikaci. Tento krok závisí do značné míry na typu aplikace.

Použijte Instrumentační klíč ke konfiguraci [sady SDK, který nainstalujete v aplikaci][start].

Sada SDK obsahuje standardní moduly, které odesílají telemetrii, aniž byste museli psát další kód. Sledovat uživatelské akce nebo diagnostikovat problémy ve více podrobností, [pomocí rozhraní API] [ api] k odesílání vlastní telemetrie.

## <a name="creating-a-resource-automatically"></a>Vytvoření prostředku automaticky
Můžete napsat [skript prostředí PowerShell](../../azure-monitor/app/powershell.md) automaticky vytvořit prostředek.

## <a name="next-steps"></a>Další postup
* [Diagnostické vyhledávání](../../azure-monitor/app/diagnostic-search.md)
* [Zkoumání metrik](../../azure-monitor/app/metrics-explorer.md)
* [Psaní analytických dotazů](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md