---
title: Jak zmírnit latenci při používání služby obličeje
titleSuffix: Azure Cognitive Services
description: Naučte se, jak zmírnit latenci při používání služby obličeje.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: v-jawe
ms.openlocfilehash: e7f4293955772697ddeea5fce9daac4b04755274
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937282"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>Postupy: zmírnění latence při používání služby Face

Při použití služby obličeje se může vyskytnout latence. Latence odkazuje na jakýkoli typ zpoždění, ke kterému dojde při komunikaci přes síť. Mezi možné příčiny latence patří:
- Fyzická vzdálenost, kterou musí každý paket cestovat od zdroje do cíle.
- Problémy s přenosovým médiem.
- Chyby ve směrovačích nebo přepínačích podél cesty přenosu.
- Čas potřebný antivirovými aplikacemi, branami firewall a dalšími mechanismy zabezpečení pro kontrolu paketů.
- V klientských nebo serverových aplikacích nefunguje správně.

V tomto tématu se seznámíte s možnými příčinami latence, které se týkají použití Cognitive Services Azure, a o tom, jak můžete tyto příčiny zmírnit.

> [!NOTE]
> Azure Cognitive Services neposkytují žádné smlouva SLA (SLA) týkající se latence.

## <a name="possible-causes-of-latency"></a>Možné příčiny latence

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Pomalé připojení mezi službu pro rozpoznávání a vzdálenou adresou URL

Některé služby Azure Cognitive Services poskytují metody, které získávají data ze vzdálené adresy URL, kterou zadáte. Například při volání [metody DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) služby Face můžete zadat adresu URL obrázku, ve kterém se služba pokusí rozpoznat obličeje.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

Služba obličeje pak musí stáhnout image ze vzdáleného serveru. Pokud je připojení ze služby obličeje na vzdáleném serveru pomalé, bude to mít vliv na dobu odezvy metody detekce.

Pokud to chcete zmírnit, zvažte [uložení image v Azure Premium BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-upload-process-images?tabs=dotnet). Příklad:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Velikost velkého nahrávání

Některé služby Azure Cognitive Services poskytují metody, které získávají data ze souboru, který nahráváte. Například při volání [metody DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) služby Face můžete nahrát obrázek, ve kterém se služba pokusí rozpoznat obličeje.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Pokud soubor k nahrání je velký, což bude mít vliv na dobu odezvy `DetectWithStreamAsync` metody, a to z následujících důvodů:
- Nahrávání souboru trvá déle.
- Pořizuje službě déle zpracovat soubor v poměru k velikosti souboru.

Hrozeb
- Zvažte [uložení image v Azure Premium BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-upload-process-images?tabs=dotnet). Příklad:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Zvažte odeslání menšího souboru.
    - Přečtěte si pokyny týkající se [vstupních dat pro detekci obličeje](https://docs.microsoft.com/azure/cognitive-services/face/concepts/face-detection#input-data) a [vstupní data pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/concepts/face-recognition#input-data).
    - Pro detekci tváře při použití modelu detekce se při `DetectionModel.Detection01` zmenšování velikosti souboru obrázku zvýší rychlost zpracování. Při použití modelu detekce se `DetectionModel.Detection02` zmenšení velikosti souboru obrázku zvýší jenom na rychlost zpracování, pokud je soubor s obrázkem menší než 1080.
    - Pro rozpoznávání tváře, zmenšení velikosti obličeje na 200x200 pixelů nemá vliv na přesnost modelu rozpoznávání.
    - Výkon `DetectWithUrlAsync` `DetectWithStreamAsync` metod a závisí také na tom, kolik ploch je v obrázku. Služba obličeje může vracet až 100 obličeje pro obrázek. Obličeje jsou seřazeny podle velikosti rámečku obličeje od velkých po malých.
    - Pokud potřebujete volat více metod služby, zvažte jejich volání paralelně, pokud to pro návrh aplikace umožňuje. Například pokud potřebujete detekovat plošky ve dvou obrázcích, aby se provedlo porovnání obličeje:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Pomalé připojení mezi výpočetním prostředkem a službou obličeje

Pokud má počítač pomalé připojení ke službě Facet, bude to mít vliv na dobu odezvy pro metody služby.

Hrozeb
- Když vytváříte své předplatné, ujistěte se, že jste vybrali oblast, která je nejblíže místu, kde je vaše aplikace hostovaná.
- Pokud potřebujete volat více metod služby, zvažte jejich volání paralelně, pokud to pro návrh aplikace umožňuje. Příklad najdete v předchozí části.

## <a name="next-steps"></a>Další kroky

V této příručce jste zjistili, jak zmírnit latenci při používání služby obličeje. V dalším kroku se dozvíte, jak škálovat z existujících objektů Person a FaceList do objektů LargePersonGroup a LargeFaceList v uvedeném pořadí.

> [!div class="nextstepaction"]
> [Příklad: použití funkce rozsáhlého škálování](how-to-use-large-scale.md)

## <a name="related-topics"></a>Související témata

- [Referenční dokumentace (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referenční dokumentace (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
