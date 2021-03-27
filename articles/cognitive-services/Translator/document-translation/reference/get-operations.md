---
title: Operace získání překladu dokumentů
titleSuffix: Azure Cognitive Services
description: Metoda get Operations vrátí seznam odeslaných požadavků dávky a stav pro každý požadavek.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c42f3081a831c267c7bc605267b99e2a916ea3d8
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612991"
---
# <a name="document-translation-get-operations"></a>Překlad dokumentů: načíst operace

Metoda get Operations vrátí seznam odeslaných požadavků dávky a stav pro každý požadavek. Tento seznam obsahuje jenom dávkové požadavky odeslané uživatelem (na základě předplatného). Stav každého požadavku je seřazen podle ID.

Pokud počet požadavků překročí limit stránkování, použije se stránkování na straně serveru. Stránkované odpovědi označují částečný výsledek a v odpovědi obsahují token pro pokračování. Absence tokenu pokračování znamená, že nejsou k dispozici žádné další stránky.

parametry dotazu $top a $skip lze použít k určení počtu výsledků, které se mají vrátit, a posunutí kolekce.

Server respektuje hodnoty určené klientem. Nicméně klienti musí být připraveni zpracovat odpovědi, které obsahují jinou velikost stránky nebo obsahují token pro pokračování.

Pokud jsou zahrnuty $top i $skip, server by měl nejprve použít $skip a pak $top na kolekci. 

> [!NOTE]
> Pokud server nemůže přijmout $top a/nebo $skip, server musí vrátit chybu do informování klienta o této chybě místo pouhého ignorování možností dotazu. Tím se snižuje riziko, že klient provádí předpoklady týkající se vrácených dat.

## <a name="request-url"></a>Adresa URL požadavku

Odeslat `GET` požadavek na:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
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
|$skip|Ne|Přeskočit $skip položky v kolekci. Pokud jsou zadány $top i $skip, je nejprve použita $skip.|
|$top|Ne|Převezme $top položky v kolekci. Pokud jsou zadány $top i $skip, je nejprve použita $skip.|

## <a name="request-headers"></a>Hlavičky požadavku

Hlavičky požadavku jsou:

|Hlavičky|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Požadovaná hlavička žádosti|

## <a name="response-status-codes"></a>Stavové kódy odpovědí

Níže jsou uvedené možné stavové kódy HTTP, které požadavek vrátí.

|Stavový kód|Description|
|--- |--- |
|200|OK. Úspěšný požadavek a vrátí stav všech operací. HeadersRetry-After: integerETag: String|
|400|Chybný požadavek. Neplatný požadavek Ověřte vstupní parametry.|
|401|Přístupu. Ověřte přihlašovací údaje.|
|500|Došlo k vnitřní chybě serveru.|
|Další stavové kódy|<ul><li>Příliš mnoho žádostí</li><li>Server je dočasně nedostupný.</li></ul>|

## <a name="get-operations-response"></a>Získat odpověď na operace

### <a name="successful-get-operations-response"></a>Úspěšná operace Get – odpověď

Následující informace jsou vráceny v úspěšné odpovědi.

|Název|Typ|Description|
|--- |--- |--- |
|id|řetězec|ID operace|
|createdDateTimeUtc|řetězec|Datum a čas vytvoření operace|
|lastActionDateTimeUtc|řetězec|Datum a čas, kdy se stav operace aktualizoval.|
|status|Řetězec|Seznam možných stavů pro úlohu nebo dokument: <ul><li>Zrušeno</li><li>Ruší</li><li>Neúspěšný</li><li>NotStarted</li><li>Spuštěno</li><li>Úspěšný</li><li>ValidationFailed</li></ul>|
|shrnutí|StatusSummary[]|Souhrn obsahující níže uvedené podrobnosti.|
|Summary. Total|integer|Počet celkových dokumentů|
|Shrnutí. selhalo|integer|Počet dokumentů se nezdařil.|
|Shrnutí. úspěch|integer|Počet dokumentů, které byly úspěšně přeloženy.|
|Shrnutí. probíhá zpracování|integer|Počet dokumentů, které probíhají.|
|Summary. notYetStarted|integer|Počet dokumentů, které ještě nebyly spuštěny při zpracování.|
|Shrnutí. zrušeno|integer|Počet zrušených dokumentů|
|Summary. totalCharacterCharged|integer|Celkový počet účtovaných znaků|

###<a name="error-response"></a>Chybová odezva

|Název|Typ|Description|
|--- |--- |--- |
|kód|řetězec|Výčty obsahující chybové kódy vysoké úrovně. Možné hodnoty:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Neautorizováno</li></ul>|
|zpráva|řetězec|Získá chybovou zprávu vysoké úrovně.|
|cílové|řetězec|Získá zdroj chyby. V případě neplatného dokumentu by to bylo například "dokumenty" nebo "ID dokumentu".|
|innerError|InnerErrorV2|Nový formát vnitřní chyby, který je v souladu s pokyny pro Cognitive Services rozhraní API. Obsahuje požadované vlastnosti ErrorCode, zpráva a volitelné vlastnosti cíl, podrobnosti (dvojice klíč-hodnota), vnitřní chyba (může být vnořený).|
|innerError. Code|řetězec|Načte řetězec chyby kódu.|
|innerError. Message|řetězec|Získá chybovou zprávu vysoké úrovně.|

## <a name="examples"></a>Příklady

### <a name="example-successful-response"></a>Příklad úspěšné odpovědi

Následuje příklad úspěšné odpovědi.

```JSON
{
  "value": [
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
  ]
}
```

### <a name="example-error-response"></a>Příklad chybové odpovědi

Následuje příklad chybové odpovědi. Schéma pro ostatní kódy chyb je stejné.

Stavový kód: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o použití překladu dokumentů a klientské knihovny, postupujte podle našeho rychlého startu.

> [!div class="nextstepaction"]
> [Začínáme s překladem dokumentů](../get-started-with-document-translation.md)
