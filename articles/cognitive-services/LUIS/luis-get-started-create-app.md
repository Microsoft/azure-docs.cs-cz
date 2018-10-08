---
title: Deset minut do vaší první aplikace LUIS
titleSuffix: Azure Cognitive Services
description: Vytvoříte aplikaci LUIS, která používá předem připravenou doménu `HomeAutomation` pro zapínání a vypínání světel a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 12a660b49d1a81865c34ceda38f041de9be31eb1
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037469"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rychlý start: Použití předem připravené aplikace domácí automatizace

V tomto rychlém startu vytvoříte aplikaci LUIS, která používá předem připravenou doménu `HomeAutomation` pro zapínání a vypínání světel a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto článku potřebujete bezplatný účet LUIS, vytvořený na portálu LUIS na webu [http://www.luis.ai](http://www.luis.ai). 

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
Aplikace můžete vytvářet a spravovat na stránce **My Apps** (Moje aplikace). 

1. Přihlaste se k portálu LUIS.

2. Vyberte **Create new app** (Vytvořit novou aplikaci).

    [![](media/luis-quickstart-new-app/app-list.png "Snímek obrazovky se seznamem aplikací")](media/luis-quickstart-new-app/app-list.png)

3. V dialogovém okně pojmenujte svou aplikaci „Home Automation“.

    [![](media/luis-quickstart-new-app/create-new-app-dialog.png "Snímek obrazovky s automaticky otevíraným dialogovým oknem Create new app (Vytvořit novou aplikaci)")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Zvolte jazykovou verzi aplikace. Pro tuto aplikaci domácí automatizace vyberte angličtinu. Potom vyberte **Done** (Hotovo). LUIS vytvoří aplikaci Home Automation. 

    >[!NOTE]
    >Jakmile se aplikace vytvoří, nemůžete změnit její jazykovou verzi. 

## <a name="add-prebuilt-domain"></a>Přidání předem připravené domény

V levém navigačním podokně vyberte **Prebuilt domains** (Předem připravené domény). Potom vyhledejte „Home“. Vyberte **Add domain** (Přidat doménu).

[![](media/luis-quickstart-new-app/home-automation.png "Snímek obrazovky domény Home Automation (Domácí automatizace) v nabídce předem připravené domény")](media/luis-quickstart-new-app/home-automation.png)

Po úspěšném přidání domény pak pole předem připravené domény zobrazuje tlačítko **Remove domain** (Odebrat doménu).

[![](media/luis-quickstart-new-app/remove-domain.png "Snímek obrazovky domény Home Automation (Domácí automatizace) s tlačítkem pro odebrání")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Záměry a entity

V levém navigačním podokně vyberte **Intents** (Záměry) a podívejte se na záměry domény Home Automation. Každý záměr má ukázkové promluvy.

> [!NOTE]
> **None** (Žádný) je záměr, který poskytují všechny aplikace LUIS. Používá se ke zpracování promluv, které neodpovídají funkci poskytované vaší aplikací. 

Vyberte záměr **HomeAutomation.TurnOff**. Vidíte, že záměr obsahuje seznam promluv, které jsou označené entitami.

[![](media/luis-quickstart-new-app/home-automation-turnon.png "Snímek obrazovky záměru HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Testování aplikace
Jakmile aplikaci vytrénujete, můžete ji otestovat. V horní navigaci vyberte **Test** (Testovat). Do podokna Interactive Testing (Interaktivní testování) zadejte testovací promluvu, například „Turn off the lights“ (Vypnout světla), a stiskněte Enter. 

```
Turn off the lights
```

Zkontrolujte, že záměr s nejvyšším bodováním odpovídá záměru, který jste pro jednotlivé testovací promluvy očekávali.

V tomto příkladu je „Turn off the lights“ (Vypnout světla) správně identifikovaný jako záměr s nejvyšším bodováním pro „HomeAutomation.TurnOff“.

[![](media/luis-quickstart-new-app/test.png "Snímek obrazovky testovacího panelu se zvýrazněnou promluvou")](media/luis-quickstart-new-app/test.png)


Znovu vyberte **Test** (Testovat) a sbalte podokno testování. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Odeslání dotazu na koncový bod s jinou promluvou

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Na konec adresy URL zadejte `turn off the living room light` a stiskněte Enter. Prohlížeč zobrazí odpověď JSON koncového bodu HTTP.

    [![](media/luis-quickstart-new-app/turn-off-living-room.png "Snímek obrazovky prohlížeče s výsledkem JSON s rozpoznaným záměrem TurnOff (Vypnout)")](media/luis-quickstart-new-app/turn-off-living-room.png)
    
## <a name="clean-up-resources"></a>Vyčištění prostředků

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

Koncový bod můžete volat prostřednictvím kódu:

> [!div class="nextstepaction"]
> [Volání koncového bodu služby LUIS pomocí kódu](luis-get-started-cs-get-intent.md)
