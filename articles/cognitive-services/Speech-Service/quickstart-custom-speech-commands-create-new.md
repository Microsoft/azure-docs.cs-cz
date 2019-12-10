---
title: 'Rychlý Start: Vytvoření vlastního příkazu (Preview) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte a otestujete hostovanou aplikaci s vlastními příkazy.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 081327e7c70128dad764d66942f1703a889f77fd
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951204"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Rychlý Start: Vytvoření vlastního příkazu (Preview)

V tomto článku se dozvíte, jak vytvořit a otestovat hostované vlastní aplikace s příkazy.
Aplikace rozpozná utterance jako "zapnout televizní program" a odpoví jednoduchou zprávou "OK, zapnutí televizního vysílání".

## <a name="prerequisites"></a>Předpoklady

- Předplatné pro rozpoznávání řeči. [Vyzkoušejte službu Speech Service zdarma](~/articles/cognitive-services/speech-service/get-started.md).

  > [!NOTE]
  > Během období Preview se pro klíče předplatného podporují jenom oblasti westus2.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Přejít do sady Speech Studio pro vlastní příkazy

1. Otevřete webový prohlížeč a přejděte do sady [Speech Studio](https://speech.microsoft.com/)
1. Zadejte přihlašovací údaje pro přihlášení k portálu.

   - Výchozím zobrazením je seznam předplatných pro rozpoznávání řeči.
     > [!NOTE]
     > Pokud nevidíte stránku vybrat odběr, můžete na ni přejít výběrem možnosti "prostředky řeči" v nabídce nastavení na horním panelu.

1. Vyberte své předplatné pro rozpoznávání řeči a pak vyberte **Přejít k studiu** .
1. Vybrat **vlastní příkazy (Preview)**

Výchozím zobrazením je seznam aplikací s vlastním příkazy, které jste vytvořili.

## <a name="create-a-custom-commands-project"></a>Vytvoření projektu s vlastními příkazy

1. Vyberte **Nový projekt** a vytvořte nový projekt.

   > [!div class="mx-imgBorder"]
   > ![vytvořit projekt](media/custom-speech-commands/create-new-project.png)

1. Zadejte název projektu a jazyk.
1. Vyberte prostředek pro vytváření obsahu. Pokud neexistují žádné platné prostředky pro vytváření obsahu, vytvořte ho výběrem možnosti **vytvořit nový prostředek**.

   > [!div class="mx-imgBorder"]
   > ![Vytvoření prostředku](media/custom-speech-commands/create-new-resource.png)

   1. Zadejte název prostředku, skupinu, umístění a cenovou úroveň.

         > [!NOTE]
         > Skupiny prostředků můžete vytvořit tak, že do pole Skupina prostředků zadáte požadovaný název skupiny prostředků. Skupina prostředků se vytvoří, když se vybere možnost **vytvořit** .

1. Kliknutím na **vytvořit** vytvořte projekt.
1. Po vytvoření vyberte svůj projekt.

Zobrazení by teď mělo být přehled vaší aplikace pro vlastní příkazy.

## <a name="create-a-new-command"></a>Vytvoření nového příkazu

Nyní můžete vytvořit příkaz. Pojďme použít příklad, který bude přebírat jeden utterance, `turn on the tv`a reagovat `Ok, turning on the TV`zprávy.

1. Vytvořte nový příkaz tak, že vyberete ikonu `+` vedle příkazů a přiřadíte jí název `TurnOn`
1. Vyberte **Uložit**.

> [!div class="mx-imgBorder"]
> ![vytvoření příkazu](media/custom-speech-commands/create-add-command.png)

Příkaz je množinou:

| Skupina            | Popis                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Ukázkové věty | Příklad projevy, jak může uživatel aktivovat tento příkaz                                                                 |
| Parametry       | Informace potřebné k dokončení příkazu                                                                                |
| Pravidla dokončení | Akce, které mají být provedeny pro splnění příkazu. Například pokud chcete reagovat na uživatele nebo komunikovat s jinou webovou službou |
| Rozšířená pravidla   | Další pravidla pro zpracování složitějších nebo složitých situací                                                              |

### <a name="add-a-sample-sentence"></a>Přidat ukázkovou větu

Pojďme začít s ukázkovými větami a zadat příklad toho, co může uživatel vyslovit:

```
turn on the tv
```

Teď nemáme žádné parametry, abychom mohli přejít na pravidla dokončení.

### <a name="add-a-completion-rule"></a>Přidat pravidlo dokončení

Nyní přidejte pravidlo dokončení pro reakci na uživatele s oznámením, že je provedena akce.

1. Vytvořte nové pravidlo dokončení tak, že vyberete ikonu `+` vedle možnosti pravidla dokončení.
1. Zadejte název pravidla.
1. Přidání akce
   1. Vytvořte novou akci reakce na řeč tak, že vyberete ikonu `+` vedle možnosti akce a vyberete `SpeechResponse`
   1. Zadejte odpověď.

   > [!NOTE]
   > Běžný text musí začínat pomlčkou. Další podrobnosti najdete [tady](https://aka.ms/sc-lg-format) .

   > [!div class="mx-imgBorder"]
   > ![vytvořit](media/custom-speech-commands/create-speech-response-action.png) reakci na řeč

1. Kliknutím na **Uložit** pravidlo uložíte.

> [!div class="mx-imgBorder"]
> ![vytvoření pravidla dokončování](media/custom-speech-commands/create-basic-completion-response-rule.png)


| Nastavení    | Navrhovaná hodnota                        | Popis                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| Název pravidla  | "ConfirmationResponse"                 | Název popisující účel pravidla          |
| Podmínky | Žádné                                   | Podmínky, které určují, kdy se pravidlo dá spustit    |
| Akce    | SpeechResponse "-OK, zapnutí televizního vysílání" | Akce, která se má provést, když je podmínka pravidla pravdivá |

## <a name="try-it-out"></a>Vyzkoušet

Otestujte chování pomocí panelu chat testu.

> [!div class="mx-imgBorder"]
> ![test pomocí webového chatu](media/custom-speech-commands/create-basic-test-chat.png)

- Zadáte: "zapnout televizor"
- Očekávaná odpověď: "OK, zapnutí televizního vysílání"

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rychlý Start: Vytvoření vlastního příkazu s parametry (Preview)](./quickstart-custom-speech-commands-create-parameters.md)
