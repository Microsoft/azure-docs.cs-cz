---
title: Volání koncových bodů HTTP a HTTPS – Azure Logic Apps
description: Odeslání odchozích požadavků do koncových bodů HTTP a HTTPS pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: df856e0d76dbd5903964bc80aa01b97b7461128a
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122702"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>Odeslání odchozích volání do koncových bodů HTTP nebo HTTPS pomocí Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrované triggeru nebo akce http můžete vytvářet automatizované úlohy a pracovní postupy, které pravidelně odesílají požadavky do libovolného koncového bodu http nebo HTTPS. Pokud místo toho chcete přijmout příchozí volání HTTP nebo HTTPS a reagovat na ně, použijte integrovaný [Trigger žádosti nebo akci reakce](../connectors/connectors-native-reqres.md).

Například můžete monitorovat koncový bod služby pro svůj web tak, že zkontrolujete jeho koncový bod podle zadaného plánu. Když v tomto koncovém bodu dojde k určité události, jako je například váš web vypíná, událost spustí pracovní postup vaší aplikace logiky a spustí zadané akce.

Pokud chcete koncový bod vyhledat nebo spustit *dotaz* na pravidelný plán, můžete jako první krok v pracovním postupu použít Trigger http. Při každé kontrole aktivační událost odesílá volání nebo *požadavek* na koncový bod. Odpověď koncového bodu Určuje, jestli je Workflow vaší aplikace logiky spuštěn. Aktivační událost projde veškerý obsah z odpovědi na akce ve vaší aplikaci logiky.

Akci HTTP můžete použít jako jakýkoliv jiný krok pracovního postupu pro volání koncového bodu, pokud chcete. Odpověď koncového bodu Určuje, jak se spustí zbývající akce pracovního postupu.

Konektor HTTP podporuje na základě schopnosti cílového koncového bodu protokol TLS (Transport Layer Security) verze 1,0, 1,1 a 1,2. Logic Apps vyjednávat s koncovým bodem pomocí nejvyšší možné podporované verze. Pokud například koncový bod podporuje 1,2, konektor používá nejprve 1,2. V opačném případě konektor používá další nejvyšší podporovanou verzi.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa URL cílového koncového bodu, který chcete volat

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikace logiky, ze které chcete zavolat cílový koncový bod. Pokud chcete začít s triggerem HTTP, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci HTTP, spusťte aplikaci logiky s libovolným triggerem, který chcete. V tomto příkladu se jako první krok používá Trigger HTTP.

## <a name="add-an-http-trigger"></a>Přidání triggeru HTTP

Tato integrovaná aktivační událost provede volání HTTP na zadanou adresu URL pro koncový bod a vrátí odpověď.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Otevřete prázdnou aplikaci logiky v návrháři aplikace logiky.

1. V návrháři do vyhledávacího pole zadejte "http" jako filtr. V seznamu **triggery** vyberte Trigger **http** .

   ![Vybrat Trigger HTTP](./media/connectors-native-http/select-http-trigger.png)

   V tomto příkladu se aktivační událost přejmenuje na Trigger HTTP, aby měl krok výstižnější název. Dále tento příklad přidá akci HTTP a oba názvy musí být jedinečné.

1. Zadejte hodnoty pro [parametry triggeru protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) , které chcete zahrnout do volání cílového koncového bodu. Nastavte opakování pro to, jak často chcete, aby aktivační událost kontrolovala cílový koncový bod.

   ![Zadejte parametry triggeru protokolu HTTP.](./media/connectors-native-http/http-trigger-parameters.png)

   Další informace o typech ověřování dostupných pro protokol HTTP najdete v tématu [ověřování aktivačních procedur a akcí http](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Chcete-li přidat další dostupné parametry, otevřete seznam **Přidat nový parametr** a vyberte požadované parametry.

1. Pokračujte v vytváření pracovního postupu aplikace logiky s akcemi, které se spustí, když se Trigger aktivuje.

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

## <a name="add-an-http-action"></a>Přidat akci HTTP

Tato Vestavěná akce provede volání HTTP na zadanou adresu URL pro koncový bod a vrátí odpověď.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Otevřete aplikaci logiky v návrháři aplikace logiky.

   V tomto příkladu se jako první krok používá Trigger HTTP.

1. V kroku, kam chcete přidat akci HTTP, vyberte **Nový krok**.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. V návrháři do vyhledávacího pole zadejte "http" jako filtr. V seznamu **Akce** vyberte akci **http** .

   ![Vybrat akci HTTP](./media/connectors-native-http/select-http-action.png)

   Tento příklad přejmenuje akci na "HTTP Action", takže krok má výstižnější název.

1. Zadejte hodnoty [parametrů akce protokolu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) , které chcete zahrnout do cílového koncového bodu.

   ![Zadejte parametry akce HTTP.](./media/connectors-native-http/http-action-parameters.png)

   Další informace o typech ověřování dostupných pro protokol HTTP najdete v tématu [ověřování aktivačních procedur a akcí http](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

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

Další informace o parametrech Trigger a Action najdete v těchto částech:

* [Parametry triggeru HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [Parametry akce HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Podrobnosti výstupu

Zde jsou další informace o výstupech z triggeru nebo akce HTTP, které vrací tyto informace:

| Název vlastnosti | type | Popis |
|---------------|------|-------------|
| hlavičky | object | Hlavičky z požadavku |
| těles | object | Objekt JSON | Objekt s obsahem textu z požadavku |
| Stavový kód | int | Stavový kód z požadavku |
|||

| Stavový kód | Popis |
|-------------|-------------|
| 200 | OK |
| 202 | Přijato |
| 400 | Nesprávná žádost |
| 401 | Neautorizováno |
| 403 | Zakázáno |
| 404 | Nenalezené |
| 500 | Došlo k vnitřní chybě serveru. Došlo k neznámé chybě. |
|||

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
