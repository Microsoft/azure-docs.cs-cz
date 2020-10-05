---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 874d76bebdfd3bd0daba1f83cb1f06c093f192ec
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89411795"
---
## <a name="get-the-training-and-prediction-keys"></a>Získání tréninkového klíče a klíče předpovědi

Projekt potřebuje k interakci se službou platnou sadu klíčů předplatného. Tyto položky najdete na  [webu Custom Vision](https://customvision.ai). Přihlaste se pomocí účtu přidruženého k účtu Azure, který jste použili k vytvoření prostředků Custom Vision. Na domovské stránce (Stránka s možností pro přidání nového projektu) vyberte __ikonu ozubeného kolečka__ vpravo nahoře. Vyhledejte prostředky pro školení a předpovědi v seznamu a rozbalte je. Tady najdete klíč školení, klíč předpovědi a hodnoty ID prostředků předpovědi. Uložte tyto hodnoty do dočasného umístění.

> [!NOTE]
> Pokud pro přístup k Custom Vision používáte Cognitive Services klíč All-in-One, zobrazí se na obrazovce nastavení jenom jeden klíč. V takovém případě použijete stejný klíč pro operace školení a předpovědi.

![Obrázek uživatelského rozhraní klíčů](../media/csharp-tutorial/training-prediction-keys.png)

Nebo můžete získat tyto klíče a ID z [Azure Portal](https://www.portal.azure.com) zobrazením Custom Vision školicích a předpovědních prostředků a přechodem na kartu __klíče__ . Najdete tu svůj školicí klíč a klíč předpovědi. Přejděte na kartu __vlastnosti__ prostředku předpovědi, abyste získali ID prostředku předpovědi.

