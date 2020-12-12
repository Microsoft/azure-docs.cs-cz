---
title: Pokyny pro zotavení po havárii pro nástroj pro rozpoznávání formulářů Azure
titleSuffix: Azure Cognitive Services
description: Naučte se používat rozhraní API pro kopírování modelů k zálohování prostředků nástroje pro rozpoznávání formulářů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 168dcf994d00ba1cb2070665ada5a55cf86cfa4a
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359808"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>Zálohování a obnovení modelů pro rozpoznávání formulářů

Když vytvoříte prostředek pro rozpoznávání formulářů v Azure Portal, zadáte oblast. Od tohoto dne se váš prostředek a všechny jeho operace budou přidružit k této konkrétní oblasti serveru Azure. Je zřídka, ale není nemožné, k navýšení problému v síti, který je v celé oblasti. Pokud vaše řešení potřebuje vždycky k dispozici, měli byste ho navrhnout tak, aby převzala služby při selhání do jiné oblasti, nebo rozdělit zatížení mezi dvě nebo víc oblastí. Oba přístupy vyžadují aspoň dva prostředky pro rozpoznávání formulářů v různých oblastech a možnost Synchronizovat [vlastní modely](./quickstarts/curl-train-extract.md) napříč oblastmi.

Rozhraní API pro kopírování umožňuje tomuto scénáři kopírovat vlastní modely z jednoho nebo jiných účtů pro rozpoznávání formulářů, které mohou existovat v libovolné podporované geografické oblasti. V této příručce se dozvíte, jak použít REST API kopírování s kudrlinkou. K vystavování požadavků můžete použít také službu požadavku HTTP, jako je například post.

## <a name="business-scenarios"></a>Obchodní scénáře

Pokud vaše aplikace nebo firma závisí na použití vlastního modelu rozpoznávání formulářů, doporučujeme model zkopírovat do jiného účtu pro rozpoznávání formulářů v jiné oblasti. Pokud dojde k oblastnímu výpadku, můžete získat přístup k modelu v oblasti, kam byl zkopírován.

##  <a name="prerequisites"></a>Předpoklady

1. Dva prostředky pro rozpoznávání formulářů v různých oblastech Azure. Pokud je nemáte, přečtěte si Azure Portal a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" vytvořte nový prostředek pro rozpoznávání formulářů " target="_blank"> vytvořit nový prostředek pro rozpoznávání formulářů <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
1. Klíč předplatného, adresa URL koncového bodu a ID předplatného prostředku pro rozpoznávání formulářů Tyto hodnoty najdete na kartě **Přehled** prostředku na Azure Portal.


## <a name="copy-api-overview"></a>Přehled rozhraní API pro kopírování

Proces kopírování vlastního modelu se skládá z následujících kroků:

1. Nejprve vydáte požadavek na autorizaci kopírování na cílový prostředek &mdash; , který je, prostředkem, který obdrží zkopírovaný model. Vrátíte adresu URL nově vytvořeného cílového modelu, který získá zkopírovaná data.
1. Dál odešlete žádost o kopírování zdrojovému prostředku &mdash; prostředku, který obsahuje model, který se má zkopírovat. Vrátíte se zpátky adresu URL, na kterou se můžete dotazovat a sledovat průběh operace.
1. Pomocí svých přihlašovacích údajů ke zdrojovému zdroji se můžete dotazovat na adresu URL průběhu, dokud nebude operace úspěšná. Můžete také zadat dotaz na nové ID modelu v cílovém prostředku a získat tak stav nového modelu.

> [!CAUTION]
> Rozhraní API pro kopírování v současné době nepodporuje ID modelů pro [složené vlastní modely](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/Compose). Vytváření modelů je funkce ve verzi Preview v 2.1 verze Preview. 2 Preview. 

## <a name="generate-copy-authorization-request"></a>Vytvořit kopii žádosti o autorizaci

Následující požadavek HTTP získá autorizaci kopírování z cílového prostředku. Je nutné zadat koncový bod a klíč cílového prostředku jako záhlaví.

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Dostanete `201\Created` odpověď s `modelId` hodnotou v těle. Tento řetězec představuje ID nově vytvořeného (prázdného) modelu. K `accessToken` tomu je potřeba, aby rozhraní API kopírovalo data na tento prostředek, a `expirationDateTimeTicks` hodnota je vypršení platnosti tokenu. Všechny tři tyto hodnoty uložte do zabezpečeného umístění.

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>Operace zahájení kopírování

Následující požadavek HTTP spustí operaci kopírování na zdrojovém prostředku. Jako hlavičku budete muset zadat koncový bod a klíč zdrojového prostředku. Všimněte si, že adresa URL požadavku obsahuje ID modelu zdrojového modelu, který chcete zkopírovat.

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/<your model ID>/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Tělo vaší žádosti musí mít následující formát. Budete muset zadat ID prostředku a název oblasti cílového prostředku. ID prostředku můžete najít na kartě **vlastnosti** prostředku v Azure Portal a název oblasti můžete najít na kartě **klíče a koncový bod** . Budete také potřebovat ID modelu, přístupový token a hodnotu vypršení platnosti, kterou jste zkopírovali z předchozího kroku.

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
}
```

> [!NOTE]
> Rozhraní API pro kopírování transparentně podporuje funkci [AEK/CMK](https://msazure.visualstudio.com/Cognitive%20Services/_wiki/wikis/Cognitive%20Services.wiki/52146/Customer-Managed-Keys) . To nevyžaduje žádné zvláštní zacházení, ale Všimněte si, že pokud kopírujete mezi nešifrovaným prostředkem do šifrovaného prostředku, je nutné zahrnout hlavičku požadavku `x-ms-forms-copy-degrade: true` . Pokud tato hlavička není zahrnutá, operace kopírování selže a vrátí `DataProtectionTransformServiceError` .

Dostanete `202\Accepted` odpověď s hlavičkou Operation-Location. Tato hodnota je adresa URL, kterou použijete ke sledování průběhu operace. Zkopírujte ho do dočasného umístění pro další krok.

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

### <a name="common-errors"></a>Běžné chyby

|Chybová|Řešení|
|:--|:--|
| 400/Chybný požadavek s `"code:" "1002"` | Indikuje chybu ověřování nebo chybně vytvořený požadavek na kopírování. Mezi běžné problémy patří: a) neplatná nebo upravená `copyAuthorization` datová část. b) hodnota pro token vypršela `expirationDateTimeTicks` ( `copyAuhtorization` datová část je platná po dobu 24 hodin). c) je neplatná nebo nepodporovaná `targetResourceRegion` . d) neplatný nebo nesprávný `targetResourceId` řetězec.
|

## <a name="track-copy-progress"></a>Sledovat průběh kopírování

Sledujte svůj průběh dotazování rozhraní API pro **výsledek získání modelu kopírování** na koncový bod zdrojového prostředku.

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Vaše odpověď se bude lišit v závislosti na stavu operace. Vyhledejte `"status"` pole v těle JSON. Pokud toto volání rozhraní API automatizujete ve skriptu, doporučujeme zadat dotaz na operaci jednou za sekundu.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="common-errors"></a>Běžné chyby

|Chybová|Řešení|
|:--|:--|
|"chyby": [{"Code": "AuthorizationError";<br>zpráva: "Chyba autorizace z důvodu <br>chybějící nebo neplatné deklarace identity autorizace. "}]   | Nastane, pokud se `copyAuthorization` datová část nebo obsah upraví z toho, co vrátilo `copyAuthorization` rozhraní API. Ujistěte se, že datová část je stejný přesný obsah, který byl vrácen z předchozího `copyAuthorization` volání.|
|"chyby": [{"Code": "AuthorizationError";<br>zpráva: nepovedlo se načíst autorizaci. <br>mezipaměť. Pokud s tím budou dál problémy používat jiné <br>cílový model, do kterého se mají kopírovat. "}] | Indikuje, že se `copyAuthorization` datová část znovu používá s požadavkem Copy. Požadavek Copy, který je úspěšný, neumožní žádné další požadavky, které používají stejnou `copyAuthorization` datovou část. Pokud vyvoláte samostatnou chybu (například ty, které jsou uvedené níže), a pak znovu spustíte kopii se stejnou datovou částí autorizace, vyvolá se tato chyba. Řešením je vygenerovat novou `copyAuthorization` datovou část a pak znovu vystavit požadavek na kopírování.|
|"chyby": [{"Code": "DataProtectionTransformServiceError";<br>zpráva: požadavek na přenos dat není povolený. <br>při přechodu na méně zabezpečené schéma ochrany dat. Podívejte se na dokumentaci nebo se obraťte na správce služby. <br>Podrobnosti. "}]    | Nastane, pokud se kopíruje mezi `AEK` povoleným prostředkem do prostředku, který není `AEK` povolený. Pokud chcete v cíli zkopírovat zašifrovaný model jako nešifrované, zadejte `x-ms-forms-copy-degrade: true` hlavičku s požadavkem Copy.|
|"chyby": [{"Code": "ResourceResolverError";<br>"zpráva": "nelze načíst informace pro prostředek rozpoznávání s ID"... ". Zajistěte, aby byl prostředek platný a existuje v zadané oblasti ' westus2 '.. "}] | Indikuje, že prostředek Azure, který ukazuje, není `targetResourceId` platným prostředkem rozpoznávání nebo neexistuje. Pokud chcete tento problém vyřešit, ověřte a znovu vydejte požadavek Copy.|


### <a name="optional-track-the-target-model-id"></a>Volitelné Sledovat ID cílového modelu 

Můžete také použít rozhraní API pro **získání vlastního modelu** ke sledování stavu operace dotazem cílového modelu. Zavolejte toto rozhraní API pomocí ID cílového modelu, které jste zkopírovali v prvním kroku.

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

V těle odpovědi se zobrazí informace o modelu. V `"status"` poli Stav modelu ověřte.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>Ukázka kódu ve složených závorkách

Následující fragmenty kódu pomocí oblé nastaví volání rozhraní API, která jsou uvedená v předchozích krocích. Pořád budete muset vyplnit informace o ID modelu a předplatném, které jsou specifické pro vaše vlastní prostředky.

### <a name="generate-copy-authorization-request"></a>Vytvořit kopii žádosti o autorizaci

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>Operace zahájení kopírování

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>Sledovat průběh kopírování

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>Další kroky

V této příručce jste zjistili, jak používat rozhraní API pro kopírování k zálohování vlastních modelů do sekundárního prostředku pro rozpoznávání formulářů. Dále si Prozkoumejte referenční materiály k rozhraní API a podívejte se, co můžete dělat s nástrojem pro rozpoznávání formulářů.
* [Referenční dokumentace REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
