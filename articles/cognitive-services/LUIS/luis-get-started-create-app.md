---
title: Vytvoření první aplikace LUIS (Language Understanding) během 10 minut – Cognitive Services LUIS | Microsoft Docs
description: V tomto rychlém startu vytvoříte aplikaci LUIS, která používá předem připravenou doménu `HomeAutomation` pro zapínání a vypínání světel a zařízení. Tato předem připravená doména poskytuje záměry, entity a příklady promluv. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/22/2018
ms.author: diberry
ms.openlocfilehash: 457f23936dec0cf85e9aebbf3e54bba37c2f3ca3
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "43769199"
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

V levém navigačním podokně vyberte **Intents** (Záměry) a podívejte se na záměry domény Home Automation. 

[![](media/luis-quickstart-new-app/home-automation-intents.png "Snímek obrazovky se seznamem záměrů a zvýrazněnými názvy záměrů v tabulce")](media/luis-quickstart-new-app/home-automation-intents.png)

Každý záměr má ukázkové promluvy.

> [!NOTE]
> **None** (Žádný) je záměr, který poskytují všechny aplikace LUIS. Používá se ke zpracování promluv, které neodpovídají funkci poskytované vaší aplikací. 

Vyberte záměr **HomeAutomation.TurnOff**. Vidíte, že záměr obsahuje seznam promluv, které jsou označené entitami.

[![](media/luis-quickstart-new-app/home-automation-turnon.png "Snímek obrazovky záměru HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-your-app"></a>Trénování aplikace

V horní navigaci vyberte **Train** (Trénovat).

[![](media/luis-quickstart-new-app/trained.png "Snímek obrazovky záměru HomeAutomation.TurnOff se zeleným oznámením o úspěchu")](media/luis-quickstart-new-app/trained.png)

## <a name="test-your-app"></a>Testování aplikace
Jakmile aplikaci vytrénujete, můžete ji otestovat. V horní navigaci vyberte **Test** (Testovat). Do podokna Interactive Testing (Interaktivní testování) zadejte testovací promluvu, například „Turn off the lights“ (Vypnout světla), a stiskněte Enter. 

```
Turn off the lights
```

Zkontrolujte, že záměr s nejvyšším bodováním odpovídá záměru, který jste pro jednotlivé testovací promluvy očekávali.

V tomto příkladu je „Turn off the lights“ (Vypnout světla) správně identifikovaný jako záměr s nejvyšším bodováním pro „HomeAutomation.TurnOff“.

[![](media/luis-quickstart-new-app/test.png "Snímek obrazovky testovacího panelu se zvýrazněnou promluvou")](media/luis-quickstart-new-app/test.png)


Znovu vyberte **Test** (Testovat) a sbalte podokno testování. 

## <a name="publish-your-app"></a>Publikování aplikace
V horní navigaci vyberte **Publish** (Publikovat). 

[![](media/luis-quickstart-new-app/publish.png "Snímek obrazovky aplikace se zvýrazněným tlačítkem Publish (Publikovat)")](media/luis-quickstart-new-app/publish.png)

Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat).

Zelený oznamovací pruh v horní části oznamuje, že se aplikace úspěšně publikovala.

[![](media/luis-quickstart-new-app/published.png "Snímek obrazovky aplikace s oznámením o úspěšném publikování")](media/luis-quickstart-new-app/published.png)

Jakmile aplikaci úspěšně publikujete, můžete použít adresu URL koncového bodu zobrazenou na stránce **Publish app** (Publikovat aplikaci).

[![](media/luis-quickstart-new-app/endpoint.png "Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněnou adresou URL koncového bodu")](media/luis-quickstart-new-app/endpoint.png)

## <a name="use-your-app"></a>Použití aplikace
Publikovaný koncový bod můžete otestovat v prohlížeči pomocí vygenerované adresy URL. Tuto adresu URL otevřete v prohlížeči a parametr URL „&q“ nastavte na testovací dotaz. Na konec adresy URL například přidejte `turn off the living room light` a potom stiskněte klávesu Enter. Prohlížeč zobrazí odpověď JSON koncového bodu HTTP.


[![](media/luis-quickstart-new-app/turn-off-living-room.png "Snímek obrazovky prohlížeče s výsledkem JSON s rozpoznaným záměrem TurnOff (Vypnout)")](media/luis-quickstart-new-app/turn-off-living-room.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Vyberte tlačítko se třemi tečkami (***...***) vpravo od názvu aplikace v seznamu aplikací a potom vyberte **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky

Koncový bod můžete volat prostřednictvím kódu:

> [!div class="nextstepaction"]
> [Volání koncového bodu služby LUIS pomocí kódu](luis-get-started-cs-get-intent.md)
