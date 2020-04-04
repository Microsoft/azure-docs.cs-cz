---
title: Připojení ke smtp z aplikací Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které posílají e-maily prostřednictvím účtu SMTP (Simple Mail Transfer Protocol) pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 1cfc53dcd730262101c0e879e0419ba3f2db4d38
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657001"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Odesílání e-mailů z účtu SMTP pomocí aplikací Azure Logic Apps

Pomocí aplikací Azure Logic Apps a konektoru SMTP (Simple Mail Transfer Protocol) můžete vytvářet automatizované úlohy a pracovní postupy, které odesílají e-maily z vašeho účtu SMTP. Můžete také mít jiné akce použít výstup z akcí SMTP. Například poté, co váš SMTP odešle e-mail, můžete upozornit svůj tým v Slack s konektorem Slack. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Váš účet SMTP a pověření uživatele

  Vaše přihlašovací údaje autorizují aplikaci logiky k vytvoření připojení a přístupu k účtu SMTP.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k účtu SMTP. Chcete-li použít akci SMTP, spusťte aplikaci logiky s aktivační událostí, jako je například aktivační událost Salesforce, pokud máte účet Salesforce.

  Můžete například spustit aplikaci logiky s aktivační událostí **When a record is created** Salesforce. 
  Tato aktivační událost se aktivuje pokaždé, když je v salesforce vytvořen nový záznam, například zájemce. 
  Tuto aktivační událost pak můžete sledovat pomocí akce SMTP **Odeslat e-mail.** Tímto způsobem při vytvoření nového záznamu aplikace logiky odešle e-mail z vašeho účtu SMTP o nový záznam.

## <a name="connect-to-smtp"></a>Připojení ke smtp

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. V posledním kroku, kam chcete přidat akci SMTP, zvolte **Nový krok**. 

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi kroky. 
   Zvolte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Do vyhledávacího pole zadejte jako filtr "smtp". V seznamu akcí vyberte požadovanou akci.

1. Po zobrazení výzvy zadejte tyto informace o připojení:

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název připojení** | Ano | Název připojení k serveru SMTP | 
   | **Adresa serveru SMTP** | Ano | Adresa serveru SMTP | 
   | **Uživatelské jméno** | Ano | Vaše uživatelské jméno pro váš účet SMTP | 
   | **Heslo** | Ano | Heslo k účtu SMTP | 
   | **Port serveru SMTP** | Ne | Konkrétní port na serveru SMTP, který chcete použít | 
   | **Povolit SSL?** | Ne | Zapněte nebo vypněte šifrování TLS/SSL. | 
   |||| 

1. Uveďte potřebné podrobnosti pro vybranou akci. 

1. Uložte aplikaci logiky nebo pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o tomto konektoru, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](https://docs.microsoft.com/connectors/smtpconnector/).

> [!NOTE]
> Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor je ise označené verze používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)