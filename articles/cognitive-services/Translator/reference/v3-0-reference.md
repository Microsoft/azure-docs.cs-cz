---
title: Odkaz na Translator Text API verze 3.0
titlesuffix: Azure Cognitive Services
description: Referenční dokumentace pro verze Translator Text API 3.0.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 9282d8af30cbfb3346394bcd71510faf8d8c8a21
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129382"
---
# <a name="translator-text-api-v30"></a>Verze Translator Text API 3.0

## <a name="whats-new"></a>Co je nového?

Verze 3 rozhraní Translator Text API poskytuje moderní webové API založené na JSON. Zvyšuje použitelnost a výkon, když sloučení stávajících funkcí do menšího počtu operací a nabízí nové funkce.

 * Přepis pro převod textu v jednom jazyce z jeden skript na jiný skript.
 * Překlad do několika jazyků v jedné žádosti.
 * Rozpoznávání jazyka, překlad a přepis v jedné žádosti.
 * Slovník, který se vyhledávací termín najít back překlady a příklady znázorňující termínů používaných v kontextu alternativní překlady.
 * Další výsledky vyhledávání jazyka.

## <a name="base-urls"></a>Základní adresy URL

Text API v3.0 je dostupná v následujících cloudu:

| Popis | Oblast | Základní adresa URL                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | Globální | API.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>Authentication

Přihlaste se k rozhraní Translator Text API ve službě Microsoft Cognitive Services odběru a používat váš klíč předplatného (k dispozici na webu Azure Portal) k ověřování. 

Nejjednodušším způsobem je předat tajný klíč Azure ke službě Translator, pomocí hlavičky žádosti `Ocp-Apim-Subscription-Key`.

Alternativou je můžete získat autorizační token od služby tokenů tajný klíč. Potom pomocí službu Translator předat ověřovací token, který `Authorization` hlavičky žádosti. Ujistěte se, pokud chcete získat autorizační token, `POST` žádost na následující adresu URL:

| Prostředí     | Adresa URL pro ověřování služby                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Tady je příklad žádosti k získání tokenu Zadaný tajný klíč:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Úspěšná žádost vrátí kódovaného přístupový token jako prostý text v textu odpovědi. Službu Translator jako nosný token v povolení předána platný token.

```
Authorization: Bearer <Base64-access_token>
```

Ověřovací token je platný 10 minut. Token, který by měl být znovu použít při vytváření více volání rozhraní Translator API. Ale pokud váš program provede požadavky rozhraní Translator API po delší dobu, potom aplikace musí požádat o nový přístupový token v pravidelných intervalech (například každých 8 minut).

Souhrnně řečeno, bude požadavek klienta na Translator API obsahovat jednu hlavičku autorizace na základě následující tabulky:

<table width="100%">
  <th width="30%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>OCP-Apim-Subscription-Key</td>
    <td>*Použít s předplatným služeb Cognitive Services, Pokud předáváte tajný klíč*.<br/>Hodnota je Azure tajný klíč pro vaše předplatné Translator Text API.</td>
  </tr>
  <tr>
    <td>Autorizace</td>
    <td>*Použít s předplatným služeb Cognitive Services, Pokud předáváte ověřovací token.*<br/>Hodnota nosný token je: "nosiče <token>".</td>
  </tr>
</table> 

## <a name="errors"></a>Chyby

Standardní chybové odpovědi je objekt JSON s dvojice název/hodnota s názvem `error`. Hodnota je také objekt JSON s vlastnostmi:

  * `code`: Kód chyby definované na serveru.

  * `message`: Řetězec poskytuje čitelné reprezentaci chyby.

Zákazník s bezplatnou zkušební verzi předplatného by například zobrazí následující chyba, po vyčerpání volného kvóty:

```
{
  "error": {
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
