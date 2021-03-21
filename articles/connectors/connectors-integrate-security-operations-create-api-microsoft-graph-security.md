---
title: Integrace a Správa operací zabezpečení & zabezpečení Microsoft Graph
description: Vylepšete ochranu před hrozbami, detekci a reakci vaší aplikace pomocí Microsoft Graph zabezpečení & Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ecfan
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: a83cd68df2f1d722517d6239bf6959075860d0b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94888534"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Zvyšte ochranu před hrozbami integrací operací zabezpečení pomocí Microsoft Graph zabezpečení & Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a konektoru [zabezpečení Microsoft Graph](/graph/security-concept-overview) můžete vylepšit způsob, jakým aplikace detekuje hrozby, chrání je a reaguje na ně tím, že vytvoří automatizované pracovní postupy pro integraci produktů, služeb a partnerů Microsoftu. Můžete například vytvořit [Azure Security Center playbooky](../security-center/workflow-automation.md) a monitorovat a spravovat Microsoft Graph entit zabezpečení, jako jsou výstrahy. Tady je několik scénářů, které podporuje Microsoft Graph Security Connector:

* Získejte výstrahy na základě dotazů nebo ID výstrahy. Můžete například získat seznam, který obsahuje upozornění s vysokou závažností.

* Aktualizujte výstrahy. Můžete například aktualizovat přiřazení výstrah, přidat komentáře k výstrahám nebo výstrahy značek.

* Monitorování při vytváření nebo změně výstrah vytvořením [odběrů výstrah (Webhooky)](/graph/api/resources/webhooks).

* Spravujte své odběry výstrah. Můžete například získat aktivní odběry, zvětšit dobu vypršení platnosti předplatného nebo odstranit odběry.

Pracovní postup vaší aplikace logiky může používat akce, které získávají odpovědi z konektoru zabezpečení Microsoft Graph a zpřístupňují výstup ostatním akcím v pracovním postupu. V pracovním postupu můžete mít také další akce, které používají výstup z akcí Microsoft Graph Security Connector. Pokud například obdržíte upozornění s vysokou závažností prostřednictvím konektoru zabezpečení Microsoft Graph, můžete tyto výstrahy odeslat v e-mailové zprávě pomocí konektoru aplikace Outlook. 

Další informace o Microsoft Graph zabezpečení najdete v tématu [Přehled rozhraní API pro Microsoft Graph zabezpečení](/graph/security-concept-overview). Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Pokud hledáte Power Automate nebo PowerApps, přečtěte si téma [co je Power automat?](https://flow.microsoft.com/) nebo [co je Power Apps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Abyste mohli používat konektor pro Microsoft Graph Security, musí vám být *explicitně udělen* souhlas správce tenanta Azure Active Directory (AD). To je součástí [požadavků na ověřování v Microsoft Graph Security](/graph/security-authorization). Tento souhlas vyžaduje ID a název aplikace konektoru zabezpečení Microsoft Graph, které můžete také najít v [Azure Portal](https://portal.azure.com):

  | Vlastnost | Hodnota |
  |----------|-------|
  | **Název aplikace** | `MicrosoftGraphSecurityConnector` |
  | **ID aplikace** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Správce tenanta Azure AD může udělit souhlas konektoru, a to podle těchto kroků:

  * [Udělení souhlasu správce tenanta pro aplikace Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

  * Při prvním spuštění aplikace logiky může vaše aplikace požádat správce tenanta Azure AD o souhlas prostřednictvím [prostředí pro souhlas v aplikaci](../active-directory/develop/application-consent-experience.md).
   
* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, do které chcete přistupovat k entitám zabezpečení Microsoft Graph, jako jsou například výstrahy. Pokud chcete použít Trigger zabezpečení Microsoft Graph, potřebujete prázdnou aplikaci logiky. Pokud chcete použít akci zabezpečení Microsoft Graph, potřebujete aplikaci logiky, která začíná vhodným triggerem pro váš scénář.

## <a name="connect-to-microsoft-graph-security"></a>Připojení k Microsoft Graph zabezpečení 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com/)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Pro prázdné aplikace logiky přidejte Trigger a všechny další akce, které chcete provést před přidáním akce zabezpečení Microsoft Graph.

   -nebo-

   Pro existující aplikace logiky v rámci posledního kroku, kde chcete přidat akci zabezpečení Microsoft Graph, vyberte **Nový krok**.

   -nebo-

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. Vyberte znaménko plus (+), které se zobrazí, a vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "Microsoft Graph Security". V seznamu akce vyberte akci, kterou chcete.

1. Přihlaste se pomocí přihlašovacích údajů pro Microsoft Graph zabezpečení.

1. Zadejte potřebné podrobnosti pro vybranou akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="add-triggers"></a>Přidat triggery

V Azure Logic Apps musí každá aplikace logiky začínat [triggerem](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když dojde ke konkrétní události nebo když dojde ke splnění určité podmínky. Pokaždé, když se Trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a začne spouštět pracovní postup vaší aplikace.

> [!NOTE] 
> Když se aktivuje Trigger, zpracuje aktivační událost všechny nové výstrahy. Pokud neobdržíte žádné výstrahy, spuštění triggeru se přeskočí. Následující dotaz na Trigger probíhá na základě intervalu opakování, který zadáte ve vlastnostech triggeru.

Tento příklad ukazuje, jak můžete spustit pracovní postup aplikace logiky, když se do vaší aplikace odesílají nové výstrahy.

1.  V Azure Portal nebo Visual Studiu vytvořte prázdnou aplikaci logiky, která otevře návrháře aplikace logiky. V tomto příkladu se používá Azure Portal.

1.  V návrháři do vyhledávacího pole zadejte jako filtr "Microsoft Graph Security". V seznamu triggery vyberte tuto aktivační událost: **u všech nových výstrah** .

1.  V aktivační události zadejte informace o výstrahách, které chcete monitorovat. Pro další vlastnosti otevřete seznam **Přidat nový parametr** a vyberte parametr pro přidání této vlastnosti aktivační události.

   | Vlastnost | Property (JSON) | Požaduje se | Typ | Description |
   |----------|-----------------|----------|------|-------------|
   | **Interval** | `interval` | Yes | Integer | Kladné celé číslo, které popisuje, jak často se pracovní postup spouští na základě frekvence. Tady jsou minimální a maximální intervaly: <p><p>-Month: 1-16 měsíců <br>Denní: 1-500 dní <br>-Hodina: 1 – 12000 hodin <br>-Minute: 1 – 72000 minut <br>-Sekunda: 1 – 9999999 sekund <p>Pokud má například interval hodnotu 6 a frekvence je "Month" (měsíc), opakování je každých 6 měsíců. |
   | **Frekvence** | `frequency` | Ano | Řetězec | Jednotka času pro opakování: **sekunda**, **minuta**, **hodina**, **den**, **týden** nebo **měsíc** |
   | **Časové pásmo** | `timeZone` | No | Řetězec | Platí pouze v případě, že zadáte čas spuštění, protože tato aktivační událost nepřijímá [posun UTC](https://en.wikipedia.org/wiki/UTC_offset). Vyberte časové pásmo, které chcete použít. |
   | **Čas spuštění** | `startTime` | No | Řetězec | Zadejte počáteční datum a čas v tomto formátu: <p><p>RRRR-MM-DDThh: mm: SS Pokud vyberete časové pásmo <p>-nebo- <p>RRRR-MM-DDThh: mm: ssZ, pokud nevyberete časové pásmo <p>Pokud například požadujete 18. září 2017 na 2:00 odp., zadejte "2017-09-18T14:00:00" a vyberte časové pásmo, například Tichomoří (běžný čas). Případně zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** Tento počáteční čas má v budoucnosti maximálně 49 let a musí následovat za [specifikací data a času ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ve [formátu data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)a času UTC, ale bez [posunu UTC](https://en.wikipedia.org/wiki/UTC_offset). Pokud nevyberete časové pásmo, je nutné na konci přidat písmeno "Z" bez mezer. Tento "Z" odkazuje na ekvivalentní [námořní čas](https://en.wikipedia.org/wiki/Nautical_time). <p>V případě jednoduchých plánů je počáteční čas prvním výskytem, ale u složitých plánů se Trigger neaktivuje dříve, než je čas spuštění. [*Jaké jsou způsoby, jak můžu použít počáteční datum a čas?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

1.  Nyní pokračujte v přidávání jedné nebo více akcí do aplikace logiky pro úlohy, které chcete provést s výsledky triggeru.

## <a name="add-actions"></a>Přidat akce

Tady najdete konkrétnější podrobnosti o používání různých akcí, které jsou k dispozici u konektoru Microsoft Graph Security.

### <a name="manage-alerts"></a>Správa výstrah

Pokud chcete filtrovat, seřadit nebo získat nejnovější výsledky, zadejte *jenom* [parametry dotazu OData podporované Microsoft Graph](/graph/query-parameters). *Nezadávejte* úplnou základní adresu URL nebo akci HTTP, například `https://graph.microsoft.com/v1.0/security/alerts` , nebo `GET` `PATCH` . Tady je konkrétní příklad, který zobrazuje parametry pro akci **získat výstrahy** , když chcete zobrazit seznam s výstrahami s vysokou závažností:

`Filter alerts value as Severity eq 'high'`

Další informace o dotazech, které můžete použít s tímto konektorem, najdete v [referenční dokumentaci k výstrahám zabezpečení Microsoft Graph](/graph/api/alert-list). Pokud chcete s tímto konektorem vytvářet vylepšená prostředí, přečtěte si další informace o [vlastnostech schématu výstrahy](/graph/api/resources/alert) , které konektor podporuje.

| Akce | Popis |
|--------|-------------|
| **Získat výstrahy** | Získejte filtrované výstrahy na základě jedné nebo více [vlastností výstrahy](/graph/api/resources/alert), například `Provider eq 'Azure Security Center' or 'Palo Alto Networks'` . | 
| **Získat upozornění podle ID** | Získat konkrétní výstrahu na základě ID výstrahy. | 
| **Aktualizovat upozornění** | Aktualizace konkrétní výstrahy na základě ID výstrahy. Chcete-li se ujistit, že jste v žádosti předávali požadované a upravitelné vlastnosti, přečtěte si téma [upravitelné vlastnosti výstrah](/graph/api/alert-update). Chcete-li například přiřadit upozornění analytikovi zabezpečení, aby bylo možné je prozkoumat, můžete aktualizovat vlastnost **přiřazeno pro** výstrahu. |
|||

### <a name="manage-alert-subscriptions"></a>Správa odběrů výstrah

Microsoft Graph podporuje [*odběry*](/graph/api/resources/subscription)nebo [*Webhooky*](/graph/api/resources/webhooks). Chcete-li získat, aktualizovat nebo odstranit odběry, zadejte [parametry dotazu OData podporované Microsoft Graph](/graph/query-parameters) do konstruktoru entity Microsoft Graph a zahrňte a `security/alerts` pak dotaz OData. *Nezahrnujte základní* adresu URL, například `https://graph.microsoft.com/v1.0` . Místo toho použijte formát v tomto příkladu:

`security/alerts?$filter=status eq 'NewAlert'`

| Akce | Popis |
|--------|-------------|
| **Vytvoření předplatných** | [Vytvořte předplatné](/graph/api/subscription-post-subscriptions) , které vás upozorní na jakékoli změny. Toto předplatné můžete filtrovat pro konkrétní typy výstrah, které chcete. Můžete například vytvořit předplatné, které vás upozorní na upozornění s vysokou závažností. |
| **Získat aktivní odběry** | [Získání neplatných odběrů](/graph/api/subscription-list) | 
| **Aktualizovat předplatné** | [Aktualizujte předplatné](/graph/api/subscription-update) ZADÁNÍm ID předplatného. Pokud třeba chcete prodloužení předplatného, můžete aktualizovat vlastnost předplatného `expirationDateTime` . | 
| **Odstranění předplatného** | [Odstraňte předplatné](/graph/api/subscription-delete) ZADÁNÍm ID předplatného. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Spravovat indikátory logiky hrozeb

Pokud chcete filtrovat, seřadit nebo získat nejnovější výsledky, zadejte *jenom* [parametry dotazu OData podporované Microsoft Graph](/graph/query-parameters). *Nezadávejte* úplnou základní adresu URL nebo akci HTTP, například `https://graph.microsoft.com/beta/security/tiIndicators` , nebo `GET` `PATCH` . Tady je konkrétní příklad, který zobrazuje parametry pro akci **Get tiIndicators** , když chcete zobrazit seznam s `DDoS` typem hrozby:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Další informace o dotazech, které můžete použít s tímto konektorem, najdete [v části "volitelné parametry dotazu" v tématu Referenční dokumentace k nástroji Microsoft Graph Security Threat Intelligence](/graph/api/tiindicators-list). Pokud chcete s tímto konektorem sestavovat vylepšená prostředí, přečtěte si další informace o [vlastnostech schématu – indikátor analýzy hrozeb](/graph/api/resources/tiindicator) , který konektor podporuje.

| Akce | Popis |
|--------|-------------|
| **Získat indikátory pro analýzu hrozeb** | Získání tiIndicators filtrovaných na základě jedné nebo více [tiIndicator vlastností](/graph/api/resources/tiindicator), například `threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Získat indikátor analýzy hrozeb podle ID** | Získá konkrétní tiIndicator na základě ID tiIndicator. | 
| **Vytvořit indikátor analýzy hrozeb** | Vytvořte nový tiIndicator publikováním do kolekce tiIndicators. Chcete-li se ujistit, že jste v žádosti předávali požadované vlastnosti, přečtěte si téma [požadované vlastnosti pro vytváření tiIndicator](/graph/api/tiindicators-post). |
| **Odeslání více ukazatelů analýzy hrozeb** | Vytvořte více nových tiIndicators publikováním kolekce tiIndicators. Chcete-li se ujistit, že jste v žádosti předávali požadované vlastnosti, přečtěte si téma [požadované vlastnosti pro odeslání více tiIndicators](/graph/api/tiindicator-submittiindicators). |
| **Aktualizovat indikátor analýzy hrozeb** | Aktualizujte konkrétní tiIndicator na základě ID tiIndicator. Abyste se ujistili, že jste v žádosti předávali požadované a upravitelné vlastnosti, přečtěte si téma [upravitelné vlastnosti pro tiIndicator](/graph/api/tiindicator-update). Chcete-li například aktualizovat akci, která má být použita, pokud se ukazatel shoduje v rámci nástroje zabezpečení targetProduct, můžete aktualizovat vlastnost **Action** tiIndicator. |
| **Aktualizace více ukazatelů analýzy hrozeb** | Aktualizace více tiIndicators Abyste se ujistili, že jste v žádosti předávali požadované vlastnosti, přečtěte si téma [požadované vlastnosti pro aktualizaci více tiIndicators](/graph/api/tiindicator-updatetiindicators). |
| **Odstranit indikátor analýzy hrozeb podle ID** | Odstraňte konkrétní tiIndicator na základě ID tiIndicator. |
| **Odstranění více ukazatelů analýzy hrozeb podle ID** | Odstraňte více tiIndicators podle jejich ID. Abyste se ujistili, že jste v žádosti předávali požadované vlastnosti, přečtěte si [požadované vlastnosti pro odstranění více tiIndicators podle ID](/graph/api/tiindicator-deletetiindicators). |
| **Odstranění více indikátorů analýzy hrozeb podle externích ID** | Odstraňte více tiIndicators externích ID. Abyste se ujistili, že jste v žádosti předávali požadované vlastnosti, přečtěte si [požadované vlastnosti pro odstranění více tiIndicators podle externích ID](/graph/api/tiindicator-deletetiindicatorsbyexternalid). |
|||

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](/connectors/microsoftgraphsecurity/)konektoru.

## <a name="next-steps"></a>Další kroky

Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
