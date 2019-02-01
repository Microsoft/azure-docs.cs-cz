---
title: Operace zabezpečení integrovat Microsoft Graph zabezpečení – Azure Logic Apps
description: Vylepšit možnosti ochrany, detekce a reakce ohrožení vaší aplikace pomocí správy operací zabezpečení pomocí zabezpečení Microsoft Graph a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/19
tags: connectors
ms.openlocfilehash: 647df9e73804c8f261b58d5ede7c4b030d448fed
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513516"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Zlepšení ochrany před internetovými útoky integrací operace zabezpečení pomocí zabezpečení Microsoft Graph a Azure Logic Apps

S [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [zabezpečení společnosti Microsoft Graph](https://docs.microsoft.com/graph/security-concept-overview) konektor, můžete vylepšit zjistí, jak vaše aplikace chrání a reaguje na ně tím, že vytvoříte automatizované pracovní postupy pro integraci Microsoft zabezpečení produktech, službách a partnerů. Můžete například vytvořit [Azure Security Center playbooky](../security-center/security-center-playbooks.md) , monitorování a Správa zabezpečení Microsoft Graph entity, třeba výstrahy. Tady je několik scénářů, které podporuje konektor Microsoft Graph zabezpečení:

* Dostávejte upozornění na základě dotazů nebo podle ID upozornění. Například můžete získat seznam, který obsahuje upozornění s vysokou závažností.
* Aktualizace výstrah. Například můžete aktualizovat upozornění přiřazení, přidání komentářů do výstrahy nebo označit výstrahy.
* Sledování, výstrah se vytvořil nebo změnil vytvořením [upozornění předplatných (webhooky)](https://docs.microsoft.com/graph/api/resources/webhooks).
* Správa vašich odběrů upozornění. Například můžete získat aktivní odběry, prodloužit dobu vypršení platnosti předplatného nebo odstranit odběry.

Pracovní postup aplikace logiky můžete použít akce, které odpovědi z konektoru nástroje zabezpečení společnosti Microsoft Graph a zpřístupnit tento výstup pro jiné akce v pracovním postupu. Je také možné další akce ve vaší pracovní postup výstupu z akce Microsoft Graph zabezpečení konektoru. Například pokud se zobrazí upozornění s vysokou závažností prostřednictvím konektoru Microsoft Graph zabezpečení, můžete odeslat dané výstrahy v e-mailovou zprávu pomocí konektoru aplikace Outlook. 

Další informace o zabezpečení společnosti Microsoft Graphu, najdete v článku [přehled rozhraní API Microsoft Graphu zabezpečení](https://aka.ms/graphsecuritydocs). Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Pokud potřebujete pro Microsoft Flow nebo PowerApps, najdete v článku [co je tok?](https://flow.microsoft.com/) nebo [co je PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* K používání konektoru Microsoft Graph zabezpečení, musíte mít *explicitně dané* souhlas správce tenanta Azure Active Directory (AD), která je součástí sady [požadavky na ověřování zabezpečení Microsoft Graphu ](https://aka.ms/graphsecurityauth). Toto vyjádření souhlasu vyžaduje ID aplikace a název, který najdete také v Microsoft Graphu Security connector [webu Azure portal](https://portal.azure.com):

   | Vlastnost | Hodnota |
   |----------|-------|
   | **Název aplikace** | `MicrosoftGraphSecurityConnector` |
   | **ID aplikace** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   K udělení souhlasu pro konektor, váš správce tenanta Azure AD buď pomocí těchto kroků:

   * [Udělit souhlas správce tenanta pro Azure AD aplikace](../active-directory/develop/v2-permissions-and-consent.md).

   * Při prvním spuštění aplikace logiky, aplikaci můžete požádat souhlas správce tenanta Azure AD prostřednictvím [prostředí pro vyjádření souhlasu aplikace](../active-directory/develop/application-consent-experience.md).
   
* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k Microsoft Graphu zabezpečení entity, třeba na alerts. V současné době tento konektor nemá žádné triggery. Tak, použít Microsoft Graph Security akci, spusťte svou aplikaci logiky s triggerem, například, **opakování** aktivační události.

## <a name="connect-to-microsoft-graph-security"></a>Připojte se k Microsoft Graphu zabezpečení 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V případě prázdné logic apps přidáte aktivační události a všechny další akce, které chcete před přidáním akce zabezpečení společnosti Microsoft Graph.

   -nebo-

   Pro existující aplikace logiky v posledním kroku, ve které chcete přidat akci Microsoft Graph zabezpečení, zvolte **nový krok**.

   -nebo-

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. 
   Vyberte znaménko plus (+), který se zobrazí a vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "microsoft graph zabezpečení". Ze seznamu akcí vyberte požadovanou akci.

1. Přihlaste se pomocí pověření zabezpečení společnosti Microsoft Graph.

1. Zadejte potřebné podrobnosti pro vybranou akci a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="add-actions"></a>Přidat akce

Tady jsou další konkrétní podrobnosti o použití různé akce, které jsou k dispozici s konektorem zabezpečení společnosti Microsoft Graph.

### <a name="manage-alerts"></a>Správa upozornění

K filtrování, řazení, nebo získat nejnovější výsledky, poskytují *pouze* [parametry dotazu ODATA podporuje Microsoft Graphu](https://docs.microsoft.com/graph/query-parameters). *Nezadávejte* dokončení základní adresu URL nebo akce HTTP, třeba `https://graph.microsoft.com/v1.0/security/alerts`, nebo `GET` nebo `PATCH` operace. Tady je konkrétní příklad, který ukazuje parametry **výstrahy** akce, pokud chcete seznam s vysokou závažností výstrahy:

`Filter alerts value as Severity eq 'high'`

Další informace o dotazech, můžete pomocí tohoto konektoru, najdete v článku [výstrahy zabezpečení Microsoft Graph referenční dokumentaci](https://docs.microsoft.com/graph/api/alert-list). K vytvoření Vylepšená rozhraní pomocí tohoto konektoru, další informace o [schéma vlastnosti výstrahy](https://docs.microsoft.com/graph/api/resources/alert) , konektor podporuje.

| Akce | Popis |
|--------|-------------|
| **Získání výstrah** | Získání výstrah filtrované na základě jedné nebo více [upozornění vlastnosti](https://docs.microsoft.com/graph/api/resources/alert), například: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Získání výstrahy podle ID** | Získání konkrétní výstrahy na základě ID výstrahy. | 
| **Aktualizace výstrahy** | Aktualizovat konkrétní výstrahu na základě ID výstrahy. <p>Ujistěte se, že předáte povinné a upravovat vlastnosti ve vaší žádosti, najdete v článku [upravovat vlastnosti výstrah](https://docs.microsoft.com/graph/api/alert-update). Například pokud chcete výstrahu přiřadit analytikovi zabezpečení, můžete zjistit, můžete aktualizovat upozornění **přiřazená** vlastnost. |
|||

### <a name="manage-alert-subscriptions"></a>Správa odběrů výstrah

Podporuje Microsoft Graphu [ *předplatná*](https://docs.microsoft.com/graph/api/resources/subscription), nebo [ *webhooky*](https://docs.microsoft.com/graph/api/resources/webhooks). Získat, aktualizovat, nebo odstranit odběry, zadejte [parametry dotazu ODATA podporuje Microsoft Graphu](https://docs.microsoft.com/graph/query-parameters) v Microsoft Graphu entitě vytvořit a zahrnují `security/alerts` za nímž následuje dotaz ODATA. 
*Nezahrnují* základní adresu URL, třeba `https://graph.microsoft.com/v1.0`. Místo toho použijte formát v tomto příkladu:

`security/alerts?$filter=status eq 'New'`

| Akce | Popis |
|--------|-------------|
| **Vytvořit odběry** | [Vytvoření odběru](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) , upozorní vás na změny. Toto předplatné pro konkrétní typy výstrah, které chcete, můžete filtrovat. Můžete například vytvořit odběr, který upozorní vás na upozornění s vysokou závažností. |
| **Získat aktivní odběry** | [Získat předplatná nevypršela](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Aktualizace předplatného** | [Aktualizace předplatného](https://docs.microsoft.com/graph/api/subscription-update) zadáním ID předplatného. Například pokud chcete rozšířit vaše předplatné, můžete aktualizovat předplatného `expirationDateTime` vlastnost. | 
| **Odstranit odběr** | [Odstranění předplatného](https://docs.microsoft.com/graph/api/subscription-delete) zadáním ID předplatného. | 
||| 

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](https://aka.ms/graphsecurityconnectorreference).

## <a name="get-support"></a>Získat podporu

Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
