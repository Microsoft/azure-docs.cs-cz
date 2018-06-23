---
title: Střední obsah pomocí lidského recenze v Azure obsah moderátora | Microsoft Docs
description: Naučte se vytvářet lidského recenze v konzole obsahu moderátora rozhraní API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: e9faf595e65ba4475a743e4cb45919fd30fbd6e8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342430"
---
# <a name="create-reviews-from-the-api-console"></a>Vytvoření recenze z konzoly pro rozhraní API

Použít API zkontrolujte [zkontrolujte operations](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) vytvořit bitovou kopii nebo text recenze pro lidské přerušování. Použít lidského moderátorů kontrolní nástroj pro zkontrolujte obsah. Pomocí této operace na základě vaší po přerušování obchodní logiky. Použijte po mít zkontrolovat svůj obsah pomocí některé z bitové kopie obsahu moderátora nebo text rozhraní API nebo jiná kognitivní rozhraní API služby. 

Jakmile lidského moderátor zkontroluje automaticky přiřazená značky a předpovědi data a odešle přerušování konečné rozhodnutí, odešle rozhraní API zkontrolujte všechny informace, které váš koncový bod rozhraní API.

## <a name="use-the-api-console"></a>Pomocí rozhraní API konzoly
Chcete-li test-drive rozhraní API pomocí konzoly služby online, je třeba několik hodnot k zadání do konzoly:

- **teamName**: název týmu, který jste vytvořili při nastavování účtu Zkontrolujte nástroj. 
- **ContentId**: Tento řetězec je předán do rozhraní API a vráceny prostřednictvím zpětné volání. ID je užitečné pro přidružení k výsledky úlohy přerušování interní identifikátory nebo metadata.
- **Metadata**: vlastní páry klíč hodnota vrácená koncový bod služby API během zpětného volání. Pokud je klíč krátký kód, který je definován v nástroji kontrolní, zobrazí se jako značku.
- **OCP-Apim-Subscription-Key**: nachází na **nastavení** kartě. Další informace najdete v tématu [přehled](overview.md).

Nejjednodušší způsob, jak získat přístup k testování konzoly je z **pověření** okno.

1.  V **pověření** vyberte [referenční dokumentace rozhraní API zkontrolujte](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

  **Zkontrolujte – vytvoření** otevře se stránka.

2.  Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu.

  ![Zkontrolujte – vytvoření výběr oblast stránky](images/test-drive-region.png)

  **Zkontrolujte – vytvoření** otevře se konzola rozhraní API.
  
3.  Zadejte hodnoty pro požadované parametry dotazu, typu obsahu a svůj klíč předplatného. V **text žádosti** zadejte obsah (například umístění bitové kopie), metadat a další informace související s obsahem.

  ![Zkontrolujte – vytvoření parametry dotazu konzoly, hlavičky a pole textu požadavku](images/test-drive-review-1.PNG)
  
4.  Vyberte **Poslat**. ID revize se vytvoří. Zkopírujte toto ID použít v následujících krocích.

  ![Zkontrolujte – vytvoření konzole odpovědi obsahu pole se zobrazí ID revize](images/test-drive-review-2.PNG)
  
5.  Vyberte **získat**a pak otevřete rozhraní API výběrem tlačítka, který odpovídá vaší oblasti. Na stránce výsledné zadejte hodnoty pro **teamName**, **ReviewID**, a **klíč předplatného**. Vyberte **odeslat** tlačítko na stránce. 

  ![Zkontrolujte – vytvoření konzoly Get výsledky](images/test-drive-review-3.PNG)
  
6.  Zobrazí se výsledky kontroly.

  ![Zkontrolujte – vytvoření konzoly pole obsahu odpovědi](images/test-drive-review-4.PNG)
  
7.  Na řídicím panelu obsahu moderátora vyberte **zkontrolujte** > **Image**. Zobrazí se bitovou kopii, která můžete zkontrolovat, připraveni k lidské zkontrolujte.

  ![Zkontrolujte nástroj obrázek Fotbalový míč](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>Další postup

Použít rozhraní API REST v kódu ani začínat [rychlý start recenze .NET](moderation-reviews-quickstart-dotnet.md) integrovat s vaší aplikací.
