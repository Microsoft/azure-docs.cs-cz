---
title: Přehled služby Azure bez serveru | Dokumentace Microsoftu
description: Další informace o vytváření výkonných řešení v cloudu bez starostí o infrastrukturu
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 97c928c34a18a5d4f3549c348a273df268ee1db0
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123304"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>Přehled: Azure s Azure Logic Apps a Azure Functions bez serveru

Aplikace bez serveru nabízí výhody nárůst rychlosti vývoje, snížení v kódu a jednoduchost měřítku.  Tento článek probírá do jiné atributy řešení bez serveru a nabídky Azure bez serveru.

## <a name="what-is-serverless"></a>Co je bez serveru?

Bez serveru, neznamená nejsou žádné servery – to pouze znamená, že nemá vývojář se starat o servery.  Velkou část tradičním vývoji aplikace je odpovídání na dotazy týkající se škálování, hostování a sledování řešení, které splňují požadavky aplikace.  S bez serveru tyto otázky se postaral o jako součást řešení.  Kromě toho aplikace bez serveru účtuje se plán založený na spotřebě.  Pokud aplikace se nikdy nepoužívali, poplatky se účtují nikdy.  Tyto funkce umožňují vývojářům soustředit se výlučně na obchodní logiku řešení.

Základní služby v Azure kolem bez serveru jsou [Azure Functions](https://azure.microsoft.com/services/functions/) a [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).  Obě tato řešení postupujte podle výše uvedených zásad a umožňuje vývojářům vytvářet robustní cloudové aplikace s minimem kódu.

## <a name="what-are-azure-functions"></a>Co jsou Azure Functions?

Azure Functions je řešení umožňující snadno spouštět malé kódy (tzv. „funkce“) v cloudu. Můžete napsat přesně takový kód, jaký potřebujete pro aktuální problém, a nestarat se o infrastrukturu k jeho spuštění nebo aplikaci jako celek. Funkce můžete ještě větší produktivitu vývoje, a můžete použít vývoj jazyk podle vlastní volby, například C#, F #, Node.js, Python nebo PHP. Plaťte jen za čas, kdy se spustí váš kód a Azure škáluje podle potřeby.

Pokud chcete rovnou začít používat Azure Functions, začněte tématem [Vytvoření první funkce Azure](../azure-functions/functions-create-first-azure-function.md). Pokud hledáte další odborné informace o Functions, přejděte k části [referenční informace pro vývojáře](../azure-functions/functions-reference.md).

## <a name="what-are-azure-logic-apps"></a>Co jsou Azure Logic Apps?

Služba Azure Logic Apps poskytuje způsob, jak zjednodušit a implementovat škálovatelné integrace a pracovní postupy v cloudu. Poskytuje vizuálním návrháři si můžete modelovat a automatizovat procesy jako sérii kroků, které volá pracovní postup.  Existují [celou řadu konektorů](../connectors/apis-list.md) cloudových a místních služeb se rychle připojit aplikace bez serveru pro jiná rozhraní API.  Aplikace logiky začíná triggerem (například „Když je přidán účet do aplikace Dynamics CRM“) a po jeho spuštění může začít mnoho kombinací akcí, převodů a podmíněné logiky.  Logic Apps je skvělou volbou, když Orchestrace různých Azure Functions v procesu – zejména v případě, že proces vyžaduje interakci s externím systému nebo rozhraní API.

Abyste mohli začít s Logic Apps, začněte s [vytváření vaší první aplikací logiky](quickstart-create-first-logic-app-workflow.md).  Pokud hledáte další odborné informace o Logic Apps, najdete v článku [referenční informace pro vývojáře](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Jak můžete sestavovat a nasazovat aplikace bez serveru v Azure?

Azure poskytuje bohatou sadu nástrojů v rámci vývoje, nasazení a Správa aplikací bez serveru.  Aplikace se dají přímo na webu Azure portal nebo pomocí [nástrojů ze sady Visual Studio](logic-apps-serverless-get-started-vs.md).  Jakmile vyvinula aplikaci může být [okamžitě nasadit](logic-apps-create-deploy-template.md).  Azure také poskytuje monitorování pro aplikace bez serveru.  Toto monitorování je přístupný z webu Azure portal, prostřednictvím rozhraní API nebo sady SDK, nebo pomocí integrovaných nástrojů do Log Analytics a Application Insights.

## <a name="next-steps"></a>Další postup

* [Začněte vytvářet aplikace bez serveru v sadě Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Vytvořit řídicí panel přehledů o zákazníky s bez serveru](logic-apps-scenario-social-serverless.md)
* [Vytvoření šablony nasazení pro aplikaci logiky.](logic-apps-create-deploy-template.md)