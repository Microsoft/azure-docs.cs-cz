---
title: Připojte se k Projectu Online z Azure Logic Apps
description: Automatizujte pracovní postupy, které sledují, vytvářejí a spravují projektové online projekty, úkoly a prostředky pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a3e90fa3e3f57c1575a7ab09f9ce6941c13adcd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83834862"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Správa projektů, úloh a prostředků Projectu Online pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru služby Project Online můžete vytvářet automatizované úlohy a pracovní postupy pro vaše projekty, úlohy a prostředky v Projectu Online prostřednictvím sady Office 365. Pracovní postupy mohou provádět tyto akce a další, například:

* Monitorování při vytváření nových projektů, úkolů nebo prostředků. Nebo sledujte, kdy se publikují nové projekty.
* Vytvořte nové projekty, úkoly nebo prostředky.
* Vypíše existující projekty nebo úkoly.
* Rezervace, vrácení se změnami nebo publikování projektů.

Project Online pomáhá plánovat, upřednostňovat a spravovat projekty a investice do portfolia projektů prakticky odkudkoli a na téměř jakémkoli zařízení tím, že poskytuje výkonné funkce řízení projektů. Můžete použít triggery služby Project Online, které získávají odpovědi z Projectu Online a zpřístupňují výstup ostatním akcím. Akce v aplikacích logiky můžete použít k provádění různých úloh v Projectu Online. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Project Online, k dispozici prostřednictvím [účtu Office 365](https://www.office.com/), 

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k datům projektu online. Pokud chcete začít s triggerem Projectu Online, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akce Projectu Online, spusťte aplikaci logiky s jinou triggerovou procedurou, například Trigger **opakování** .

## <a name="connect-to-project-online"></a>Připojit k Projectu Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Zvolit cestu: 

   * Pro prázdné aplikace logiky zadejte do vyhledávacího pole "Project Online" jako filtr. 
   V seznamu triggery vyberte aktivační událost, kterou chcete. 

     -nebo-

   * Pro existující aplikace logiky v kroku, kde chcete přidat akci, vyberte možnost **Nový krok**. Do vyhledávacího pole zadejte jako filtr "Project Online". V seznamu akce vyberte akci, kterou chcete.

1. Pokud budete vyzváni k přihlášení k Projectu Online, přihlaste se.

   Vaše přihlašovací údaje opravňují aplikaci logiky vytvořit připojení k Projectu Online a přistupovat k datům.

1. Zadejte potřebné podrobnosti pro vybraný Trigger nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](/connectors/projectonline/)konektoru.

## <a name="get-support"></a>Získání podpory

* Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)