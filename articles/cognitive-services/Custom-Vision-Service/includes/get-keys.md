---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 874d76bebdfd3bd0daba1f83cb1f06c093f192ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021252"
---
## <a name="get-the-training-and-prediction-keys"></a>Získání tréninkového klíče a klíče předpovědi

Projekt potřebuje k interakci se službou platnou sadu klíčů předplatného. Tyto položky najdete na  [webu Custom Vision](https://customvision.ai). Přihlaste se pomocí účtu přidruženého k účtu Azure, který jste použili k vytvoření prostředků Custom Vision. Na domovské stránce (Stránka s možností pro přidání nového projektu) vyberte __ikonu ozubeného kolečka__ vpravo nahoře. Vyhledejte prostředky pro školení a předpovědi v seznamu a rozbalte je. Tady najdete klíč školení, klíč předpovědi a hodnoty ID prostředků předpovědi. Uložte tyto hodnoty do dočasného umístění.

> [!NOTE]
> Pokud pro přístup k Custom Vision používáte Cognitive Services klíč All-in-One, zobrazí se na obrazovce nastavení jenom jeden klíč. V takovém případě použijete stejný klíč pro operace školení a předpovědi.

![Obrázek uživatelského rozhraní klíčů](../media/csharp-tutorial/training-prediction-keys.png)

Nebo můžete získat tyto klíče a ID z [Azure Portal](https://www.portal.azure.com) zobrazením Custom Vision školicích a předpovědních prostředků a přechodem na kartu __klíče__ . Najdete tu svůj školicí klíč a klíč předpovědi. Přejděte na kartu __vlastnosti__ prostředku předpovědi, abyste získali ID prostředku předpovědi.

