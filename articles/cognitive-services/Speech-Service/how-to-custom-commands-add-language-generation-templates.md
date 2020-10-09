---
title: 'Postupy: použití šablon generování jazyka pro odpovědi na řeč – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak používat šablony generování jazyka s vlastními příkazy. Šablony generování jazyka umožňují přizpůsobit odpovědi odeslané klientovi a zavádět odchylky v odpovědích.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 0cbc57922b31f1b3879bb2cad8a988a1ba4cc368
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85307591"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>Přidání šablon generování jazyka pro hlasové odpovědi

V tomto článku se dozvíte, jak používat šablony generování jazyka s vlastními příkazy. Šablony generování jazyka umožňují přizpůsobit odpovědi odeslané klientovi a zavádět odchylky v odpovědích. Přizpůsobení generování jazyka lze dosáhnout:

- Použití šablon generování jazyka
- Použití adaptivních výrazů

## <a name="prerequisites"></a>Požadavky

Je nutné provést kroky v následujících článcích:

> [!div class="checklist"]
> * [Postupy: vytváření aplikací pomocí jednoduchých příkazů](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Postupy: Přidání parametrů k příkazům](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>Přehled šablon generování jazyka

Šablony vlastních příkazů jsou založené na [šablonách LG](https://aka.ms/speech/cc-lg-format)pro BotFramework. Vzhledem k tomu, že vlastní příkazy v případě potřeby vytvoří novou šablonu LG (to znamená pro odezvy řeči v parametrech nebo akcích), nemusíte zadávat název šablony LG. Takže místo definování šablony jako:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Stačí definovat pouze tělo šablony bez názvu, a to následujícím způsobem.

> [!div class="mx-imgBorder"]
> ![Příklad editoru šablon](./media/custom-commands/template-editor-example.png)


Tato změna zavádí variaci odpovědí na převod řeči odesílaných klientovi. Pro stejné utterance by tedy bylo možné odpovídající odpověď na řeč náhodně vyskladnit z poskytnutých možností.

Využití šablon LG vám také umožňuje definovat složité odezvy řeči pro příkazy pomocí adaptivních výrazů. Další podrobnosti najdete ve [formátu šablon LG](https://aka.ms/speech/cc-lg-format) . Vlastní příkazy ve výchozím nastavení podporují všechny možnosti s následujícími drobnými rozdíly:

* Entity šablon LG jsou reprezentovány jako $ {EntityName}. Ve vlastních příkazech nepoužíváme entity, ale parametry se dají použít jako proměnné s jedním z těchto reprezentacemi $ {parameterName} nebo {parameterName}.
* Složení a rozšíření šablon nejsou ve vlastních příkazech podporovány. Důvodem je to, že soubor nikdy neupravujte `.lg` přímo, ale pouze odpovědi na automaticky vytvořené šablony.
* Vlastní funkce, které jsou vložené pomocí LG, se ve vlastních příkazech nepodporují. Předdefinované funkce jsou pořád podporovány.
* Možnosti (Strict, replaceNull & lineBreakStyle) se ve vlastních příkazech nepodporují.

## <a name="add-template-responses-to-turnonoff-command"></a>Přidat odpovědi šablony do příkazu TurnOnOff

Úpravou příkazu **TurnOnOff** přidejte nový parametr s následující konfigurací:

| Nastavení            | Navrhovaná hodnota       | 
| ------------------ | --------------------- | 
| Název               | `SubjectContext`         | 
| Je globální          | unchecked             | 
| Požaduje se           | unchecked               | 
| Typ               | Řetězec                |
| Výchozí hodnota      | `all` |
| Konfigurace      | Přijměte předdefinované vstupní hodnoty z interního katalogu. | 
| Předdefinované vstupní hodnoty | `room`, `bathroom`, `all`|

### <a name="modify-completion-rule"></a>Upravit pravidlo dokončení

Upravte oddíl **Actions** pro stávající pravidlo dokončení **ConfirmationResponse**. V automaticky otevíraném okně **Upravit akci** přepněte na **Editor šablon** a nahraďte text následujícím příkladem.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Proveďte **analýzu** a **testování** aplikace následujícím způsobem. Všimněte si proměnlivosti odezvy z důvodu použití více alternativ hodnoty šablony a také použití adaptivních výrazů.

* Vstup: zapnutí televizního vysílání
* Výstup: OK, zapnutí televizního vysílání
* Vstup: zapnutí televizního vysílání
* Výstup: Hotovo, zapnuto v televizi
* Vstup: vypnout světla
* Výstup: OK, vypnout všechna světla
* Vstup: vypnout kvadranty místností
* Výstup: OK, vypnutí místnostních indikátorů

## <a name="use-custom-voice"></a>Použití sady Vlastní hlas

Dalším způsobem přizpůsobení odpovědí vlastních příkazů je výběr vlastního výstupního hlasu. Pomocí následujícího postupu přepnete výchozí hlas na vlastní hlas.

1. V aplikaci Custom Commands (vlastní příkazy) v levém podokně vyberte **Nastavení** .
1. V prostředním podokně vyberte **vlastní hlas** .
1. V tabulce vyberte požadovaný vlastní nebo veřejný hlas.
1. Vyberte **Uložit**.

> [!div class="mx-imgBorder"]
> ![Ukázky vět s parametry](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Pro **veřejné hlasy**jsou **typy neuronové** dostupné jenom pro konkrétní oblasti. Pokud chcete ověřit dostupnost, přečtěte si téma [standardní a neuronové hlasy podle oblasti nebo koncového bodu](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - **Vlastní hlasy**si můžete vytvořit na stránce vlastní hlasového projektu. Viz Začínáme [s vlastním hlasem](./how-to-custom-voice.md).

Aplikace teď bude ve vybraném hlasu reagovat místo na výchozí hlas.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Integrujte vlastní příkazy pomocí sady Speech SDK](./how-to-custom-commands-setup-speech-sdk.md).