---
title: Připojení k Sendgridu v Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovních postupů, které odesílání e-mailů a spravovat seznamy adresátů SendGrid pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: c8747210a77879d551e323a7c0e46a9ab013fa3f
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887185"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Odeslání e-mailů a spravovat seznamy adresátů SendGrid pomocí Azure Logic Apps

S Azure Logic Apps a konektor SendGrid můžete vytvořit automatizovaných úloh a pracovních postupů, které odesílání e-mailů a spravovat vaše seznamy příjemců, například:

* Odeslání e-mailu.
* Přidáte příjemce do seznamu.
* Získat, přidávat a spravovat globálního potlačení.

SendGrid akce ve službě logic apps můžete použít k provedení těchto úloh. Také můžete mít další akce pomocí výstupu z akce SendGrid. 

Tento konektor zajišťuje jenom akce, takže ke spuštění aplikace logiky použít samostatné aktivační událost, například **opakování** aktivační události. Pokud pravidelně přidat příjemce do seznamů, můžete například odeslat e-mailu o příjemci a seznamy pomocí konektoru Office 365 Outlook nebo konektor Outlook.com.
Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* A [účtu SendGrid](https://www.sendgrid.com/) a [klíč rozhraní API SendGrid](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Klíč rozhraní API povolí aplikace logiky k vytvoření připojení a přístup k vašemu účtu SendGrid.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k vašemu účtu SendGrid. Použít akci SendGrid, spuštění aplikace logiky s jinou aktivační událost, například, **opakování** aktivační události.

## <a name="connect-to-sendgrid"></a>Připojení k Sendgridu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. Zvolte cestu: 

   * V posledním kroku, ve které chcete přidat akci, zvolte **nový krok**. 

     -nebo-

   * Mezi kroky, ve které chcete přidat akci přesuňte ukazatel nad šipku mezi kroky. 
   Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "sendgrid". V seznamu akcí vyberte požadovanou akci.

1. Zadejte název připojení. 

1. Zadejte klíč rozhraní API SendGrid a klikněte na tlačítko **vytvořit**.

1. Zadejte potřebné podrobnosti pro vybranou akci a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/sendgrid/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)