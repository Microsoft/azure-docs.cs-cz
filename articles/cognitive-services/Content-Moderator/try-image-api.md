---
title: Moderování obrázků s konzolou rozhraní API – Content Moderator
titlesuffix: Azure Cognitive Services
description: Vyzkoušejte si moderování obrázků v konzole nástroje Content Moderator API.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: a88eb1e0fc91fb47a95c8b1fea84cfac32674266
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224951"
---
# <a name="moderate-images-from-the-api-console"></a>Moderování obrázků z konzoly pro rozhraní API

Použití [rozhraní API pro moderování obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) v Azure Content Moderator k zahájení pracovních postupů kontroly a kontrola moderování obsahu bitové kopie. Moderování úlohy kontroluje váš obsah pro vulgárních výrazů a porovná je s vlastní a sdílené seznamů zakázaných položek.

## <a name="use-the-api-console"></a>Pomocí rozhraní API konzoly
Předtím, než můžete vyzkoušet rozhraní API v konzole online, budete potřebovat klíč předplatného. To je umístěn na **nastavení** kartě **Ocp-Apim-Subscription-Key** pole. Další informace najdete v tématu [přehled](overview.md).

1.  Přejděte na [reference k rozhraní API pro moderování obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

  **Obrázek – vyhodnocení** otevře se stránka pro moderování obrázků.

2. Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Zkuste Image - vyhodnotit výběr oblasti stránek](images/test-drive-region.png)
  
  **Obrázek – vyhodnocení** otevře se konzola rozhraní API.

3. V **Ocp-Apim-Subscription-Key** zadejte váš klíč předplatného.

  ![Zkuste Image - vyhodnotit klíč předplatného konzoly](images/try-image-api-1.PNG)

4. V **text žádosti** pole, použít výchozí Ukázkový obrázek nebo zadejte obrázek, který chcete prohledat. Samotný obrázek jako binární soubor, můžete zadat bit dat nebo zadejte adresu URL veřejně přístupná pro bitovou kopii. 

  V tomto příkladu použijte cestě zadané v **text žádosti** a potom vyberte **odeslat**. 

   ![Zkuste Image – vyhodnocení těla požadavku konzoly](images/try-image-api-2.PNG)

  Toto je image na dané adrese URL:

  ![Zkuste Image - vyhodnotit konzoly Ukázkový obrázek](images/sample-image.jpg) 

5. Vyberte **Poslat**.

6. Rozhraní API vrátí skóre pravděpodobnosti pro každou klasifikaci. Také vrátí zjištění, jestli image splňuje podmínky (**true** nebo **false**). 

  ![Zkuste Image – vyhodnotit skóre pravděpodobnosti konzoly a stanovení podmínky](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Detekce tváře

Rozhraní API pro moderování obrázků můžete použít k vyhledání tváří v obrázku. Tato možnost může být užitečné, když máte obavy o ochraně osobních údajů a chcete zabránit odeslání na vaší platformě konkrétní tváře. 

1.  V [reference k rozhraní API pro moderování obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), v nabídce vlevo v části **Image**vyberte **najít čelí**. 

  **Image - najít čelí** otevře se stránka.

2.  Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Zkuste Image – vyhledání výběr oblasti stránek tváří](images/test-drive-region.png)

  **Image - najít čelí** otevře se konzola rozhraní API.

3. Zadejte obrázek, který chcete prohledat. Samotný obrázek jako binární soubor, můžete zadat bit dat nebo zadejte adresu URL veřejně přístupná pro bitovou kopii. Tento příklad obsahuje odkazy na bitovou kopii, která se používá v CNN scénáře.

  ![Zkuste Image – vyhledání Ukázkový obrázek tváře](images/try-image-api-face-image.jpg)

  ![Zkuste Image – vyhledání tváří ukázková žádost](images/try-image-api-face-request.png)

4. Vyberte **Poslat**. V tomto příkladu rozhraní API vyhledá dvě tváře a vrátí jejich souřadnice na obrázku.

   ![Zkuste Image – vyhledání tváří ukázkové odpovědi pole obsahu](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Text zjišťování prostřednictvím funkce technologie OCR

Možnost Content Moderator OCR můžete použít k detekci textu v obrázcích.

1. V [reference k rozhraní API pro moderování obrázků](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), v nabídce vlevo v části **Image**vyberte **OCR**. 

  **Image - OCR** otevře se stránka.

2. Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Obrázek – výběr oblasti stránek technologie OCR](images/test-drive-region.png)

  **Image - OCR** otevře se konzola rozhraní API.

3. V **Ocp-Apim-Subscription-Key** zadejte váš klíč předplatného.

4. V **text žádosti** pole, použijte výchozí Ukázkový obrázek. Toto je stejný obrázek, který se používá v předchozí části.

5. Vyberte **Poslat**. Byl extrahován text se zobrazí ve formátu JSON:

  ![Obrázek – OCR ukázkové odpovědi pole obsahu](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Další postup

Použití rozhraní REST API ve vašem kódu nebo začínat [rychlý úvod k .NET pro moderování obrázků](image-moderation-quickstart-dotnet.md) integrovat s vaší aplikací.
