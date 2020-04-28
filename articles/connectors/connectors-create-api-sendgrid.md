---
title: Připojení k SendGrid z Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které odesílají e-maily a spravují poštovní seznamy v SendGrid pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 998020c5e39c8d50e8a14c74c43b7b435752f43d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789303"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Posílání e-mailů a Správa poštovních seznamů v SendGrid pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru SendGrid můžete vytvářet automatizované úlohy a pracovní postupy, které odesílají e-maily a spravují seznamy příjemců, například:

* Odeslat e-mail.
* Přidejte příjemce do seznamů.
* Získejte, přidejte a spravujte globální potlačení.

K provedení těchto úloh můžete použít akce SendGrid ve svých aplikacích logiky. Můžete také použít jiné akce výstup z akcí SendGrid. 

Tento konektor nabízí jenom akce, takže můžete spustit aplikaci logiky pomocí samostatné triggeru, jako je například Trigger **opakování** . Pokud například do seznamů často přidáte příjemce, můžete odeslat e-maily o příjemci a seznamech pomocí konektoru Office 365 Outlook Connector nebo konektoru Outlook.com.
Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* [Účet SendGrid](https://www.sendgrid.com/) a [klíč rozhraní SendGrid API](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Klíč rozhraní API autorizuje vaši aplikaci logiky, aby vytvořila připojení a měl přístup k vašemu účtu SendGrid.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k účtu SendGrid. Pokud chcete použít akci SendGrid, spusťte aplikaci logiky s jinou triggerovou procedurou, například Trigger **opakování** .

## <a name="connect-to-sendgrid"></a>Připojit se k SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Zvolit cestu: 

   * V posledním kroku, kam chcete přidat akci, vyberte možnost **Nový krok**. 

     -nebo-

   * Mezi kroky, do kterých chcete přidat akci, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. 
   Vyberte symbol plus (**+**), který se zobrazí, a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte "SendGrid" jako filtr. V seznamu akce vyberte akci, kterou chcete.

1. Zadejte název připojení. 

1. Zadejte svůj klíč rozhraní API SendGrid a pak zvolte **vytvořit**.

1. Zadejte potřebné podrobnosti pro vybranou akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](/connectors/sendgrid/)konektoru.

## <a name="get-support"></a>Získání podpory

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)