---
title: Moderování obsahu pomocí rozhraní API konzoly – Content Moderator recenze prováděné lidmi
titlesuffix: Azure Cognitive Services
description: Operace kontroly API zkontrolujte můžete využít k vytvoření image nebo text kontroly pro lidské moderování.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 7e4c750e7470c6a76e56bd67d6a134b1b61535eb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218386"
---
# <a name="create-reviews-from-the-api-console"></a>Vytvoření kontroly z konzoly pro rozhraní API

Použít API zkontrolujte [zkontrolujte operace](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) k vytvoření image nebo text revize pro lidské moderování. Pomocí nástroje pro recenze lidských moderátorů připravený obsah. Pomocí této operace na základě vašich po moderování obchodní logiky. Poté, co jste naskenovali svůj obsah pomocí některé z Content Moderator image nebo text API nebo jiné API služeb Cognitive Services, použijte ji. 

Poté, co lidské moderátory kontroly automaticky přiřazená značky a data předpovědí a odešle moderování konečné rozhodnutí, odešle rozhraní API pro kontrolu všechny informace do vašeho koncového bodu rozhraní API.

## <a name="use-the-api-console"></a>Pomocí rozhraní API konzoly
Mohli vyzkoušet rozhraní API pomocí konzole online, budete potřebovat několik hodnot, zadejte do konzoly:

- **teamName**: Název týmu, který jste vytvořili při nastavování účtu nástroj pro revize. 
- **ContentId**: Tento řetězec je předán rozhraní API a vrátí přes zpětného volání. ID je užitečné pro přidružení výsledky úlohy moderování interní identifikátory nebo metadata.
- **Metadata**: Vrátí do vašeho koncového bodu rozhraní API během zpětného volání vlastní páry klíč hodnota. Pokud je klíč krátký kód, který je definován v nástroj pro recenze, zobrazí se jako značku.
- **Ocp-Apim-Subscription-Key**: Na **nastavení** kartu. Další informace najdete v tématu [Přehled](overview.md).

Nejjednodušší způsob, jak přistupovat k testovací konzoly je z **pověření** okna.

1.  V **pověření** okně [reference k rozhraní API pro kontrolu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

  **Zkontrolovat – vytvoření** otevře se stránka.

2.  Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu.

  ![Zkontrolujte: vytvoření stránky oblast výběru](images/test-drive-region.png)

  **Zkontrolovat – vytvoření** otevře se konzola rozhraní API.
  
3.  Zadejte hodnoty pro požadované parametry dotazu, typu obsahu a klíč předplatného. V **text žádosti** zadejte obsah (například umístění bitové kopie), metadata a další informace související s obsahem.

  ![Projděte si – vytvoření parametry dotazu konzoly, záhlaví a pole textu požadavku](images/test-drive-review-1.PNG)
  
4.  Vyberte **Poslat**. ID revize se vytvoří. Zkopírujte toto ID se má použít v následujících krocích.

  ![Projděte si – vytvoření konzoly odpovědi obsahu pole se zobrazí ID revize](images/test-drive-review-2.PNG)
  
5.  Vyberte **získat**a pak otevřete rozhraní API tak, že vyberete tlačítko, které odpovídá vaší oblasti. Na stránce výsledný zadejte hodnoty pro **teamName**, **ReviewID**, a **klíč předplatného**. Vyberte **odeslat** tlačítko na stránce. 

  ![Projděte si – vytvoření konzoly získat výsledky](images/test-drive-review-3.PNG)
  
6.  Zobrazí se výsledky kontroly.

  ![Projděte si – vytvoření pole obsahu odpovědi konzoly](images/test-drive-review-4.PNG)
  
7.  Na řídicím panelu Content Moderator, vyberte **revize** > **Image**. Obrázek, který jste se zobrazí, připravena pro recenze prováděné lidmi.

  ![Zkontrolujte nástroj image soccer koule](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>Další postup

Použití rozhraní REST API ve vašem kódu nebo začínat [rychlý úvod k .NET revize](moderation-reviews-quickstart-dotnet.md) integrovat s vaší aplikací.
