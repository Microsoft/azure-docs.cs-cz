---
title: Pracovní postupy moderování – Content Moderator
titlesuffix: Azure Cognitive Services
description: Pomocí pracovních postupů pomocí operace úlohy revizi rozhraní API můžete automatizovat kontroly lidských v the smyčky na základě prahových hodnot a obsahu zásad.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 3a3ff5ed325205c5ae17aaff053127f4e84504bf
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262655"
---
# <a name="automate-moderation-reviews-with-workflows"></a>Automatizace kontroly moderování s pracovními postupy

Content Moderator zahrnuje nástroje a rozhraní API pro správu pracovních postupů. Využijte pracovní postupy s [operace úlohy zkontrolujte API](review-api.md) k automatizaci vytváření lidských v the smyčky revizi na základě prahových hodnot a obsahu zásad.

Rozhraní API pro kontrolu nabízí tyto způsoby zahrnutí jistotu lidské do procesu moderování obsahu:

1. **Úlohy** operace pro spuštění moderování s podporou počítače i lidsky zkontrolujte vytvoření jako jeden krok.
1. **Zkontrolujte** operace pro lidské zkontrolujte vytvoření mimo krok moderování.
1. **Pracovního postupu** operace pro pracovní postupy automatizující prohledávání pomocí prahové hodnoty pro správu zkontrolujte vytvoření.

Tento článek se týká **pracovního postupu** operace. Čtení [úloh a kontrol](review-api.md) přehled a další informace o moderování obsahu úloh a kontrol.

Rezervuje **výchozí** pracovního postupu je nejlepší způsob, jak začít používat Principy pracovních postupů v Content Moderatoru.

## <a name="your-first-workflow"></a>Svůj první pracovní postup

Svůj první pracovní postup se dodává jako součást balíčku s vaší [Zkontrolujte nástroj team](https://contentmoderator.cognitive.microsoft.com/). Zaregistrujte, pokud jste tak již neučinili.

Přejděte [zkontrolujte pracovní postupy nástroje](Review-Tool-User-Guide/Workflows.md) obrazovky na kartě nastavení. Zobrazí **výchozí** pracovní postup, jak je znázorněno na následujícím obrázku:

![Content Moderator pracovních postupů](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>Otevřít výchozí pracovní postup

Použití **upravit** možnost otevření pracovního postupu Úprava stránky, jak je znázorněno na následujícím obrázku: ![Content Moderator výchozí pracovní postup](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>Zobrazení návrháře

Zobrazí **návrháře** kartu pro pracovní postup. Návrhář zobrazení se zobrazuje následující kroky:

1. **Podmínku** pro pracovní postup, který se má vyhodnotit. V takovém případě pracovní postup volání rozhraní Content Moderator vaší image rozhraní API a kontroly, jestli `isAdult` výstup rovná `true`.
1. **Akce** má být provedena, pokud je splněna podmínka. V takovém případě vytvoří pracovní postup přezkoumání v nástroj pro recenze Pokud `isAdult` výstup je `true`.

![Content Moderator výchozí pracovní postup - návrháře](images/default-workflow-designer.png)

### <a name="the-json-view"></a>Zobrazení JSON

Vyberte **JSON** kartu pro zobrazení definice JSON pracovního postupu.

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

Pracovní postupy v Content Moderatoru se snadno konfigurovatelné a flexibilní. Pokud integrované návrháře nesplňuje vaše požadavky, zapsat definice pracovního postupu **JSON** formátu. Pak pomocí definice JSON s [pracovního postupu API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) k vytváření a správě pracovního postupu z vaší aplikace.

## <a name="define-a-custom-workflow"></a>Definovat vlastní pracovní postup

Content Moderator pracovního postupu možností povolit definice a používání vlastních pracovních postupů. Použití [zkontrolujte pracovní postupy nástroje s postupy](Review-Tool-User-Guide/Workflows.md) článku definovat vlastní pracovní postup. Tento pracovní postup používá funkci OCR Content Moderator k extrakci textu z Ukázkový obrázek. Potom vytvoří kontrolu v nástroji pro kontrolu.

### <a name="the-sample-image"></a>Ukázkový obrázek

Uložit [Ukázkový obrázek](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) na váš místní disk. Musíte tuto bitovou kopii pro vaše cvičení.

### <a name="the-designer-view"></a>Zobrazení návrháře

Vyberte **návrháře** kartu a [kurz vytvoření pracovního postupu](Review-Tool-User-Guide/Workflows.md) definovat vlastní pracovní postup. Následující obrázek ukazuje návrháře **podmínku** zobrazení. Využijte kurz zobrazíte zbytek postupu.

![Content Moderator – stav pracovního postupu](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>Zobrazení JSON

Vyberte **JSON** kartu zobrazíte následující definici JSON váš vlastní pracovní postup. Všimněte si, že jak **If-Then** příkazy v definici JSON odpovídají kroky definován pomocí návrháře zobrazení.

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

Po otestování pracovního postupu na obrazovce pracovních postupů se vytvoří následující kontroly. Přejděte **Image** kartu **zkontrolujte** zobrazíte zkontrolovali.
Pracovní postup vytvořit, že revize protože primární podmínky testování kladné přítomnost text. Zkontrolujte také zvýrazněna **`a`** značky při kontrole bitové kopie.

![Content Moderator – výstupní jednoduchý pracovní postup](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>Pomocí kombinace rozšířených pracovních postupů

### <a name="the-sample-image"></a>Ukázkový obrázek

Použijte stejný [Ukázkový obrázek](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) , která byla použita v předchozí části.

Tomto okamžiku přibližně však změňte primární podmínky do kombinace dvou kontroly. Kromě kontroly toho pro text, zkontrolujte, zda text obsahuje všechny vulgárních výrazů. Pracovní postup vytvoří kontrolu, pokud najde text **a** zjistí vulgárních výrazů v ní.

### <a name="the-designer-view"></a>Zobrazení návrháře

Chcete-li změnit **podmínku** k **kombinaci**, upravte pracovní postup. Následující obrázek ukazuje nové zobrazení, které se zobrazí v návrháři.

![Content Moderator – podmínky změny pracovního postupu](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>Zobrazení JSON

Vyberte **JSON** kartu zobrazíte následující definici JSON upravený vlastní pracovní postup. Všimněte si, že jak **If-Then** příkazy v definici JSON odpovídají nové kroky, které jste přidali do pracovního postupu.

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

Po otestování pracovního postupu, zjistíte, že žádná kontrola se vytvořil. Potvrďte neexistence žádné kontroly, přejděte na **Image** kartu **zkontrolujte**.
Pracovní postup kontroly nevytvořil, protože se nepovedlo rozpoznat vulgárních výrazů v byl extrahován text.

![Content Moderator – výstup změny pracovního postupu](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>Pracovní postup rozhraní API

[Operace pracovního postupu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) poskytuje programovací rozhraní pro funkce pracovního postupu. Získat podrobnosti pracovního postupu vytváření pracovních postupů a aktualizovat definice pracovního postupu pomocí rozhraní API pracovního postupu.

### <a name="get-all-workflow-details"></a>Podrobnosti o pracovním postupu GET [All]

**Pracovní postup Get** operace přijímá následující vstupy:

- **Tým**: ID týmu, který jste vytvořili při nastavování vašeho [Zkontrolujte nástroj účet](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Název pracovního postupu. Použití `default` začínat.
- **Ocp-Apim-Subscription-Key**: Na **nastavení** kartu. Další informace najdete v tématu [Přehled](overview.md).

Pokud je operace úspěšná, **stav odpovědi** je `200 OK` a **obsah odpovědi** zobrazí definice pracovního postupu ve formátu JSON.
Další informace najdete v článku [pracovního postupu rozhraní API konzoly rychlého startu](try-review-api-job.md).

### <a name="create-or-update-workflow"></a>Vytvořit nebo aktualizovat pracovní postup

Operace vytvoření a aktualizace je umožněno vytvoření pracovního postupu z rozhraní API.

**Pracovní postup vytvoření nebo aktualizace** operace přijímá následující vstupy:

- **Tým**: ID týmu, který jste vytvořili při nastavování vašeho [Zkontrolujte nástroj účet](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Název pracovního postupu. Použití `default` začínat.
- **Ocp-Apim-Subscription-Key**: Na **nastavení** kartu. Další informace najdete v tématu [Přehled](overview.md).

Pokud je operace úspěšná, **stav odpovědi** je `200 OK` a **obsah odpovědi** zobrazí `true`. Další informace, [testovací verze `Create` operace](try-review-api-job.md).

## <a name="next-steps"></a>Další postup

Zjistěte, jak vytvářet vlastní pracovní postupy, přečtěte si [přečtěte si kurz pracovního postupu nástroje](Review-Tool-User-Guide/Workflows.md). 

Testovací verze [pracovního postupu rozhraní API konzoly](try-review-api-job.md) a používat rozhraní REST API ukázky kódu. 

Nakonec použijte váš vlastní pracovní postupy s **úlohy** operace jako shon v [rozhraní API pro úlohu konzoly](try-review-api-job.md) a [rychlý úvod k .NET úlohy](moderation-jobs-quickstart-dotnet.md).
