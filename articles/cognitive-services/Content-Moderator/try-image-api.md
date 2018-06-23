---
title: Střední obrázky s Azure obsahu moderátora | Microsoft Docs
description: Test-Drive přerušování image v konzole obsahu moderátora rozhraní API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: fec54826c70ae10e56c68406f629c56639985295
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342457"
---
# <a name="moderate-images-from-the-api-console"></a>Střední bitové kopie z konzoly pro rozhraní API

Použití [Image přerušování API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) v Azure obsah moderátora zahájíte kontroly a zkontrolujte přerušování pracovní postupy pro obsahu bitové kopie. Přerušování úlohy kontroly obsahu testujeme a porovná je s sdílené a vlastní rozhraní API.

## <a name="use-the-api-console"></a>Pomocí rozhraní API konzoly
Než můžete test-drive rozhraní API v konzole online, musíte svůj klíč předplatného. To se nachází na **nastavení** ve **Ocp-Apim-Subscription-Key** pole. Další informace najdete v tématu [přehled](overview.md).

1.  Přejděte na [referenční dokumentace rozhraní API přerušování Image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

  **Obrázek – vyhodnotit** bitové kopie, otevře se stránka přerušování.

2. Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Zkuste bitovou kopii – vyhodnotit výběr oblast stránky](images/test-drive-region.png)
  
  **Obrázek – vyhodnotit** otevře se konzola rozhraní API.

3. V **Ocp-Apim-Subscription-Key** zadejte svůj klíč předplatného.

  ![Zkuste bitovou kopii – vyhodnotit klíč předplatného konzoly](images/try-image-api-1.PNG)

4. V **text žádosti** pole, použít výchozí obrázek ukázkové nebo zadejte bitovou kopii ke kontrole. Můžete odeslat bitovou kopii sám sebe jako binární bit dat nebo zadat adresu URL veřejně přístupný pro bitovou kopii. 

  V tomto příkladu použijte v zadané cestě **text žádosti** a pak vyberte **odeslat**. 

   ![Zkuste bitovou kopii – vyhodnocení textu žádosti konzoly](images/try-image-api-2.PNG)

  Toto je bitovou kopii na této adrese URL:

  ![Zkuste bitovou kopii – vyhodnotit obrázek ukázkové konzoly](images/sample-image.jpg) 

5. Vyberte **Poslat**.

6. Rozhraní API vrací pravděpodobnost skóre pro každé klasifikace. Vrátí také při zjišťování, zda obrázek splňuje podmínky (**true** nebo **false**). 

  ![Zkuste bitovou kopii – vyhodnotit konzoly pravděpodobnosti skóre a podmínky rozhodnutí](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Detekce tváře

Rozhraní API přerušování Image můžete najít tyto řezy v obraze. Tato možnost může být užitečné, když máte aspekty ochrany osobních údajů a chcete zabránit odeslání na vaši platformu konkrétní řez. 

1.  V [referenční dokumentace rozhraní API přerušování Image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), v levé nabídce v části **Image**, vyberte **najít otočená**. 

  **Image - najít otočená** otevře se stránka.

2.  Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Zkuste bitovou kopii – najít vybrané oblasti řezy stránky](images/test-drive-region.png)

  **Image - najít otočená** otevře se konzola rozhraní API.

3. Zadejte bitovou kopii ke kontrole. Můžete odeslat bitovou kopii sám sebe jako binární bit dat, nebo zadat adresu URL veřejně přístupný pro bitovou kopii. Tento příklad obsahuje odkazy na obrázek, který se používá v CNN scénáře.

  ![Zkuste bitovou kopii – najít řezy Ukázkový obrázek](images/try-image-api-face-image.jpg)

  ![Zkuste bitovou kopii – najít řezy ukázková žádost](images/try-image-api-face-request.png)

4. Vyberte **Poslat**. V tomto příkladu rozhraní API vyhledá dva řezy a vrátí jejich souřadnice v bitové kopii.

   ![Zkuste bitovou kopii – najít řezy ukázka pole obsahu odpovědi](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Detekce text prostřednictvím funkce rozpoznávání znaků

Funkce rozpoznávání obsahu moderátora znaků můžete použít k detekci text do bitových kopií.

1. V [referenční dokumentace rozhraní API přerušování Image](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), v levé nabídce v části **Image**, vyberte **rozpoznávání znaků**. 

  **Image - rozpoznávání znaků** otevře se stránka.

2. Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu. 

  ![Obrázek – výběr oblast stránky rozpoznávání znaků](images/test-drive-region.png)

  **Image - rozpoznávání znaků** otevře se konzola rozhraní API.

3. V **Ocp-Apim-Subscription-Key** zadejte svůj klíč předplatného.

4. V **text žádosti** pole, použijte výchozí ukázka image. Toto je stejný obrázek, který je použit v předchozí části.

5. Vyberte **Poslat**. Extrahované text se zobrazuje ve formátu JSON:

  ![Obrázek – pole obsahu odpovědi ukázka rozpoznávání znaků](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Další postup

Použít rozhraní API REST v kódu ani začínat [image přerušování .NET rychlý Start](image-moderation-quickstart-dotnet.md) integrovat s vaší aplikací.
