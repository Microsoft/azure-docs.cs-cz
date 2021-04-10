---
title: Metoda překladu dokumentu získat stav dokumentu
titleSuffix: Azure Cognitive Services
description: Metoda získat stav dokumentu vrátí stav pro určitý dokument.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 79bc3d076c1a7e164cab9c3231b29be84370e04a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613004"
---
# <a name="document-translation-get-document-status"></a>Překlad dokumentu: získat stav dokumentu

Metoda získat stav dokumentu vrátí stav pro určitý dokument. Metoda vrátí stav překladu pro určitý dokument na základě ID žádosti a ID dokumentu.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `GET` požadavek na:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents/{documentId}
```

Přečtěte si, jak najít [vlastní název domény](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Všechny požadavky rozhraní API na službu překladu dokumentů vyžadují vlastní koncový bod domény**.
> * Koncový bod, který se nachází na vašich Azure Portal _klíčů prostředků a koncových_ bodech globálního překladatele, nemůžete použít `api.cognitive.microsofttranslator.com` k převedení požadavků HTTP na překlad dokumentů.

## <a name="request-parameters"></a>Parametry žádosti

Parametry žádosti předané řetězci dotazu jsou:

|Parametr dotazu|Povinné|Popis|
|--- |--- |--- |
|documentId|Ano|ID dokumentu|
|id|Ano|ID dávky.|
## <a name="request-headers"></a>Hlavičky požadavku

Hlavičky požadavku jsou:

|Hlavičky|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Požadovaná hlavička žádosti|

## <a name="response-status-codes"></a>Stavové kódy odpovědí

Níže jsou uvedené možné stavové kódy HTTP, které požadavek vrátí.

|Stavový kód|Description|
|--- |--- |
|200|OK. Požadavek je úspěšný a služba ho přijímá. Vrátí se podrobnosti o operaci. HeadersRetry-After: integerETag: String|
|401|Přístupu. Ověřte přihlašovací údaje.|
|404|Nenalezeno. Prostředek se nenašel.|
|500|Došlo k vnitřní chybě serveru.|
|Další stavové kódy|<ul><li>Příliš mnoho žádostí</li><li>Server je dočasně nedostupný.</li></ul>|

## <a name="get-document-status-response"></a>Získat odpověď na stav dokumentu

### <a name="successful-get-document-status-response"></a>Odpověď na stav dokumentu se úspěšně zobrazila

|Název|Typ|Description|
|--- |--- |--- |
|program|řetězec|Umístění dokumentu nebo složky.|
|createdDateTimeUtc|řetězec|Datum a čas vytvoření operace|
|lastActionDateTimeUtc|řetězec|Datum a čas, kdy se stav operace aktualizoval.|
|status|Řetězec|Seznam možných stavů pro úlohu nebo dokument: <ul><li>Zrušeno</li><li>Ruší</li><li>Neúspěšný</li><li>NotStarted</li><li>Spuštěno</li><li>Úspěšný</li><li>ValidationFailed</li></ul>|
|na|řetězec|Kód jazyka se dvěma písmeny do jazyka. Podívejte se na seznam jazyků.|
|progress|číslo|Průběh překladu, je-li k dispozici|
|id|řetězec|ID dokumentu|
|characterCharged|integer|Znaky účtované rozhraním API|

### <a name="error-response"></a>Chybová odezva

|Název|Typ|Description|
|--- |--- |--- |
|kód|řetězec|Výčty obsahující chybové kódy vysoké úrovně. Možné hodnoty:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Neautorizováno</li></ul>|
|zpráva|řetězec|Získá chybovou zprávu vysoké úrovně.|
|innerError|InnerErrorV2|Nový formát vnitřní chyby, který je v souladu s pokyny pro Cognitive Services rozhraní API. Obsahuje požadované vlastnosti ErrorCode, zpráva a volitelné vlastnosti target, Details (dvojice klíč-hodnota), vnitřní chyba (může být vnořený).|
|innerError. Code|řetězec|Načte řetězec chyby kódu.|
|innerError. Message|řetězec|Získá chybovou zprávu vysoké úrovně.|

## <a name="examples"></a>Příklady

### <a name="example-successful-response"></a>Příklad úspěšné odpovědi
Následující objekt JSON je příkladem úspěšné odpovědi.

```JSON
{
  "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Running",
  "to": "fr",
  "progress": 0.1,
  "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
  "characterCharged": 0
}
```

### <a name="example-error-response"></a>Příklad chybové odpovědi

Následující objekt JSON je příkladem chybové odpovědi. Schéma pro ostatní kódy chyb je stejné.

Stavový kód: 401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o použití překladu dokumentů a klientské knihovny, postupujte podle našeho rychlého startu.

> [!div class="nextstepaction"]
> [Začínáme s překladem dokumentů](../get-started-with-document-translation.md)
