---
title: Reference k překladateli V 3.0
titleSuffix: Azure Cognitive Services
description: Referenční dokumentace pro překladatele V 3.0 Verze 3 překladatele nabízí moderní webové rozhraní API založené na formátu JSON.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 8/11/2020
ms.author: lajanuar
ms.openlocfilehash: 567e28ee7f698565d6ad0020db7abdca0557f053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100650758"
---
# <a name="translator-v30"></a>Překladatel v 3.0

## <a name="whats-new"></a>Co je nového

Verze 3 překladatele nabízí moderní webové rozhraní API založené na formátu JSON. Zlepšuje použitelnost a výkon tím, že konsoliduje stávající funkce do menšího počtu operací a poskytuje nové funkce.

 * Převádění pro převod textu v jednom jazyce z jednoho skriptu na jiný skript.
 * Překlad do několika jazyků v jednom požadavku.
 * Rozpoznání, překlad a převod jazyka v jednom požadavku.
 * Slovník pro vyhledání alternativních překladů termínu k nalezení zpětných překladů a příkladů, které označují podmínky používané v kontextu.
 * Přesnější výsledky detekce jazyka.

## <a name="base-urls"></a>Základní adresy URL

Microsoft Translator se obsluhuje z více umístění datových center. V současné době se nacházejí ve 10 [zeměpisných oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Severní Amerika:** Východní USA, Střed USA – jih, Středozápadní USA a Západní USA 2 
* **Asie a Tichomoří:** Korea – jih, Japonsko – východ, jihovýchodní Asie a Austrálie – východ
* **Evropa:** Severní Evropa, Západní Evropa, Švýcarsko – sever <sup>1, 2</sup>a Švýcarsko – západ <sup>1, 2</sup>

Žádosti překladače Microsoftu jsou ve většině případů zpracovávány v datacentru, které je nejblíže místu, kde požadavek vznikl. V případě selhání datacentra se může žádost směrovat mimo zeměpisnou oblast Azure.

Pokud chcete vynutit zpracování žádosti konkrétními geografickými oblastmi Azure, změňte globální koncový bod v požadavku rozhraní API na požadovaný geografický koncový bod:

|Description|Geografické Azure|Základní adresa URL (zeměpisný koncový bod)|
|:--|:--|:--|
|Azure|Globální (mimo oblast)|   api.cognitive.microsofttranslator.com|
|Azure|USA|   api-nam.cognitive.microsofttranslator.com|
|Azure|Evropa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asie a Tichomoří|    api-apc.cognitive.microsofttranslator.com|

<sup>1</sup> zákazník s prostředkem umístěným v Švýcarsko – sever nebo Švýcarsko – západ může zajistit, aby jejich požadavky na textové rozhraní API byly obsluhovány v rámci Švýcarska. Pokud chcete zajistit, aby se požadavky zpracovaly ve Švýcarsku, vytvořte prostředek překladatele v oblasti prostředků Švýcarsko – sever nebo Švýcarsko – západ a pak ve svých požadavcích rozhraní API použijte vlastní koncový bod prostředku. Příklad: Pokud vytvoříte prostředek překladatele v Azure Portal s názvem "oblast prostředků" jako "Švýcarsko – sever" a název prostředku je "My-ch-n", váš vlastní koncový bod je " https://my-ch-n.cognitiveservices.azure.com ". A vzorový požadavek k překladu:
```curl
// Pass secret key and region using headers to a custom endpoint
curl -X POST " my-ch-n.cognitiveservices.azure.com/translator/text/v3.0/translate?to=fr" \
-H "Ocp-Apim-Subscription-Key: xxx" \
-H "Ocp-Apim-Subscription-Region: switzerlandnorth" \
-H "Content-Type: application/json" \
-d "[{'Text':'Hello'}]" -v
```
<sup>2</sup> . Vlastní Překladatel není aktuálně k dispozici ve Švýcarsku.

## <a name="authentication"></a>Authentication

Přihlaste se k odběru překladatele nebo [Cognitive Services více službami](https://azure.microsoft.com/pricing/details/cognitive-services/) ve službě Azure Cognitive Services a použijte svůj klíč předplatného (dostupný v Azure Portal) k ověření. 

K ověření předplatného můžete použít tři hlavičky. Tato tabulka popisuje, jak se používají jednotlivé tyto funkce:

|Hlavičky|Description|
|:----|:----|
|Ocp-Apim-Subscription-Key|Pokud předáváte *tajný klíč, použijte u předplatného Cognitive Services*.<br/>Hodnota je tajný klíč Azure pro váš odběr překladatele.|
|Autorizace|*Pokud předáváte ověřovací token, použijte u předplatného Cognitive Services.*<br/>Hodnota je nosným tokenem: `Bearer <token>` .|
|OCP – APIM – předplatné – oblast|*Použijte s Cognitive Services prostředkem více služeb a regionálního překladatele.*<br/>Hodnota je oblast prostředku služby Multi-Service nebo oblastního překladatele. Tato hodnota je při použití globálního prostředku překladatele volitelná.|

###  <a name="secret-key"></a>Tajný klíč
První možností je ověřit pomocí `Ocp-Apim-Subscription-Key` hlavičky. Přidejte `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` hlavičku do žádosti.

#### <a name="authenticating-with-a-global-resource"></a>Ověřování pomocí globálního prostředku

Když použijete [globální prostředek překladatele](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation), je nutné zahrnout jedno záhlaví pro volání překladatele.

|Hlavičky|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Hodnota je tajný klíč Azure pro váš odběr překladatele.|

Tady je příklad žádosti o volání překladatele pomocí globálního prostředku překladatele.

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Ověřování pomocí regionálního prostředku

Když použijete [prostředek místní překladatele](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).
Existují dvě hlavičky, které potřebujete k volání překladatele.

|Hlavičky|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Hodnota je tajný klíč Azure pro váš odběr překladatele.|
|OCP – APIM – předplatné – oblast| Hodnota je oblast prostředku překladatele. |

Tady je příklad žádosti o volání překladatele pomocí prostředku místního překladatele.

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Ověřování pomocí prostředku s více službami

Když použijete prostředek více služeb pro rozpoznávání služby. Díky tomu můžete k ověřování žádostí o více služeb použít jediný tajný klíč. 

Když použijete tajný klíč s více službami, musíte do své žádosti zahrnout dvě ověřovací hlavičky. Existují dvě hlavičky, které potřebujete k volání překladatele.

|Hlavičky|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Hodnota je tajný klíč Azure pro prostředek s více službami.|
|OCP – APIM – předplatné – oblast| Hodnota je oblast prostředku s více službami. |

Pro předplatné rozhraní API pro více služeb text je vyžadována oblast. Oblast, kterou jste vybrali, je jediná oblast, kterou můžete použít pro překlad textu při použití klíče předplatného s více službami a musí se jednat o stejnou oblast, jakou jste zvolili při registraci k předplatnému více služeb prostřednictvím Azure Portal.

Dostupné oblasti jsou `australiaeast` , `brazilsouth` , `canadacentral` , `centralindia` , `centralus` , `centraluseuap` , `eastasia` , `eastus` , `eastus2` , `francecentral` , `japaneast` , `japanwest` ,,, `koreacentral` `northcentralus` `northeurope` , `southcentralus` , `southeastasia` , `uksouth` , `westcentralus` ,,, `westeurope` `westus` `westus2` a `southafricanorth` .

Pokud předáte tajný klíč v řetězci dotazu s parametrem, je `Subscription-Key` nutné zadat oblast s parametrem dotazu `Subscription-Region` .

### <a name="authenticating-with-an-access-token"></a>Ověřování pomocí přístupového tokenu
Alternativně můžete pro přístupový token vyměňovat svůj tajný klíč. Tento token je součástí každé žádosti jako `Authorization` záhlaví. Chcete-li získat autorizační token, proveďte `POST` požadavek na následující adresu URL:

| Typ prostředku     | Adresa URL ověřovací služby                                |
|-----------------|-----------------------------------------------------------|
| Globální          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regionální nebo vícenásobná služba | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

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

Ověřovací token je platný po dobu 10 minut. Při provádění více volání překladatele by měl být token znovu použit. Pokud však váš program provede v delší době požadavky na překladatele, musí váš program požádat o nový přístupový token v pravidelných intervalech (například každých 8 minut).

## <a name="virtual-network-support"></a>Podpora virtuální sítě

Služba Translator je teď dostupná s funkcemi Virtual Network (VNET) ve všech oblastech veřejného cloudu Azure. Pokud chcete povolit Virtual Network, přečtěte si téma [Konfigurace virtuálních sítí Azure Cognitive Services](../../cognitive-services-virtual-networks.md?tabs=portal). 

Když tuto funkci zapnete, musíte pro volání překladatele použít vlastní koncový bod. Nemůžete použít globální koncový bod překladatele (api.cognitive.microsofttranslator.com) a nemůžete ho ověřit pomocí přístupového tokenu.

Vlastní koncový bod můžete najít po vytvoření [prostředku překladatele](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) a povolení přístupu z vybraných sítí a soukromých koncových bodů.

|Hlavičky|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Hodnota je tajný klíč Azure pro váš odběr překladatele.|
|OCP – APIM – předplatné – oblast| Hodnota je oblast prostředku překladatele. Tato hodnota je volitelná, pokud je prostředek `global`|

Tady je příklad žádosti o volání překladatele pomocí vlastního koncového bodu.

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Chyby

Standardní chybová odpověď je objekt JSON s dvojicí název/hodnota s názvem `error` . Hodnota je také objekt JSON s vlastnostmi:

  * `code`: Kód chyby definovaný serverem.
  * `message`: Řetězec, který poskytuje reprezentaci chyby v čitelném člověkě.

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
| 401015| "Poskytnuté přihlašovací údaje jsou určené pro rozhraní API pro rozpoznávání řeči. Tato žádost vyžaduje přihlašovací údaje pro rozhraní text API. Použít odběr pro překladatele. "|
| 403000| Tato operace není povolena.|
| 403001| Operace není povolená, protože předplatné překročilo svoji kvótu Free.|
| 405000| Metoda Request není pro požadovaný prostředek podporována.|
| 408001| Připravuje se požadovaný systém překladu. Zkuste to prosím znovu za několik minut.|
| 408002| Při čekání na příchozí datový proud vypršel časový limit žádosti. Klient nevytvořil žádost v době, kdy byl server připraven čekat. Klient může požadavek zopakovat bez pozdějšího provedení změn.|
| 415000| Hlavička Content-Type chybí nebo je neplatná.|
| 429000, 429001, 429002| Server odmítl požadavek, protože klient překročil omezení požadavků.|
| 500000| Došlo k neočekávané chybě. Pokud chyba přetrvává, ohlaste ji pomocí data a času chyby, identifikátor požadavku z hlavičky odpovědi X-RequestId a identifikátor klienta z hlavičky požadavku X-ClientTraceId.|
| 503000| Služba je dočasně nedostupná. Zkuste to prosím znovu. Pokud chyba přetrvává, ohlaste ji pomocí data a času chyby, identifikátor požadavku z hlavičky odpovědi X-RequestId a identifikátor klienta z hlavičky požadavku X-ClientTraceId.|

## <a name="metrics"></a>Metriky 
Metriky umožňují zobrazit informace o využití a dostupnosti překladatele v Azure Portal v části metriky, jak je znázorněno na následujícím snímku obrazovky. Další informace najdete v tématu [metriky dat a platforem](../../../azure-monitor/essentials/data-platform-metrics.md).

![Metriky translatoru](../media/translatormetrics.png)

Tato tabulka uvádí dostupné metriky s popisem způsobu jejich použití pro monitorování volání rozhraní API pro překlad.

| Metriky | Description |
|:----|:-----|
| TotalCalls| Celkový počet volání rozhraní API.|
| TotalTokenCalls| Celkový počet volání rozhraní API prostřednictvím služby tokenu pomocí ověřovacího tokenu|
| SuccessfulCalls| Počet úspěšných volání.|
| TotalErrors| Počet volání s chybovou odezvou.|
| BlockedCalls| Počet volání, která překročily limit nebo kvótu.|
| ServerErrors| Počet volání s interní chybou serveru (5XX).|
| ClientErrors| Počet volání s chybou na straně klienta (4XX).|
| Latence| Doba k dokončení žádosti v milisekundách|
| CharactersTranslated| Celkový počet znaků v příchozím textovém požadavku.|
