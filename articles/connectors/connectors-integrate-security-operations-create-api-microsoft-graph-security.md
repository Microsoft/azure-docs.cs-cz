---
title: Integrace a Správa operací zabezpečení – Azure Logic Apps & Microsoft Graph zabezpečení
description: Vylepšete ochranu před hrozbami, detekci a reakci vaší aplikace pomocí Microsoft Graph zabezpečení & Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 845f57d84f49bdd964cc6f61790faff093f59466
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679095"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Zvyšte ochranu před hrozbami integrací operací zabezpečení pomocí Microsoft Graph zabezpečení & Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a konektoru [zabezpečení Microsoft Graph](https://docs.microsoft.com/graph/security-concept-overview) můžete vylepšit způsob, jakým aplikace detekuje hrozby, chrání je a reaguje na ně tím, že vytvoří automatizované pracovní postupy pro integraci produktů, služeb a partnerů Microsoftu. Můžete například vytvořit [Azure Security Center playbooky](../security-center/security-center-playbooks.md) a monitorovat a spravovat Microsoft Graph entit zabezpečení, jako jsou výstrahy. Tady je několik scénářů podporovaných nástrojem Microsoft Graph Security Connector:

* Získejte výstrahy na základě dotazů nebo ID výstrahy. Můžete například získat seznam, který obsahuje upozornění s vysokou závažností.
* Aktualizujte výstrahy. Můžete například aktualizovat přiřazení výstrah, přidat komentáře k výstrahám nebo výstrahy značek.
* Monitorování při vytváření nebo změně výstrah vytvořením [odběrů výstrah (Webhooky)](https://docs.microsoft.com/graph/api/resources/webhooks).
* Spravujte své odběry výstrah. Můžete například získat aktivní odběry, zvětšit dobu vypršení platnosti předplatného nebo odstranit odběry.

Pracovní postup vaší aplikace logiky může používat akce, které získávají odpovědi z konektoru zabezpečení Microsoft Graph a zpřístupňují výstup ostatním akcím v pracovním postupu. V pracovním postupu můžete mít také další akce, které používají výstup z akcí Microsoft Graph Security Connector. Pokud například obdržíte upozornění s vysokou závažností prostřednictvím konektoru zabezpečení Microsoft Graph, můžete tyto výstrahy odeslat v e-mailové zprávě pomocí konektoru aplikace Outlook. 

Další informace o Microsoft Graph zabezpečení najdete v tématu [Přehled rozhraní API pro Microsoft Graph zabezpečení](https://aka.ms/graphsecuritydocs). Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Pokud hledáte Microsoft Flow nebo PowerApps, přečtěte si téma [co je Flow?](https://flow.microsoft.com/) nebo [co je PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Pokud chcete použít Microsoft Graph Security Connector, musíte *explicitně udělit* souhlas správce tenanta Azure Active Directory (AD), který je součástí [Microsoft Graph požadavků na ověření zabezpečení](https://aka.ms/graphsecurityauth). Tento souhlas vyžaduje ID a název aplikace konektoru zabezpečení Microsoft Graph, které můžete také najít v [Azure Portal](https://portal.azure.com):

   | Vlastnost | Hodnota |
   |----------|-------|
   | **Název aplikace** | `MicrosoftGraphSecurityConnector` |
   | **ID aplikace** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Správce tenanta Azure AD může udělit souhlas konektoru, a to podle těchto kroků:

   * [Udělte souhlasu správce tenanta pro aplikace Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

   * Během prvního spuštění vaší aplikace logiky může vaše aplikace požádat o souhlas správce tenanta Azure AD prostřednictvím [prostředí pro vyjádření souhlasu s aplikací](../active-directory/develop/application-consent-experience.md).
   
* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, do které chcete přistupovat k entitám zabezpečení Microsoft Graph, jako jsou například výstrahy. V současné době tento konektor neobsahuje žádné triggery. Pokud tedy chcete použít akci zabezpečení Microsoft Graph, spusťte aplikaci logiky s triggerem, například triggerem **opakování** .

## <a name="connect-to-microsoft-graph-security"></a>Připojení k Microsoft Graph zabezpečení 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com/)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Pro prázdné Logic Apps přidejte Trigger a všechny další akce, které chcete provést, než přidáte akci Microsoft Graph zabezpečení.

   -nebo-

   Pro existující aplikace logiky v rámci posledního kroku, kde chcete přidat akci zabezpečení Microsoft Graph, vyberte možnost **Nový krok**.

   -nebo-

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. 
   Zvolte znaménko plus (+), které se zobrazí, a vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "Microsoft Graph Security". V seznamu akce vyberte akci, kterou chcete.

1. Přihlaste se pomocí přihlašovacích údajů pro Microsoft Graph zabezpečení.

1. Zadejte potřebné podrobnosti pro vybranou akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="add-actions"></a>Přidat akce

Tady najdete konkrétnější podrobnosti o používání různých akcí, které jsou k dispozici u konektoru Microsoft Graph Security.

### <a name="manage-alerts"></a>Správa upozornění

Pokud chcete filtrovat, seřadit nebo získat nejnovější výsledky, zadejte *jenom* [parametry dotazu OData podporované Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Nezadávejte* úplnou základní adresu URL nebo akci HTTP, například `https://graph.microsoft.com/v1.0/security/alerts` nebo operaci `GET` nebo `PATCH`. Tady je konkrétní příklad, který zobrazuje parametry pro akci **získat výstrahy** , když chcete zobrazit seznam s výstrahami s vysokou závažností:

`Filter alerts value as Severity eq 'high'`

Další informace o dotazech, které můžete použít s tímto konektorem, najdete v [referenční dokumentaci k výstrahám zabezpečení Microsoft Graph](https://docs.microsoft.com/graph/api/alert-list). Pokud chcete s tímto konektorem vytvářet vylepšená prostředí, přečtěte si další informace o [vlastnostech schématu výstrahy](https://docs.microsoft.com/graph/api/resources/alert) , které konektor podporuje.

| Akce | Popis |
|--------|-------------|
| **Získat výstrahy** | Získejte filtrované výstrahy na základě jedné nebo více [vlastností výstrahy](https://docs.microsoft.com/graph/api/resources/alert), například: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Získat upozornění podle ID** | Získat konkrétní výstrahu na základě ID výstrahy. | 
| **Aktualizovat upozornění** | Aktualizace konkrétní výstrahy na základě ID výstrahy. <p>Chcete-li se ujistit, že jste v žádosti předávali požadované a upravitelné vlastnosti, přečtěte si téma [upravitelné vlastnosti výstrah](https://docs.microsoft.com/graph/api/alert-update). Chcete-li například přiřadit upozornění analytikovi zabezpečení, aby bylo možné je prozkoumat, můžete aktualizovat vlastnost **přiřazeno pro** výstrahu. |
|||

### <a name="manage-alert-subscriptions"></a>Správa odběrů výstrah

Microsoft Graph podporuje [*odběry*](https://docs.microsoft.com/graph/api/resources/subscription)nebo [*Webhooky*](https://docs.microsoft.com/graph/api/resources/webhooks). Chcete-li získat, aktualizovat nebo odstranit odběry, zadejte [parametry dotazu OData podporované Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) do konstruktoru entity Microsoft Graph a přidejte `security/alerts` následovaný dotazem OData. 
*Nezahrnujte základní* adresu URL, například `https://graph.microsoft.com/v1.0`. Místo toho použijte formát v tomto příkladu:

`security/alerts?$filter=status eq 'New'`

| Akce | Popis |
|--------|-------------|
| **Vytvoření předplatných** | [Vytvořte předplatné](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) , které vás upozorní na jakékoli změny. Toto předplatné můžete filtrovat pro konkrétní typy výstrah, které chcete. Můžete například vytvořit předplatné, které vás upozorní na upozornění s vysokou závažností. |
| **Získat aktivní odběry** | [Získání neplatných odběrů](https://docs.microsoft.com/graph/api/subscription-list) | 
| **Aktualizovat předplatné** | [Aktualizujte předplatné](https://docs.microsoft.com/graph/api/subscription-update) ZADÁNÍm ID předplatného. Pokud třeba chcete prodloužení předplatného, můžete aktualizovat vlastnost `expirationDateTime` předplatného. | 
| **Odstranit předplatné** | [Odstraňte předplatné](https://docs.microsoft.com/graph/api/subscription-delete) ZADÁNÍm ID předplatného. | 
||| 

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](https://aka.ms/graphsecurityconnectorreference)konektoru.

## <a name="get-support"></a>Získat podporu

Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
