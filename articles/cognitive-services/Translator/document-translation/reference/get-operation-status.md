---
title: Stav operace získání překladu dokumentu
titleSuffix: Azure Cognitive Services
description: Metoda get stav operace vrátí stav pro požadavek na překlad dokumentu.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: e8aa9b93dc089cc05dd5157a7ac84cceb5f6fcd5
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612988"
---
# <a name="document-translation-get-operation-status"></a>Překlad dokumentu: získat stav operace

Metoda get Documents status Returns vrátí stav pro požadavek na překlad dokumentu. Stav zahrnuje celkový stav žádosti a stav dokumentů, které jsou přeloženy v rámci této žádosti.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `GET` požadavek na:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
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
|id|Ano|ID operace.|

## <a name="request-headers"></a>Hlavičky požadavku

Hlavičky požadavku jsou:

|Hlavičky|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Požadovaná hlavička žádosti|

## <a name="response-status-codes"></a>Stavové kódy odpovědí

Níže jsou uvedené možné stavové kódy HTTP, které požadavek vrátí.

|Stavový kód|Description|
|--- |--- |
|200|OK. Úspěšná žádost a vrátí stav operace dávkového převodu. HeadersRetry-After: integerETag: String|
|401|Přístupu. Ověřte přihlašovací údaje.|
|404|Prostředek se nenašel.|
|500|Došlo k vnitřní chybě serveru.|
|Další stavové kódy|<ul><li>Příliš mnoho žádostí</li><li>Server je dočasně nedostupný.</li></ul>|

## <a name="get-operation-status-response"></a>Odpověď na stav operace Get

### <a name="successful-get-operation-status-response"></a>Úspěšná odpověď na stav operace Get

Následující informace jsou vráceny v úspěšné odpovědi.

|Název|Typ|Description|
|--- |--- |--- |
|id|řetězec|ID operace|
|createdDateTimeUtc|řetězec|Datum a čas vytvoření operace|
|lastActionDateTimeUtc|řetězec|Datum a čas, kdy se stav operace aktualizoval.|
|status|Řetězec|Seznam možných stavů pro úlohu nebo dokument: <ul><li>Zrušeno</li><li>Ruší</li><li>Neúspěšný</li><li>NotStarted</li><li>Spuštěno</li><li>Úspěšný</li><li>ValidationFailed</li></ul>|
|shrnutí|StatusSummary|Souhrn obsahující níže uvedené podrobnosti.|
|Summary. Total|integer|Celkový počet|
|Shrnutí. selhalo|integer|Počet neúspěšných|
|Shrnutí. úspěch|integer|Počet úspěšných.|
|Shrnutí. probíhá zpracování|integer|Počet probíhajících.|
|Summary. notYetStarted|integer|Počet, který se ještě nespustil.|
|Shrnutí. zrušeno|integer|Počet zrušených.|
|Summary. totalCharacterCharged|integer|Celkový počet znaků účtovaných rozhraním API|

###<a name="error-response"></a>Chybová odezva

|Název|Typ|Description|
|--- |--- |--- |
|kód|řetězec|Výčty obsahující chybové kódy vysoké úrovně. Možné hodnoty:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Neautorizováno</li></ul>|
|zpráva|řetězec|Získá chybovou zprávu vysoké úrovně.|
|cílové|řetězec|Získá zdroj chyby. Například by to byl "dokumenty" nebo "ID dokumentu" pro neplatný dokument.|
|innerError|InnerErrorV2|Nový formát vnitřní chyby, který je v souladu s pokyny pro Cognitive Services rozhraní API. Obsahuje požadované vlastnosti ErrorCode, zpráva a volitelné vlastnosti target, Details (dvojice klíč-hodnota), vnitřní chyba (může být vnořený).|
|innerError. Code|řetězec|Načte řetězec chyby kódu.|
|innerError. Message|řetězec|Získá chybovou zprávu vysoké úrovně.|

## <a name="examples"></a>Příklady

### <a name="example-successful-response"></a>Příklad úspěšné odpovědi

Následující objekt JSON je příkladem úspěšné odpovědi.

```JSON
{
  "id": "727bf148-f327-47a0-9481-abae6362f11e",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Succeeded",
  "summary": {
    "total": 10,
    "failed": 1,
    "success": 9,
    "inProgress": 0,
    "notYetStarted": 0,
    "cancelled": 0,
    "totalCharacterCharged": 0
  }
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
