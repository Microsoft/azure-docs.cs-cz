---
title: Koncové body služby volání pomocí protokolu HTTP nebo HTTPS
description: Odesílání odchozích požadavků HTTP nebo HTTPS do koncových bodů služby z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 8aefe851708c0b8d8780d03e4364e034e783bf4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297188"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Koncové body služby volání přes HTTP nebo HTTPS z Azure Logic Apps

Pomocí [aplikací Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrované aktivační události nebo akce HTTP můžete vytvářet automatizované úlohy a pracovní postupy, které odesílají požadavky koncovým bodům služby přes HTTP nebo HTTPS. Můžete například sledovat koncový bod služby pro váš web kontrolou tohoto koncového bodu podle konkrétního plánu. Když se zadaná událost stane v tomto koncovém bodě, jako je například váš web bude dolů, událost aktivuje pracovní postup aplikace logiky a spustí akce v tomto pracovním postupu. Pokud chcete místo toho přijímat příchozí volání HTTPS a reagovat na ně, použijte předdefinovanou [akci aktivační události požadavku nebo odpovědi](../connectors/connectors-native-reqres.md).

> [!NOTE]
> Na základě funkce cílového koncového bodu podporuje konektor HTTP zabezpečení transportní vrstvy (TLS) verze 1.0, 1.1 a 1.2. Logic Apps vyjednává s koncovým bodem přes pomocí nejvyšší podporovanou verzi je to možné. Takže například pokud koncový bod podporuje 1.2, konektor používá 1.2 jako první. V opačném případě konektor používá další nejvyšší podporovanou verzi.

Chcete-li zkontrolovat nebo *dotazování* koncového bodu v opakovaném plánu, [přidejte aktivační událost HTTP](#http-trigger) jako první krok v pracovním postupu. Pokaždé, když aktivační událost zkontroluje koncový bod, aktivační událost volá nebo odešle *požadavek* na koncový bod. Odpověď koncového bodu určuje, zda je spuštěn pracovní postup aplikace logiky. Aktivační událost předá jakýkoli obsah z koncového bodu odpověď na akce ve vaší aplikaci logiky.

Chcete-li volat koncový bod z libovolného místa v pracovním postupu, [přidejte akci HTTP](#http-action). Odpověď koncového bodu určuje, jak budou spuštěny zbývající akce pracovního postupu.

> [!IMPORTANT]
> Pokud aktivační událost nebo akce protokolu HTTP obsahuje tato záhlaví, aplikace Logic Apps odebere tato záhlaví z generované zprávy požadavku bez zobrazení upozornění nebo chyby:
>
> * `Accept-*`
> * `Allow`
> * `Content-*`s těmito `Content-Disposition`výjimkami: , `Content-Encoding`a`Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Přestože aplikace Logika nezastaví ukládání aplikací logiky, které používají aktivační událost HTTP nebo akce s těmito záhlavími, Logic Apps ignoruje tyto hlavičky.

Tento článek ukazuje, jak přidat aktivační událost HTTP nebo akci do pracovního postupu aplikace logiky.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa URL cílového koncového bodu, který chcete volat

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud s aplikacemi logiky tečujete, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

* Aplikace logiky, odkud chcete volat cílový koncový bod. Chcete-li začít aktivační událostí PROTOKOLU HTTP, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci HTTP, spusťte aplikaci logiky s libovolnou aktivační událostí, kterou chcete. Tento příklad používá aktivační událost HTTP jako první krok.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Přidání aktivační události HTTP

Tato předdefinovaná aktivační událost provede volání HTTP na zadanou adresu URL pro koncový bod a vrátí odpověď.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Otevřete prázdnou aplikaci logiky v Návrháři aplikací logiky.

1. Pod vyhledávacím polem návrháře vyberte **Předdefinované**. Do vyhledávacího pole `http` zadejte jako filtr. V seznamu **Aktivační události** vyberte aktivační událost **HTTP.**

   ![Vybrat aktivační událost HTTP](./media/connectors-native-http/select-http-trigger.png)

   Tento příklad přejmenuje aktivační událost na "aktivační událost HTTP" tak, aby krok měl popisnější název. Příklad také později přidá akci HTTP a oba názvy musí být jedinečné.

1. Zadejte hodnoty parametrů [aktivační události PROTOKOLU HTTP,](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) které chcete zahrnout do volání cílového koncového bodu. Nastavte opakování pro to, jak často má aktivační událost kontrolovat cílový koncový bod.

   ![Zadat parametry aktivační události PROTOKOLU HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Pokud vyberete jiný typ ověřování než **Žádný**, nastavení ověřování se liší v závislosti na výběru. Další informace o typech ověřování dostupných pro protokol HTTP naleznete v těchto tématech:

   * [Přidání ověřování k odchozím hovorům](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Ověření přístupu k prostředkům pomocí spravovaných identit](../logic-apps/create-managed-service-identity.md)

1. Chcete-li přidat další dostupné parametry, otevřete seznam **Přidat nový parametr** a vyberte požadované parametry.

1. Pokračujte v vytváření pracovního postupu aplikace logiky pomocí akcí, které se spustí při aktivaci aktivační události.

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Přidání akce HTTP

Tato předdefinovaná akce provede volání HTTP na zadanou adresu URL pro koncový bod a vrátí odpověď.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Otevřete aplikaci logiky v Návrháři aplikací logiky.

   Tento příklad používá aktivační událost HTTP jako první krok.

1. Pod krokem, kam chcete přidat akci HTTP, vyberte **Nový krok**.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi kroky. Vyberte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. V části **Zvolit akci**vyberte **Předdefinované**. Do vyhledávacího pole `http` zadejte jako filtr. V seznamu **Akce** vyberte akci **HTTP.**

   ![Vybrat akci HTTP](./media/connectors-native-http/select-http-action.png)

   Tento příklad přejmenuje akci na "akce HTTP" tak, aby krok měl popisnější název.

1. Zadejte hodnoty pro [parametry akce HTTP,](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) které chcete zahrnout do volání cílového koncového bodu.

   ![Zadat parametry akce PROTOKOLU HTTP](./media/connectors-native-http/http-action-parameters.png)

   Pokud vyberete jiný typ ověřování než **Žádný**, nastavení ověřování se liší v závislosti na výběru. Další informace o typech ověřování dostupných pro protokol HTTP naleznete v těchto tématech:

   * [Přidání ověřování k odchozím hovorům](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Ověření přístupu k prostředkům pomocí spravovaných identit](../logic-apps/create-managed-service-identity.md)

1. Chcete-li přidat další dostupné parametry, otevřete seznam **Přidat nový parametr** a vyberte požadované parametry.

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

## <a name="content-with-multipartform-data-type"></a>Obsah s vícedílným/formulářovým datovým typem

Chcete-li zpracovat obsah, který má `multipart/form-data` typ v požadavcích HTTP, můžete přidat objekt JSON, který obsahuje atributy `$content-type` a `$multipart` do těla požadavku HTTP pomocí tohoto formátu.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Předpokládejme například, že máte aplikaci logiky, která odešle požadavek HTTP POST pro soubor `multipart/form-data` aplikace Excel na web pomocí rozhraní API tohoto webu, které podporuje typ. Tato akce může vypadat takto:

![Vícedílná data formuláře](./media/connectors-native-http/http-action-multipart.png)

Zde je stejný příklad, který ukazuje definici JSON akce HTTP v definici základního pracovního postupu:

```json
{
   "HTTP_action": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>Referenční informace ke konektorům

Další informace o parametrech aktivační události a akce naleznete v těchto částech:

* [Parametry aktivační události HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parametry akce HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Podrobnosti o výstupu

Zde je další informace o výstupech z aktivační události HTTP nebo akce, která vrací tyto informace:

| Název vlastnosti | Typ | Popis |
|---------------|------|-------------|
| Záhlaví | objekt | Záhlaví z požadavku |
| text | objekt | Objekt JSON | Objekt s obsahem těla z požadavku |
| stavový kód | int | Stavový kód z požadavku |
|||

| Kód stavu | Popis |
|-------------|-------------|
| 200 | OK |
| 202 | Accepted |
| 400 | Chybný požadavek |
| 401 | Neautorizováno |
| 403 | Forbidden |
| 404 | Nebyl nalezen. |
| 500 | Vnitřní chyba serveru. Došlo k neznámé chybě. |
|||

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
