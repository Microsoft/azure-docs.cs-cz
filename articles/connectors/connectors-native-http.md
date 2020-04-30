---
title: Volání koncových bodů služby pomocí protokolu HTTP nebo HTTPS
description: Odesílat odchozí požadavky HTTP nebo HTTPS do koncových bodů služby z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 9ed3d960b3f5653ea8706b39559c9d5a71c45a6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81867639"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Volání koncových bodů služby přes HTTP nebo HTTPS z Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrované triggeru nebo akce http můžete vytvářet automatizované úlohy a pracovní postupy, které odesílají požadavky do koncových bodů služby přes protokol HTTP nebo HTTPS. Například můžete monitorovat koncový bod služby pro svůj web tak, že zkontrolujete jeho koncový bod podle konkrétního plánu. Když v tomto koncovém bodu dojde k zadané události, jako je například váš web vypíná, událost aktivuje pracovní postup vaší aplikace logiky a spustí akce v tomto pracovním postupu. Pokud místo toho chcete přijmout příchozí volání HTTPS a reagovat na ně, použijte integrovaný [Trigger žádosti nebo akci reakce](../connectors/connectors-native-reqres.md).

> [!NOTE]
> Konektor HTTP podporuje na základě schopnosti cílového koncového bodu protokol TLS (Transport Layer Security) verze 1,0, 1,1 a 1,2. Logic Apps vyjednávat s koncovým bodem pomocí nejvyšší možné podporované verze. Pokud například koncový bod podporuje 1,2, použije se jako první konektor 1,2. V opačném případě konektor používá další nejvyšší podporovanou verzi.
>
> Konektor HTTP nepodporuje zprostředkované certifikáty TLS/SSL pro ověřování.

Pokud chcete zjistit nebo zadat *dotaz* na koncový bod podle opakovaného plánu, [přidejte Trigger http](#http-trigger) jako první krok pracovního postupu. Pokaždé, když Trigger zkontroluje koncový bod, Trigger zavolá nebo odešle *požadavek* na koncový bod. Odpověď koncového bodu Určuje, jestli je Workflow vaší aplikace logiky spuštěn. Aktivační událost předá do akcí ve vaší aplikaci logiky veškerý obsah z odpovědi koncového bodu.

Pokud chcete zavolat koncový bod odkudkoli jinde ve vašem pracovním postupu, [přidejte akci HTTP](#http-action). Odpověď koncového bodu Určuje, jak se spustí zbývající akce pracovního postupu.

> [!IMPORTANT]
> Pokud aktivační událost nebo akce HTTP obsahují tyto hlavičky, Logic Apps odebere tato záhlaví z vygenerované zprávy požadavku bez zobrazení upozornění nebo chyby:
>
> * `Accept-*`
> * `Allow`
> * `Content-*`s těmito výjimkami `Content-Disposition`: `Content-Encoding`, a`Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> I když Logic Apps nebrání v ukládání aplikací logiky, které používají Trigger HTTP nebo akci s těmito záhlavími, Logic Apps tyto hlavičky ignoruje.

Tento článek ukazuje, jak přidat Trigger nebo akci HTTP do pracovního postupu aplikace logiky.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa URL cílového koncového bodu, který chcete volat

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

* Aplikace logiky, ze které chcete zavolat cílový koncový bod. Pokud chcete začít s triggerem HTTP, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci HTTP, spusťte aplikaci logiky s libovolným triggerem, který chcete. V tomto příkladu se jako první krok používá Trigger HTTP.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Přidání triggeru HTTP

Tato integrovaná aktivační událost provede volání HTTP na zadanou adresu URL pro koncový bod a vrátí odpověď.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Otevřete prázdnou aplikaci logiky v návrháři aplikace logiky.

1. V poli hledání návrháře vyberte možnost **předdefinované**. Do vyhledávacího pole zadejte `http` jako filtr. V seznamu **triggery** vyberte Trigger **http** .

   ![Vybrat Trigger HTTP](./media/connectors-native-http/select-http-trigger.png)

   V tomto příkladu se aktivační událost přejmenuje na Trigger HTTP, aby měl krok výstižnější název. Dále tento příklad přidá akci HTTP a oba názvy musí být jedinečné.

1. Zadejte hodnoty pro [parametry triggeru protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) , které chcete zahrnout do volání cílového koncového bodu. Nastavte opakování pro to, jak často chcete, aby aktivační událost kontrolovala cílový koncový bod.

   ![Zadejte parametry triggeru protokolu HTTP.](./media/connectors-native-http/http-trigger-parameters.png)

   Pokud vyberete jiný typ ověřování než **žádné**, nastavení ověřování se liší v závislosti na vašem výběru. Další informace o typech ověřování dostupných pro protokol HTTP najdete v těchto tématech:

   * [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Ověření přístupu k prostředkům pomocí spravovaných identit](../logic-apps/create-managed-service-identity.md)

1. Chcete-li přidat další dostupné parametry, otevřete seznam **Přidat nový parametr** a vyberte požadované parametry.

1. Pokračujte v vytváření pracovního postupu aplikace logiky s akcemi, které se spustí, když se Trigger aktivuje.

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Přidat akci HTTP

Tato Vestavěná akce provede volání HTTP na zadanou adresu URL pro koncový bod a vrátí odpověď.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Otevřete aplikaci logiky v návrháři aplikace logiky.

   V tomto příkladu se jako první krok používá Trigger HTTP.

1. V kroku, kam chcete přidat akci HTTP, vyberte **Nový krok**.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. Vyberte symbol plus (**+**), který se zobrazí, a pak vyberte **přidat akci**.

1. V části **zvolit akci**vyberte **předdefinovaná**. Do vyhledávacího pole zadejte `http` jako filtr. V seznamu **Akce** vyberte akci **http** .

   ![Vybrat akci HTTP](./media/connectors-native-http/select-http-action.png)

   Tento příklad přejmenuje akci na "HTTP Action", takže krok má výstižnější název.

1. Zadejte hodnoty [parametrů akce protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) , které chcete zahrnout do cílového koncového bodu.

   ![Zadejte parametry akce HTTP.](./media/connectors-native-http/http-action-parameters.png)

   Pokud vyberete jiný typ ověřování než **žádné**, nastavení ověřování se liší v závislosti na vašem výběru. Další informace o typech ověřování dostupných pro protokol HTTP najdete v těchto tématech:

   * [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Ověření přístupu k prostředkům pomocí spravovaných identit](../logic-apps/create-managed-service-identity.md)

1. Chcete-li přidat další dostupné parametry, otevřete seznam **Přidat nový parametr** a vyberte požadované parametry.

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

## <a name="content-with-multipartform-data-type"></a>Obsah s datovým typem multipart/form-

Chcete-li zpracovat obsah `multipart/form-data` , který je typu v požadavcích http, můžete přidat objekt JSON, který `$content-type` obsahuje `$multipart` atributy a, do těla požadavku HTTP pomocí tohoto formátu.

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

Předpokládejme například, že máte aplikaci logiky, která odesílá požadavek HTTP POST pro excelový soubor na web pomocí rozhraní API tohoto webu, které podporuje daný `multipart/form-data` typ. Tady je postup, jak by tato akce mohla vypadat:

![Data formuláře v částech](./media/connectors-native-http/http-action-multipart.png)

Tady je stejný příklad, který ukazuje definici JSON akce HTTP v základní definici pracovního postupu:

```json
"HTTP_action": {
   "inputs": {
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

Další informace o parametrech Trigger a Action najdete v těchto částech:

* [Parametry triggeru HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parametry akce HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Podrobnosti výstupu

Zde jsou další informace o výstupech z triggeru nebo akce HTTP, které vrací tyto informace:

| Název vlastnosti | Typ | Popis |
|---------------|------|-------------|
| záhlaví | objekt | Hlavičky z požadavku |
| text | objekt | Objekt JSON | Objekt s obsahem textu z požadavku |
| stavový kód | int | Stavový kód z požadavku |
|||

| Kód stavu | Popis |
|-------------|-------------|
| 200 | OK |
| 202 | Accepted |
| 400 | Chybný požadavek |
| 401 | Neautorizováno |
| 403 | Forbidden |
| 404 | Nenalezeno |
| 500 | Vnitřní chyba serveru. Došlo k neznámé chybě. |
|||

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
