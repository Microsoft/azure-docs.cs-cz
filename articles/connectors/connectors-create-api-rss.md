---
title: Připojení k informačním kanálům RSS z Aplikací Azure Logic
description: Automatizace úloh a pracovních postupů, které monitorují a spravují informační kanály RSS pomocí aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 3f1e092c2ff325cdcbc32c617af316d6fbe6dd74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789337"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Správa informačních kanálů RSS pomocí aplikací Azure Logic Apps

Pomocí aplikací Azure Logic Apps a konektoru RSS můžete vytvářet automatizované úlohy a pracovní postupy pro libovolný informační kanál RSS, například:

* Sledujte, kdy jsou publikovány položky informačního kanálu RSS.
* Seznam všech položek informačního kanálu RSS.

RSS (Rich Site Summary), také nazývaný Really Simple Syndication, je populární formát pro syndikaci webu a používá se pro publikování často aktualizovaného obsahu, jako jsou blogové příspěvky a novinové titulky. Mnoho vydavatelů obsahu poskytuje informační kanál RSS, takže se uživatelé mohou přihlásit k odběru tohoto obsahu. 

Můžete použít aktivační událost RSS, která získává odpovědi z informačního kanálu RSS a zpřístupňuje výstup dalším akcím. Akci RSS můžete použít ve svých aplikacích logiky k provedení úlohy s informačním kanálem RSS. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Adresa URL informačního kanálu RSS

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k informačnímu kanálu RSS. Chcete-li začít s aktivační událostí RSS, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci RSS, spusťte aplikaci logiky s jinou aktivační událostí, například aktivační událost **opakování.**

## <a name="connect-to-an-rss-feed"></a>Připojení k informačnímu kanálu RSS

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Zvolte cestu: 

   * U prázdných aplikací logiky zadejte do vyhledávacího pole jako filtr "rss". V seznamu aktivačních událostí vyberte požadovanou aktivační událost. 

     -nebo-

   * U existujících aplikací logiky v yberte v kroku, kde chcete přidat akci, **nový krok**. Při filtrování do pole hledání zadejte „rss“. V seznamu akcí vyberte požadovanou akci.

1. Poskytněte potřebné podrobnosti pro vybranou aktivační událost nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](/connectors/rss/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)