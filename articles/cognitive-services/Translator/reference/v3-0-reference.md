---
title: Referenční dokumentace k Translator Text API V 3.0
titleSuffix: Azure Cognitive Services
description: Referenční dokumentace pro Translator Text API V 3.0
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: a441ca83230a1c715aadda79683964aaab6d6213
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72252968"
---
# <a name="translator-text-api-v30"></a>Translator Text API v 3.0

## <a name="whats-new"></a>Co je nového

Verze 3 Translator Text API poskytuje moderní webové rozhraní API založené na formátu JSON. Zlepšuje použitelnost a výkon tím, že konsoliduje stávající funkce do menšího počtu operací a poskytuje nové funkce.

 * Převádění pro převod textu v jednom jazyce z jednoho skriptu na jiný skript.
 * Překlad do několika jazyků v jednom požadavku.
 * Rozpoznání, překlad a převod jazyka v jednom požadavku.
 * Slovník pro vyhledání alternativních překladů termínu k nalezení zpětných překladů a příkladů, které označují podmínky používané v kontextu.
 * Přesnější výsledky detekce jazyka.

## <a name="base-urls"></a>Základní adresy URL

Microsoft Translator se obsluhuje z více umístění datových center. V současné době se nacházejí ve 10 [zeměpisných oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Severní Amerika:** Východní USA, Střed USA – jih, Středozápadní USA a Západní USA 2 
* **Asie a Tichomoří:** Korea – jih, Japonsko – východ, jihovýchodní Asie a Austrálie – východ
* **Evropa:** Severní Evropa a Západní Evropa

Požadavky na Microsoft Translator Text API jsou ve většině případů zpracovávány v datacentru, které je nejblíže místu, kde požadavek vznikl. V případě selhání datacentra se může žádost směrovat mimo zeměpisnou oblast Azure.

Pokud chcete vynutit zpracování žádosti konkrétními geografickými oblastmi Azure, změňte globální koncový bod v požadavku rozhraní API na požadovaný oblastní koncový bod:

|Description|Geografické Azure|Základní adresa URL|
|:--|:--|:--|
|Azure|Globální (mimo oblast)|   api.cognitive.microsofttranslator.com|
|Azure|Spojené státy|   api-nam.cognitive.microsofttranslator.com|
|Azure|Evropa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asie a Tichomoří|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Ověřování

Přihlaste se k odběru služby Translator Text API nebo [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) ve službě Azure Cognitive Services a použijte svůj klíč předplatného (k dispozici v Azure Portal) k ověření. 

K ověření předplatného můžete použít tři hlavičky. Tato tabulka popisuje, jak se používají jednotlivé tyto funkce:

|Hlavičky|Description|
|:----|:----|
|OCP-APIM-Subscription-Key|Pokud předáváte *tajný klíč, použijte u předplatného Cognitive Services*.<br/>Hodnota je tajný klíč Azure pro vaše předplatné Translator Text API.|
|Autorizace|*Pokud předáváte ověřovací token, použijte u předplatného Cognitive Services.*<br/>Hodnota je nosný token: `Bearer <token>`.|
|OCP – APIM – předplatné – oblast|*Pokud předáváte tajný klíč s více službami, použijte u služby Cognitive Services předplatné s více službami.*<br/>Hodnota je oblast pro předplatné s více službami. Tato hodnota je volitelná, pokud nepoužíváte předplatné s více službami.|

###  <a name="secret-key"></a>Tajný klíč
První možností je ověřit pomocí hlavičky `Ocp-Apim-Subscription-Key`. Přidejte do žádosti hlavičku `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>`.

### <a name="authorization-token"></a>Autorizační token
Alternativně můžete pro přístupový token vyměňovat svůj tajný klíč. Tento token je součástí každé žádosti jako hlavička `Authorization`. Pokud chcete získat autorizační token, udělejte na následující adrese URL požadavek `POST`:

| Hlediska     | Adresa URL ověřovací služby                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Tady je příklad žádostí o získání tokenu, který má daný tajný klíč:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Úspěšný požadavek vrátí kódovaný přístupový token jako prostý text v těle odpovědi. Platný token se předává službě Translator jako nosný token v autorizaci.

```http
Authorization: Bearer <Base64-access_token>
```

Ověřovací token je platný po dobu 10 minut. Token by se měl znovu použít při provádění více volání rozhraní API pro překladatele. Pokud však váš program provede v delší době požadavky na rozhraní API pro překladatele, musí váš program požádat o nový přístupový token v pravidelných intervalech (například každých 8 minut).

### <a name="multi-service-subscription"></a>Předplatné s více službami

Poslední možností ověřování je použití předplatného služby pro rozpoznávání služeb. Díky tomu můžete k ověřování žádostí o více služeb použít jediný tajný klíč. 

Když použijete tajný klíč s více službami, musíte do své žádosti zahrnout dvě ověřovací hlavičky. První předá tajný klíč, druhý určuje oblast přidruženou k vašemu předplatnému. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

Pro předplatné rozhraní API pro více služeb text je vyžadována oblast. Oblast, kterou jste vybrali, je jediná oblast, kterou můžete použít pro překlad textu při použití klíče předplatného s více službami a musí se jednat o stejnou oblast, jakou jste zvolili při registraci k předplatnému více služeb prostřednictvím Azure Portal.

Dostupné oblasti jsou `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centralus`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `francecentral`, 0, 2, 3, 4, 5, 6, 8, 9, 0 @no__t t-21 a 2.

Pokud předáte tajný klíč v řetězci dotazu s parametrem `Subscription-Key`, je nutné zadat oblast s parametrem dotazu `Subscription-Region`.

Pokud používáte nosný token, musíte získat token z koncového bodu oblasti: `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`.


## <a name="errors"></a>Chyby

Standardní chybová odpověď je objekt JSON s dvojicí název/hodnota s názvem `error`. Hodnota je také objekt JSON s vlastnostmi:

  * `code`: kód chyby definovaný serverem.
  * `message`: řetězec, který poskytuje reprezentaci chyby v čitelném člověkě.

Například zákazník s bezplatným zkušebním předplatným může po vyčerpání bezplatné kvóty získat následující chybu:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Kód chyby je číslo na 6 číslic, ve kterém se kombinují stavový kód HTTP s kódem, za nímž následuje 3 číslice a další kategorizace chyby. Běžné kódy chyb:

| Kód | Description |
|:----|:-----|
| 400000| Jeden ze vstupů požadavku není platný.|
| 400001| Parametr scope je neplatný.|
| 400002| Parametr Category je neplatný.|
| 400003| Specifikátor jazyka chybí nebo je neplatný.|
| 400004| Specifikátor cílového skriptu ("ke skriptu") chybí nebo je neplatný.|
| 400005| Vstupní text nebyl nalezen nebo je neplatný.|
| 400006| Kombinace jazyka a skriptu není platná.|
| 400018| Specifikátor zdrojového skriptu ("ze skriptu") chybí nebo je neplatný.|
| 400019| Jeden ze zadaných jazyků není podporován.|
| 400020| Jeden z prvků v poli vstupního textu není platný.|
| 400021| Parametr verze rozhraní API chybí nebo je neplatný.|
| 400023| Jedna z zadaných párů jazyků není platná.|
| 400035| Zdrojový jazyk (pole od) není platný.|
| 400036| Cílový jazyk (do pole "do") chybí nebo je neplatný.|
| 400042| Jedna ze zadaných možností (pole "Options") není platná.|
| 400043| ID trasování klienta (pole ClientTraceId nebo hlavička X-ClientTranceId) chybí nebo je neplatné.|
| 400050| Vstupní text je příliš dlouhý. Zobrazit [omezení požadavků](../request-limits.md).|
| 400064| Parametr překladu chybí nebo je neplatný.|
| 400070| Počet cílových skriptů (parametr ToScript) se neshoduje s počtem cílových jazyků (do parametru).|
| 400071| Hodnota není platná pro TextType.|
| 400072| Pole vstupního textu má příliš mnoho prvků.|
| 400073| Parametr skriptu je neplatný.|
| 400074| Tělo požadavku není platný formát JSON.|
| 400075| Kombinace jazyků a kategorií není platná.|
| 400077| Překročila se maximální velikost požadavku. Zobrazit [omezení požadavků](../request-limits.md).|
| 400079| Vlastní systém požadovaný pro překlad mezi jazyky a a neexistuje.|
| 400080| Pro jazyk nebo skript není podporováno překódování.|
| 401000| Požadavek není autorizovaný, protože přihlašovací údaje chybí nebo jsou neplatné.|
| 401015| "Poskytnuté přihlašovací údaje jsou určené pro rozhraní API pro rozpoznávání řeči. Tato žádost vyžaduje přihlašovací údaje pro rozhraní text API. K Translator Text API použijte předplatné. "|
| 403000| Tato operace není povolena.|
| 403001| Operace není povolená, protože předplatné překročilo svoji kvótu Free.|
| 405000| Metoda Request není pro požadovaný prostředek podporována.|
| 408001| Připravuje se požadovaný systém překladu. Zkuste to prosím znovu za několik minut.|
| 408002| Při čekání na příchozí datový proud vypršel časový limit žádosti. Klient nevytvořil žádost v době, kdy byl server připraven čekat. Klient může požadavek zopakovat bez pozdějšího provedení změn.|
| 415000| Hlavička Content-Type chybí nebo je neplatná.|
| 429000, 429001, 429002| Server odmítl požadavek, protože klient překročil omezení požadavků.|
| 500000| Došlo k neočekávané chybě. Pokud chyba přetrvává, ohlaste ji pomocí data a času chyby, identifikátor požadavku z hlavičky odpovědi X-RequestId a identifikátor klienta z hlavičky požadavku X-ClientTraceId.|
| 503000| Služba je dočasně nedostupná. Zkuste to prosím znovu. Pokud chyba přetrvává, ohlaste ji pomocí data a času chyby, identifikátor požadavku z hlavičky odpovědi X-RequestId a identifikátor klienta z hlavičky požadavku X-ClientTraceId.|

