---
title: Přehled služby Azure bez serveru | Microsoft Docs
description: Vytvořte výkonné řešení v cloudu, bez nutnosti vezměte v úvahu infrastruktury.
keywords: ''
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 30c7dd36efca5ffcae770fe310a43a107d9136af
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="overview-of-azure-serverless-with-functions-and-logic-apps"></a>Přehled služby Azure bez serveru s funkcí a Logic Apps

Aplikace bez serveru nabízejí výhod zvýšení rychlosti vývoj, snížení kódu a jednoduchost s měřítkem.  Tento článek přejde do různé atributy bez serveru řešení a Azure bez serveru nabídky.

## <a name="what-is-serverless"></a>Co je bez serveru?

Bez serveru neznamená nejsou žádné servery – stačí znamená to, že vývojář nemá starat o serverech.  Velkou část vývoj tradiční aplikací je odpovědi na otázky ohledně škálování, hostování a monitorování řešení, aby vyhovovalo požadavkům aplikace.  S bez serveru tyto otázky se postarat jako součást řešení.  Kromě toho se účtují bez serveru aplikace na plán na základě spotřeby.  Pokud aplikace je nikdy nepoužívali, započítány jsou nikdy zpoplatněny.  Tyto funkce umožňují vývojářům soustředit se výlučně na obchodní logiku řešení.

Základní služby v Azure kolem bez serveru jsou [Azure Functions](https://azure.microsoft.com/services/functions/) a [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).  Obě tato řešení podle zásad výše a umožňují vývojářům vytvářet aplikace robustní cloudu s minimálními kódem.

## <a name="what-are-azure-functions"></a>Co je Azure Functions?

Azure Functions je řešení umožňující snadno spouštět malé kódy (tzv. „funkce“) v cloudu. Můžete napsat přesně takový kód, jaký potřebujete pro aktuální problém, a nestarat se o infrastrukturu k jeho spuštění nebo aplikaci jako celek. Funkce provádět vývoj i zvýšit produktivitu a můžete použít svůj vývoj jazyk podle vlastní volby, například C#, F #, Node.js, Python nebo PHP. Platí se pouze za době spuštění kódu a Azure škáluje podle potřeby.

Pokud chcete rovnou začít používat Azure Functions, začněte tématem [Vytvoření první funkce Azure](../azure-functions/functions-create-first-azure-function.md). Pokud hledáte další odborné informace o Functions, přejděte k části [referenční informace pro vývojáře](../azure-functions/functions-reference.md).

## <a name="what-are-azure-logic-apps"></a>Jaké jsou Azure Logic Apps?

Azure Logic Apps poskytuje způsob, jak zjednodušit a implementovat škálovatelné integrace a pracovních postupů v cloudu. Poskytuje vizuální návrhář modelu a automatizaci jako řadu kroků volat pracovní postup.  Existují [mnoho konektory](../connectors/apis-list.md) přes cloud a místní služby rychle připojení bez serveru aplikace pro jiná rozhraní API.  Aplikace logiky začíná triggerem (například „Když je přidán účet do aplikace Dynamics CRM“) a po jeho spuštění může začít mnoho kombinací akcí, převodů a podmíněné logiky.  Služba Logic Apps je je služba skvělou volbou při různých Azure Functions v procesu - Orchestrace, zejména v případě, že proces vyžaduje interakci s externím systému nebo rozhraní API.

Chcete-li začít s Logic Apps, můžete začít s [vytvoření první aplikace logiky](quickstart-create-first-logic-app-workflow.md).  Pokud hledáte další odborné informace o Logic Apps, přečtěte si téma [referenční informace pro vývojáře](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Jak můžete vytvořit a nasadit bez serveru aplikace v Azure?

Azure poskytuje bohatou sadu nástrojů pro vývoj, nasazení a správu aplikací bez serveru.  Aplikace se dají vytvářet přímo na portálu Azure nebo s [tooling ze sady Visual Studio](logic-apps-serverless-get-started-vs.md).  Jakmile aplikace byla vytvořena, může být [nasadit okamžitě](logic-apps-create-deploy-template.md).  Azure poskytuje také monitorování pro aplikace, bez serveru.  Toto monitorování je přístupná z portálu Azure, prostřednictvím rozhraní API nebo sady SDK nebo pomocí integrovaného nástroje analýzy protokolů a Application Insights.

## <a name="next-steps"></a>Další postup

* [Začínáme sestavení bez serveru aplikace v sadě Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Vytvořte zákazníka přehledný řídicí panel s bez serveru](logic-apps-scenario-social-serverless.md)
* [Vytvoření šablony nasazení pro aplikaci logiky](logic-apps-create-deploy-template.md)