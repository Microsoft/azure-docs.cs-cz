---
title: Moderování obrázků pomocí konzoly API – moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro moderování obrázků v Moderátoru obsahu Azure můžete zahájit prohledávací a recenzní pracovní postupy pro moderování obsahu bitových obrázků.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 714621fdcc307ee8b29567fc0d95ca41d31aa9e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75448260"
---
# <a name="moderate-images-from-the-api-console"></a>Moderování obrázků z konzoly rozhraní API

Pomocí [rozhraní API pro moderování obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) v Moderátoru obsahu Azure můžete zahájit prohledávací a recenzní pracovní postupy pro moderování obsahu bitových obrázků. Úloha moderování prohledá váš obsah z důvodu vulgárních výrazů a porovná jej s vlastními a sdílenými seznamy blokování.

## <a name="use-the-api-console"></a>Použití konzoly rozhraní API
Než budete moci testovací disk API v online konzole, budete potřebovat klíč předplatného. To se nachází na kartě **Nastavení,** v **poli Ocp-Apim-Subscription-Key.** Další informace najdete v tématu [Přehled](overview.md).

1. Přejděte na [odkaz rozhraní API pro moderování obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   Otevře se stránka **Obrázek – Vyhodnocení** moderování obrázků.

2. V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu. 

   ![Try Image - Vyhodnocení výběru oblasti stránky](images/test-drive-region.png)
  
   Otevře se **konzola Image - Evaluate** API.

3. Do pole **Ocp-Apim-Subscription-Key** zadejte klíč předplatného.

   ![Try Image – vyhodnocení klíče předplatného konzoly](images/try-image-api-1.PNG)

4. V poli **Vyžádat tělo** použijte výchozí ukázkový obraz nebo určete obraz, který chcete skenovat. Samotný obrázek můžete odeslat jako binární bitová data nebo zadat veřejně přístupnou adresu URL pro obrázek. 

   V tomto příkladu použijte cestu uvedenou v poli **Tělo požadavku** a pak vyberte **Odeslat**. 

   ![Try Image - Vyhodnocení těla požadavku konzoly](images/try-image-api-2.PNG)

   Toto je obrázek na této adrese URL:

   ![Try Image – vyhodnocení ukázkového obrázku konzoly](images/sample-image.jpg) 

5. Vyberte **Poslat**.

6. Rozhraní API vrátí skóre pravděpodobnosti pro každou klasifikaci. Vrátí také určení, zda obrázek splňuje podmínky **(true** nebo **false).** 

   ![Try Image - Vyhodnotit skóre pravděpodobnosti konzoly a stanovení stavu](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Rozpoznávání tváře

Rozhraní API pro moderování obrázků můžete použít k vyhledání tváří v obraze. Tato možnost může být užitečná, pokud máte obavy o ochranu osobních údajů a chcete zabránit tomu, aby byla na vaší platformě zveřejněna určitá tvář. 

1. V [odkazu rozhraní API pro moderování obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)vyberte v levém menu v **části Obrázek** **možnost Najít plochy**. 

   Otevře se stránka **Obraz - Najít tváře.**

2. V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu. 

   ![Try Image - Výběr oblasti stránky Najít tváře](images/test-drive-region.png)

   Otevře se konzola Rozhraní API **pro hledání tváří.**

3. Určete obraz, který chcete skenovat. Samotný obrázek můžete odeslat jako binární bitová data nebo zadat veřejně přístupnou adresu URL obrázku. Tento příklad odkazuje na obrázek, který se používá v příběhu CNN.

   ![Try Image - Najít tváře ukázkový obrázek](images/try-image-api-face-image.jpg)

   ![Try Image - Požadavek na ukázkový dotaz najít tváře](images/try-image-api-face-request.png)

4. Vyberte **Poslat**. V tomto příkladu rozhraní API najde dvě tváře a vrátí jejich souřadnice v bitové kopii.

   ![Try Image - Najít tváře ukázkový obsah odpovědi pole](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Detekce textu pomocí funkce OCR

K detekci textu v obrázcích můžete použít funkci Rozpoznávání OCR moderátora obsahu.

1. V [odkazu rozhraní API pro moderování obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)vyberte v levém menu v části **Obrázek** **rozpoznávání OCR**. 

   Otevře se **stránka Obrázek - OCR.**

2. V **části Ovládací konzole pro testování rozhraní Open API**vyberte oblast, která nejpřesněji popisuje vaši polohu. 

   ![Obrázek – výběr oblasti stránky OCR](images/test-drive-region.png)

   Otevře se konzola **Image - OCR** API.

3. Do pole **Ocp-Apim-Subscription-Key** zadejte klíč předplatného.

4. V poli **Vyžádat tělo** použijte výchozí ukázkový obrázek. Jedná se o stejný obrázek, který se používá v předchozí části.

5. Vyberte **Poslat**. Extrahovaný text je zobrazen v JSON:

   ![Obrázek – pole obsahu ukázkové odpovědi ocr](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Další kroky

Pomocí rozhraní REST API v kódu nebo podle [rychlého startu sady .NET SDK](dotnet-sdk-quickstart.md) přidejte do aplikace moderování obrázků.
