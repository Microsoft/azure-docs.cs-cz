---
title: 'Rychlý start: vytvoření aplikace'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Vytvoříte aplikaci LUIS, která používá předem připravenou doménu `HomeAutomation` pro zapínání a vypínání světel a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 86a09d3d11f9b713bb6228498c072d80e7c45d6b
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261073"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rychlý start: Použití předem připravené aplikace automatizace Domovská stránka

V tomto rychlém startu vytvoříte aplikaci LUIS, která používá předem připravenou doménu `HomeAutomation` pro zapínání a vypínání světel a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto článku potřebujete bezplatný účet LUIS, vytvořený na portálu LUIS na webu [https://www.luis.ai](https://www.luis.ai). 

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
Aplikace můžete vytvářet a spravovat na stránce **My Apps** (Moje aplikace). 

1. Přihlaste se k portálu LUIS.

2. Vyberte **Create new app** (Vytvořit novou aplikaci).

    [![Snímek obrazovky aplikace seznam](media/luis-quickstart-new-app/app-list.png "snímek obrazovky seznamu aplikací")](media/luis-quickstart-new-app/app-list.png)

3. V dialogovém okně pojmenujte svou aplikaci „Home Automation“.

    [![Snímek obrazovky vytváření nové aplikace automaticky otevíraná okna](media/luis-quickstart-new-app/create-new-app-dialog.png "snímek obrazovky vytváření nové aplikace automaticky otevíraná okna")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Zvolte jazykovou verzi aplikace. Pro tuto aplikaci domácí automatizace vyberte angličtinu. Potom vyberte **Done** (Hotovo). LUIS vytvoří aplikaci Home Automation. 

    >[!NOTE]
    >Jakmile se aplikace vytvoří, nemůžete změnit její jazykovou verzi. 

## <a name="add-prebuilt-domain"></a>Přidání předem připravené domény

V levém navigačním podokně vyberte **Prebuilt domains** (Předem připravené domény). Potom vyhledejte „Home“. Vyberte **Add domain** (Přidat doménu).

[![Snímek obrazovky z Domů automatizace domény vyznačeny v nabídce předem připravených domény](media/luis-quickstart-new-app/home-automation.png "domény – snímek obrazovky z Domů automatizace vyznačeny v nabídce předem připravených domény")](media/luis-quickstart-new-app/home-automation.png)

Po úspěšném přidání domény pak pole předem připravené domény zobrazuje tlačítko **Remove domain** (Odebrat doménu).

[![Snímek obrazovky z Domů automatizace domény pomocí tlačítka odebrat](media/luis-quickstart-new-app/remove-domain.png "domény – snímek obrazovky z Domů automatizace pomocí tlačítka odebrat")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Záměry a entity

V levém navigačním podokně vyberte **Intents** (Záměry) a podívejte se na záměry domény Home Automation. Každý záměr má ukázkové promluvy.

> [!NOTE]
> **None** (Žádný) je záměr, který poskytují všechny aplikace LUIS. Používá se ke zpracování promluv, které neodpovídají funkci poskytované vaší aplikací. 

Vyberte záměr **HomeAutomation.TurnOff**. Vidíte, že záměr obsahuje seznam promluv, které jsou označené entitami.

[![Snímek obrazovky HomeAutomation.TurnOff záměr](media/luis-quickstart-new-app/home-automation-turnon.png "snímek obrazovky HomeAutomation.TurnOff záměr")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Testování aplikace
Jakmile aplikaci vytrénujete, můžete ji otestovat. V horní navigaci vyberte **Test** (Testovat). Do podokna Interactive Testing (Interaktivní testování) zadejte testovací promluvu, například „Turn off the lights“ (Vypnout světla), a stiskněte Enter. 

```
Turn off the lights
```

Zkontrolujte, že záměr s nejvyšším bodováním odpovídá záměru, který jste pro jednotlivé testovací promluvy očekávali.

V tomto příkladu je „Turn off the lights“ (Vypnout světla) správně identifikovaný jako záměr s nejvyšším bodováním pro „HomeAutomation.TurnOff“.

[![Snímek obrazovky testovací panel utterance zvýrazněnou](media/luis-quickstart-new-app/test.png "panelu testů snímek obrazovky s utterance zvýrazněnou")](media/luis-quickstart-new-app/test.png)


Znovu vyberte **Test** (Testovat) a sbalte podokno testování. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Odeslání dotazu na koncový bod s jinou promluvou

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Na konec adresy URL zadejte `turn off the living room light` a stiskněte Enter. Prohlížeč zobrazí odpověď JSON koncového bodu HTTP.

    [![Snímek obrazovky prohlížeče s výsledkem JSON zjistí záměru vypnutí](media/luis-quickstart-new-app/turn-off-living-room.png "snímek obrazovky prohlížeče s výsledkem JSON zjistí záměru vypnutí")](media/luis-quickstart-new-app/turn-off-living-room.png)
    
## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

Koncový bod můžete volat prostřednictvím kódu:

> [!div class="nextstepaction"]
> [Volání koncového bodu služby LUIS pomocí kódu](luis-get-started-cs-get-intent.md)
