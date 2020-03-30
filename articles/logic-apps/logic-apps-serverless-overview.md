---
title: Přehled – Azure Serverless pro cloudové aplikace a řešení
description: Naučte se vytvářet cloudové aplikace a řešení bez obav o infrastrukturu pomocí Azure Logic Apps a Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 0f20bb5fb249ad6bac862afe2b0e8eee4b32e2a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666546"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure Serverless: Přehled pro vytváření cloudových aplikací a řešení s Azure Logic Apps a Funkce Azure

[Aplikace bez serveru](https://azure.microsoft.com/solutions/serverless/) nabízejí výhody, jako je zvýšená rychlost vývoje, snížený kód, jednoduchost a škálování. Tento článek popisuje různé atributy bezserverových řešení a nabídky Azure bez serveru.

## <a name="what-is-serverless"></a>Co je bez serveru?

Serverless neznamená, že neexistují žádné servery, ale spíše vývojáři se nemusí starat o servery. Velká část vývoje tradičních aplikací odpovídá na otázky týkající se škálování, hostování a monitorování řešení tak, aby splňovaly požadavky aplikace. S bez serveru, tyto otázky jsou postaráno jako součást řešení. Kromě toho se aplikace bez serveru účtují podle plánu založeného na spotřebě. Pokud se aplikace nikdy nepoužívá, nevzniká žádný poplatek. Tyto funkce pomáhají vývojářům soustředit se výhradně na obchodní logiku řešení.

Základní služby Azure pro server bez jsou [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) a Azure [Functions](https://azure.microsoft.com/services/functions/). Obě řešení se řídí dříve popsanými zásadami a pomáhají vývojářům vytvářet robustní cloudové aplikace s minimálním kódem.

## <a name="what-is-azure-logic-apps"></a>Co je Azure Logic Apps?

[Azure Logic Apps](logic-apps-overview.md) poskytuje způsob, jak zjednodušit a implementovat škálovatelné integrace a pracovní postupy v cloudu. Tato služba poskytuje vizuální návrhář emodelovat a automatizovat proces jako řadu kroků nazývaných pracovní postup. Existuje mnoho [konektorů](../connectors/apis-list.md) napříč cloudovými službami a místními systémy, které rychle připojují aplikaci bez serveru k jiným rozhraním API. Každá aplikace logiky začíná aktivační událostí, například "Když je účet přidán do aplikace Dynamics CRM". Po spuštění aktivační události může pracovní postup spustit kombinace akcí, převodů a podmíněné logiky. Logic Apps je skvělá volba při orchestraci různých funkcí Azure v procesu, zejména v případě, že proces vyžaduje interakci s externím systémem nebo rozhraní API.

Začněte s logic apps, začněte s [vytvářením první aplikace logiky](quickstart-create-first-logic-app-workflow.md). Další technické informace o logic aplikacích naleznete v [odkazu pro vývojáře](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>Co je služba Azure Functions?

Azure Functions je služba pro snadné spuštění částí kódu nebo "funkcí" v cloudu. Můžete napsat pouze kód potřebný pro aktuální problém, bez obav o celou aplikaci nebo požadovanou infrastrukturu. Funkce mohou vývoj ještě produktivnější a můžete použít svůj vývojový jazyk, který si vyberete, například C#, F#, Node.js, Python nebo PHP. Platíte jenom za dobu, po kterou se váš kód spustí a Azure se podle potřeby škáluje.

Pokud chcete začít s Funkcemi Azure, [začněte](../azure-functions/functions-create-first-azure-function.md)s create your first Azure Function . Další technické informace o funkcích naleznete v [odkazu pro vývojáře](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Jak můžu v Azure vytvářet a nasazovat aplikace bez serveru?

Azure poskytuje bohaté nástroje pro vývoj, nasazování a správu aplikací bez serveru. Aplikace můžete vytvářet přímo na webu Azure Portal, pomocí [nástrojů ve Visual Studiu](logic-apps-serverless-get-started-vs.md)nebo visual studia [.](quickstart-create-logic-apps-visual-studio-code.md) Po vytvoření aplikace můžete [tuto aplikaci rychle nasadit pomocí šablon Azure Resource Manager .](logic-apps-deploy-azure-resource-manager-templates.md) Azure také poskytuje monitorování, ke kterému máte přístup prostřednictvím portálu Azure, prostřednictvím rozhraní API nebo sad SDK nebo s integrovanými nástroji pro protokoly Azure Monitor a Application Insights.

## <a name="next-steps"></a>Další kroky

* [Vytvoření aplikace bez serveru ve Visual Studiu](logic-apps-serverless-get-started-vs.md)
* [Vytvoření řídicího panelu přehledů zákazníků bez serveru](logic-apps-scenario-social-serverless.md)
* [Automatizace nasazení aplikace logiky](logic-apps-azure-resource-manager-templates-overview.md)
