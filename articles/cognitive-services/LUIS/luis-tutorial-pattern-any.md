---
title: Kurz použití pattern.any entity k vylepšení predikce služby LUIS – Azure | Dokumentace Microsoftu
titleSuffix: Cognitive Services
description: V tomto kurzu používá k vylepšení predikce služby LUIS záměr a entity pattern.any entity.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 58a1871774683e2af66e12a03bdaf8d6bcea831c
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493254"
---
# <a name="tutorial-improve-app-with-patternany-entity"></a>Kurz: Zlepšení aplikace s entitou pattern.any

V tomto kurzu použijte ke zvýšení záměr a entity předpovědi pattern.any entity.  

> [!div class="checklist"]
* Zjistěte, kdy a jak používat pattern.any
* Vytvoření vzor, který využívá pattern.any
* Postup ověření vylepšení predikce

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci lidských zdrojů [vzorku role](luis-tutorial-pattern-roles.md) kurzu [importovat](luis-how-to-start-new-app.md#import-new-app) JSON do nové aplikace ve [LUIS](luis-reference-regions.md#luis-website) webu. App k importu se nachází v [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-roles-HumanResources.json) úložiště GitHub.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `patt-any`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 

## <a name="the-purpose-of-patternany"></a>Účelem pattern.any
Pattern.any entity umožňuje najít data volnou formou kde znění entity ztěžuje zjistit koncové entity od zbytku utterance. 

Tuto aplikaci lidských zdrojů pomáhá zaměstnancům najít firemní formuláře. Formuláře byly přidány ve [regulárního výrazu kurzu](luis-quickstart-intents-regex-entity.md). Názvy formulářů v tomto kurzu používá regulárních výrazů k extrakci název formuláře, který byl správně naformátovaný jako jsou názvy formulářů tučně v následující tabulce utterance:

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

Různé délky zahrnuje fráze, které může matou LUIS o ukončení entity. Použijete Pattern.any entitu ve vzorci můžete zadat začátek a konec název formuláře, LUIS správně extrahuje název formuláře.

**Zatímco vzorky umožňují poskytovat méně příklad projevy, pokud nejsou zjištěny entity, neodpovídá vzoru.**

## <a name="add-example-utterances-to-the-existing-intent-findform"></a>Přidání projevů příklad na existující záměr FindForm 
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

5. Trénování aplikace.

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testování nový vzor pro extrakci dat volného tvaru
1. Vyberte **testování** v horním panelu otevřete panel testů. 

2. Zadejte následující utterance: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Vyberte **zkontrolujte, jestli se** podle výsledku zobrazíte výsledky testů pro entitu a záměr.

    Entita `FormName` je nalezena jako první, pak se najde vzorek, určení záměr. Pokud máte výsledku testu, kde entity, které nejsou zjištěny, a proto není nalezen vzor, budete muset přidat další příklad projevy na záměr (ne vzor).

4. Zavřít panel testů tak, že vyberete **testování** tlačítko v horním navigačním panelu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o použití rolí pomocí vzoru](luis-tutorial-pattern-roles.md)