---
title: Připojení k informačním kanálům RSS z Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které sledují a spravují kanály RSS pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 3f1e092c2ff325cdcbc32c617af316d6fbe6dd74
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789337"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Správa kanálů RSS pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru RSS můžete vytvářet automatizované úlohy a pracovní postupy pro všechny kanály RSS, například:

* Monitorování při publikování položek informačního kanálu RSS.
* Vypíše všechny položky informačního kanálu RSS.

RSS (Rich web Summary), označované taky jako Really Simple Syndication, je oblíbený formát pro webovou syndikaci a používá se k publikování často aktualizovaného obsahu, jako jsou blogové příspěvky a titulky zpráv. Mnoho vydavatelů obsahu poskytuje informační kanál RSS, aby se uživatelé mohli přihlásit k odběru tohoto obsahu. 

Můžete použít Trigger RSS, který získá odpovědi z informačního kanálu RSS a zpřístupní výstup ostatním akcím. Pomocí akce RSS v aplikacích logiky můžete provést úlohu s informačním kanálem RSS. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Adresa URL informačního kanálu RSS

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k informačnímu kanálu RSS. Pokud chcete začít s triggerem RSS, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci RSS, spusťte aplikaci logiky s jinou triggerovou procedurou, například Trigger **opakování** .

## <a name="connect-to-an-rss-feed"></a>Připojení k informačnímu kanálu RSS

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Zvolit cestu: 

   * Pro prázdné aplikace logiky zadejte do vyhledávacího pole "RSS" jako filtr. V seznamu triggery vyberte aktivační událost, kterou chcete. 

     -nebo-

   * Pro existující aplikace logiky v kroku, kde chcete přidat akci, vyberte možnost **Nový krok**. Při filtrování do pole hledání zadejte „rss“. V seznamu akce vyberte akci, kterou chcete.

1. Zadejte potřebné podrobnosti pro vybraný Trigger nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](/connectors/rss/)konektoru.

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)