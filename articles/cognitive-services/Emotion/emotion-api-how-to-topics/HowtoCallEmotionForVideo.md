---
title: 'Příklad: Volání rozhraní API pro rozpoznávání Emocí pro videa'
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak volat rozhraní API pro rozpoznávání emocí ve videu v rámci služeb Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: sample
ms.date: 02/06/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: b7e49b5376782e0dfa0676c0e9637e64b8e44c9a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237717"
---
# <a name="example-call-emotion-api-for-video"></a>Příklad: Volání rozhraní API pro rozpoznávání Emocí pro videa

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání emocí se přestane používat 15. února 2019. Funkce rozpoznávání emocí je teď obecně dostupná v rámci [rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/). 

Tato příručka ukazuje, jak volat rozhraní API pro rozpoznávání emocí ve videu. Ukázky jsou napsané v jazyce C# pomocí klientské knihovny rozhraní API pro rozpoznávání emocí ve videu.

### <a name="Prep">Příprava</a>
Pokud chcete použít rozhraní API pro rozpoznávání emocí ve videu, budete potřebovat video s lidmi, ideálně video s lidmi, kteří stojí tváří ke kameře.

### <a name="Step1">Krok 1: Povolit volání rozhraní API</a>
Ke každému volání rozhraní API pro rozpoznávání emocí ve videu potřebujete klíč předplatného. Tento klíč je potřeba předat buď jako parametr řetězce dotazu, nebo ho zadat v hlavičce požadavku. Pokud chcete klíč předplatného předat pomocí řetězce dotazu, použijte jako příklad následující adresu URL požadavku na rozhraní API pro rozpoznávání emocí ve videu:

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

Klíč předplatného můžete alternativně zadat také v hlavičce požadavku HTTP:

```
ocp-apim-subscription-key: <Your subscription key>
```

Pokud používáte klientskou knihovnu, klíč předplatného se předává prostřednictvím konstruktoru třídy VideoServiceClient. Příklad:

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
Pokud potřebujete získat klíč předplatného, přejděte na [předplatná](https://azure.microsoft.com/try/cognitive-services/).

### <a name="Step2">Krok 2: Nahrát video ke službě a zkontrolovat stav</a>
Základním způsobem, jak volat rozhraní API pro rozpoznávání emocí ve videu, je přímo nahrát video. Provede se to odesláním požadavku POST s typem obsahu application/octet-stream společně s daty přečtenými z videosouboru. Maximální velikost videa je 100 MB.

Při použití klientské knihovny se předáním objektu datového proudu provádí stabilizace prostřednictvím nahrávání. Viz následující příklad:

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

Mějte na paměti, že metoda CreateOperationAsync objektu VideoServiceClient je asynchronní. Kvůli použití klauzule await musí být jako asynchronní označená také volající metoda.
Pokud je video již na webu a má veřejnou adresu URL, můžete k rozhraní API pro rozpoznávání emocí ve videu přistupovat zadáním této adresy URL. V tomto příkladu bude textem žádosti řetězec JSON obsahující adresu URL.

Při použití klientské knihovny je možné snadno provést stabilizaci prostřednictvím adresy URL s využitím dalšího přetížení metody CreateOperationAsync.


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

Tato metoda nahrávání bude stejná pro všechna volání rozhraní API pro rozpoznávání emocí ve videu.

Po nahrání videa bude další operací, kterou budete chtít provést, kontrola stavu. Vzhledem k tomu, že videosoubory jsou obvykle větší a různorodější než jiné soubory, můžou uživatelé v tomto kroku očekávat dlouhou dobu zpracování. Tato doba závisí na velikosti a délce souboru.

Při použití klientské knihovny můžete načíst stav a výsledek operace pomocí metody GetOperationResultAsync.


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
Obvykle by se na straně klienta měl pravidelně načítat stav operace, dokud nebude Succeeded (Úspěch) nebo Failed (Neúspěch).

```
OperationResult operationResult;
while (true)
{
      operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);
      if (operationResult.Status == OperationStatus.Succeeded || operationResult.Status == OperationStatus.Failed)
      {
           break;
      }

      Task.Delay(30000).Wait();
}

```

Jakmile se stav operace VideoOperationResult změní na Succeeded (Úspěch), můžete výsledek načíst přetypováním VideoOperationResult na VideoOperationInfoResult<VideoAggregateRecognitionResult> a přistupovat k němu v poli ProcessingResult.

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">Krok 3: Získávání a pochopení rozpoznávání emocí a sledování výstup ve formátu JSON</a>

Výstup výsledku obsahuje metadata o tvářích v daném souboru ve formátu JSON.

Jak je popsáno v kroku 2, jakmile se stav změní na Succeeded (Úspěch), výstup JSON bude k dispozici v poli ProcessingResult objektu OperationResult.

Kód JSON rozpoznávání a sledování emocí obsahuje následující atributy:

Atribut | Popis
-------------|-------------
Verze | Odkazuje na verzi JSON rozhraní API pro rozpoznávání emocí ve videu.
Timescale | Počet impulzů ve videu za sekundu.
Posun  |Posun času pro časová razítka. Ve verzi 1.0 rozhraní API pro rozpoznávání emocí ve videu bude tato hodnota vždy 0. V budoucích podporovaných scénářích se tato hodnota může změnit.
Framerate | Počet snímků ve videu za sekundu.
Fragments   | Metadata se rozdělují na různé menší části, které se označují jako fragmenty. Každý fragment obsahuje začátek, dobu trvání, číslo intervalu a události.
Start   | Čas zahájení první události vyjádřený v impulzech.
Doba trvání |  Délka fragmentu vyjádřená v impulzech.
Interval |  Délka jednotlivých událostí v rámci fragmentu vyjádřená v impulzech.
Události  | Pole hodnot. Vnější pole představuje jeden časový interval. Vnitřní pole se skládá z 0 nebo více událostí, které se v tomto bodu v čase odehrály.
windowFaceDistribution |    Procento tváří, v jejichž výrazu byla během události konkrétní emoce.
windowMeanScores |  Průměrné skóre jednotlivých emocí ve výrazech tváří na snímku.

Důvodem formátování JSON tímto způsobem je připravit rozhraní API na budoucí scénáře, kde bude důležité načítat rychle metadata a spravovat velké datové proudy výsledků. Toto formátování využívá techniky fragmentace (umožňující rozdělit metadata na části podle času a stahovat pouze to, co potřebujete) i segmentace (umožňující rozdělit události v případě, že najednou budou příliš velké). S transformací dat vám pomůže několik jednoduchých výpočtů. Pokud se například událost zahájila v čase 6 300 (impulzů) a má časovou osu 2 997 (impulzů za sekundu) a snímkovou frekvenci 29,97 (snímků za sekundu), pak:

*   Zahájení / časová osa = 2,1 sekund
*   Počet sekund × (snímková frekvence / časová osa) = 63 snímků

Níže je jednoduchý příklad extrakce JSON do formátu jednotlivých snímků pro účely rozpoznávání a sledování emocí:

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
Emoce se v průběhu času vyrovnávají, proto pokud někdy budete vytvářet vizualizaci, která vašimi výsledky překryje původní video, odečtěte od uvedených časových značek 250 milisekund.

### <a name="Summary">Souhrn</a>
V této příručce jste se seznámili s funkcemi rozhraní API pro rozpoznávání emocí ve videu: postup nahrání videa, kontrola jeho stavu a načtení metadat rozpoznávání emocí.

Další podrobnosti o rozhraní API najdete v referenční příručce k rozhraní API: [Referenční informace k rozhraní API pro rozpoznávání emocí ve videu](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e).
