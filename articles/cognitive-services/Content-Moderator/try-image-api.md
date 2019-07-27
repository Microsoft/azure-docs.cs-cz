---
title: Střední obrázky s konzolou API – Content Moderator
titleSuffix: Azure Cognitive Services
description: Použití rozhraní API pro moderování imagí v Azure Content Moderator k zahájení kontrol a revizí pracovních postupů moderování pro obsah obrázků.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 20353d488cba255925d21017b77d1def2d06b30b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561242"
---
# <a name="moderate-images-from-the-api-console"></a>Střední obrázky z konzoly API

Použití [rozhraní API pro moderování imagí](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) v Azure Content moderator k zahájení kontrol a revizí pracovních postupů moderování pro obsah obrázků. Úloha moderování zkontroluje obsah pro vulgární výrazy a porovná ho s vlastními a sdílenými zakázanými operacemi.

## <a name="use-the-api-console"></a>Použití konzoly API
Než budete moct otestovat rozhraní API v online konzole, budete potřebovat svůj klíč předplatného. Najdete ho na kartě **Nastavení** v poli **OCP-APIM-Subscription-Key** . Další informace najdete v tématu [Přehled](overview.md).

1. Přejít na [Reference k rozhraní API pro moderování imagí](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)

   Otevře se stránka **pro** moderování image vyhodnocování obrazu.

2. V případě **konzoly Open API Testing**vyberte oblast, která nejlépe popisuje vaše umístění. 

   ![Vyzkoušet výběr oblasti stránky a vyhodnotit image](images/test-drive-region.png)
  
   Otevře se konzola rozhraní API **pro vyhodnocení bitové kopie** .

3. Do pole **OCP-APIM-Subscription-Key** zadejte svůj klíč předplatného.

   ![Vyzkoušejte klíč předplatného konzoly pro vyhodnocení image](images/try-image-api-1.PNG)

4. V poli **Text žádosti** použijte výchozí ukázkovou bitovou kopii nebo určete obrázek, který chcete prohledat. Samotný obrázek můžete odeslat jako binární data nebo zadat veřejně přístupnou adresu URL pro obrázek. 

   V tomto příkladu použijte cestu zadanou v poli **Text žádosti** a pak vyberte **Odeslat**. 

   ![Vyzkoušení image – tělo žádosti konzoly](images/try-image-api-2.PNG)

   Obrázek na této adrese URL:

   ![Vyzkoušejte ukázkový obrázek konzoly pro vyhodnocení image](images/sample-image.jpg) 

5. Vyberte **Poslat**.

6. Rozhraní API vrací skóre pravděpodobnosti pro každou klasifikaci. Také vrátí určení, zda bitová kopie splňuje podmínky (**true** nebo **false**). 

   ![Vyzkoušejte hodnocení pravděpodobnosti a určení podmínky v konzole pro vyhodnocení image](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Rozpoznávání tváře

Rozhraní API pro moderování obrázků můžete použít k vyhledání plošek v obrázku. Tato možnost může být užitečná, když máte obavy z ochrany osobních údajů a chcete zabránit tomu, aby se na vaší platformě publikoval konkrétní obličej. 

1. V referenčních informacích k [rozhraní API pro moderování imagí](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)v nabídce vlevo v části **Obrázek**vyberte **Najít obličeje**. 

   Otevře se stránka **s obrázkem obličeje** .

2. V případě **konzoly Open API Testing**vyberte oblast, která nejlépe popisuje vaše umístění. 

   ![Vyzkoušejte si obrázek – najde výběr oblasti stránky obličeje](images/test-drive-region.png)

   Otevře se konzola rozhraní API **pro obrázky – najít obličeje** .

3. Zadejte obrázek, který se má prohledat. Samotný obrázek můžete odeslat jako binární data nebo zadat veřejně přístupnou adresu URL k obrázku. Tento příklad odkazuje na obrázek, který se používá v CNNm scénáři.

   ![Vyzkoušejte si ukázku obrázku – vyhledání obličeje](images/try-image-api-face-image.jpg)

   ![Vyzkoušejte si ukázkovou žádost o image – vyhledání obličeje](images/try-image-api-face-request.png)

4. Vyberte **Poslat**. V tomto příkladu rozhraní API najde dvě obličeje a vrátí jejich souřadnice v obrázku.

   ![Vyzkoušejte si pole obsah odpovědi na obrázek ukázkové obličeje.](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Rozpoznávání textu prostřednictvím funkce OCR

K detekci textu v obrázcích můžete použít funkci Content Moderator optické rozpoznávání znaků.

1. V referenčních informacích k [rozhraní API pro moderování imagí](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)v nabídce vlevo v části **Obrázek**vyberte **optické rozpoznávání znaků**. 

   Otevře se stránka **Obrázek – optické rozpoznávání znaků** .

2. V případě **konzoly Open API Testing**vyberte oblast, která nejlépe popisuje vaše umístění. 

   ![Obrázek – výběr oblasti stránky s rozpoznáváním OCR](images/test-drive-region.png)

   Otevře se konzola rozhraní API **pro optické rozpoznávání obrazu** .

3. Do pole **OCP-APIM-Subscription-Key** zadejte svůj klíč předplatného.

4. V poli **Text žádosti** použijte výchozí ukázkovou bitovou kopii. To je stejný obrázek, který se používá v předchozí části.

5. Vyberte **Poslat**. Extrahovaný text se zobrazí ve formátu JSON:

   ![Obrázek – pole obsahu odpovědi vzorku pro optické rozpoznávání znaků](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Další postup

Pomocí REST API ve vašem kódu nebo začněte s aplikací pro [rychlý Start pro moderování imagí .NET](image-moderation-quickstart-dotnet.md) pro integraci s vaší aplikací.
