---
title: Moderování obsahu s využitím recenze prováděné lidmi v Azure Content Moderator | Dokumentace Microsoftu
description: Informace o vytvoření recenze prováděné lidmi v konzole nástroje Content Moderator API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 214695ed3e23d1f501d6d4691104b3f8a91f6efc
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866504"
---
# <a name="create-reviews-from-the-api-console"></a>Vytvoření kontroly z konzoly pro rozhraní API

Použít API zkontrolujte [zkontrolujte operace](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) k vytvoření image nebo text revize pro lidské moderování. Pomocí nástroje pro recenze lidských moderátorů připravený obsah. Pomocí této operace na základě vašich po moderování obchodní logiky. Poté, co jste naskenovali svůj obsah pomocí některé z Content Moderator image nebo text API nebo jiné API služeb Cognitive Services, použijte ji. 

Poté, co lidské moderátory kontroly automaticky přiřazená značky a data předpovědí a odešle moderování konečné rozhodnutí, odešle rozhraní API pro kontrolu všechny informace do vašeho koncového bodu rozhraní API.

## <a name="use-the-api-console"></a>Pomocí rozhraní API konzoly
Mohli vyzkoušet rozhraní API pomocí konzole online, budete potřebovat několik hodnot, zadejte do konzoly:

- **teamName**: název týmu, který jste vytvořili při nastavování účtu nástroj pro revize. 
- **ContentId**: Tento řetězec je předán rozhraní API a vrátí přes zpětného volání. ID je užitečné pro přidružení výsledky úlohy moderování interní identifikátory nebo metadata.
- **Metadata**: vlastní páry klíč hodnota vrácená do vašeho koncového bodu rozhraní API během zpětného volání. Pokud je klíč krátký kód, který je definován v nástroj pro recenze, zobrazí se jako značku.
- **OCP-Apim-Subscription-Key**: na **nastavení** kartu. Další informace najdete v tématu [přehled](overview.md).

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
