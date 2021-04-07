---
title: Přehled – Azure bez serveru pro cloudové aplikace a řešení
description: Naučte se vytvářet cloudové aplikace a řešení, aniž byste se museli starat o infrastrukturu pomocí Azure Logic Apps a Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 470857883144f4bf8c21a1921afacb7ba3f5ca00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788091"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure bez serveru: Přehled vytváření cloudových aplikací a řešení s využitím Azure Logic Apps a Azure Functions

Aplikace bez [serveru](https://azure.microsoft.com/solutions/serverless/) nabízejí výhody, jako je zvýšená rychlost vývoje, snížený kód, jednoduchost a škálování. Tento článek popisuje různé atributy řešení bez serveru a nabídky bez serveru Azure.

## <a name="what-is-serverless"></a>Co je bez serveru?

Bez serveru se neznamená, že neexistují žádné servery, ale vývojáři se nemusí starat o servery. Rozsáhlá součást tradičního vývoje aplikací zodpovídá dotazy týkající se škálování, hostování a monitorování řešení pro splnění požadavků aplikace. S bez serveru se tyto otázky postarují jako součást řešení. Kromě toho se aplikace bez serveru účtují podle plánu založeného na spotřebě. Pokud se aplikace nikdy nepoužívá, nic se neúčtuje. Tyto funkce pomůžou vývojářům soustředit se výhradně na obchodní logiku řešení.

Základní služby Azure pro bez serveru jsou [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) a [Azure Functions](https://azure.microsoft.com/services/functions/). Obě řešení se řídí dříve popsanými principy a můžou vývojářům přispět k vytváření robustních cloudových aplikací s minimálním kódem.

## <a name="what-is-azure-logic-apps"></a>Co je Azure Logic Apps?

[Azure Logic Apps](logic-apps-overview.md) poskytuje způsob, jak zjednodušit a implementovat škálovatelné integrace a pracovní postupy v cloudu. Tato služba poskytuje vizuálního návrháře pro modelování a automatizaci procesu jako série kroků označovaných jako pracovní postup. Existuje mnoho [konektorů](../connectors/apis-list.md) napříč Cloud Services a místními systémy, které rychle připojují aplikace bez serveru k jiným rozhraním API. Každá aplikace logiky začíná triggerem, například "když je účet přidán do Dynamics CRM". Po aktivování triggeru může pracovní postup spustit kombinace akcí, převodů a podmíněné logiky. Logic Apps je skvělou volbou při orchestraci různých Azure Functions v procesu, zejména v případě, že proces vyžaduje interakci s externím systémem nebo rozhraním API.

Pokud chcete začít s Logic Apps, začněte [vytvořením první aplikace logiky](quickstart-create-first-logic-app-workflow.md). Další technické informace o Logic Apps najdete v [referenčních](logic-apps-workflow-definition-language.md)informacích pro vývojáře.

## <a name="what-is-azure-functions"></a>Co je Azure Functions?

Azure Functions je služba, která umožňuje snadno spouštět části kódu nebo funkce v cloudu. Můžete napsat jenom kód, který je nezbytný pro aktuální problém, aniž byste se museli starat o celou aplikaci nebo požadovanou infrastrukturu. Funkce můžou zvýšit produktivitu vývoje a můžete použít vývojářský jazyk podle vlastního výběru, jako je C#, F #, Node.js, Python nebo PHP. Platíte jenom za čas, kdy váš kód běží, a Azure podle potřeby škáluje.

Pokud chcete začít s Azure Functions, začněte s [vytvořením první funkce Azure Functions](../azure-functions/functions-get-started.md). Další technické informace o funkcích najdete v [referenčních](../azure-functions/functions-reference.md)informacích pro vývojáře.

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Jak můžu v Azure sestavovat a nasazovat aplikace bez serveru?

Azure poskytuje bohatý Nástroj pro vývoj, nasazování a správu aplikací bez serveru. Můžete vytvářet aplikace přímo v Azure Portal, pomocí [nástrojů v aplikaci Visual Studio](logic-apps-serverless-get-started-vs.md)nebo [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md). Po sestavení aplikace můžete [tuto aplikaci rychle nasadit pomocí Azure Resource Manager šablon](logic-apps-deploy-azure-resource-manager-templates.md). Azure také poskytuje monitorování, které můžete používat prostřednictvím Azure Portal, prostřednictvím rozhraní API nebo sad SDK nebo integrované nástroje pro protokoly Azure Monitor a Application Insights.

## <a name="next-steps"></a>Další kroky

* [Sestavení aplikace bez serveru v aplikaci Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Vytvoření řídicího panelu zákaznických přehledů s využitím bez serveru](logic-apps-scenario-social-serverless.md)
* [Automatizace nasazení aplikace logiky](logic-apps-azure-resource-manager-templates-overview.md)