---
title: Kurz použití vzorců k vylepšení predikce služby LUIS – Azure | Dokumentace Microsoftu
titleSuffix: Azure
description: V tomto kurzu používá vzor pro příkazy k vylepšení predikce služby LUIS záměr a entity.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 12105829f62b988760d3bbf18000466fd27b9aff
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888326"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>Kurz: Použití vzorců k vylepšení predikce

V tomto kurzu pomocí vzorů zvýšit předpovědi záměr a entity.  

> [!div class="checklist"]
* Jak určit, že vzor by umožňují, aby aplikace
* Postup vytvoření vzoru 
* Použití předem připravená a vlastní entity ve vzoru 
* Postup ověření vylepšení predikce modelu
* Jak přidat roli do entity k vyhledání kontextově založené na entity
* Postup přidání Pattern.any najít entity volného tvaru

Pro účely tohoto článku potřebujete bezplatný účet [LUIS](luis-reference-regions.md), abyste mohli vytvořit svou aplikaci LUIS.

## <a name="import-humanresources-app"></a>Importovat aplikaci lidských zdrojů
V tomto kurzu importuje aplikaci lidských zdrojů. Aplikace obsahuje tři záměry: None, GetEmployeeOrgChart, GetEmployeeBenefits. Dvě entity, které k němu má aplikace: předem připravených číslo a zaměstnance. Entita zaměstnanců je jednoduché entity k extrakci jméno zaměstnance. 

1. Vytvořte nový soubor aplikace LUIS s názvem `HumanResources.json`. 

2. Zkopírujte následující definici aplikace do souboru:

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. Na službu LUIS **aplikace** stránce **novou aplikaci Import**. 

4. V **novou aplikaci Import** dialogového okna, vyberte `HumanResources.json` soubor vytvořený v kroku 1.

5. Vyberte **GetEmployeeOrgChart** záměr, pak změnu oproti **zobrazení entity** k **tokeny zobrazení**. Několik projevy příkladu jsou uvedeny. Každý utterance obsahuje název, který je entita zaměstnance. Všimněte si, že každý název se liší a že se liší pro každý utterance uspořádání pomocí jiné volby slov. Tuto různorodost pomáhá další širokou škálu projevy LUIS.

    ![Snímek obrazovky s cílem stránku přepínat zobrazení entity](media/luis-tutorial-pattern/utterances-token-view.png)

6. Vyberte **trénování** v horním navigačním panelu tak moct trénovat aplikace. Vyčkat, než panelu zelené úspěch.

7. Vyberte **Test** v horním panelu. Zadejte `Who does Patti Owens report to?` vyberte ENTER. Vyberte **zkontrolujte, jestli se** pod utterance zobrazíte další informace o testu.
    
    Jméno zaměstnance Patti Owens nebyla použita v ještě příkladu utterance. Toto je test ke zjištění, jak dobře LUIS se naučili, je tato utterance pro `GetEmployeeOrgChart` záměr a entitou zaměstnance by měly být `Patti Owens`. Výsledek by měl mít míň než 50 % (. 50) pro `GetEmployeeOrgChart` záměr. Naším záměrem je správný, skóre je nízký. Zaměstnanec entity se také správně identifikuje jako `Patti Owens`. Vzory zvýšit Toto skóre počáteční předpovědi. 

    ![Snímek obrazovky testovací panel](media/luis-tutorial-pattern/original-test.png)

8. Zavřít panel testů tak, že vyberete **testování** tlačítko v horním navigačním panelu. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Vzory běžných projevy s menším počtem příklady naučit LUIS
Vzhledem k povaze domény lidských zdrojů existuje pár běžné způsoby s žádostí o relacích zaměstnancům v organizacích. Příklad:

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

Tyto projevy jsou příliš zavřít do určit kontextové jedinečnost jednotlivých bez zadání mnoho příkladů utterance. Přidáním vzor záměru LUIS zjišťuje běžné vzory utterance pro záměru bez poskytnutí mnoho příkladů utterance. 

Příklad šablony projevy záměru zahrnují:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

Vzor je kombinací porovnávání regulárních výrazů a strojové učení. Potom zadejte některé šablonu utterance příklady LUIS další vzorek. Tyto příklady, spolu s záměr projevů poskytnout LUIS lepší představu, z jaké projevy přizpůsobit záměr a kde, v rámci utterance, že entita existuje. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>Přidání projevů šablony

1. V levém navigačním panelu v části **zvýšit výkon aplikace**vyberte **vzory** z levé navigace.

2. Vyberte **GetEmployeeOrgChart** záměr, pak zadejte následující projevy šablony, jeden po druhém, vyberete zadejte po každé šablony utterance:

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    `{Employee}` Syntaxe označí entitu umístění v rámci šablony utterance jako i které entitě je. 

    ![Snímek obrazovky zadání projevy šablony pro záměr](./media/luis-tutorial-pattern/enter-pattern.png)

3. Vyberte **Train** v horním navigačním panelu. Vyčkat, než panelu zelené úspěch.

4. Vyberte **Test** v horním panelu. Zadejte `Who does Patti Owens report to?` v textovém poli. Vyberte možnost Enter. Toto je stejný utterance testovány v předchozí části. Výsledek by měla být vyšší pro `GetEmployeeOrgChart` záměr. 

    Skóre je teď mnohem vyšší. Služba LUIS zjistili relevantní k příslušnému záměru vzor bez zadání mnoho příkladů.

    ![Výsledek panel testů snímek obrazovky s vysokým skóre](./media/luis-tutorial-pattern/high-score.png)

    Entita je nalezena jako první, pak se najde vzorek, která záměr. Pokud máte výsledku testu, kde entity není zjištěna, a proto není nalezen vzor, budete muset přidat další příklad projevy na záměr (ne vzor). 

5. Zavřít panel testů tak, že vyberete **testování** tlačítko v horním navigačním panelu.

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>Použijte entity s rolí ve vzoru
Aplikace LUIS slouží ke snadnější zaměstnance přesunout z jednoho umístění do druhého. Je příkladu utterance `Move Bob Jones from Seattle to Los Colinas`. Každé umístění v utterance má odlišný význam. Los Colinas je cílové umístění pro přesun Praha je původní umístění. Aby bylo možné rozlišovat těchto umístěních ve vzoru v následujících částech vytvoříte jednoduchou entitu pro umístění s dvě role: zdroj a cíl. 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>Vytvořit nový záměr pro přesouvání lidí a prostředky
Vytvořte nový záměr pro jakékoli projevy, které jsou o přesouvání lidí nebo prostředky.

1. Vyberte **záměry** z levého navigačního panelu
2. Vyberte **vytvořit nové záměr**
3. Název nové záměr `MoveAssetsOrPeople`
4. Přidání projevů příkladu:

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![Snímek obrazovky utterance příklad pro MoveAssetsOrPeople záměr](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    Účelem projevy příkladu je poskytnout dostatek příklady. Pokud později v testu, nebude nalezena entita umístění, a proto není zjištěna vzor, přejděte zpět k tomuto kroku a přidejte další příklady. Potom trénováním a testováním znovu. 

5. Označování entit v příkladu projevy s entitou zaměstnance když vyberete jméno klikněte příjmení v utterance poté vyberete zaměstnance entity v seznamu.

    ![Snímek obrazovky projevy v MoveAssetsOrPeople označené zaměstnance entity](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. Vyberte text `portland` v utterance `move travis hinton from portland to orlando`. V místním dialogovém okně, zadejte nový název entity `Location`a vyberte **vytvořit novou entitu**. Zvolte **jednoduché** typu entity a vyberte **provádí**.

    ![Snímek obrazovky vytváření nové entity v umístění](./media/luis-tutorial-pattern/create-new-location-entity.png)

    Označte rest názvy umístění v projevy. 

    ![Snímek obrazovky se všechny entity označené](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    Vzor výběru aplikace word a pořadí je zřejmý na předchozím obrázku. Pokud byste byli **není** pomocí vzorů, a projevy na záměr zřejmé vzor, který je dobrá indikace toho byste měli použít vzory. 

    Pokud očekáváte, že širokou škálu projevy, namísto vzor, ty by projevy nesprávný příklad. V takovém případě byste měli nejrůznějšími projevy termín nebo volba slov, utterance, a umístění entity. 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>Přidat roli do umístění entity 
Role jde použít jenom pro vzory. Přidání role zdroj a cíl k entitě umístění. 

1. Vyberte **entity** v levém navigačním panelu klikněte **umístění** ze seznamu entit.

2. Přidat `Origin` a `Destination` rolí k entitě.

    ![Snímek obrazovky s novou entitu s rolemi](./media/luis-tutorial-pattern/location-entity.png)

    Role nejsou označeny na stránce MoveAssetsOrPeople záměru, protože role neexistují na záměr projevů. Existují jenom na projevy vzor šablony. 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>Přidání projevů šablony, které používá umístění a cílové role
Přidání projevů šablony, využívající novou entitu.

1. Vyberte **vzory** z levé navigace.

2. Vyberte **MoveAssetsOrPeople** záměr.

3. Zadejte nové šablony utterance pomocí nové entity `Move {Employee} from {Location:Origin} to {Location:Destination}`. Syntaxe pro entitu a role uvnitř šablony utterance `{entity:role}`.

    ![Snímek obrazovky s novou entitu s rolemi](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. Aplikace pro novou záměr, entit a způsob trénování.

### <a name="test-the-new-pattern-for-role-data-extraction"></a>Testování nový vzor pro extrakci dat role
Ověřte nový vzor testu.

1. Vyberte **Test** v horním panelu. 
2. Zadejte utterance `Move Tammi Carlson from Bellingham to Winthrop`.
3. Vyberte **zkontrolujte, jestli se** podle výsledku zobrazíte výsledky testů pro entitu a záměr.

    ![Snímek obrazovky s novou entitu s rolemi](./media/luis-tutorial-pattern/test-with-roles.png)

    Entity jsou nalezena jako první, pak se najde vzorek, která záměr. Pokud máte výsledku testu, kde entity, které nejsou zjištěny, a proto není nalezen vzor, budete muset přidat další příklad projevy na záměr (ne vzor). 

4. Zavřít panel testů tak, že vyberete **testování** tlačítko v horním navigačním panelu.

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>Použití Pattern.any entity najít volného tvaru entity ve vzoru
Tato aplikace Lidskézdroje také pomáhá zaměstnancům najít firemní formuláře. Mnohé z formuláře mají názvy, které se liší v délce. Různé délky zahrnuje fráze, které by mohly uvádět LUIS o ukončení název formuláře. Použití **Pattern.any** entitu ve vzorci můžete zadat začátek a konec název formuláře, LUIS správně extrahuje název formuláře. 

### <a name="create-a-new-intent-for-the-form"></a>Vytvořit nový záměr pro formulář
Vytvořte nový záměr pro projevy, které hledáte formuláře.

1. Vyberte **záměry** z levého navigačního panelu.

2. Vyberte **Create new intent** (Vytvořit nový záměr).

3. Pojmenujte novou záměr `FindForm`.

4. Přidáte příkladu utterance.

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![Snímek obrazovky s novou entitu s rolemi](./media/luis-tutorial-pattern/intent-findform.png)

    Název formuláře je `What to do when a fire breaks out in the Lab`. Utterance se s žádostí o umístění ve formě a je také s dotazem, který musí podepsat ověřování zaměstnance, přečtěte si ho. Bez Pattern.any entity by bylo obtížné pochopit, kde název formuláře skončí a extrahovat název formuláře jako entitu utterance.

### <a name="create-a-patternany-entity-for-the-form-title"></a>Vytvoření entity Pattern.any pro název formuláře
Entita Pattern.any umožňuje u entity různou délku. Funguje pouze v vzor protože vzor označuje začátek a konec entity. Pokud zjistíte, že váš model, pokud obsahuje Pattern.any, výpisy entity nesprávně, použijte [explicitní seznam](luis-concept-patterns.md#explicit-lists) abyste tento problém opravili. 

1. Vyberte **entity** v levém navigačním panelu.

2. Vyberte **Create new entity** (Vytvořit novou entitu). 

3. Název entity `FormName` s typem **Pattern.any**. V tomto kurzu konkrétní nepotřebujete přidat všechny role v entitě.

    ![Obrázek dialogového okna pro název entity a typ entity](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Přidejte vzor, který se používá Pattern.any

1. Vyberte **vzory** z levé navigace.

2. Vyberte **FindForm** záměr.

3. Zadejte šablonu utterance pomocí nové entity `Where is the form {FormName} and who needs to sign it after I read it?`

    ![Snímek obrazovky utterance šablony pomocí pattern.any entity](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. Aplikace pro novou záměr, entit a způsob trénování.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testování nový vzor pro extrakci dat volného tvaru
1. Vyberte **testování** v horním panelu otevřete panel testů. 

2. Zadejte utterance `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`.

3. Vyberte **zkontrolujte, jestli se** podle výsledku zobrazíte výsledky testů pro entitu a záměr.

    ![Snímek obrazovky utterance šablony pomocí pattern.any entity](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    Entita je nalezena jako první, pak se najde vzorek, která záměr. Pokud máte výsledku testu, kde entity, které nejsou zjištěny, a proto není nalezen vzor, budete muset přidat další příklad projevy na záměr (ne vzor).

4. Zavřít panel testů tak, že vyberete **testování** tlačítko v horním navigačním panelu.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Uděláte to tak, vyberte tři tečky (***...*** ) napravo od názvu aplikace v seznamu aplikací vyberte **odstranit**. V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přečtěte si osvědčené postupy pro aplikace LUIS](luis-concept-best-practices.md)