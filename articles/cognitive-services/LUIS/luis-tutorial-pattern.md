---
title: Kurz pomocí vzorce ke zlepšení LEOŠ předpovědi - Azure | Microsoft Docs
titleSuffix: Azure
description: V tomto kurzu použijte vzor pro záměry LEOŠ záměr a entity předpovědi.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: d2d680a1e2749c6e0a5b17876915c5c0d86eb420
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "35343920"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>Kurz: Vzory použít ke zlepšení předpovědi

V tomto kurzu pomocí vzorce zvětšit záměr a entity předpovědi.  

> [!div class="checklist"]
* Jak identifikovat, vzor pomohou vaší aplikace
* Postup vytvoření vzor 
* Postup použití předem a vlastní entity ve tvaru 
* Postup ověření vylepšení předpovědi vzor
* Postup přidání role na entitu najít kontextově na základě entity
* Postup přidání Pattern.any najít vlastní entity

V tomto článku budete potřebovat bezplatný [LEOŠ] [ LUIS] účet, aby bylo možné vytvořit aplikace LEOŠ.

## <a name="import-humanresources-app"></a>Import Lidskézdroje aplikace
V tomto kurzu importuje Lidskézdroje aplikace. Aplikace má tři záměry: None, GetEmployeeOrgChart, GetEmployeeBenefits. Aplikace má dvě entity: předem číslo a zaměstnanci. Zaměstnanec entita je jednoduché entity k extrakci název zaměstnance. 

1. Vytvoření nového souboru LEOŠ aplikace a pojmenujte ji `HumanResources.json`. 

2. Zkopírujte následující definici aplikace do souboru:

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. Na LEOŠ **aplikace** vyberte **importovat nové aplikace**. 

4. V **novou aplikaci Import** dialogovém okně, vyberte `HumanResources.json` souborů, které jste vytvořili v kroku 1.

5. Vyberte **GetEmployeeOrgChart** záměr a potom změnu z **entity zobrazení** k **tokeny zobrazení**. Několik utterances příkladu jsou uvedeny. Každý utterance obsahuje název, který je entita zaměstnanců. Všimněte si, že každý název je jiné a zda je pro každý utterance jiné uspořádání pomocí jiné volby slov. Tyto rozdíly v pomáhá LEOŠ další širokou škálu utterances.

    ![Snímek obrazovky záměr stránka s přepínat stav zobrazení entit](media/luis-tutorial-pattern/utterances-token-view.png)

6. Vyberte **cvičení** v horním navigačním panelu ke cvičení aplikace. Počkejte na panelu zelená úspěch.

7. Vyberte **Test** v horním panelu. Zadejte `Who does Patti Owens report to?` a pak vyberte zadejte. Vyberte **kontroly** pod utterance zobrazíte další informace o nástroji pro testování.
    
    V utterance příklad ještě nebyla použita jméno zaměstnance, Patti Owens. Toto je test ke zjištění, jak dobře LEOŠ naučili tento utterance je `GetEmployeeOrgChart` záměr a entity zaměstnance by měla být `Patti Owens`. Výsledek by měla být menší než 50 % (. 50) pro `GetEmployeeOrgChart` záměr. Když je správný, skóre je nízký. Je zaměstnanec také správně identifikován jako `Patti Owens`. Vzory zvýšení skóre Tato počáteční předpovědi. 

    ![Snímek obrazovky testovací panely](media/luis-tutorial-pattern/original-test.png)

8. Zavřete testovací panelu výběrem **testování** tlačítko v horním navigačním panelu. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Vzory naučit LEOŠ běžné utterances s méně příklady
Vzhledem k povaze domény lidských zdrojů jsou několik běžné způsoby zeptat se na zaměstnance vztahy v organizacích. Příklad:

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

Tyto utterances jsou příliš zavřete na určení kontextové jedinečnosti jednotlivých bez zadání mnoho utterance příklady. Přidáním vzor záměrem LEOŠ zjišťuje obecné utterance vzory pro záměrem bez nutnosti zadávat mnoho utterance příklady. 

Příklad šablony utterances pro tento záměrné zahrnout:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

Vzor je kombinací odpovídající regulárnímu výrazu a strojové učení. Potom zadejte některé šablonu utterance příklady LEOŠ další vzorek. Tyto příklady, společně s úmyslem utterances poskytnout LEOŠ lepší pochopení, o jaké utterances přizpůsobit záměr a kde, v rámci utterance, entita existuje. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>Přidat šablonu utterances

1. V levém navigačním panelu v části **zlepšit výkon aplikace**, vyberte **vzory** z levé navigaci.

2. Vyberte **GetEmployeeOrgChart** záměr, pak zadejte následující utterances šablony, jeden po druhém, výběr zadejte po každé šablony utterance:

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    `{Employee}` Syntaxe označí entitu umístění v rámci šablony utterance jako a taky které entitě je. 

    ![Snímek obrazovky zadání utterances šablony pro záměru](./media/luis-tutorial-pattern/enter-pattern.png)

3. Vyberte **Train** v horním navigačním panelu. Počkejte na panelu zelená úspěch.

4. Vyberte **Test** v horním panelu. Zadejte `Who does Patti Owens report to?` v textovém poli. Vyberte zadejte. Toto je stejný utterance testována v předchozí části. Výsledek by měla být vyšší `GetEmployeeOrgChart` záměr. 

    Skóre je nyní mnohem lepší. LEOŠ zjistili relevantní pro záměr vzor bez zadání mnoho příkladů.

    ![Snímek obrazovky testovací panel s vysokou skóre způsobit](./media/luis-tutorial-pattern/high-score.png)

    Entita nachází nejprve, pak se najde vzorek, označující záměr. Pokud máte výsledků testů, kde entity není zjištěna, a proto není nalezen vzor, budete muset přidat další příklad utterances na záměr (ne vzor). 

5. Zavřete testovací panelu výběrem **testování** tlačítko v horním navigačním panelu.

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>Použijte entity s rolí ve tvaru
LEOŠ aplikace se používají pro zaměstnance přesunout z jednoho umístění do druhého. Je utterance příklad `Move Bob Jones from Seattle to Los Colinas`. Každé umístění utterance má jiný význam. Praha je původní umístění a Los Colinas cílové umístění pro přesunutí. Aby bylo možné odlišit od těchto umístěních ve tvaru, v následujících částech vytvoříte jednoduché entity pro umístění s dvě role: původní a cílové. 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>Vytvořit nový záměr pro přesun osoby a prostředky
Vytvořte nový záměr pro všechny utterances, které jsou o přesunutí osoby nebo prostředky.

1. Vyberte **záměry** z levé navigaci
2. Vyberte **vytvořit nové záměr**
3. Název nové záměru `MoveAssetsOrPeople`
4. Přidejte utterances příklad:

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![Snímek obrazovky příklad utterance pro MoveAssetsOrPeople záměru](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    Účelem utterances příklad je poskytnout dostatek příklady. Pokud později v testu, nebyla zjištěna entity umístění, a proto není zjištěna vzor, vraťte se k tomuto kroku a přidejte další příklady. Potom trénování a testování znovu. 

5. Označte entity v příkladu utterances s entitou zaměstnanec výběr křestní jméno pak příjmení v utterance a poté v seznamu vyberete zaměstnanec entity.

    ![Snímek obrazovky utterances v MoveAssetsOrPeople označené jako zaměstnanec entity](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. Vyberte text `portland` v utterance `move travis hinton from portland to orlando`. V dialogovém okně automaticky otevírané okno, zadejte nový název entity `Location`a vyberte **vytvořit novou entitu**. Vyberte **jednoduché** typem entity a vyberte **provádí**.

    ![Snímek obrazovky vytváření nové entity v umístění](./media/luis-tutorial-pattern/create-new-location-entity.png)

    Označte zbytek názvy umístění v utterances. 

    ![Snímek obrazovky všechny entity označena](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    Vzor word výběr a pořadí je zřejmé předchozí obrázek. Pokud byste byli **není** pomocí vzorce, a utterances na záměr zřejmé vzor, který je dobrá indikace toho by měla být pomocí vzorce. 

    Pokud očekáváte, že širokou škálu utterances místo vzor, by se mohlo jednat utterances nesprávný příklad. V takovém případě by mohlo nejrůznějšími utterances termín nebo word volba, délka utterance a entity umístění. 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>Přidání role umístění entity 
Role dá použít jenom v případě vzorů. Přidání rolí původní a cílové umístění entitě. 

1. Vyberte **entity** v levé navigaci, pak **umístění** ze seznamu entit.

2. Přidat `Origin` a `Destination` rolí k entitě.

    ![Snímek obrazovky novou entitu s rolemi](./media/luis-tutorial-pattern/location-entity.png)

    Role nejsou označeny na stránce MoveAssetsOrPeople záměrné, protože rolí neexistují v záměrné utterances. Existují jenom na utterances vzor šablony. 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>Přidat utterances šablony, které používá role umístění a cíl
Přidejte utterances šablony, které používají nové entity.

1. Vyberte **vzory** z levé navigaci.

2. Vyberte **MoveAssetsOrPeople** záměr.

3. Zadejte nové šablony utterance pomocí nové entity `Move {Employee} from {Location:Origin} to {Location:Destination}`. Syntaxe pro entity a role uvnitř utterance šablony je `{entity:role}`.

    ![Snímek obrazovky novou entitu s rolemi](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. Cvičení aplikaci pro nový záměr, entitu a vzor.

### <a name="test-the-new-pattern-for-role-data-extraction"></a>Otestovat novou vzor pro extrakci data role
Ověřte nový vzorek se testu.

1. Vyberte **Test** v horním panelu. 
2. Zadejte utterance `Move Tammi Carlson from Bellingham to Winthrop`.
3. Vyberte **kontroly** za výsledek, který má-li zobrazit výsledky testování pro entitu a záměr.

    ![Snímek obrazovky novou entitu s rolemi](./media/luis-tutorial-pattern/test-with-roles.png)

    Entity, které se nacházejí nejprve, pak se najde vzorek, označující záměr. Pokud máte výsledků testů, kde entity, které nejsou zjištěny, a proto není nalezen vzor, budete muset přidat další příklad utterances na záměr (ne vzor). 

4. Zavřete testovací panelu výběrem **testování** tlačítko v horním navigačním panelu.

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>Použijte Pattern.any entity najít vlastní entity ve tvaru
Tuto aplikaci Lidskézdroje také pomáhá zaměstnanci najít forms společnosti. Mnoho formuláře má produkty, které jsou různých délku. Různé délky zahrnuje frází, které by mohly uvádět LEOŠ o které končí název formuláře. Použití **Pattern.any** entity ve tvaru umožňuje zadat začátek a konec název formuláře, LEOŠ správně extrahuje název formuláře. 

### <a name="create-a-new-intent-for-the-form"></a>Vytvořit nový záměr pro daný formulář
Vytvořte nový záměr pro utterances, které hledáte formulářů.

1. Vyberte **záměry** z levé navigaci.

2. Vyberte **vytvořit nové záměr**.

3. Název nové záměr `FindForm`.

4. Přidáte utterance příklad.

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![Snímek obrazovky novou entitu s rolemi](./media/luis-tutorial-pattern/intent-findform.png)

    Název formuláře je `What to do when a fire breaks out in the Lab`. Utterance se žádostí o umístění ve formě a je také s dotazem, kdo potřebuje podepište ho ověřování zaměstnanec, přečtěte si ho. Bez Pattern.any entita je obtížné zjistit, kde formuláře název končí a rozbalte název formuláře jako entita utterance.

### <a name="create-a-patternany-entity-for-the-form-title"></a>Vytvořit entitu Pattern.any pro nadpis formuláře
Entita Pattern.any umožňuje entity různé délky. Pracuje pouze ve tvaru protože vzoru označuje začátek a konec entity. Pokud zjistíte, že vaše vzor, pokud obsahuje Pattern.any, extrahuje entity nesprávně, použijte [explicitní seznam](luis-concept-patterns.md#explicit-lists) abyste tento problém opravili. 

1. Vyberte **entity** v levém navigačním panelu.

2. Vyberte **vytvořit novou entitu**. 

3. Název entity `FormName` s typem **Pattern.any**. V tomto kurzu konkrétní není potřeba přidat do entity žádné role.

    ![Obrázek dialogového okna pro název entity a typ entity](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Přidat vzor, který používá Pattern.any

1. Vyberte **vzory** z levé navigaci.

2. Vyberte **FindForm** záměr.

3. Zadejte šablonu utterance, pomocí nové entity `Where is the form {FormName} and who needs to sign it after I read it?`

    ![Snímek obrazovky utterance šablony pomocí pattern.any entity](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. Cvičení aplikaci pro nový záměr, entitu a vzor.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Otestovat novou vzor pro extrakci vlastní data
1. Vyberte **testování** z horním panelu otevřete panel testu. 

2. Zadejte utterance `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`.

3. Vyberte **kontroly** za výsledek, který má-li zobrazit výsledky testování pro entitu a záměr.

    ![Snímek obrazovky utterance šablony pomocí pattern.any entity](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    Entita nachází nejprve, pak se najde vzorek, označující záměr. Pokud máte výsledků testů, kde entity, které nejsou zjištěny, a proto není nalezen vzor, budete muset přidat další příklad utterances na záměr (ne vzor).

4. Zavřete testovací panelu výběrem **testování** tlačítko v horním navigačním panelu.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud již nepotřebujete, odstraňte aplikaci LEOŠ. To pokud chcete udělat, vyberte v seznamu aplikací, vyberte v nabídce tři tečky (...) vpravo od názvu aplikace **odstranit**. V místním dialogovém okně **aplikaci odstranit?**, vyberte **Ok**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Pomocí seznamu frázi ke zlepšení předpovědi](luis-tutorial-interchangeable-phrase-list.md)

[LUIS]: luis-reference-regions.md