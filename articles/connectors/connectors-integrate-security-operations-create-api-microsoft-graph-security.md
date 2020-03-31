---
title: Integrace a správa operací zabezpečení & zabezpečení microsoft graphu
description: Vylepšete ochranu, detekci a odezvu aplikace pomocí microsoft graphu security & Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: b4f51b192d1a7c0ee14a769321793753e8217dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598829"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Zlepšete ochranu před hrozbami integrací operací zabezpečení s microsoftgraphem security & Azure Logic Apps

Pomocí [aplikací Azure Logic Apps](../logic-apps/logic-apps-overview.md) a konektoru Microsoft Graph [Security](https://docs.microsoft.com/graph/security-concept-overview) můžete zlepšit způsob, jakým vaše aplikace detekuje, chrání a reaguje na hrozby, a to vytvořením automatizovaných pracovních postupů pro integraci produktů, služeb a partnerů Microsoftu pro zabezpečení. Můžete například vytvořit [playbooky Azure Security Center,](../security-center/security-center-playbooks.md) které monitorují a spravují entity zabezpečení Microsoft Graphu, jako jsou výstrahy. Tady jsou některé scénáře podporované konektorem Microsoft Graph Security:

* Získejte upozornění na základě dotazů nebo ID výstrahy. Můžete například získat seznam, který obsahuje výstrahy vysoké závažnosti.

* Aktualizujte výstrahy. Můžete například aktualizovat přiřazení výstrah, přidávat komentáře k výstrahám nebo označovat výstrahy.

* Sledujte, kdy jsou výstrahy vytvořeny nebo změněny vytvořením [odběrů výstrah (webhooků).](https://docs.microsoft.com/graph/api/resources/webhooks)

* Spravujte předplatná s výstrahami. Můžete například získat aktivní předplatná, prodloužit dobu vypršení platnosti předplatného nebo odstranit odběry.

Pracovní postup aplikace logiky můžete použít akce, které získat odpovědi z konektoru Microsoft Graph Security a zpřístupnit tento výstup pro jiné akce ve vašem pracovním postupu. Můžete také mít další akce v pracovním postupu použít výstup z akce konektormicrosoft graph zabezpečení. Pokud například získáte výstrahy s vysokou závažností prostřednictvím konektoru Zabezpečení aplikace Microsoft Graph, můžete tyto výstrahy odeslat v e-mailové zprávě pomocí konektoru aplikace Outlook. 

Další informace o zabezpečení aplikace Microsoft Graph najdete v tématu [Přehled rozhraní Microsoft Graph Security API](https://aka.ms/graphsecuritydocs). Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Pokud hledáte Microsoft Flow nebo PowerApps, přečtěte [What is PowerApps?](https://powerapps.microsoft.com/) si, [co je to konto?](https://flow.microsoft.com/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Abyste mohli používat konektor pro Microsoft Graph Security, musí vám být *explicitně udělen* souhlas správce tenanta Azure Active Directory (AD). To je součástí [požadavků na ověřování v Microsoft Graph Security](https://aka.ms/graphsecurityauth). Tento souhlas vyžaduje ID a název aplikace konektoru Microsoft Graph Security, které najdete také na [webu Azure Portal](https://portal.azure.com):

  | Vlastnost | Hodnota |
  |----------|-------|
  | **Název aplikace** | `MicrosoftGraphSecurityConnector` |
  | **ID aplikace** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Chcete-li udělit souhlas pro konektor, správce klienta Azure AD můžete postupovat buď takto:

  * [Udělení souhlasu správce tenanta pro aplikace Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

  * Při prvním spuštění aplikace logiky může vaše aplikace požádat správce tenanta Azure AD o souhlas prostřednictvím [prostředí pro souhlas v aplikaci](../active-directory/develop/application-consent-experience.md).
   
* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k entitám zabezpečení Microsoft Graphsecurity, jako jsou výstrahy. Chcete-li použít aktivační událost Zabezpečení aplikace Microsoft Graph Security, potřebujete prázdnou aplikaci logiky. Chcete-li použít akci Zabezpečení microsoft graphu, potřebujete aplikaci logiky, která začíná příslušnou aktivační událostí pro váš scénář.

## <a name="connect-to-microsoft-graph-security"></a>Připojení k zabezpečení aplikace Microsoft Graph 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com/)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Pro prázdné aplikace logiky přidejte aktivační událost a všechny další akce, které chcete před přidáním akce Zabezpečení microsoftgraphu.

   -nebo-

   U existujících aplikací logiky vyberte v posledním kroku, ve kterém chcete přidat akci Zabezpečení microsoftgraphu, **nový krok**.

   -nebo-

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi kroky. Vyberte znaménko plus (+), které se zobrazí, a vyberte **Přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "Microsoft Graph Security". V seznamu akcí vyberte požadovanou akci.

1. Přihlaste se pomocí přihlašovacích údajů microsoft graphsecurity.

1. Poskytněte potřebné podrobnosti pro vybranou akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="add-triggers"></a>Přidání aktivačních událostí

V Azure Logic Apps musí každá aplikace logiky začínat [aktivační událostí](../logic-apps/logic-apps-overview.md#logic-app-concepts), která se aktivuje, když dojde k určité události nebo když je splněna určitá podmínka. Pokaždé, když se spustí aktivační událost, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup aplikace.

> [!NOTE] 
> Když se aktivuje aktivační událost, aktivační událost zpracuje všechny nové výstrahy. Pokud nejsou přijaty žádné výstrahy, spuštění aktivační události je přeskočeno. K dalšímu dotazování aktivačních událostí dochází na základě intervalu opakování, který zadáte ve vlastnostech aktivační události.

Tento příklad ukazuje, jak můžete spustit pracovní postup aplikace logiky při odeslání nových upozornění do vaší aplikace.

1.  Na webu Azure Portal nebo Visual Studiu vytvořte prázdnou aplikaci logiky, která otevře Návrhář aplikace logiky. Tento příklad používá portál Azure.

1.  V návrháři zadejte do vyhledávacího pole jako filtr "Microsoft Graph Security". Ze seznamu aktivačních událostí vyberte tuto aktivační událost: **U všech nových výstrah**

1.  V aktivační události zadejte informace o výstrahách, které chcete sledovat. Chcete-li získat další vlastnosti, otevřete seznam **Přidat nový parametr** a vyberte parametr pro přidání této vlastnosti do aktivační události.

   | Vlastnost | Vlastnost (JSON) | Požaduje se | Typ | Popis |
   |----------|-----------------|----------|------|-------------|
   | **Interval** | `interval` | Ano | Integer | Kladné celé číslo, které popisuje, jak často se pracovní postup spouští na základě frekvence. Zde jsou minimální a maximální intervaly: <p><p>- Měsíc: 1-16 měsíců <br>- Den: 1-500 dní <br>- Hodina: 1-12 000 hodin <br>- Minuta: 1-72 000 minut <br>- Druhý: 1-9,999,999 sekund <p>Pokud je například interval 6 a frekvence je "Měsíc", opakování je každých 6 měsíců. |
   | **Frequency** | `frequency` | Ano | Řetězec | Jednotka času pro opakování: **Sekunda**, **Minuta**, **Hodina**, **Den**, **Týden**nebo **Měsíc** |
   | **Časové pásmo** | `timeZone` | Ne | Řetězec | Použije se pouze v případě, že zadáte čas zahájení, protože tato aktivační událost nepřijímá [posun UTC](https://en.wikipedia.org/wiki/UTC_offset). Vyberte časové pásmo, které chcete použít. |
   | **Čas spuštění** | `startTime` | Ne | Řetězec | Zadejte počáteční datum a čas v tomto formátu: <p><p>YYYY-MM-DDThh:mm:ss, pokud vyberete časové pásmo <p>-nebo- <p>YYYY-MM-DDThh:mm:ssZ, pokud nevyberete časové pásmo <p>Pokud například chcete 18.9.2017 v 14:00, zadejte "2017-09-18T14:00:00" a vyberte časové pásmo, například tichomořský standardní čas. Nebo zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** Tento čas zahájení má v budoucnu maximálně 49 let a musí se řídit [specifikací data iso 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ve [formátu data času UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [posunu UTC](https://en.wikipedia.org/wiki/UTC_offset). Pokud nevyberete časové pásmo, musíte přidat písmeno "Z" na konci bez mezer. Toto "Z" odkazuje na ekvivalentní [námořní čas](https://en.wikipedia.org/wiki/Nautical_time). <p>Pro jednoduché plány je počáteční čas prvním výskytem, zatímco pro složité plány se aktivační událost nespustí dříve než čas zahájení. [*Jakými způsoby lze použít počáteční datum a čas?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

1.  Teď pokračujte v přidávání jedné nebo více akcí do aplikace logiky pro úkoly, které chcete provést s výsledky aktivační události.

## <a name="add-actions"></a>Přidání akcí

Tady jsou konkrétnější podrobnosti o použití různých akcí, které jsou k dispozici v konektoru Microsoft Graph Security.

### <a name="manage-alerts"></a>Správa upozornění

Chcete-li filtrovat, řadit nebo získat nejnovější výsledky, zadejte *pouze* [parametry dotazu ODATA podporované aplikací Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Nezadávejte* úplnou základní adresu URL nebo například akci `GET` `PATCH` HTTP `https://graph.microsoft.com/v1.0/security/alerts`nebo operaci nebo. Tady je konkrétní příklad, který zobrazuje parametry akce **Získat výstrahy,** když chcete, aby byl seznam s výstrahami vysoké závažnosti:

`Filter alerts value as Severity eq 'high'`

Další informace o dotazech, které lze použít s tímto konektorem, naleznete v [referenční dokumentaci výstrah zabezpečení microsoft graphu](https://docs.microsoft.com/graph/api/alert-list). Chcete-li vytvořit rozšířené možnosti s tímto konektorem, další informace o [výstrahy vlastností schématu,](https://docs.microsoft.com/graph/api/resources/alert) které podporuje konektor.

| Akce | Popis |
|--------|-------------|
| **Dostávat upozornění** | Získejte upozornění filtrovaná na základě jedné nebo `Provider eq 'Azure Security Center' or 'Palo Alto Networks'`více [vlastností výstrah](https://docs.microsoft.com/graph/api/resources/alert), například . | 
| **Upozornění podle ID** | Získejte konkrétní výstrahu na základě ID výstrahy. | 
| **Upozornění na aktualizaci** | Aktualizujte konkrétní výstrahu na základě ID výstrahy. Chcete-li zajistit, že v žádosti předáte požadované a upravitelné vlastnosti, přečtěte [si informace o upravitelných vlastnostech výstrah](https://docs.microsoft.com/graph/api/alert-update). Chcete-li například přiřadit výstrahu analytikovi zabezpečení, aby mohl prozkoumat, můžete aktualizovat vlastnost **Přiřazeno k vlastnosti** výstrahy. |
|||

### <a name="manage-alert-subscriptions"></a>Správa odběrů výstrah

Microsoft Graph podporuje [*odběry*](https://docs.microsoft.com/graph/api/resources/subscription)nebo [*webhooky*](https://docs.microsoft.com/graph/api/resources/webhooks). Chcete-li získat, aktualizovat nebo odstranit odběry, zadejte [parametry dotazu ODATA](https://docs.microsoft.com/graph/query-parameters) `security/alerts` podporované Microsoft Graph na konstrukci entity Microsoft Graph a zahrnout následuje dotaz ODATA. *Nezahrnejte* základní adresu URL, `https://graph.microsoft.com/v1.0`například . Místo toho použijte formát v tomto příkladu:

`security/alerts?$filter=status eq 'New'`

| Akce | Popis |
|--------|-------------|
| **Vytvoření předplatných** | [Vytvořte předplatné,](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) které vás upozorní na všechny změny. Toto předplatné můžete filtrovat pro konkrétní typy výstrah, které chcete. Můžete například vytvořit předplatné, které vás upozorní na výstrahy vysoké závažnosti. |
| **Získání aktivních předplatných** | [Získejte neprošlá předplatná](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Aktualizovat předplatné** | [Aktualizujte předplatné](https://docs.microsoft.com/graph/api/subscription-update) poskytnutím ID předplatného. Chcete-li například rozšířit předplatné, můžete aktualizovat `expirationDateTime` vlastnost předplatného. | 
| **Odstranění předplatného** | [Odstraňte předplatné](https://docs.microsoft.com/graph/api/subscription-delete) poskytnutím ID předplatného. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Správa indikátorů analýzy hrozeb

Chcete-li filtrovat, řadit nebo získat nejnovější výsledky, zadejte *pouze* [parametry dotazu ODATA podporované aplikací Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Nezadávejte* úplnou základní adresu URL nebo například akci `GET` `PATCH` HTTP `https://graph.microsoft.com/beta/security/tiIndicators`nebo operaci nebo. Zde je konkrétní příklad, který zobrazuje parametry akce **Získat tiIndicators,** pokud `DDoS` chcete seznam, který má typ hrozby:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Další informace o dotazech, které lze použít s tímto konektorem, naleznete [v tématu Volitelné parametry dotazu v referenční dokumentaci indikátoru analýzy hrozeb zabezpečení microsoft graphu](https://docs.microsoft.com/graph/api/tiindicators-list?view=graph-rest-beta&tabs=http). Chcete-li vytvořit rozšířené možnosti s tímto konektorem, další informace o [indikátoru analýzy ohrožení vlastností schématu,](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta) který konektor podporuje.

| Akce | Popis |
|--------|-------------|
| **Získání indikátorů analýzy hrozeb** | Získejte filtrace tiIndicators na základě jedné nebo více [vlastností tiIndicator](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta), například`threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Získání indikátoru analýzy hrozeb podle ID** | Získejte konkrétní tiIndicator na základě ID tiIndicator. | 
| **Vytvoření indikátoru analýzy hrozeb** | Vytvořte nový tiIndicator zaúčtováním do kolekce tiIndicators. Chcete-li se ujistit, že předáte požadované vlastnosti v požadavku, podívejte se na [požadované vlastnosti pro vytvoření tiIndicator](https://docs.microsoft.com/graph/api/tiindicators-post?view=graph-rest-beta&tabs=http). |
| **Odeslání více indikátorů analýzy hrozeb** | Vytvořte více nových tiIndicators zaúčtováním kolekce tiIndicators. Chcete-li se ujistit, že předáte požadované vlastnosti v požadavku, podívejte se na [požadované vlastnosti pro odeslání více indikátorů tiIndicators](https://docs.microsoft.com/graph/api/tiindicator-submittiindicators?view=graph-rest-beta&tabs=http). |
| **Aktualizovat indikátor analýzy hrozeb** | Aktualizujte konkrétní tiIndicator na základě ID tiIndicator. Chcete-li se ujistit, že v požadavku předáte požadované a upravitelné vlastnosti, přečtěte [si upravitelné vlastnosti aplikace tiIndicator](https://docs.microsoft.com/graph/api/tiindicator-update?view=graph-rest-beta&tabs=http). Chcete-li například aktualizovat akci, která se použije, pokud je indikátor spárován z nástroje zabezpečení targetProduct, můžete aktualizovat vlastnost **akce** tiIndicator. |
| **Aktualizace více indikátorů analýzy hrozeb** | Aktualizujte více indikátorů tiIndicators. Chcete-li se ujistit, že předáte požadované vlastnosti v požadavku, podívejte se na [požadované vlastnosti pro aktualizaci více indikátorů tiIndicators](https://docs.microsoft.com/graph/api/tiindicator-updatetiindicators?view=graph-rest-beta&tabs=http). |
| **Odstranit indikátor analýzy hrozeb podle ID** | Odstraňte konkrétní tiIndicator na základě ID tiIndicator. |
| **Odstranění více indikátorů analýzy hrozeb podle ID** | Odstraňte více indikátorů tiIndicators podle jejich ID. Chcete-li se ujistit, že předáte požadované vlastnosti v požadavku, podívejte se na [požadované vlastnosti pro odstranění více indikátorů tiIndicators podle ID](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicators?view=graph-rest-beta&tabs=http). |
| **Odstranění více indikátorů analýzy hrozeb pomocí externích ID** | Odstraňte více indikátorů tiIndicators pomocí externích ID. Chcete-li se ujistit, že předáte požadované vlastnosti v požadavku, podívejte se na [požadované vlastnosti pro odstranění více indikátorů tiIndicators pomocí externích ID](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicatorsbyexternalid?view=graph-rest-beta&tabs=http). |
|||

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](https://aka.ms/graphsecurityconnectorreference).

## <a name="next-steps"></a>Další kroky

Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
