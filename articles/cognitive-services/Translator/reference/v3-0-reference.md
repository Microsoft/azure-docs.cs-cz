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
ms.openlocfilehash: 8302a444f28e4fb330a1eedbac9a5da762979d6c
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52681955"
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

Microsoft Translator je provádět z více umístění datového centra. Aktuálně jsou umístěny v 6 [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Jižní Americe:** západní USA 2 a střed USA – západ 
* **Asie a Tichomoří:** jihovýchodní Asii a Korea – jih
* **Evropa:** Severní Evropa a západní Evropa

Požadavky na rozhraní Microsoft Translator Text API jsou ve většině případů zpracovat datacenter, která je nejblíž k původu žádosti. V případě datacenter selhání může být žádost směrována mimo oblast.

Vynutit požadavek na ošetřit konkrétní datové centrum, změňte globální koncový bod rozhraní API požadavek na požadovanou místní koncový bod:

|Popis|Oblast|Základní adresa URL|
|:--|:--|:--|
|Azure|Globální|  API.cognitive.microsofttranslator.com|
|Azure|Severní Amerika|   rozhraní API nam.cognitive.microsofttranslator.com|
|Azure|Evropa|  rozhraní API eur.cognitive.microsofttranslator.com|
|Azure|Asie a Tichomoří|    rozhraní API apc.cognitive.microsofttranslator.com|


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
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Kód chyby je, chybu zařadit 6místným číselným číslo kombinování stavový kód HTTP 3 číslice následované číslem 3 číslice na další. Běžné kódy chyb jsou:

| Kód | Popis |
|:----|:-----|
| 400000| Jeden ze vstupů požadavku není platný.|
| 400001| Parametr "scope" je neplatná.|
| 400002| Parametr "kategorie" je neplatný.|
| 400003| Specifikátor jazyk je chybějící nebo neplatný.|
| 400004| Cílový specifikátor skriptu ("do skriptu") je chybějící nebo neplatný.|
| 400005| Vstupní text je chybějící nebo neplatný.|
| 400006| Kombinace jazyka a skriptu není platná.|
| 400018| Specifikátorem zdroje skriptu ("ze skriptu") je chybějící nebo neplatný.|
| 400019| Jeden zadaný jazyk není podporován.|
| 400020| Jeden z prvků v poli vstupní text není platný.|
| 400021| Parametr verze rozhraní API je chybějící nebo neplatný.|
| 400023| Jedna z dvojice zadaný jazyk není platný.|
| 400035| Zdrojový jazyk ("z" pole) není platný.|
| 400036| Cílový jazyk ("do" pole) je chybějící nebo neplatný.|
| 400042| Jednu z možností zadané ("Options" pole) není platný.|
| 400043| ID trasování klienta (ClientTraceId pole nebo záhlaví X-ClientTranceId) je chybějící nebo neplatný.|
| 400050| Vstupní text je příliš dlouhý.|
| 400064| Parametr "překladu" je chybějící nebo neplatný.|
| 400070| Počet skriptů target (ToScript parametr) neodpovídá počtu cíl jazyky (parametr).|
| 400071| Hodnota není platná pro TextType.|
| 400072| Vstupní textové pole má příliš mnoho prvků.|
| 400073| Parametr skriptu není platná.|
| 400074| Text žádosti nemá platný formát JSON.|
| 400075| Kombinace jazyka párování a kategorii není platné.|
| 400077| Byla překročena maximální požadavek velikost.|
| 400079| Vlastní systém požadovaný pro překlad mezi z a do jazyka neexistuje.|
| 401000| Požadavek není autorizovaný, protože jsou přihlašovací údaje chybí nebo je neplatný.|
| 401015| "Zadané přihlašovací údaje jsou pro rozhraní API pro rozpoznávání řeči. Tato žádost vyžaduje přihlašovací údaje pro rozhraní Text API. Použijte prosím předplatné Translator Text API."|
| 403000| Operace není povolena.|
| 403001| Operace není povolena, protože předplatné překročila její kvótu.|
| 405000| Metoda žádosti se nepodporuje pro požadovaný prostředek.|
| 408001| Vlastní překladový systém požadovaný ještě není k dispozici. Zkuste to prosím znovu za pár minut.|
| 415000| Hlavička Content-Type je chybějící nebo neplatný.|
| 429000, 429001, 429002| Server odmítl požadavek, protože klient zasílá příliš mnoho požadavků. Snižte frekvenci požadavků vyhnout omezení šířky pásma.|
| 500000| Došlo k neočekávané chybě. Pokud chyba přetrvává, nahlaste to datum/čas chyby, požádejte o identifikátor odpovědi hlavičku X-RequestId a identifikátor klienta z hlavičky X-ClientTraceId.|
| 503000| Služba je dočasně nedostupná. Zkuste to prosím znovu. Pokud chyba přetrvává, nahlaste to datum/čas chyby, požádejte o identifikátor odpovědi hlavičku X-RequestId a identifikátor klienta z hlavičky X-ClientTraceId.|

