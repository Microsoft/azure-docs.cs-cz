---
title: Překladač Text API V3.0 Reference
titleSuffix: Azure Cognitive Services
description: Referenční dokumentace pro překladač text API V3.0. Verze 3 překladač text api poskytuje moderní Webové rozhraní API založené na JSON.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 3/13/2020
ms.author: swmachan
ms.openlocfilehash: 4180dc6127fb2d31465400b1b25fb7e2d68f4754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369161"
---
# <a name="translator-text-api-v30"></a>Překladač Text API v3.0

## <a name="whats-new"></a>Co je nového?

Verze 3 překladač text api poskytuje moderní Webové rozhraní API založené na JSON. Zlepšuje použitelnost a výkon konsolidací existujících funkcí do menšího počtu operací a poskytuje nové funkce.

 * Přepis převést text v jednom jazyce z jednoho skriptu do jiného skriptu.
 * Překlad do více jazyků v jedné žádosti.
 * Detekce jazyka, překlad a přepis v jedné žádosti.
 * Slovník pro vyhledávání alternativních překladů termínu, hledání zpětných překladů a příkladů zobrazujících termíny používané v kontextu.
 * Více informativní výsledky detekce jazyka.

## <a name="base-urls"></a>Základní adresy URL

Microsoft Translator je obsluhován z více umístění datových center. V současné době se nacházejí v 10 [geografických oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Severní a Jižní Amerika:** Východní USA, Střední jižní USA, Západní střed USA a Západní USA 2 
* **Asie a Tichomoří:** Korea – jih, Japonsko – východ, jihovýchodní Asie a Austrálie – východ
* **Evropa:** Severní Evropa a západní Evropa

Požadavky na rozhraní Microsoft Translator Text API jsou ve většině případů zpracovány datovým centrem, které je nejblíže k místu, odkud pochází. V případě selhání datového centra může být požadavek směrován mimo azure geografie.

Chcete-li vynutit zpracování požadavku konkrétní geografickou oblastí Azure, změňte globální koncový bod v požadavku rozhraní API na požadovaný místní koncový bod:

|Popis|Azure geografie|Základní adresa URL|
|:--|:--|:--|
|Azure|Globální (neregionální)|   api.cognitive.microsofttranslator.com|
|Azure|Spojené státy|   api-nam.cognitive.microsofttranslator.com|
|Azure|Evropa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asie a Tichomoří|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Ověřování

Přihlaste se k odběru rozhraní Translator Text API nebo [multi-service cognitive services](https://azure.microsoft.com/pricing/details/cognitive-services/) ve službách Azure Cognitive Services a použijte klíč předplatného (dostupný na webu Azure Portal) k ověření. 

Existují tři záhlaví, které můžete použít k ověření předplatného. Tato tabulka popisuje, jak se jednotlivé položky používají:

|Hlavičky|Popis|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Pokud předáváte tajný klíč, použijte předplatné služby Cognitive Services*.<br/>Hodnota je tajný klíč Azure pro vaše předplatné překladače text api.|
|Autorizace|*Pokud předáváte ověřovací token, použijte předplatné služeb Cognitive Services.*<br/>Hodnota je token nosiče: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Používejte s víceslužbami cognitive services a regionální překladatel zdroje.*<br/>Hodnota je oblast zdroje víceslužeb nebo regionálního překladatele. Tato hodnota je volitelná při použití prostředku globálního překladače.|

###  <a name="secret-key"></a>Tajný klíč
První možností je ověření `Ocp-Apim-Subscription-Key` pomocí hlavičky. Přidejte `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` záhlaví do vašeho požadavku.

#### <a name="authenticating-with-a-global-resource"></a>Ověřování pomocí globálního zdroje

Při použití [prostředku globálního překladače](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)je třeba zahrnout jednu hlavičku pro volání rozhraní API překladače.

|Hlavičky|Popis|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Hodnota je tajný klíč Azure pro vaše předplatné překladače text api.|

Zde je příklad požadavku na volání rozhraní API překladače pomocí prostředku globálního překladače

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsoft.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Ověřování pomocí regionálního zdroje

Při použití [zdroje regionálního překladatele](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).
Existují 2 záhlaví, které je třeba volat překladač rozhraní API.

|Hlavičky|Popis|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Hodnota je tajný klíč Azure pro vaše předplatné překladače text api.|
|Ocp-Apim-Subscription-Region| Hodnota je oblast prostředku překladače. |

Zde je příklad požadavku na volání rozhraní API překladače pomocí zdroje regionálního překladače

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsoft.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Ověřování pomocí prostředku s více službami

Při použití prostředku služby Cognitive Service více služeb. To umožňuje použít jeden tajný klíč k ověření požadavků na více služeb. 

Při použití klíče s více službami je nutné do požadavku zahrnout dvě hlavičky ověřování. Existují 2 záhlaví, které je třeba volat překladač rozhraní API.

|Hlavičky|Popis|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Hodnota je tajný klíč Azure pro prostředek více služeb.|
|Ocp-Apim-Subscription-Region| Hodnota je oblast prostředku s více službami. |

Oblast je vyžadována pro předplatné víceservisních textových rozhraní API. Vybraná oblast je jediná oblast, kterou můžete použít pro překlad textu při použití klíče předplatného s více službami, a musí se jednat o stejnou oblast, kterou jste vybrali při registraci předplatného s více službami prostřednictvím portálu Azure.

Dostupné oblasti `australiaeast` `brazilsouth`jsou `canadacentral` `centralindia`, `centralus` `centraluseuap`, `eastasia` `eastus`, `eastus2` `francecentral`, `japaneast` `japanwest`, `koreacentral` `northcentralus`, `northeurope` `southcentralus`, `southeastasia` `uksouth`, `westcentralus` `westeurope`, `westus`, , , , , , , , , , `westus2`, , a `southafricanorth`.

Pokud předáte tajný klíč v řetězci `Subscription-Key`dotazu s parametrem , `Subscription-Region`musíte zadat oblast s parametrem dotazu .

### <a name="authenticating-with-an-access-token"></a>Ověřování pomocí přístupového tokenu
Případně můžete vyměnit tajný klíč za přístupový token. Tento token je součástí každého `Authorization` požadavku jako záhlaví. Chcete-li získat autorizační `POST` token, požádejte o adresu URL na následující adresu URL:

| Typ prostředku     | Adresa URL ověřovací služby                                |
|-----------------|-----------------------------------------------------------|
| Globální          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regionální nebo vícenásobné služby | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Zde jsou příklady požadavků na získání tokenu, který je přidělen tajný klíč:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Úspěšný požadavek vrátí kódovaný přístupový token jako prostý text v těle odpovědi. Platný token je předán službě Translator jako nosný token v autorizaci.

```http
Authorization: Bearer <Base64-access_token>
```

Ověřovací token je platný po dobu 10 minut. Token by měl být znovu použit při více volání překladače API. Pokud však váš program provádí požadavky na rozhraní API překladače po delší dobu, musí váš program v pravidelných intervalech požadovat nový přístupový token (například každých 8 minut).

## <a name="virtual-network-support"></a>Podpora virtuální sítě

Služba Překladatel je nyní k dispozici s`WestUS2` `EastUS`možnostmi virtuální sítě v omezených oblastech `SouthCentralUS`( , , `WestUS`, `Central US EUAP`. `global`). Informace o povolení virtuální sítě naleznete [v tématu Konfigurace virtuálních sítí Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal). 

Po zapnutí této funkce, musíte použít vlastní koncový bod pro volání rozhraní API překladače. Nelze použít koncový bod globálního překladače ("api.cognitive.microsofttranslator.com") a nelze ověřit pomocí přístupového tokenu.

Vlastní koncový bod můžete najít po vytvoření [prostředku překladače](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).

|Hlavičky|Popis|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Hodnota je tajný klíč Azure pro vaše předplatné překladače text api.|
|Ocp-Apim-Subscription-Region| Hodnota je oblast prostředku překladače. Tato hodnota je nepovinná, pokud je prostředek`global`|

Tady je ukázková žádost o volání rozhraní API překladače pomocí vlastního koncového bodu

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>chyby

Standardní chybová odpověď je objekt JSON s `error`názvem dvojice název/hodnota . Hodnota je také objekt JSON s vlastnostmi:

  * `code`: Kód chyby definovaný serverem.
  * `message`: Řetězec poskytující lidsky čitelnou reprezentaci chyby.

Například zákazník s bezplatným zkušebním předplatným obdrží po vyčerpání volné kvóty následující chybu:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Kód chyby je šestimístné číslo kombinující 3místný stavový kód HTTP následovaný třímístným číslem pro další kategorizaci chyby. Běžné kódy chyb jsou:

| kód | Popis |
|:----|:-----|
| 400000| Jeden ze vstupů požadavku není platný.|
| 400001| Parametr "obor" je neplatný.|
| 400002| Parametr "kategorie" je neplatný.|
| 400003| Specifikátor jazyka chybí nebo je neplatný.|
| 400004| Specifikátor cílového skriptu ("Chcete-li skript") chybí nebo je neplatný.|
| 400005| Vstupní text chybí nebo je neplatný.|
| 400006| Kombinace jazyka a skriptu není platná.|
| 400018| Specifikátor zdrojového skriptu ("Ze skriptu") chybí nebo je neplatný.|
| 400019| Jeden ze zadaných jazyků není podporován.|
| 400020| Jeden z prvků v poli vstupního textu není platný.|
| 400021| Parametr verze rozhraní API chybí nebo je neplatný.|
| 400023| Jeden ze zadaných jazykových párů není platný.|
| 400035| Zdrojový jazyk (pole Od) není platný.|
| 400036| Cílový jazyk (pole Do) chybí nebo je neplatný.|
| 400042| Jedna ze zadaných možností (pole Možnosti) není platná.|
| 400043| ID trasování klienta (pole ClientTraceId nebo hlavička X-ClientTranceId) chybí nebo je neplatné.|
| 400050| Vstupní text je příliš dlouhý. Zobrazit [omezení požadavků](../request-limits.md).|
| 400064| Parametr "překlad" chybí nebo je neplatný.|
| 400070| Počet cílových skriptů (parametr ToScript) neodpovídá počtu cílových jazyků (To parametr).|
| 400071| Hodnota není platná pro TextType.|
| 400072| Pole vstupního textu má příliš mnoho prvků.|
| 400073| Parametr skriptu není platný.|
| 400074| Tělo požadavku není platné JSON.|
| 400075| Kombinace páru jazyků a kategorií není platná.|
| 400077| Byla překročena maximální velikost požadavku. Zobrazit [omezení požadavků](../request-limits.md).|
| 400079| Vlastní systém požadovaný pro překlad mezi z a do jazyka neexistuje.|
| 400080| Přepis není podporován pro jazyk nebo skript.|
| 401000| Požadavek není autorizován, protože pověření chybí nebo jsou neplatná.|
| 401015| "Zakalená pověření jsou pro rozhraní API řeči. Tento požadavek vyžaduje pověření pro rozhraní API textu. Použijte předplatné překladače text api."|
| 403000| Operace není povolena.|
| 403001| Operace není povolena, protože předplatné překročilo svou volnou kvótu.|
| 405000| Metoda požadavku není pro požadovaný prostředek podporována.|
| 408001| Připravuje se požadovaný překladový systém. Opakujte akci za několik minut.|
| 408002| Požadavek časový mat na příchozí datový proud. Klient nepředložil požadavek v době, kdy byl server připraven čekat. Klient může požadavek kdykoli v pozdější době zopakovat bez úprav.|
| 415000| Záhlaví Typu obsahu chybí nebo je neplatné.|
| 429000, 429001, 429002| Server požadavek zamítl, protože klient překročil limity požadavků.|
| 500000| Došlo k neočekávané chybě. Pokud chyba přetrvává, oznamte ji s datem a časem chyby, identifikátorem požadavku z hlavičky odpovědi X-RequestId a identifikátorem klienta z hlavičky požadavku X-ClientTraceId.|
| 503000| Služba je dočasně nedostupná. Zkuste to prosím znovu. Pokud chyba přetrvává, oznamte ji s datem a časem chyby, identifikátorem požadavku z hlavičky odpovědi X-RequestId a identifikátorem klienta z hlavičky požadavku X-ClientTraceId.|

## <a name="metrics"></a>Metriky 
Metriky umožňují zobrazit informace o využití a dostupnosti překladače na webu Azure Portal v části metriky, jak je znázorněno na následujícím snímku obrazovky. Další informace naleznete v [tématu Data a metriky platformy](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

![Metriky překladače](../media/translatormetrics.png)

V této tabulce jsou uvedeny dostupné metriky s popisem způsobu, jakým se používají ke sledování volání překladového rozhraní API.

| Metriky | Popis |
|:----|:-----|
| Celkový počet volání| Celkový počet volání rozhraní API.|
| Volání TotalToken| Celkový počet volání rozhraní API prostřednictvím služby tokenu pomocí ověřovacího tokenu.|
| Úspěšné volání| Počet úspěšných volání.|
| Celkemchyby| Počet volání s chybovou odpovědí.|
| Blokované hovory| Počet volání, která překročila rychlost nebo limit kvóty.|
| Serverchyby| Počet volání s interní chybou serveru(5XX).|
| Chyby klienta| Počet volání s chybou na straně klienta(4XX).|
| Latence| Doba trvání dokončení požadavku v milisekundách.|
| ZnakyPřeloženo| Celkový počet znaků v žádosti o příchozí text.|
