---
title: Připojení k informačním kanálům RSS z Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovní postupy monitorování a Správa informačních kanálů RSS s využitím Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: c8d1122572764dda1fc550a06ae254109e3bf033
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885794"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Správa informačních kanálů RSS s využitím Azure Logic Apps

S Azure Logic Apps a konektor RSS můžete vytvořit automatizovaných úloh a pracovních postupů pro jakékoli RSS kanálu, například:

* Monitorování při publikování položky informačního kanálu RSS.
* Zobrazí všechny položky informačního kanálu RSS.

RSS (Rich Site Summary), také nazývané Really Simple Syndication, je známý formát pro web syndikace a slouží k publikování pravidelně aktualizovaného obsahu, jako jsou příspěvky blogu a titulky. Mnoho vydavatelů obsahu poskytuje informačního kanálu, uživatelé se mohou přihlásit do tohoto obsahu. 

Můžete použít aktivační událost RSS, který získává odpovědi z informačního kanálu RSS a zpřístupní výstup do jiné akce. Akce RSS ve svých aplikacích logiky můžete použít k provádění úlohy s informačním kanálu RSS. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Adresa URL informačního kanálu RSS

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k RSS informačního kanálu. Spuštění pomocí aktivační událost RSS [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci RSS, spusťte svou aplikaci logiky s další aktivační události, například, **opakování** aktivační události.

## <a name="connect-to-an-rss-feed"></a>Připojte se k informačním kanálu RSS

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. Zvolte cestu: 

   * V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "rss". V seznamu triggerů vyberte trigger, který chcete. 

     -nebo-

   * Pro existující aplikace logiky, v části krok, ve které chcete přidat akci, zvolte **nový krok**. Při filtrování do pole hledání zadejte „rss“. V seznamu akcí vyberte požadovanou akci.

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/rss/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)