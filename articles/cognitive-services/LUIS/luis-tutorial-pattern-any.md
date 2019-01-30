---
title: Pattern.Any entity
titleSuffix: Azure Cognitive Services
description: Pomocí entity Pattern.any můžete extrahovat data ze správně naformátovaných promluv, kde je možné snadno zaměnit konec dat se zbývajícími slovy promluvy.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: d26a8ba40d145515d35de929682125ba9d3e397f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55205775"
---
# <a name="tutorial-extract-free-form-data-with-patternany-entity"></a>Kurz: Extrahování dat volného tvaru pomocí Pattern.any entity

V tomto kurzu použijete entitu Pattern.any k extrakci dat ze správně naformátovaných promluv, kde je možné snadno zaměnit konec dat se zbývajícími slovy promluvy. 

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Importovat ukázková aplikace
> * Přidání ukázkových promluv do stávající entity
> * Vytvoření entity Pattern.any
> * Vytvoření vzoru
> * Trénování
> * Test nového vzoru

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-patternany-entity"></a>Použití Pattern.any entity

Entita Pattern.any umožňuje vyhledat data volného tvaru, kde je kvůli formulaci entity obtížné odlišit konec entity od zbytku promluvy. 

Tato aplikace pro lidské zdroje pomáhá zaměstnancům najít formuláře společnosti. 

|Promluva|
|--|
|Where is **HRF-123456**? (Kde je HRF-123456?)|
|Who authored **HRF 123234**? (Kdo vytvořil HRF 123234?)|
|**HRF-456098** is published in French? (Je HRF-456098 ve francouzštině?)|

Každý formulář však má formátovaný název použitý v předchozí tabulce i popisný název, jako je například `Request relocation from employee new to the company 2018 version 5`. 

Projevy s popisnými názvy formulářů vypadají takto:

|Promluva|
|--|
|Where is **Request relocation from employee new to the company 2018 version 5**? (Kde je Žádost o přeložení nového zaměstnance společnosti z roku 2018 verze 5?)|
|Who authored **Request relocation from employee new to the company 2018 version 5**? (Kdo vytvořil Žádost o přeložení nového zaměstnance společnosti z roku 2018 verze 5?)|
|**Request relocation from employee new to the company 2018 version 5**? (Je Žádost o přeložení nového zaměstnance společnosti z roku 2018 verze 5 ve francouzštině?)|

Různá délka promluv zahrnuje slova, která můžou službě LUIS komplikovat určení konce entity. Když ve vzoru použijete entitu Pattern.any, můžete určit začátek a konec názvu formuláře, aby služba LUIS mohla správně extrahovat název formuláře.

|Příklad šablony promluvy|
|--|
|Where is {FormName}[?] (Kde je {FormName}[?])|
|Who authored {FormName}[?] (Kdo vytvořil {FormName}[?])|
|{FormName} is published in French[?] (Je {FormName} ve francouzštině[?])|

## <a name="import-example-app"></a>Importovat ukázková aplikace
Pokračujte s aplikací **HumanResources**, kterou jste vytvořili v posledním kurzu. 

Použijte k tomu následující postup:

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-pattern-roles-HumanResources.json) a uložte si ho.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `patt-any`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

## <a name="add-example-utterances"></a>Přidat ukázkové promluvy 
Pokud je obtížné vytvořit a označit entitu FormName, odeberte předem připravenou entitu keyPhrase. 

1. V horní navigaci vyberte **Build** (Sestavení) a pak v levé navigaci vyberte **Intents** (Záměry).

2. V seznamu záměrů vyberte **FindForm** (Vyhledat formulář).

3. Přidejte několik ukázkových promluv:

    |Ukázková promluva|
    |--|
    |Where is the form **What to do when a fire breaks out in the Lab** and who needs to sign it after I read it? (Kde je formulář „Co dělat, když v laboratoři vypukne požár“ a kdo ho musí podepsat, až ho přečtu?)|
    |Where is **Request relocation from employee new to the company** on the server? (Kde na serveru je Žádost o přeložení nového zaměstnance společnosti?)|
    |Who authored **Health and wellness requests on the main campus** and what is the most current version? (Kdo vytvořil „Zásady zachování zdraví a duševní pohody v hlavním kampusu“ a jaká je nejnovější verze?)|
    |I'm looking for the form named **Office move request including physical assets**. (Hledám formulář s názvem „Žádost o přesun kanceláře včetně fyzických prostředků“.) |

    Bez entity Pattern.any by pro službu LUIS bylo kvůli mnoha variantám názvů formulářů obtížné zjistit, kde končí název formuláře.

## <a name="create-a-patternany-entity"></a>Vytvoření entity Pattern.any
Entita Pattern.any extrahuje entity různé délky. Funguje pouze ve vzoru, protože vzor označuje začátek a konec entity. Pokud zjistíte, že váš vzor zahrnující entitu Pattern.any neextrahuje entity správně, můžete tento problém opravit pomocí [explicitního seznamu](luis-concept-patterns.md#explicit-lists). 

1. V levé navigaci vyberte **Entities** (Entity).

2. Vyberte **Create new entity** (Vytvořit novou entitu), zadejte název `FormName` a jako typ vyberte **Pattern.any**. Vyberte **Done** (Hotovo). 

    Entitu není možné označit v záměru, protože entita Pattern.any je platná pouze ve vzoru. 

    Pokud chcete, aby extrahovaná data zahrnovala i další entity, jako jsou entity number nebo datetimeV2, budete muset vytvořit složenou entitu obsahující entity Pattern.any i number a datetimeV2.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Přidání vzoru využívajícího entitu Pattern.any

1. V levé navigaci vyberte **Patterns** (Vzory).

2. Vyberte záměr **FindForm** (Vyhledat formulář).

3. Zadejte následující šablony promluv, které používají novou entitu:

    |Šablony promluv|
    |--|
    |Where is the form ["]{FormName}["] and who needs to sign it after I read it[?] (Kde je formulář ["]{FormName}["] a kdo ho musí podepsat, až ho přečtu[?])|
    |Where is ["]{FormName}["] on the server[?] (Kde na serveru je ["]{FormName}["][?])|
    |Who authored ["]{FormName}["] and what is the most current version[?] (Kdo vytvořil ["]{FormName}["] a jaká je nejnovější verze[?])|
    |I'm looking for the form named ["]{FormName}["][.] (Hledám formulář s názvem ["]{FormName}["][.])|

    Pokud chcete zohlednit různé varianty formuláře, jako je použití jednoduchých uvozovek místo dvojitých nebo tečky místo otazníku, vytvořte pro každou variantu nový vzor.

4. Pokud jste odebrali entitu keyPhrase, přidejte ji zpátky do aplikace. 

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Test nového vzoru pro extrakci dat volného tvaru
1. Výběrem možnosti **Test** na horním panelu otevřete panel testování. 

2. Zadejte následující promluvu: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Pod výsledkem vyberte **Inspect** (Prozkoumat) a zobrazte výsledky testu pro entitu a záměr.

    Jako první se našla entita `FormName` a pak vzor určující záměr. Pokud máte výsledek testu, kde se nezjistily entity a tedy se ani nenašel vzor, musíte do záměru (ne do vzoru) přidat více ukázkových promluv.

4. Zavřete panel testování výběrem tlačítka **Test** v horní navigaci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu se do stávajícího záměru přidaly ukázkové promluvy a pak se vytvořila nová entita Pattern.any pro název formuláře. Pak se vytvořil vzor pro stávající záměr s novými ukázkovými promluvami a entitou. Interaktivní testování ukázalo, že se předpověděl vzor a jeho záměr, protože se našla entita. 

> [!div class="nextstepaction"]
> [Naučte se, jak používat role spolu se vzorem](luis-tutorial-pattern-roles.md)
