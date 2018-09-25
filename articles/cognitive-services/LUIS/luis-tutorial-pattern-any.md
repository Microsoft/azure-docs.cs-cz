---
title: 'Kurz 5: Pattern.any entity pro textu volného tvaru'
titleSuffix: Azure Cognitive Services
description: Použijte pattern.any entity extrahovat data z projevy, kde je správně naformátovaný projevy a kde konec dat může snadno zaměnitelná s zbývající slova utterance.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: a5b964877f1c089015812abdf365b22c57267bb0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031352"
---
# <a name="tutorial-5-extract-free-form-data"></a>Kurz: 5. Extrakce dat volného tvaru

V tomto kurzu použijte pattern.any entity extrahovat data z projevy, kde jsou správně naformátovaný projevy a kde konec dat může snadno zaměnitelná s zbývající slova utterance. 

Pattern.any entity umožňuje najít data volného tvaru, kde znění entity ztěžuje zjistit koncové entity od zbytku utterance. 

Tuto aplikaci lidských zdrojů pomáhá zaměstnancům najít firemní formuláře. 

|Promluva|
|--|
|Kde je **HRF 123456**?|
|Autora **HRF 123234**?|
|**HRF 456098** je publikovaná ve francouzštině?|

Každý formulář má však obě formátovaný název, použít v předchozí tabulce, stejně jako popisný název, například `Request relocation from employee new to the company 2018 version 5`. 

Projevy s názvem popisný formulář vypadat takto:

|Promluva|
|--|
|Kde je **žádat nová verze 5 společnosti 2018 zaměstnance přemístění**?|
|Autora **"Žádat přemístění zaměstnance nové verze 5 společnosti 2018"**?|
|**Žádat nová verze 5 společnosti 2018 zaměstnance přemístění** je publikovaná ve francouzštině?|

Různé délky obsahuje slova, které může matou LUIS o ukončení entity. Použijete Pattern.any entitu ve vzorci můžete zadat začátek a konec název formuláře, LUIS správně extrahuje název formuláře.

|Příklad utterance šablony|
|--|
|Kde je {FormName} [?]|
|Autora {FormName} [?]|
|{FormName} je publikovaná ve francouzštině [?]|

**V tomto kurzu se dozvíte, jak:**

> [!div class="checklist"]
> * Použít existující ukázková aplikace
> * Přidání projevů příklad do existující entity
> * Vytvoření Pattern.any entity
> * Vytvoření modelu
> * Trénování
> * Nový vzor testu

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Použití existující aplikace
Pokračovat s aplikací vytvořili v posledním kurzu s názvem **Lidskézdroje**. 

Pokud nemáte aplikaci lidských zdrojů z předchozí kurz o službě, použijte následující kroky:

1.  Stáhněte a uložte [souboru JSON aplikace](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-roles-HumanResources.json).

2. Importujte ve formátu JSON do nové aplikace.

3. Z **spravovat** části na **verze** kartu, naklonujte na verzi a pojmenujte ho `patt-any`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze, protože se používají jako součást trasu adresy URL název nesmí obsahovat žádné znaky, které nejsou platné v adrese URL.

## <a name="add-example-utterances"></a>Přidání projevů příklad 
Odeberte keyPhrase předem připravených entit, pokud je těžké vytvořit a označovat FormName entity. 

1. Vyberte **sestavení** v horním navigačním panelu vyberte **záměry** z levého navigačního panelu.

2. Vyberte **FindForm** ze seznamu záměry.

3. Přidání projevů některé příklad:

    |Příklad utterance|
    |--|
    |Kde je formulář **co dělat, když je dělí v testovacím prostředí** a který je potřeba po jsem si jej podepsat?|
    |Kde je **žádat přemístění zaměstnance, nová společnost** na serveru?|
    |Autora "**stavu a duševní pohoda požadavky v hlavním campusech**" a jaká je nejnovější verze?|
    |Dívám se tam pro daný formulář s názvem "**Office přesunout žádosti včetně fyzické prostředky**". |

    Bez Pattern.any entity by bylo obtížné pro LUIS pochopit, kde končí název formuláře z důvodu mnoho variant názvy formulářů.

## <a name="create-a-patternany-entity"></a>Vytvoření Pattern.any entity
Pattern.any entity extrahuje entity různou délku. Funguje pouze v vzor protože vzor označuje začátek a konec entity. Pokud zjistíte, že váš model, pokud obsahuje Pattern.any, výpisy entity nesprávně, použijte [explicitní seznam](luis-concept-patterns.md#explicit-lists) abyste tento problém opravili. 

1. Vyberte **entity** v levém navigačním panelu.

2. Vyberte **vytvořit novou entitu**, zadejte název `FormName`a vyberte **Pattern.any** jako typ. Vyberte **Done** (Hotovo). 

    Entity v záměr nelze popisek, protože Pattern.any platí pouze ve vzorku. 

    Pokud chcete extrahovaná data k začlenění jinými entitami, jako je číslo nebo datetimeV2, musíte vytvořit složenou entitu, která zahrnuje Pattern.any, stejně jako číslo a datetimeV2.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Přidejte vzor, který se používá Pattern.any

1. Vyberte **vzory** z levé navigace.

2. Vyberte **FindForm** záměr.

3. Zadejte následující šablony projevy, které používají nové entity:

    |Projevy šablony|
    |--|
    |Kde je formulář ["] {FormName} ["] a kdo potřebuje podepište ho po jsem si ho [?]|
    |Kde je ["] {FormName} ["] na serveru [?]|
    |Autora ["] {FormName} ["] a jaká je nejnovější verze [?]|
    |Dívám se tam pro daný formulář s názvem ["] {FormName} ["] [.]|

    Pokud chcete účet každodenně formu jako je například jednoduchých uvozovek a být místo dvojitých uvozovek nebo období místo otazník, vytvořte nový vzor pro každou změnu.

4. Pokud jste odebrali keyPhrase entity, můžete ji přidáte do aplikace. 

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testování nový vzor pro extrakci dat volného tvaru
1. Vyberte **testování** v horním panelu otevřete panel testů. 

2. Zadejte následující utterance: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Vyberte **zkontrolujte, jestli se** podle výsledku zobrazíte výsledky testů pro entitu a záměr.

    Entita `FormName` je nalezena jako první, pak se najde vzorek, určení záměr. Pokud máte výsledku testu, kde entity, které nejsou zjištěny, a proto není nalezen vzor, budete muset přidat další příklad projevy na záměr (ne vzor).

4. Zavřít panel testů tak, že vyberete **testování** tlačítko v horním navigačním panelu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu přidá příklad projevy na existující záměr, pak vytvoří nové Pattern.any pro název formuláře. Tento kurz vytvoří vzor pro existující záměr s novou příklad projevy a entity. Interaktivní testování jsme si ukázali, že vzor a jejímu záměru byly předvídat, protože entita byla nalezena. 

> [!div class="nextstepaction"]
> [Další informace o použití rolí pomocí vzoru](luis-tutorial-pattern-roles.md)