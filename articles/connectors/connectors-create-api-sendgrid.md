---
title: Připojení k SendGrid z aplikací Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které odesílají e-maily a spravují seznamy adresátů v SendGrid pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 998020c5e39c8d50e8a14c74c43b7b435752f43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789303"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Odesílání e-mailů a správa seznamů adresátů v SendGrid pomocí Azure Logic Apps

Pomocí aplikací Azure Logic Apps a konektoru SendGrid můžete vytvářet automatizované úkoly a pracovní postupy, které odesílají e-maily a spravují seznamy příjemců, například:

* Pošlete e-mail.
* Přidejte příjemce do seznamů.
* Získejte, přidejte a spravujte globální potlačení.

Tyto úlohy můžete použít pomocí akcí SendGrid v aplikacích logiky. Můžete také mít jiné akce použít výstup z SendGrid akce. 

Tento konektor poskytuje pouze akce, takže ke spuštění aplikace logiky, použijte samostatnou aktivační událost, jako je například aktivační událost **opakování.** Pokud například pravidelně přidáváte příjemce do seznamů, můžete odesílat e-maily o příjemcích a seznamech pomocí konektoru Office 365 Outlook nebo Outlook.com konektoru.
Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* [Účet SendGrid](https://www.sendgrid.com/) a [klíč rozhraní API SendGrid](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Klíč rozhraní API autorizuje aplikaci logiky k vytvoření připojení a přístupu k účtu SendGrid.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k účtu SendGrid. Chcete-li použít akci SendGrid, spusťte aplikaci logiky s jinou aktivační událostí, například aktivační událost **Opakování.**

## <a name="connect-to-sendgrid"></a>Připojit se k SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Zvolte cestu: 

   * V posledním kroku, kam chcete přidat akci, zvolte **Nový krok**. 

     -nebo-

   * Mezi kroky, kam chcete přidat akci, přesuňte ukazatel myši na šipku mezi kroky. 
   Zvolte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Do vyhledávacího pole zadejte jako filtr "sendgrid". V seznamu akcí vyberte požadovanou akci.

1. Zadejte název připojení. 

1. Zadejte klíč rozhraní API SendGrid a pak zvolte **Vytvořit**.

1. Poskytněte potřebné podrobnosti pro vybranou akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](/connectors/sendgrid/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)