---
title: Střední obrázky s konzolou API – Content Moderator
titleSuffix: Azure Cognitive Services
description: Použití rozhraní API pro moderování imagí v Azure Content Moderator k zahájení kontrol a revizí pracovních postupů moderování pro obsah obrázků.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 542fadd5e5ab91be7b7113064bf8c998dae08d12
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912935"
---
# <a name="moderate-images-from-the-api-console"></a>Střední obrázky z konzoly API

Použití [rozhraní API pro moderování imagí](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) v Azure Content moderator k zahájení kontrol a revizí pracovních postupů moderování pro obsah obrázků. Úloha moderování kontroluje obsah pro vulgární výrazy a porovnává je s vlastními a sdílenými adres.

## <a name="use-the-api-console"></a>Použití konzoly API
Než budete moct otestovat rozhraní API v online konzole, budete potřebovat svůj klíč předplatného. Najdete ho na kartě **Nastavení** v poli **OCP-APIM-Subscription-Key** . Další informace najdete v tématu [Přehled](overview.md).

1. Přejít na [Reference k rozhraní API pro moderování imagí](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)

   Otevře se stránka pro moderování image **vyhodnocování** obrazu.

2. V případě **konzoly Open API Testing** vyberte oblast, která nejlépe popisuje vaše umístění. 

   ![Vyzkoušet výběr oblasti stránky a vyhodnotit image](images/test-drive-region.png)
  
   Otevře se konzola rozhraní API **pro vyhodnocení bitové kopie** .

3. Do pole **OCP-APIM-Subscription-Key** zadejte svůj klíč předplatného.

   ![Vyzkoušejte klíč předplatného konzoly pro vyhodnocení image](images/try-image-api-1.PNG)

4. V poli **Text žádosti** použijte výchozí ukázkovou bitovou kopii nebo určete obrázek, který chcete prohledat. Samotný obrázek můžete odeslat jako binární data nebo zadat veřejně přístupnou adresu URL pro obrázek. 

   V tomto příkladu použijte cestu zadanou v poli **Text žádosti** a pak vyberte **Odeslat** . 

   ![Vyzkoušení image – tělo žádosti konzoly](images/try-image-api-2.PNG)

   Obrázek na této adrese URL:

   ![Vyzkoušejte ukázkový obrázek konzoly pro vyhodnocení image](images/sample-image.jpg) 

5. Vyberte **Poslat** .

6. Rozhraní API vrací skóre pravděpodobnosti pro každou klasifikaci. Také vrátí určení, zda bitová kopie splňuje podmínky ( **true** nebo **false** ). 

   ![Vyzkoušejte hodnocení pravděpodobnosti a určení podmínky v konzole pro vyhodnocení image](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Rozpoznávání tváře

Rozhraní API pro moderování obrázků můžete použít k vyhledání plošek v obrázku. Tato možnost může být užitečná, když máte obavy z ochrany osobních údajů a chcete zabránit tomu, aby se na vaší platformě publikoval konkrétní obličej. 

1. V [referenčních informacích k rozhraní API pro moderování imagí](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)v nabídce vlevo v části **Obrázek** vyberte **Najít obličeje** . 

   Otevře se stránka **s obrázkem obličeje** .

2. V případě **konzoly Open API Testing** vyberte oblast, která nejlépe popisuje vaše umístění. 

   ![Vyzkoušejte si obrázek – najde výběr oblasti stránky obličeje](images/test-drive-region.png)

   Otevře se konzola rozhraní API **pro obrázky – najít obličeje** .

3. Zadejte obrázek, který se má prohledat. Samotný obrázek můžete odeslat jako binární data nebo zadat veřejně přístupnou adresu URL k obrázku. Tento příklad odkazuje na obrázek, který se používá v CNNm scénáři.

   ![Vyzkoušejte si ukázku obrázku – vyhledání obličeje](images/try-image-api-face-image.jpg)

   ![Vyzkoušejte si ukázkovou žádost o image – vyhledání obličeje](images/try-image-api-face-request.png)

4. Vyberte **Poslat** . V tomto příkladu rozhraní API najde dvě obličeje a vrátí jejich souřadnice v obrázku.

   ![Vyzkoušejte si pole obsah odpovědi na obrázek ukázkové obličeje.](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Rozpoznávání textu prostřednictvím funkce OCR

K detekci textu v obrázcích můžete použít funkci Content Moderator optické rozpoznávání znaků.

1. V [referenčních informacích k rozhraní API pro moderování imagí](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)v nabídce vlevo v části **Obrázek** vyberte **optické rozpoznávání znaků** . 

   Otevře se stránka **Obrázek – optické rozpoznávání znaků** .

2. V případě **konzoly Open API Testing** vyberte oblast, která nejlépe popisuje vaše umístění. 

   ![Obrázek – výběr oblasti stránky s rozpoznáváním OCR](images/test-drive-region.png)

   Otevře se konzola rozhraní API **pro optické rozpoznávání obrazu** .

3. Do pole **OCP-APIM-Subscription-Key** zadejte svůj klíč předplatného.

4. V poli **Text žádosti** použijte výchozí ukázkovou bitovou kopii. To je stejný obrázek, který se používá v předchozí části.

5. Vyberte **Poslat** . Extrahovaný text se zobrazí ve formátu JSON:

   ![Obrázek – pole obsahu odpovědi vzorku pro optické rozpoznávání znaků](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Další kroky

Použijte REST API ve vašem kódu nebo použijte Průvodce pro [.NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) pro přidání moderování obrázků do aplikace.