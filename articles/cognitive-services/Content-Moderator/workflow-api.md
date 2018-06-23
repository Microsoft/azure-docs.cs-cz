---
title: Azure obsahu moderátora - přerušování pracovních | Microsoft Docs
description: Použití pracovních postupů s obsahu přerušování.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/04/2018
ms.author: sajagtap
ms.openlocfilehash: 079fcd119f1536f9e76ca57fccc76538b3c3ed78
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342525"
---
# <a name="moderation-workflows"></a>Přerušování pracovních postupů

Obsahu moderátora obsahuje nástroje a rozhraní API pro správu pracovních postupů. Použití pracovních postupů s [operace úlohy zkontrolujte API](review-api.md) k automatizaci vytváření zkontrolujte lidské v the smyčky na základě vašeho obsahu zásady a prahové hodnoty.

Kontrola rozhraní API nabízí tyto způsoby do procesu obsahu přerušování zahrnout lidského dohledu:

1. **Úlohy** operací pro spouštění počítače asistované přerušování a lidské zkontrolujte vytvoření jako jeden krok.
1. **Zkontrolujte** operací pro lidské zkontrolujte vytvoření mimo přerušování krok.
1. **Pracovního postupu** zkontrolujte operací pro správu pracovních postupů, které automatizují kontrolu s prahovými hodnotami pro vytvoření.

Tento článek se zabývá **pracovního postupu** operace. Pro čtení [úlohy a zkontroluje](review-api.md) přehled Další informace o obsahu přerušování úlohy a zkontroluje.

Rezervování **výchozí** pracovní postup je nejlepší způsob, jak začít pracovat na pochopení pracovních postupů v obsahu moderátora.

## <a name="your-first-workflow"></a>První pracovního postupu

Vaše první pracovní postup obsahuje také program s vaší [Zkontrolujte nástroj team](https://contentmoderator.cognitive.microsoft.com/). Pokud jste tak již neučinili, zaregistrujte si.

Přejděte na [zkontrolujte pracovní postupy nástroje pro](Review-Tool-User-Guide/Workflows.md) obrazovky na kartě nastavení. Zobrazí **výchozí** pracovní postup, jak je znázorněno na následujícím obrázku:

![Obsahu moderátora pracovních postupů](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>Otevřete výchozí pracovní postup

Použití **upravit** možnost otevření pracovního postupu, úprava stránky, jak je znázorněno na následujícím obrázku: ![obsahu moderátora výchozí pracovní postup](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>Návrhář zobrazení

Zobrazí **Návrhář** kartě pro pracovní postup. Návrhář zobrazení se zobrazuje následující kroky:

1. **Podmínku** pro pracovní postup k vyhodnocení. V takovém případě pracovního postupu volání obsahu moderátora na obrázku rozhraní API a kontroly jestli `isAdult` výstup rovná `true`.
1. **Akce** provést, pokud je splněna podmínka. V takovém případě vytvoří pracovní postup kontrolu v nástroji zkontrolujte Pokud `isAdult` výstup je `true`.

![Obsahu moderátora výchozí pracovní postup - návrháře](images/default-workflow-designer.png)

### <a name="the-json-view"></a>Zobrazení JSON

Vyberte **JSON** karty zobrazíte JSON definice pracovního postupu.

    {
        "Type": "Logic",
        If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="key-learning"></a>Klíče učení

Pracovní postupy v obsahu moderátora jsou snadno konfiguruje a flexibilní. Pokud integrované návrháře nesplňuje vaše požadavky, zapisovat definice pracovního postupu v **JSON** formátu. Pak použijte definici JSON s [pracovního postupu API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) k vytváření a správě pracovního postupu z vaší aplikace.

## <a name="define-a-custom-workflow"></a>Definovat vlastní pracovní postup

Možnosti pracovní postup obsahu moderátora povolit definování a používání vlastních pracovních postupů. Použití [zkontrolujte postupy pracovní postupy nástroje](Review-Tool-User-Guide/Workflows.md) článek k definování vlastní pracovní postup. Tento pracovní postup používá funkce rozpoznávání znaků obsahu moderátora k rozbalte text z ukázkového obrázku. V nástroji zkontrolujte poté vytvoří kontrolu.

### <a name="the-sample-image"></a>Ukázka bitové kopie

Uložit [Ukázkový obrázek](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) na místní jednotku. Je nutné tuto bitovou kopii pro vaše cvičení.

### <a name="the-designer-view"></a>Návrhář zobrazení

Vyberte **Návrhář** kartě a [kurz vytvoření pracovního postupu](Review-Tool-User-Guide/Workflows.md) k definování vlastní pracovní postup. Následující obrázek ukazuje nástroje designer **podmínku** zobrazení. Naleznete v kurzu, který najdete v části Další kroky.

![Obsahu moderátora - podmínku pracovního postupu](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>Zobrazení JSON

Vyberte **JSON** karty zobrazíte následující definici JSON vlastní pracovního postupu. Všimněte si jak **Pokud potom** příkazy v definici JSON odpovídají kroky definované pomocí návrháře zobrazení.

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="workflow-result"></a>Výsledek pracovního postupu

Po dokončení testu pracovního postupu z obrazovky pracovních postupů, se vytvoří následující revize. Přejděte na **Image** v části **zkontrolujte** zobrazíte zkontrolovali.
Pracovní postup vytvořit, že kontrola protože testována primární podmínky kladné přítomnost text. Zkontrolujte také zvýrazněných **`a`** značku zkontrolujte bitové kopie.

![Obsahu moderátora – výstup jednoduché pracovního postupu](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>Pokročilé pracovního postupu s kombinací

### <a name="the-sample-image"></a>Ukázka bitové kopie

Použijte stejný [Ukázkový obrázek](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) , který se používá v předchozí části.

Tento čas přibližně však změňte vaší primární podmínky do kombinací dvou kontroly. Kromě kontroly textu, zkontrolujte, zda text má všechny vulgárnost. Pracovní postup vytvoří kontrolu, pokud najde text **a** zjistí vulgárnost v ní.

### <a name="the-designer-view"></a>Návrhář zobrazení

Chcete-li změnit **podmínku** k **kombinace**, upravit pracovní postup. Následující obrázek znázorňuje nové zobrazení, které se zobrazí v návrháři.

![Obsahu moderátora - podmínku upravené pracovního postupu](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>Zobrazení JSON

Vyberte **JSON** karty zobrazíte následující definici JSON upravený vlastní pracovního postupu. Všimněte si jak **Pokud potom** příkazy v definici JSON odpovídají nové kroky, které jste přidali do pracovního postupu.

    {
        "Type": "Logic",
        "If": {
        "Left": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Right": {
            "ConnectorName": "moderator",
            "OutputName": "text.HasProfanity",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition",
            "AlternateInput": "moderator.ocrText"
            },
        "Combine": "AND",
        "Type": "Combine"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

    
### <a name="workflow-result"></a>Výsledek pracovního postupu

Po dokončení testu pracovního postupu zjistíte, jestli je vytvořená žádná kontrola. Potvrdit absenci všechny kontrolní, přejděte na **Image** v části **zkontrolujte**.
Pracovní postup nevytvořila kontrola, protože se nepodařilo zjistit vulgárnost v extrahované textu.

![Obsahu moderátora – výstup upravené pracovního postupu](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>Pracovní postup rozhraní API

[Operace pracovního postupu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) nabízí programovací rozhraní k funkcím pracovního postupu. Vytvořit pracovní postupy, získat podrobnosti pracovního postupu a aktualizovat definice pracovního postupu pomocí rozhraní API služby pracovního postupu.

### <a name="get-all-workflow-details"></a>Podrobnosti o pracovní postup GET [All]

**Pracovní postup Get** operace přijímá následující zadání:

- **tým**: ID týmu, který jste vytvořili při nastavování vaší [Zkontrolujte nástroj účet](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: název pracovního postupu. Použití `default` na začátku.
- **OCP-Apim-Subscription-Key**: nachází na **nastavení** kartě. Další informace najdete v tématu [přehled](overview.md).

Pokud je operace úspěšná, **stav odpovědi** je `200 OK` a **obsah odpovědi** pole zobrazuje definice pracovního postupu ve formátu JSON.
Další informace, přečtěte si [rychlé spuštění pracovního postupu API konzoly](try-review-api-job.md).

### <a name="create-or-update-workflow"></a>Vytvořit nebo aktualizovat pracovního postupu

Operace vytvoření a aktualizace umožňuje vytváření pracovního postupu z rozhraní API.

**Pracovní postup vytvoření nebo aktualizace** operace přijímá následující zadání:

- **tým**: ID týmu, který jste vytvořili při nastavování vaší [Zkontrolujte nástroj účet](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: název pracovního postupu. Použití `default` na začátku.
- **OCP-Apim-Subscription-Key**: nachází na **nastavení** kartě. Další informace najdete v tématu [přehled](overview.md).

Pokud je operace úspěšná, **stav odpovědi** je `200 OK` a **obsah odpovědi** pole zobrazí `true`. Další informace, [vyzkoušejte `Create` operaci](try-review-api-job.md).

## <a name="next-steps"></a>Další postup

Naučte se vytvářet vlastní pracovní postupy, podívejte se [zkontrolujte kurzu pracovního postupu pro nástroj](Review-Tool-User-Guide/Workflows.md). 

Vyzkoušejte [pracovního postupu API konzoly](try-review-api-job.md) a používat ukázky kódu rozhraní REST API. 

Nakonec použijte vaše vlastní pracovní postupy s **úlohy** operace jako shon v [rozhraní API pro úlohu konzoly](try-review-api-job.md) a [rychlé spuštění úlohy .NET](moderation-jobs-quickstart-dotnet.md).
