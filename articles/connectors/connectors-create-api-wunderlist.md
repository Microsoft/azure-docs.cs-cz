---
title: Připojení k Wunderlistu z Azure Logic Apps
description: Automatizujte úlohy a pracovní postupy, které sledují a spravují seznamy, úkoly, připomenutí a další v účtu Wunderlistu pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5ac13595bd77238aaede5fa3bdc3a35ef69e8504
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789116"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Monitorování a Správa Wunderlistu pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru Wunderlistu můžete vytvářet automatizované úlohy a pracovní postupy, které sledují a spravují seznamy todo, úkoly, připomenutí a další v účtu Wunderlistu, a to i další akce, třeba:

* Sledujte, kdy se nové úlohy vytvoří, když jsou úkoly splatné nebo když dojde k připomenutí.
* Vytvářejte a spravujte seznamy, poznámky, úkoly, dílčí úkoly a další.
* Nastavte připomenutí.
* Získat seznamy, úkoly, dílčí úkoly, připomenutí, soubory, poznámky, komentáře a další.

[Wunderlistu](https://www.wunderlist.com/) je služba, která vám pomůže plánovat, spravovat a dokončí vaše projekty, seznamy úkolů a úkoly – na jakémkoli zařízení, kdekoli. Můžete použít triggery, které získávají odpovědi z vašeho účtu Wunderlistu, a zpřístupnit výstup ostatním akcím. Můžete použít akce, které provádějí úlohy s účtem Wunderlistu. Můžete také použít jiné akce výstup z akcí Wunderlistu. Například když jsou nové úlohy splatné, můžete zprávy odesílat pomocí konektoru časové rezervy. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Účet Wunderlistu a přihlašovací údaje uživatele

   Vaše přihlašovací údaje autorizují vaši aplikaci logiky, aby vytvořila připojení a měl přístup k vašemu účtu Wunderlistu.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete získat přístup k vašemu účtu Yammer. Pokud chcete začít s triggerem Wunderlistu, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci Wunderlistu, spusťte aplikaci logiky s jinou triggerovou procedurou, například Trigger **opakování** .

## <a name="connect-to-wunderlist"></a>Připojení k Wunderlistu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Zvolit cestu: 

   * Pro prázdné Logic Apps do vyhledávacího pole zadejte "wunderlistu" jako filtr. 
   V seznamu triggery vyberte aktivační událost, kterou chcete. 

     -nebo-

   * Pro existující aplikace logiky: 
   
     * V posledním kroku, kam chcete přidat akci, vyberte možnost **Nový krok**. 

       -nebo-

     * Mezi kroky, do kterých chcete přidat akci, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. 
     Vyberte symbol plus (**+**), který se zobrazí, a pak vyberte **přidat akci**.
     
       Do vyhledávacího pole zadejte "wunderlistu" jako filtr. 
       V seznamu akce vyberte akci, kterou chcete.

1. Pokud se zobrazí výzva, abyste se přihlásili k Wunderlistu, přihlaste se hned, abyste mohli přístup umožnit.

1. Zadejte potřebné podrobnosti pro vybraný Trigger nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](/connectors/wunderlist/)konektoru.

## <a name="get-support"></a>Získání podpory

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)