---
title: Připojení k Projectu Online v Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace pracovních postupů, které monitorování, vytvářet a spravovat projekty Projectu Online, úloh a prostředků pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 663363d05c1875d22a0ecc0478abcf7e0ec89c99
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230365"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Spravovat projekty Projectu Online, úloh a prostředků pomocí Azure Logic Apps

S Azure Logic Apps a Projectu Online connector můžete vytvořit automatizovaných úloh a pracovních postupů pro projekty, úkoly a prostředky v Projectu Online prostřednictvím Office 365. Vaše pracovní postupy můžete provést tyto akce a další, například:

* Monitorování při vytváření nových projektů, úkolů nebo prostředky. Nebo sledování, se publikují nové projekty.
* Vytvořte nové projekty, úkoly nebo prostředky.
* Seznam existujících projektů nebo úlohy.
* Podívejte se, vrátit se změnami nebo publikovat projekty.

Project Online umožňuje plánovat, určit prioritu a správa projektů a investic do portfolia projektů z téměř odkudkoli na téměř jakémkoli zařízení tím, že poskytuje možnosti správy výkonné projektu. Můžete použít Projectu Online aktivačních událostí, které odpovědi z Projectu Online a zpřístupnit výstup dalších akcí. Akce ve svých aplikacích logiky můžete provádět různé úlohy v Projectu Online. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Project Online, dostupná prostřednictvím [účet Office 365](https://www.office.com/), 

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přistupovat ke svým datům Projectu Online. Spustit s triggerem Projectu Online [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít Projectu Online akce, spusťte svou aplikaci logiky s další aktivační události, například, **opakování** aktivační události.

## <a name="connect-to-project-online"></a>Připojte se k Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. Zvolte cestu: 

   * V případě prázdné logic apps do vyhledávacího pole zadejte "Projectu Online" jako filtr. 
   V seznamu triggerů vyberte trigger, který chcete. 

     -nebo-

   * Pro existující aplikace logiky, v části krok, ve které chcete přidat akci, zvolte **nový krok**. Do vyhledávacího pole zadejte jako filtr "Projectu Online". V seznamu akcí vyberte požadovanou akci.

1. Pokud se zobrazí výzva k přihlášení k Projectu Online, přihlaste se hned.

   Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení k Projectu Online a přístup k vašim datům.

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/projectonline/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)