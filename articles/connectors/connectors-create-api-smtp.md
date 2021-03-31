---
title: Připojení k SMTP z Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které odesílají e-maily prostřednictvím účtu SMTP (Simple Mail Transfer Protocol) pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 9989d0ebd95bfe5ee49be2ba76b73e07630b519a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87283941"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Odeslání e-mailu z účtu SMTP pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru SMTP (Simple Mail Transfer Protocol) můžete vytvářet automatizované úlohy a pracovní postupy, které odesílají e-maily z vašeho účtu SMTP. Můžete také použít další akce výstup z akcí SMTP. Například po odeslání e-mailu prostřednictvím protokolu SMTP můžete týmu Odeslat výstrahu do časové rezervy pomocí konektoru časové rezervy. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Účet SMTP a přihlašovací údaje uživatele

  Vaše přihlašovací údaje autorizují vaši aplikaci logiky, aby vytvořila připojení a měl přístup k vašemu účtu SMTP.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k účtu SMTP. Pokud chcete použít akci SMTP, spusťte aplikaci logiky s triggerem, jako je například Trigger služby Salesforce, pokud máte účet Salesforce.

  Aplikaci logiky můžete například spustit pomocí triggeru **při vytvoření záznamu** Salesforce. 
  Tato aktivační událost se aktivuje pokaždé, když se v Salesforce vytvoří nový záznam, třeba zájemce. 
  Tuto aktivační událost pak můžete sledovat pomocí akce **Odeslat E-mail** SMTP. Tímto způsobem bude při vytváření nového záznamu aplikace logiky odeslána z vašeho účtu SMTP e-mail o novém záznamu.

## <a name="connect-to-smtp"></a>Připojení k SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. V posledním kroku, kam chcete přidat akci SMTP, vyberte **Nový krok**. 

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. 
   Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "SMTP". V seznamu akce vyberte akci, kterou chcete.

1. Po zobrazení výzvy zadejte tyto informace o připojení:

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Název připojení** | Ano | Název připojení k serveru SMTP | 
   | **Adresa serveru SMTP** | Ano | Adresa serveru SMTP | 
   | **Uživatelské jméno** | Ano | Vaše uživatelské jméno pro váš účet SMTP | 
   | **Heslo** | Ano | Heslo pro váš účet SMTP | 
   | **Port serveru SMTP** | Ne | Konkrétní port na serveru SMTP, který chcete použít | 
   | **Povolit SSL?** | Ne | Zapněte nebo vypněte šifrování TLS/SSL. | 
   |||| 

1. Zadejte potřebné podrobnosti pro vybranou akci. 

1. Uložte aplikaci logiky nebo pokračujte v sestavování pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další technické podrobnosti o této spojnici, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/smtpconnector/).

> [!NOTE]
> V případě Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-Label používá místo toho [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
