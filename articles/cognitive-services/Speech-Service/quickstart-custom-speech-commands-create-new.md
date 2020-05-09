---
title: 'Rychlý Start: Vytvoření vlastního příkazu (Preview) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte a otestujete hostovanou aplikaci s vlastními příkazy.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: f31d7279b73bab7aefda4c4b6570500d05cb89d7
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872520"
---
# <a name="quickstart-create-a-custom-commands-app-preview"></a>Rychlý Start: Vytvoření vlastní aplikace s příkazy (Preview)

V tomto rychlém startu se dozvíte, jak vytvořit a otestovat vlastní aplikaci příkazů.
Vytvořená aplikace bude zpracovávat projevy, jako je "zapnutí televizního vysílání" a odpověď pomocí jednoduché zprávy "OK, zapnutí televizního vysílání".

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Vytvoření prostředku Azure Speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > V současné době vlastní příkazy podporují pouze odběry řeči v oblastech westus, westus2 a neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Přejít do sady Speech Studio pro vlastní příkazy

1. Otevřete webový prohlížeč a přejděte do sady [Speech Studio](https://speech.microsoft.com/)
1. Zadejte přihlašovací údaje pro přihlášení k portálu.

   - Výchozím zobrazením je seznam předplatných pro rozpoznávání řeči.
     > [!NOTE]
     > Pokud nevidíte stránku vybrat odběr, můžete na ni přejít výběrem možnosti "prostředky řeči" v nabídce nastavení na horním panelu.

1. Vyberte své předplatné pro rozpoznávání řeči a pak vyberte **Přejít k studiu**.
1. Vyberte **vlastní příkazy**.

     - Výchozím zobrazením je seznam aplikací pro vlastní příkazy, které máte v rámci vybraného předplatného.

## <a name="create-a-custom-commands-project"></a>Vytvoření projektu s vlastními příkazy

1. Vyberte **Nový projekt** a vytvořte nový projekt.

   > [!div class="mx-imgBorder"]
   > ![Vytvoření projektu](media/custom-speech-commands/create-new-project.png)

1. Zadejte název projektu.
1. Z rozevíracího seznamu vyberte jazyk.
1. V rozevíracím seznamu vyberte prostředek pro vytváření obsahu. Pokud neexistují žádné platné prostředky pro vytváření obsahu, vytvořte ho kliknutím na **vytvořit nový prostředek pro vytváření obsahu Luis**.

   > [!div class="mx-imgBorder"]
   > ![Vytvoření prostředku](media/custom-speech-commands/create-new-resource.png)

   - Zadejte název prostředku, skupinu prostředků.
   - V rozevíracím seznamu vyberte hodnotu pro umístění a cenovou úroveň.

      > [!NOTE]
      > Skupiny prostředků můžete vytvořit tak, že do pole Skupina prostředků zadáte požadovaný název skupiny prostředků. Skupina prostředků se vytvoří, když se vybere možnost **vytvořit** .

1. Potom vyberte **vytvořit** a vytvořte tak projekt.
1. Po vytvoření vyberte svůj projekt.

    - Vaše zobrazení by teď mělo být přehled vaší nově vytvořené aplikace Custom Commands.

## <a name="update-luis-resources-optional"></a>Aktualizace prostředků LUIS (volitelné)

Můžete aktualizovat prostředek pro vytváření obsahu, který byl nastaven v okně Nový projekt, a nastavit předpověď prostředku. Prostředek předpovědi se používá pro rozpoznávání po publikování vlastní aplikace příkazů. Pro fáze vývoje a testování nepotřebujete žádný předpovědní prostředek.

1. V levém podokně vyberte **Nastavení** a potom přejděte do části **prostředky Luis** v prostředním podokně.
1. Vyberte prostředek předpovědi, nebo ho vytvořte výběrem možnosti **vytvořit nový prostředek**.
1. Vyberte **Uložit**.
    
    > [!div class="mx-imgBorder"]
    > ![Nastavení prostředků LUIS](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Vzhledem k tomu, že prostředek pro vytváření podporuje jenom 1 000 požadavků koncového bodu předpovědi za měsíc, budete muset před publikováním aplikace Custom Commands mandatorily nastavit prostředek předpovědi LUIS.


## <a name="create-a-new-command"></a>Vytvoření nového příkazu

Pojďme vytvořit jednoduchý příkaz, který bude mít jednu utterance, `turn on the tv`a odpovědět se zprávou. `Ok, turning on the tv`

1. Vytvořte nový příkaz výběrem `+ New command` ikony přítomné v levém horním podokně. Zobrazí se nové automaticky otevírané okno s názvem **Nový příkaz**.
1. Zadejte hodnotu pole **název** jako `TurnOn`.
1. Vyberte **Vytvořit**.

Prostřední podokno zařadí různé vlastnosti příkazu:


| Konfigurace            | Popis                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Příklady vět | Příklad projevy, jak může uživatel aktivovat tento příkaz                                                                 |
| Parametry       | Informace potřebné k dokončení příkazu                                                                                |
| Pravidla dokončení | Akce, které mají být provedeny pro splnění příkazu. Například, pokud chcete reagovat na uživatele nebo komunikovat s jinou webovou službou. |
| Pravidla interakce   | Další pravidla pro zpracování složitějších nebo složitých situací                                                              |


> [!div class="mx-imgBorder"]
> ![Vytvoření příkazu](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Přidat ukázkové věty

Pojďme začít s příkladem části věty a zadat příklad, co může uživatel vyslovit.

1. V prostředním podokně a ve většině případů v pravém podokně vyberte **ukázkové věty** . Přidejte příklady:

    ```
    turn on the tv
    ```

1. V `Save` horní části tohoto podokna vyberte ikonu k dispozici.

Teď nemáme žádné parametry, abychom mohli přejít k části **pravidla dokončení** .

### <a name="add-a-completion-rule"></a>Přidat pravidlo dokončení

Teď přidejte pravidlo dokončení s následující konfigurací. Toto pravidlo označuje uživatele, že je prováděna akce plnění.


| Nastavení    | Navrhovaná hodnota                          | Popis                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Název pravidla  | ConfirmationResponse                  | Název popisující účel pravidla          |
| Podmínky | Žádné                                     | Podmínky, které určují, kdy se pravidlo dá spustit    |
| Akce    | SpeechResponse "-OK, zapnutí televizního vysílání" | Akce, která se má provést, když je podmínka pravidla pravdivá |

1. Kliknutím na `+Add` ikonu v horní části prostředního podokna vytvořte nové pravidlo dokončení.
1. Zadejte hodnotu v části **Name (název** ).
1. Přidání akce
   1. Kliknutím na **+ přidat akci** v oddílu **Actions (akce** ) vytvořte novou akci.
   1. V automaticky otevíraném okně **Nová akce** vyberte `Send speech response` z rozevíracího seznamu možnosti pro **typ**.
   1. Vyberte `Simple editor` pro pole **Response** .
       - Do pole **první variace** zadejte hodnotu pro odpověď jako`Ok, turning on the tv`

   > [!div class="mx-imgBorder"]
   > ![Vytvoření odpovědi na řeč](media/custom-speech-commands/create-speech-response-action.png)

1. Kliknutím na **Save (Uložit** ) pravidlo uložte.
1. Zpátky v části **pravidla dokončování** vyberte **Uložit** a uložte všechny změny. 

> [!div class="mx-imgBorder"]
> ![Vytvoření pravidla dokončení](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Vyzkoušejte si to.

Testování chování pomocí panelu chat testu
1. Vyberte `Train` ikonu, která se nachází v pravém podokně.
1. Po dokončení školení vyberte `Test`. Zobrazí se nový test, který se zobrazí **v okně aplikace** .
    - Zadáte: zapnout televizor
    - Očekávaná odpověď: OK, zapnutí televizního vysílání


> [!div class="mx-imgBorder"]
> ![Testování pomocí webového chatu](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rychlý Start: Vytvoření vlastního příkazu s parametry (Preview)](./quickstart-custom-speech-commands-create-parameters.md)
