---
title: 'Úvodní příručka: Vytvoření vlastního příkazu (preview) – služba řeči'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte a otestujete aplikaci hostovaných vlastních příkazů.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76155583"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Úvodní příručka: Vytvoření vlastního příkazu (náhled)

V tomto článku se dozvíte, jak vytvořit a otestovat hostovizované vlastní příkazy aplikace.
Aplikace rozpozná utterance jako "zapnout televizi" a reagovat s jednoduchou zprávou "Ok, zapnutí tv".

## <a name="prerequisites"></a>Požadavky

- Předplatné řeči.

Pokud nemáte odběr řeči, můžete ho vytvořit tak, že přejdete do [aplikace Speech Studio](https://speech.microsoft.com/) a vyberete možnost Vytvořit prostředek pro rozpoznávání **řeči**.

  > [!div class="mx-imgBorder"]
  > [![Vytvoření projektu](media/custom-speech-commands/create-new-subscription.png)](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > Během náhledu je podporována pouze oblast westus2.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Přejděte do aplikace Speech Studio pro vlastní příkazy

1. Otevření webového prohlížeče a přechod do [aplikace Speech Studio](https://speech.microsoft.com/)
1. Zadejte přihlašovací údaje pro přihlášení k portálu.

   - Výchozí zobrazení je seznam odběrů řeči
     > [!NOTE]
     > Pokud nevidíte stránku vybraného předplatného, můžete se tam procházet tak, že z nabídky nastavení na horním panelu vyberete "Prostředky řeči".

1. Vyberte předplatné řeči a pak **vyberte Přejít do studia.**
1. Vybrat **vlastní příkazy (náhled)**

Výchozí zobrazení je seznam aplikací vlastních příkazů, které jste vytvořili.

## <a name="create-a-custom-commands-project"></a>Vytvoření projektu vlastních příkazů

1. Chcete-li vytvořit nový projekt, vyberte **možnost Nový projekt.**

   > [!div class="mx-imgBorder"]
   > ![Vytvoření projektu](media/custom-speech-commands/create-new-project.png)

1. Zadejte název projektu a jazyk.
1. Vyberte zdroj pro vytváření. Pokud neexistují žádné platné vývojové prostředky, vytvořte je výběrem **možnosti Vytvořit nový zdroj**.

   > [!div class="mx-imgBorder"]
   > ![Vytvoření prostředku](media/custom-speech-commands/create-new-resource.png)

   1. Zadejte název zdroje, skupinu, umístění a cenovou úroveň.

         > [!NOTE]
         > Skupiny prostředků můžete vytvořit zadáním požadovaného názvu skupiny prostředků do pole Skupina zdrojů. Skupina prostředků bude vytvořena při výběru **možnosti Vytvořit.**

1. Chcete-li vytvořit projekt, klepněte na tlačítko **Vytvořit.**
1. Po vytvoření vyberte projekt.

Vaše zobrazení by nyní mělo být přehledem aplikace Vlastní příkazy.

## <a name="update-luis-resources-optional"></a>Aktualizace prostředků služby LUIS (volitelné)

Můžete aktualizovat sadu zdrojových zdrojů v novém okně projektu a nastavit zdroj předpovědi, který slouží k rozpoznání vstupů za běhu.

> [!NOTE]
> Budete muset nastavit prostředek předpověď před vaše aplikace požaduje předpovědi nad rámec 1 000 požadavků poskytnutých zdroj pro vytváření.

> [!div class="mx-imgBorder"]
> ![Nastavení prostředků služby LUIS](media/custom-speech-commands/set-luis-resources.png)

1. Přejděte do podokna Prostředků LUIS výběrem **nastavení** v levém podokně a potom **na prostředky LUIS** z prostředního podokna.
1. Vyberte zdroj předpovědi nebo ho vytvořte výběrem **možnosti Vytvořit nový zdroj.**
1. Vyberte **Uložit**.

## <a name="create-a-new-command"></a>Vytvoření nového příkazu

Nyní můžete vytvořit příkaz. Použijeme příklad, který bude mít jeden `turn on the tv`utterance , a `Ok, turning on the TV`odpovědět se zprávou .

1. Vytvoření nového příkazu `+` výběrem ikony vedle příkazů a jeho pojmenování`TurnOn`
1. Vyberte **Uložit**.

> [!div class="mx-imgBorder"]
> ![Vytvoření příkazu](media/custom-speech-commands/create-add-command.png)

Příkaz je sada:

| Skupina            | Popis                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Ukázkové věty | Příklad projevy, které může uživatel spouštět, aby aktivoval tento příkaz                                                                 |
| Parametry       | Informace potřebné k dokončení příkazu                                                                                |
| Pravidla dokončení | Akce, které mají být přijata ke splnění příkazu. Chcete-li například reagovat na uživatele nebo komunikovat s jinou webovou službou |
| Pokročilá pravidla   | Další pravidla pro řešení konkrétnějších nebo složitějších situací                                                              |

### <a name="add-a-sample-sentence"></a>Přidání vzorové věty

Začněme ukázkovými větami a uvažujme příklad toho, co může uživatel říci:

```
turn on the tv
```

Prozatím nemáme žádné parametry, takže můžeme přejít k pravidlům dokončení.

### <a name="add-a-completion-rule"></a>Přidání pravidla dokončení

Nyní přidejte pravidlo dokončení reagovat na uživatele označující, že probíhá akce.

1. Vytvoření nového pravidla dokončení `+` výběrem ikony vedle pravidel dokončení
1. Zadejte název pravidla.
1. Přidání akce
   1. Vytvořte novou akci reakce na `+` řeč výběrem ikony vedle položky Akce a výběrem`SpeechResponse`
   1. Zadejte odpověď

   > [!NOTE]
   > Běžný text musí začínat pomlčkou. Pro více informací, jděte [zde](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![Vytvoření odpovědi na řeč](media/custom-speech-commands/create-speech-response-action.png)

1. Chcete-li pravidlo uložit, klepněte na **tlačítko Uložit.**

> [!div class="mx-imgBorder"]
> ![Vytvoření pravidla dokončení](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Nastavení    | Navrhovaná hodnota                          | Popis                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Název pravidla  | "Potvrzeníodpověď"                   | Název popisující účel pravidla          |
| Podmínky | Žádný                                     | Podmínky, které určují, kdy může být pravidlo spuštěno    |
| Akce    | SpeechResponse "- Ok, zapnutí televizoru" | Akce, která má být v případě, že je splněna podmínka pravidla |

## <a name="try-it-out"></a>Vyzkoušejte si to.

Otestujte chování pomocí panelu Testovací chat.

> [!div class="mx-imgBorder"]
> ![Test pomocí webového chatu](media/custom-speech-commands/create-basic-test-chat.png)

- Zadáte: "zapnout televizi"
- Očekávaná odpověď: "Ok, zapnutí televize"

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Úvodní příručka: Vytvoření vlastního příkazu s parametry (náhled)](./quickstart-custom-speech-commands-create-parameters.md)
