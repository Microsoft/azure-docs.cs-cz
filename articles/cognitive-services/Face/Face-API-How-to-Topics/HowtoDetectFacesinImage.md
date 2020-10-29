---
title: Rozpoznávání plošek na obrázku – tvář
titleSuffix: Azure Cognitive Services
description: Tato příručka ukazuje, jak použít detekci obličeje k extrakci atributů, jako je pohlaví, věk nebo pozice z daného obrázku.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: f7a740b1015bda80000f65180eda2c5e618670da
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911235"
---
# <a name="get-face-detection-data"></a>Získat data o detekci obličeje

Tato příručka ukazuje, jak použít detekci obličeje k extrakci atributů, jako je pohlaví, věk nebo pozice z daného obrázku. Fragmenty kódu v této příručce jsou napsané v jazyce C# pomocí klientské knihovny Azure Cognitive Services Face. Stejné funkce jsou k dispozici prostřednictvím [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

V této příručce se dozvíte, jak:

- Získá umístění a rozměry ploch v obrázku.
- Získejte na obrázku umístění různých orientačních bodů, jako jsou žákům, nos a pusa.
- Odhadujte pohlaví, věk, emoce a další atributy zjištěné plochy.

## <a name="setup"></a>Nastavení

V tomto průvodci se předpokládá, že už jste nastavili objekt [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) s názvem s `faceClient` klíčovým předplatným a adresou URL koncového bodu. Z tohoto místa můžete použít funkci detekce obličeje voláním [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), který se používá v tomto průvodci nebo v [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Pokyny, jak tuto funkci nastavit, najdete v některém z rychlých startů.

Tato příručka se zaměřuje na konkrétní volání detekce, například jaké argumenty můžete předat a co můžete s vrácenými daty dělat. Doporučujeme zadat dotaz jenom na funkce, které potřebujete. Dokončení každé operace trvá déle.

## <a name="get-basic-face-data"></a>Získat základní data obličeje

Chcete-li najít plošky a získat jejich umístění na obrázku, zavolejte metodu [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet) nebo [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet) s parametrem _returnFaceId_ nastaveným na **hodnotu true** . Toto nastavení je výchozí.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

Můžete zadat dotaz na vrácené objekty [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) pro jejich jedinečná ID a obdélník, který dává souřadnici v pixelech na ploše.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Informace o tom, jak analyzovat umístění a rozměry obličeje, najdete v tématu [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Tento obdélník obvykle obsahuje oči, eyebrows, nos a pusu. Horní okraje hlav, EARS a Chin nejsou nutně zahrnuté. Chcete-li použít obdélník obličeje k oříznutí celé hlavy nebo získání střední pozice na výšku, například pro obrázek typu fotografie s ID, můžete obdélník v každém směru rozšířit.

## <a name="get-face-landmarks"></a>Získat orientační vzhledy

[Přední orientační](../concepts/face-detection.md#face-landmarks) body jsou sada snadno hledaných bodů na plošku, jako jsou žákům nebo špička v nos. Pokud chcete získat data orientačních bodů, nastavte parametr _detectionModel_ na **detectionModel. Detection01** a parametr _returnFaceLandmarks_ na **hodnotu true** .

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

Následující kód ukazuje, jak můžete načíst umístění nos a žáků:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

Pomocí dat orientačních bodů můžete také přesně vypočítat směr plochy. Například můžete definovat rotaci obličeje jako vektor od středu úst až po střed oči. Následující kód vypočítá tento vektor:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Když znáte směr plochy, můžete obdélníkový rám otočit tak, aby byl lépe zarovnaný. Chcete-li oříznout plošky v obrázku, můžete obrázek programově otočit tak, aby se plošky vždy zobrazovaly vzhůru.

## <a name="get-face-attributes"></a>Získat atributy obličeje

Kromě obdélníků a orientačních bodů může rozhraní API pro detekci obličeje analyzovat několik koncepčních atributů plošky. Úplný seznam najdete v části koncepční [atributy Face](../concepts/face-detection.md#attributes) .

Chcete-li analyzovat atributy tváře, nastavte parametr _detectionModel_ na **detectionModel. Detection01** a parametr _ReturnFaceAttributes_ na seznam hodnot [FaceAttributeType enum](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) .

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Pak Získejte odkazy na vracená data a udělejte více operací podle vašich potřeb.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Další informace o jednotlivých atributech najdete v koncepční příručce pro [rozpoznávání tváře a atributy](../concepts/face-detection.md) .

## <a name="next-steps"></a>Další kroky

V této příručce jste zjistili, jak používat různé funkce detekce tváře. Potom tyto funkce Integrujte do své aplikace pomocí podrobného kurzu.

- [Kurz: Vytvoření aplikace WPF pro zobrazení dat obličeje v obrázku](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>Související témata

- [Referenční dokumentace (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referenční dokumentace (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)