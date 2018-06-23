---
title: Překladač Microsoft Text API V3.0 odkaz | Microsoft Docs
description: Referenční dokumentace rozhraní API aktualizace V3.0 Microsoft překladač Text.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: cfaa9584e833b137b417d9074fbfcf606eb21388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342804"
---
#<a name="translator-text-api-v30"></a>Překladač Text API v3.0

## <a name="whats-new"></a>Co je nového?

Verze 3 rozhraní API služby Microsoft překladač Text poskytuje moderní Web API založenými na JSON. Vylepšuje použitelnost a výkon sloučením stávajících funkcí do menšího počtu operací a poskytuje nové funkce.

 * Přepis převést text v jednom jazyce z jednoho skriptu na jiném skriptu.
 * Překlad, aby více jazyků v jedné žádosti.
 * Zjišťování jazyka, překlad a přepis v jedné žádosti.
 * Slovník k vyhledávání alternativní překlady podmínek, najít back překlady a příklady zobrazující termínů používaných v kontextu.
 * Výsledky vyhledávání informativnější jazyk.

## <a name="base-urls"></a>Základní adresy URL

Rozhraní API v3.0 textu je k dispozici v následujících cloudu:

| Popis | Oblast | Základní adresa URL                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | Globální | API.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>Authentication

Přihlášení k odběru překladač API Text v kognitivní služby společnosti Microsoft a použít svůj klíč předplatného (k dispozici na webu Azure portal) k ověření. 

Nejjednodušší způsob je předat vaše Azure tajný klíč služby překladač pomocí hlavička požadavku `Ocp-Apim-Subscription-Key`.

Další možností je použití tajný klíč k získání autorizační token od služby tokenů. Pak předejte autorizační token překladač služby pomocí `Authorization` hlavičky žádosti. Ujistěte se, pokud chcete získat autorizační token, `POST` požadavek na následující adresu URL:

| Prostředí     | Adresa URL služby ověřování                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Zde je příklad žádosti k získání tokenu Zadaný tajný klíč:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

V případě úspěšné žádosti vrátí kódovaného přístupový token jako prostý text v textu odpovědi. Platný token předaný překladač služby, jako token nosiče v povolení.

```
Authorization: Bearer <Base64-access_token>
```

Ověřovací token je platný 10 minut. Token musí být znovu použít při vytváření více volání rozhraní API překladač. Ale pokud váš program provede požadavky rozhraní API překladač po delší dobu, pak vašeho programu musíte požádat o nový přístupový token v pravidelných intervalech (například každých 8 minut).

To Shrneme, bude požadavek klienta do rozhraní API překladač obsahovat jeden autorizační hlavičky prováděné v následující tabulce:

<table width="100%">
  <th width="30%">Záhlaví</th>
  <th>Popis</th>
  <tr>
    <td>OCP-Apim-Subscription-Key</td>
    <td>*Použít s předplatným služby kognitivní předáte tajný klíč*.<br/>Hodnota je Azure tajný klíč pro vaše předplatné přístup k rozhraní API Text překladač.</td>
  </tr>
  <tr>
    <td>Autorizace</td>
    <td>*Použít s předplatným služby kognitivní předáte ověřovací token.*<br/>Hodnota je token nosiče: ' nosiče <token>'.</td>
  </tr>
</table> 

## <a name="errors"></a>Chyby

Standardní cíl chybové odpovědi je objekt JSON dvojice název hodnota s názvem `error`. Hodnota je také objekt JSON s vlastnostmi:

  * `code`: Kód chyby definované na serveru.

  * `message`: Řetězec poskytnutí čitelná pro člověka reprezentaci této chyby.

Zákazník s bezplatnou zkušební verzi předplatného by například dojít k následující chybě po vyčerpání volného kvóty:

```
{
  "error": {
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
