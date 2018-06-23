---
title: Volání rozpoznávání emocí úrovně rozhraní API pro Video | Microsoft Docs
description: Zjistěte, jak volat rozhraní API pro rozpoznávání emocí úrovně pro Video v kognitivní služby.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 0875013b2061a84e3e23ae90c1106382672fdca6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342719"
---
# <a name="how-to-call-emotion-api-for-video"></a>Jak volat rozpoznávání emocí úrovně rozhraní API pro Video

> [!IMPORTANT]
> 30. října 2017 ukončen video Preview rozhraní API. Vyzkoušet nový [Preview rozhraní API Indexer Video](https://azure.microsoft.com/services/cognitive-services/video-indexer/) k snadno rozbalte statistiky z videa a vylepšení možnosti zjišťování obsahu, jako je například výsledky hledání, pomocí zjišťování mluvené slovo, řezy, znaků a emoce. [Další informace](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Tato příručka ukazuje, jak volat rozhraní API pro rozpoznávání emocí úrovně pro Video. Ukázky jsou napsané v C# pomocí rozhraní API pro rozpoznávání emocí úrovně Video klienta knihovny.

### <a name="Prep">Příprava</a> 
Chcete-li použít rozhraní API pro rozpoznávání emocí úrovně pro Video, budete potřebovat video, které zahrnuje osob, ideálně video, kde uživatele, kteří se setkávají fotoaparát.

### <a name="Step1">Krok 1: Autorizovat volání rozhraní API</a> 
Každé volání rozhraní API pro rozpoznávání emocí úrovně pro Video vyžaduje klíč předplatného. Tento klíč musí být buď předána parametr řetězce dotazu nebo zadaným v hlavičce žádosti. Při předat klíč předplatného pomocí řetězce dotazu, použijte následující adresu URL požadavku pro rozhraní API pro rozpoznávání emocí úrovně pro Video jako příklad:

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

Jako alternativu klíč předplatného můžete také uvést v hlavičce požadavku HTTP:

```
ocp-apim-subscription-key: <Your subscription key>
```

Při použití klientské knihovny, klíč předplatného předána v konstruktoru třídy VideoServiceClient. Příklad:

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
Získat klíč předplatného, najdete v tématu [odběry] (https://azure.microsoft.com/try/cognitive-services/). 

### <a name="Step2">Krok 2: Nahrání video ke službě a zkontrolujte stav</a>
Nejzákladnější možnost provádět všechny rozhraní API pro rozpoznávání emocí úrovně Video volání je tím, že nahrajete video přímo. K tomu je potřeba odeslání žádosti o "POST" se typ obsahu application/octet-stream společně s data načtená z videosouboru. Maximální velikost videa je 100MB.

Pomocí klientské knihovny, ustálení prostřednictvím odesílání provádí předáním do datového proudu objektu. Viz následující příklad:

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

Upozorňujeme, že metodě CreateOperationAsync VideoServiceClient je asynchronní. Volání metody by měl být označen jako asynchronní také chcete-li použít klauzuli await.
Pokud na video již je na webu a má veřejnou adresu URL, rozpoznávání emocí úrovně rozhraní API pro Video přístupná zadáním adresy URL. V tomto příkladu bude textu žádosti řetězec formátu JSON, který obsahuje adresu URL.

Pomocí klientské knihovny, ustálení prostřednictvím adresy URL se dají snadno provádět pomocí jiné přetížení metody CreateOperationAsync.


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

Tato metoda nahrávání budou stejné pro všechny rozhraní API pro rozpoznávání emocí úrovně pro volání Video. 

Jakmile jste nahráli video, je další operace, kterou budete chtít provést zkontrolujte stav. Protože video soubory jsou obvykle větší a více různých než ostatní soubory, uživatelé mohou očekávat s dlouhým doba zpracování v tomto kroku. Doba závisí na velikosti a délku souboru.

Pomocí klientské knihovny, je možné načíst stav operace a výsledek metodou GetOperationResultAsync.


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
Obvykle na straně klienta by měl pravidelně načíst stav operace dokud je stav zobrazen jako "Succeeded" nebo "Failed".

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

Stav VideoOperationResult, když je zobrazena jako "Bylo úspěšně dokončeno" výsledek může načíst přetypování VideoOperationResult k VideoOperationInfoResult<VideoAggregateRecognitionResult> a přístup k poli ProcessingResult.

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">Krok 3: Načítání a pochopení rozpoznávání rozpoznávání emocí úrovně a sledování výstup JSON</a>

Výsledek výstup obsahuje metadata z tyto řezy v dané souboru ve formátu JSON.

Jak je popsáno v kroku 2, výstup JSON je dostupná v poli ProcessingResult výsledek, když jeho stav se zobrazí jako "Succeeded".

Detekce vzhled a sledování JSON obsahuje následující atributy:

Atribut | Popis
-------------|-------------
Verze | Odkazuje na verzi rozhraní API pro rozpoznávání emocí úrovně pro formát JSON Video.
Časová osa | "Rysky" za sekundu videa.
Posun  |Časový posun pro časová razítka. Ve verzi 1.0 rozpoznávání emocí úrovně rozhraní API pro videa bude vždy 0. V budoucích Podporované scénáře tato hodnota může změnit.
Kmitočet snímků | Počet snímků za sekundu videa.
Fragmenty   | Metadata se Vyjmout na jiné menší části názvem fragmenty. Každý fragment obsahuje počáteční, doba trvání, číslo intervalu a událostí.
Start   | Čas zahájení první událost v rysky.
Doba trvání |  Délka fragment v rysky.
Interval |  Délka všechny události v rámci fragment v rysky.
Události  | Pole události. Vnější pole představuje jeden časový interval. Vnitřní pole se skládá z 0 nebo více událostí, které bylo provedeno v tomto bodě v čase.
windowFaceDistribution |    Procento otočená tak, aby měl konkrétní rozpoznávání emocí úrovně během události.
windowMeanScores |  Střední skóre pro každý emoce tyto řezy v bitové kopii.

Z důvodu pro formátování JSON tímto způsobem je nastavit rozhraní API pro budoucí scénáře, kde bude potřeba načíst metadata rychle a spravovat velké proud výsledky. Formátování v aplikaci je pomocí technik fragmentace (umožňuje rozdělit metadat v části založené na čase, kde si můžete stáhnout pouze to, co potřebujete) a segmentace (umožňuje rozdělit události, pokud se zobrazí příliš velký). Některé jednoduché výpočty můžete transformovat data. Například, pokud událost zahájená 6300 (rysky), s časovou 2997 (rysky za sekundu) a kmitočet snímků 29,97 (snímků za sekundu), pak:

*   Spuštění nebo časový rámec = 2.1 sekund
*   Sekund x (kmitočet snímků nebo časová osa) = 63 rámce

Níže je jednoduchý příklad extrahování JSON do za formát rámce pro detekci vzhled a sledování:

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
Protože emoce vyhlazení v čase, pokud někdy vytvořit vizualizaci do překrytí výsledky nahoře na původní video, odečtena 250 milisekund ze zadané časová razítka.

### <a name="Summary">Souhrn</a>
V této příručce jste se naučili o funkce rozhraní API pro rozpoznávání emocí úrovně pro Video: jak můžete nahrát video, zkontrolujte stav, načítání metadat rozpoznávání rozpoznávání emocí úrovně.

Další informace o rozhraní API. Podrobnosti najdete v tématu Referenční příručka rozhraní API "[rozpoznávání emocí úrovně rozhraní API pro odkaz na Video](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e)".
