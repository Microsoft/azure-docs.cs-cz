---
title: Připojení k Trello z Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které sledují a spravují seznamy, panely a karty v projektech Trello pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: d1426bbe2064067b26f53a8f8e5d4aa842ede8c0
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829694"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Monitorování a Správa Trello pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru Trello můžete vytvářet automatizované úlohy a pracovní postupy, které sledují a spravují seznamy Trello, karty, panely, členy týmu atd. například:

* Monitorování při přidání nových karet do panelů a seznamů 
* Vytváření, získávání a Správa desek, karet a seznamů.
* Přidejte komentáře a členy do karet.
* Seznam panelů, štítků na panelu, karet na panelech, komentářů karet, členů karty, členů týmu a týmů, ve kterých jste členem. 
* Získejte týmy.

Můžete použít triggery, které získávají odpovědi z vašeho účtu Trello, a zpřístupnit výstup ostatním akcím. Můžete použít akce, které provádějí úlohy s účtem Trello. Můžete také použít jiné akce výstup z akcí Trello. Například když je přidána nová karta do panelu nebo seznamu, můžete odeslat zprávy pomocí konektoru časové rezervy. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Účet Trello a přihlašovací údaje uživatele

  Vaše přihlašovací údaje autorizují vaši aplikaci logiky, aby vytvořila připojení a měl přístup k vašemu účtu Trello.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k účtu Trello. Pokud chcete začít s triggerem Trello, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci Trello, spusťte aplikaci logiky s triggerem, například triggerem **opakování** .

## <a name="connect-to-trello"></a>Připojení k Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Pro prázdné Logic Apps do vyhledávacího pole zadejte "Trello" jako filtr. V seznamu triggery vyberte aktivační událost, kterou chcete. 

   -nebo-

   Pro existující aplikace logiky v rámci posledního kroku, kam chcete přidat akci, vyberte možnost **Nový krok**. 
   Do vyhledávacího pole zadejte "Trello" jako filtr. 
   V seznamu akce vyberte akci, kterou chcete.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. 
   Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. Pokud budete vyzváni k přihlášení k Trello, autorizujte přístup pro vaši aplikaci logiky a přihlaste se.

1. Zadejte potřebné podrobnosti pro vybraný Trigger nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](/connectors/trello/)konektoru.

## <a name="get-support"></a>Získat podporu

* Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)