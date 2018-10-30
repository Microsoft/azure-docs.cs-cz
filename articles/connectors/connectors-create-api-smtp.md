---
title: Připojení k SMTP serveru z Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovních postupů, které odesílání e-mailu prostřednictvím účtu SMTP (Simple Mail Transfer Protocol) pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 78b1eb6272fa97ef392e97723454d29cf56bb4bf
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230501"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Odeslání e-mailu z účtu SMTP s Azure Logic Apps

S Azure Logic Apps a konektoru přenos protokolu SMTP (Simple Mail) můžete vytvořit automatizovaných úloh a pracovních postupů, které odeslání e-mailu z účtu SMTP. Také můžete mít další akce pomocí výstupu z akce protokolu SMTP. Poté, co váš SMTP odešle e-mailu, můžete upozornit vašemu týmu v Slack prostřednictvím konektoru Slack. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Váš SMTP účtu a přihlašovací údaje uživatele

  Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení a otevřete váš účet SMTP.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k vašemu účtu SMTP. Pokud chcete použít akce protokolu SMTP, spusťte aplikaci logiky s triggerem, například aktivační událost Salesforce, pokud již máte účet služby Salesforce.

  Například můžete spustit aplikaci logiky s **při vytvoření záznamu** aktivační události Salesforce. 
  Tento trigger se spustí pokaždé, když se v Salesforce vytvoří nový záznam, jako je například nový zájemce. 
  Potom postupujte podle tohoto aktivační událost s SMTP **odeslání e-mailu** akce. Tímto způsobem, když se vytvoří nový záznam, aplikace logiky odešle e-mailu z účtu SMTP o nový záznam.

## <a name="connect-to-smtp"></a>Připojte se k SMTP serveru

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V posledním kroku, ve které chcete přidat akci SMTP, vyberte **nový krok**. 

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. 
   Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "smtp". V seznamu akcí vyberte požadovanou akci.

1. Po zobrazení výzvy zadejte tyto informace o připojení:

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název připojení** | Ano | Název připojení k serveru SMTP | 
   | **Adresa serveru SMTP** | Ano | Adresa serveru SMTP | 
   | **Uživatelské jméno** | Ano | Vaše uživatelské jméno pro svůj účet SMTP | 
   | **Heslo** | Ano | Heslo pro váš účet SMTP | 
   | **Port serveru SMTP** | Ne | Konkrétní port na serveru SMTP, kterou chcete použít | 
   | **Povolit SSL?** | Ne | Zapnutí nebo vypnutí šifrování SSL. | 
   |||| 

1. Zadejte potřebné podrobnosti pro vybranou akci. 

1. Uložení aplikace logiky nebo pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/smtpconnector/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)