---
title: Připojení k Twilio z Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které spravují globální zprávy SMS, MMS a IP prostřednictvím vašeho účtu Twilio pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: e5b218efd9c8cfaad99d76d8118d181390a977c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789099"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Správa zpráv v Twiliu pomocí Aplikací Azure Logic Apps

Pomocí aplikací Azure Logic Apps a konektoru Twilio můžete vytvářet automatizované úlohy a pracovní postupy, které v Twiliu získají, odešlou a zobrazí seznam zpráv, které zahrnují globální SMS, MMS a IP zprávy. Tyto akce můžete použít k provádění úkolů s účtem Twilio. Můžete také mít jiné akce použít výstup z akcí Twilio. Například při příchodu nové zprávy můžete odeslat obsah zprávy s konektorem Slack. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Pochází z [Twilio](https://www.twilio.com/): 

  * ID účtu Twilio a [ověřovací token](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), které najdete na řídicím panelu Twilio

    Vaše přihlašovací údaje autorizují aplikaci logiky k vytvoření připojení a přístupu k účtu Twilio z aplikace logiky. 
    Pokud používáte zkušební účet Twilio, můžete posílat SMS pouze na *ověřená* telefonní čísla.

  * Ověřené telefonní číslo Twilio, které může posílat SMS

  * Ověřené telefonní číslo Twilio, které může přijímat SMS

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k účtu Twilio. Chcete-li použít akci Twilio, spusťte aplikaci logiky s jinou aktivační událostí, například aktivační událost **opakování.**

## <a name="connect-to-twilio"></a>Připojení k Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Zvolte cestu: 

     * V posledním kroku, kam chcete přidat akci, zvolte **Nový krok**. 

       -nebo-

     * Mezi kroky, kam chcete přidat akci, přesuňte ukazatel myši na šipku mezi kroky. 
     Zvolte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.
     
       Do vyhledávacího pole zadejte jako filtr "twilio". 
       V seznamu akcí vyberte požadovanou akci.

1. Zadejte potřebné podrobnosti pro připojení a pak zvolte **Vytvořit**:

   * Název, který chcete použít pro připojení
   * ID vašeho účtu Twilio 
   * Váš přístupový (ověřovací) token Twilio

1. Poskytněte potřebné podrobnosti pro vybranou akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](/connectors/twilio/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)