---
title: 'Rychlý Start: Vytvoření vlastní příkazy Preview App-Speech Service'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte a otestujete hostované vlastní příkazy aplikace ve verzi Preview. Aplikace zpracuje projevy.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: nitinme
ms.openlocfilehash: 5b54923bb667053ffc886b335ab9d1b0cf4cb754
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509351"
---
# <a name="quickstart-create-a-custom-commands-preview-app"></a>Rychlý Start: Vytvoření vlastní příkazy Preview aplikace

V tomto rychlém startu se dozvíte, jak vytvořit a otestovat vlastní aplikaci příkazů.
Aplikace bude zpracovávat projevy jako "zapnout televizor" a odpovědět na jednoduchou zprávu, jako je "OK, zapnutí televizního vysílání".

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Vytvoření prostředku Azure Speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > V současné době vlastní příkazy podporují pouze odběry řeči v oblastech westus, westus2 a neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Přejít do sady Speech Studio pro vlastní příkazy

1. Ve webovém prohlížeči přejdete na [Speech Studio](https://speech.microsoft.com/).
1. Zadejte přihlašovací údaje pro přihlášení k portálu.

   Výchozím zobrazením je seznam předplatných pro rozpoznávání řeči.
    > [!NOTE]
    > Pokud nevidíte stránku vybrat odběr, můžete ji získat výběrem možnosti **prostředky řeči** v nabídce nastavení v horní části obrazovky.

1. Vyberte své předplatné pro rozpoznávání řeči a pak vyberte **Přejít k studiu**.
1. Vyberte **vlastní příkazy**.

     Výchozím zobrazením je seznam aplikací Custom Commands, které máte ve vybraném předplatném.

## <a name="create-a-custom-commands-project"></a>Vytvoření projektu s vlastními příkazy

1. Vyberte **Nový projekt** a vytvořte projekt.

   > [!div class="mx-imgBorder"]
   > ![Vytvoření projektu](media/custom-speech-commands/create-new-project.png)

1. Do pole **název** zadejte název projektu.
1. V seznamu **jazyk** vyberte jazyk.
1. V seznamu **prostředek pro vytváření Luis** vyberte prostředek pro vytváření obsahu. Pokud neexistují žádné platné prostředky pro vytváření obsahu, vytvořte si ho tak, že vyberete **vytvořit nový prostředek pro vytváření obsahu Luis**.

   > [!div class="mx-imgBorder"]
   > ![Vytvoření prostředku](media/custom-speech-commands/create-new-resource.png)

   1. Do pole **název prostředku** zadejte název prostředku.
   1. V seznamu **Skupina prostředků** vyberte skupinu prostředků.
   1. V seznamu **umístění** vyberte umístění.
   1. V seznamu **cenová úroveň** vyberte úroveň.

      > [!NOTE]
      > Skupinu prostředků můžete vytvořit tak, že do pole **Skupina prostředků** zadáte název skupiny prostředků. Skupina prostředků se vytvoří, když vyberete **vytvořit**.

1. Vyberte **Vytvořit**.
1. Po vytvoření projektu ho vyberte.

    Nyní byste měli vidět přehled vaší nové aplikace Custom Commands.

## <a name="update-luis-resources-optional"></a>Aktualizace prostředků LUIS (volitelné)

Můžete aktualizovat prostředek pro vytváření obsahu, který jste vybrali v okně **Nový projekt** , a nastavit předpověď prostředku. Pokud je vaše aplikace pro vlastní příkazy publikována, je prostředek předpovědi použit pro rozpoznávání. Během fází vývoje a testování nepotřebujete předpověď prostředků.

1. V levém podokně vyberte **Nastavení** a v prostředním podokně vyberte **Luis prostředky** .
1. Vyberte prostředek předpovědi, nebo ho vytvořte výběrem možnosti **vytvořit nový prostředek**.
1. Vyberte **Uložit**.
    
    > [!div class="mx-imgBorder"]
    > ![Nastavení prostředků LUIS](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Vzhledem k tomu, že prostředek pro vytváření podporuje jenom 1 000 požadavků koncového bodu předpovědi za měsíc, budete muset před publikováním aplikace Custom Commands nastavit prostředek předpovědi LUIS.


## <a name="create-a-command"></a>Vytvoření příkazu

Pojďme vytvořit jednoduchý příkaz, který bude mít jednu utterance, `turn on the tv` a odpovědět se zprávou `Ok, turning on the tv` .

1. Vytvořte příkaz výběrem **příkazu nový** v horní části levého podokna. Otevře se **nové okno příkazu** .
1. Do pole **název** zadejte **TurnOn**.
1. Vyberte **Vytvořit**.

Prostřední podokno obsahuje seznam vlastností příkazu:


| Konfigurace            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Příklady vět** | Příklady projevy, jak může uživatel spustit příkaz.                                                                 |
| **Parametry**       | Informace potřebné k dokončení příkazu                                                                                |
| **Pravidla dokončení** | Akce, které budou provedeny pro splnění příkazu. Například můžete reagovat na uživatele nebo komunikovat s jinou webovou službou. |
| **Pravidla interakce**   | Další pravidla, která budou zpracovávat konkrétnější nebo složitou situaci.                                                              |


> [!div class="mx-imgBorder"]
> ![Vytvoření příkazu](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Přidat ukázkové věty

Pojďme začít s **příkladem části věty** . Nabídneme vám příklad, co může uživatel vyslovit.

1. V prostředním podokně vyberte **příklad vět** . 
1. V pravém podokně přidejte příklady:

    ```
    turn on the tv
    ```

1. V horní části podokna vyberte **Uložit** .

Teď ale nemusíme mít parametry, takže se můžeme přesunout do oddílu **pravidla dokončení** .

### <a name="add-a-completion-rule"></a>Přidat pravidlo dokončení

Teď přidejte pravidlo dokončení, které má následující konfiguraci. Toto pravidlo přikáže uživateli, že je prováděna akce plnění.


| Nastavení    | Navrhovaná hodnota                          | Popis                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| **Název**  | **ConfirmationResponse**                  | Název popisující účel pravidla.          |
| **Podmínky** | Žádné                                     | Podmínky, které určují, kdy může být pravidlo spuštěno.    |
| **Akce**    | **Odeslat odezvu na řeč – > ok a zapnout televizor** | Akce, která má být provedena v případě, kdy je podmínka pravidla pravdivá. |

1. Kliknutím na **Přidat** v horní části prostředního podokna vytvořte nové pravidlo dokončení.
1. Zadejte název do pole **Název**.
1. Přidat akci.
   1. Vytvořte akci výběrem možnosti **přidat akci** v části **Akce** .
   1. V okně **Upravit akci** vyberte v seznamu **typ** možnost **Odeslat odpověď řeči**.
   1. V části **odpověď**vyberte **jednoduchý editor**. V poli **první variace** zadejte **OK a zapněte televizi**.

   > [!div class="mx-imgBorder"]
   > ![Vytvořit odpověď](media/custom-speech-commands/create-speech-response-action.png)

1. Kliknutím na **Uložit** uložte pravidlo.
1. Zpátky v části **pravidla dokončování** vyberte **Uložit** a uložte všechny změny. 

> [!div class="mx-imgBorder"]
> ![Vytvoření pravidla dokončení](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Vyzkoušejte si to.

Otestujte chování pomocí panelu chat testu.
1. V horní části pravého podokna vyberte **vlak** .
1. Po dokončení školení vyberte **test**. Zobrazí se nové okno **test aplikace** .
    - Zadejte **zapnout televizi** .
    - Očekávaná odpověď: **OK, zapnutí televizního vysílání**


> [!div class="mx-imgBorder"]
> ![Testování chování](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rychlý Start: Vytvoření vlastní příkazy Preview aplikace s parametry](./quickstart-custom-speech-commands-create-parameters.md)
