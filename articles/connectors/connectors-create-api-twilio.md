---
title: Připojení k Twilio z Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které spravují globální zprávy SMS, MMS a IP, prostřednictvím účtu Twilio pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: d144960972f5a1b45e88cc3a0ea015925cae3b91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288148"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Správa zpráv v Twilio pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru Twilio můžete vytvářet automatizované úlohy a pracovní postupy, které získávají, odesílají a vypisovat zprávy v Twilio, které zahrnují globální zprávy SMS, MMS a IP. Tyto akce můžete použít k provádění úloh s účtem Twilio. Můžete také použít jiné akce výstup z akcí Twilio. Například při doručení nové zprávy můžete odeslat obsah zprávy pomocí konektoru časové rezervy. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Z [Twilio](https://www.twilio.com/): 

  * ID účtu Twilio a [ověřovací token](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), který najdete na řídicím panelu Twilio

    Vaše přihlašovací údaje opravňují aplikaci logiky vytvořit připojení a přistupovat k účtu Twilio z vaší aplikace logiky. 
    Pokud používáte zkušební účet Twilio, můžete server SMS odeslat jenom pro *ověřená* telefonní čísla.

  * Ověřený Twilio telefonní číslo, které může poslat SMS

  * Ověřené telefonní číslo Twilio, které může přijímat SMS

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k účtu Twilio. Pokud chcete použít akci Twilio, spusťte aplikaci logiky s jinou triggerovou procedurou, například Trigger **opakování** .

## <a name="connect-to-twilio"></a>Připojení k Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Zvolit cestu: 

     * V posledním kroku, kam chcete přidat akci, vyberte možnost **Nový krok**. 

       -nebo-

     * Mezi kroky, do kterých chcete přidat akci, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. 
     Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.
     
       Do vyhledávacího pole zadejte "Twilio" jako filtr. 
       V seznamu akce vyberte akci, kterou chcete.

1. Zadejte potřebné údaje pro připojení a pak zvolte **vytvořit**:

   * Název, který se má použít pro připojení
   * ID účtu Twilio 
   * Token pro přístup k Twilio (ověřování)

1. Zadejte potřebné podrobnosti pro vybranou akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](/connectors/twilio/)konektoru.

## <a name="get-support"></a>Získání podpory

* Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
