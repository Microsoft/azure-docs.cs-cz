---
title: Přidání entity do aplikace LUIS
titleSuffix: Azure Cognitive Services
description: Přidání entity (klíčových dat v doméně vaší aplikace) v aplikacích Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: e82955da24e127e5536c2e40ad2cccf07c5fa173
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031998"
---
# <a name="manage-entities"></a>Správa entit
Po identifikaci vaší aplikace [záměry](luis-concept-intent.md), budete muset [popisek příklad projevy](luis-concept-utterance.md) s [entity](luis-concept-entity-types.md). Entity jsou důležité části příkaz nebo dotaz a může být důležitá pro vaši klientskou aplikaci k provedení svých úkolů. 

Můžete přidat, upravit nebo odstranit entity v aplikaci prostřednictvím **seznam entit** na **entity** stránky. Služba LUIS nabízí dva hlavní typy entit: [předem připravených entit](luis-reference-prebuilt-entities.md)a vlastní entity.

V následujících částech jsou k dispozici v aplikaci LUIS, pouze z **sestavení** oddílu. **Sestavení** odkaz bude v horním navigačním panelu. Jednou uvnitř **sestavení** vyberte **entity** z levé navigační nabídce. Po přidání entity do aplikace, pokud je entita se naučili počítače, můžete [Popisek entity](luis-how-to-add-example-utterances.md) uvnitř utterance. Po školení a publikování aplikace může přijímat entity data [extrahovat](luis-concept-data-extraction.md) z do predikce. 

## <a name="add-prebuilt-entity"></a>Přidat předem připravených entit
Předem připravených entit, které jsou definovány v [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text) projektu. Jsou přidány do aplikace běžné předem připravených entit *číslo* a *datetimeV2*. 

1. Ve vaší aplikaci z **sestavení** části a potom klikněte na tlačítko **entity** na levém panelu.
 
2. Na **entity** stránce **spravovat předem připravených entit**.

3. V **přidat nebo odebrat předem připravených entit** dialogové okno, vyberte **číslo** a **datetimeV2** předem připravených entit. Potom vyberte **Done** (Hotovo).

    ![Dialogové okno snímek obrazovky přidání předem připravených entit](./media/add-entities/list-of-prebuilt-entities.png)

    Zobrazit [extrakce dat](luis-concept-data-extraction.md#prebuilt-entity-data) Další informace o extrahování předem připravených entit z koncového bodu odpověď na dotaz JSON.

## <a name="add-simple-entities"></a>Přidání jednoduchého entit
Jednoduché entita je obecné entity, která popisuje jeden koncept. 

1. Ve vaší aplikaci z **sestavení** části a potom klikněte na tlačítko **entity** v levém panelu a pak vyberte **vytvořit novou entitu**.

2. V místním dialogovém okně zadejte `Airline` v **název Entity** vyberte **jednoduché** z **typ Entity** seznamu a pak vyberte **provádí**.

    ![Snímek obrazovky dialogového okna pro vytvoření jednoduché entity letecká společnost](./media/add-entities/create-simple-airline-entity.png)

    Zobrazit [extrakce dat](luis-concept-data-extraction.md#simple-entity-data) Další informace o extrahování jednoduchou entitu z koncového bodu odpověď na dotaz JSON. Vyzkoušejte jednoduchou entitu [rychlý Start](luis-quickstart-primary-and-secondary-data.md) Další informace o tom, jak použít jednoduché entity.

## <a name="add-regular-expression-entities"></a>Přidání entit regulárního výrazu
Regulární výraz entita se používá vyžádá si data z utterance podle regulárních výrazů, které zadáte. 

1. Ve vaší aplikaci, vyberte **entity** v levém navigačním panelu a pak vyberte **vytvořit novou entitu**.

2. V místním dialogovém okně zadejte `AirFrance Flight` v **název Entity** vyberte **regulárního výrazu** z **typ Entity** seznamu, zadejte regulární výraz `AFR[0-9]{3,4}`a pak vyberte **provádí**. 

    Tento regulární výraz let AirFrance očekává tři znaky, doslova `AFR`, pak 3 nebo 4 číslic. Číslice může být jakékoli číslo od 0 do 9. Odpovídá regulárnímu výrazu AirFrance letu čísla jako: "AFR101", "ARF1302" a "AFR5006". Zobrazit [extrakce dat](luis-concept-data-extraction.md) Další informace o extrahování entity z koncového bodu odpověď na dotaz JSON.

    ![Obrázek dialogového okna mohla vytvořit entita regulárního výrazu](./media/add-entities/regex-entity-create-dialog.png)

    Zobrazit [extrakce dat](luis-concept-data-extraction.md#regular-expression-entity-data) Další informace o extrahování entity regulárního výrazu z koncového bodu odpověď na dotaz JSON. Zkuste entity regulárního výrazu [rychlý Start](luis-quickstart-intents-regex-entity.md) získat další informace o tom, jak používat entity regulární výraz.

## <a name="add-hierarchical-entities"></a>Přidání hierarchické entit
Hierarchické entity je kategorie kontextově zkušenosti a související entity. V následujícím příkladu obsahuje původní a cílové umístění. 

V utterance `Book 2 tickets from Seattle to Cairo`, Seattle, je původní umístění a Cairo je cílové umístění. Každé umístění je kontextově různých a zkušenosti z pořadí slov a požadované aplikace word v utterance.

Přidání hierarchické entit, proveďte následující kroky: 

1. Ve vaší aplikaci, vyberte **entity** v levém navigačním panelu a pak vyberte **vytvořit novou entitu**.

2. V místním dialogovém okně zadejte `Location` v **název Entity** a potom vyberte **hierarchické** z **typ Entity** seznamu.

    ![Přidání hierarchické entity](./media/add-entities/hier-location-entity-creation.png)

3. Vyberte **přidat podřízenou položku**, a potom zadejte "Zdroj" **podřízené č. 1** pole. 

4. Vyberte **přidat podřízenou položku**, a potom zadejte "Cíl" **podřízené č. 2** pole. Vyberte **Done** (Hotovo).
5. 
    >[!NOTE]
    >Pokud chcete odstranit dítě, vyberte ikony koše vedle sebe.

    >[!CAUTION]
    >Podřízené entity názvy musí být jedinečný ve všech entit v jediné aplikaci. Dva různé hierarchické entity nemůže obsahovat podřízené entity se stejným názvem. 

    Zobrazit [extrakce dat](luis-concept-data-extraction.md#hierarchical-entity-data) Další informace o extrahování hierarchické entity z koncového bodu odpověď na dotaz JSON. Zkuste hierarchické entity [rychlý Start](luis-quickstart-intent-and-hier-entity.md) získat další informace o tom, jak používat hierarchické entity.

## <a name="add-composite-entities"></a>Přidat složené entity
Je možné definovat vztahy mezi několika existující entity vytvořením složený entity. V následujícím příkladu obsahuje počet lístky, původu a cílové umístění. 

V utterance `Book 2 tickets from Seattle to Cairo`, číslo 2 je odpovídají předem připravených entit, Seattle, je původní umístění a Cairo je cílové umístění. Každá entita je součástí větší, nadřazená entita po vytvoření složeného entity.

1. Ve své aplikaci přidat předem připravených entit **číslo**. Pokyny najdete v tématu [přidat předem připravených entit](#add-prebuilt-entity). 

2. Přidání entity hierarchické `Location`, včetně podtyp: `origin`, `destination`. Další pokyny najdete v tématu [přidat hierarchické entity](#add-hierarchical-entities). 

3. Vyberte **entity** v levém navigačním panelu a pak vyberte **vytvořit novou entitu**.

4. V místním dialogovém okně zadejte `TicketsOrder` v **název Entity** a potom vyberte **složené** z **typ Entity** seznamu.

5. Vyberte **přidat podřízenou položku** přidáte novou podřízenou položku.

6. V **podřízené č. 1**, vyberte entitu **číslo** ze seznamu.

7. V **podřízené č. 2**, vyberte entitu **Location::Origin** ze seznamu. 

8. V **podřízené 3**, vyberte entitu **Location::Destination** ze seznamu. 

9. Vyberte **Done** (Hotovo).

    ![Obrázek dialogového okna pro vytvoření složeného entity](./media/add-entities/ticketsorder-composite-entity.png)

    >[!NOTE]
    >Pokud chcete odstranit podřízený, vyberte tlačítko koše vedle sebe.

    Zobrazit [extrakce dat](luis-concept-data-extraction.md#composite-entity-data) Další informace o extrahování entita složený z koncového bodu odpověď na dotaz JSON. Zkuste složený entity [kurzu](luis-tutorial-composite-entity.md) Další informace o tom, jak použít složené entity.


## <a name="add-patternany-entities"></a>Přidání Pattern.any entit
[Pattern.Any](luis-concept-entity-types.md) entity platí jenom v [vzory](luis-how-to-model-intent-pattern.md). Tato entita pomáhá najít koncové entity různé délky a výběr slov LUIS. Protože tato entita se používá ve vzorku, LUIS ví, je-li koncové entity utterance.

Pokud má aplikace `FindBookInfo` záměru, název knihy může kolidovat s záměru předpovědi. Aby bylo možné vyjasnit, která slova jsou v názvu adresáře, použijte Pattern.any v rámci vzoru. Predikce služby LUIS začíná utterance. Nejprve utterance je zaškrtnuto a pro entity, když entity, které najde, pak vzor je zaškrtnuto a. 

V utterance `Who wrote the book Ask and when was it published?`, název knihy, požádejte, je velmi obtížné, protože není kontextově jasné, kde název končí a kde začíná rest utterance. Názvů knih může být jakékoli pořadí slov jediné slovo, včetně složitých frází pomocí interpunkce a nesmyslná řazení slov. Vzoru můžete vytvořit entitu kde může být extrahována entity úplné a přesné. Po nalezení název knihy `FindBookInfo` záměr očekává se, že vzhledem k tomu, který je cílem pro vzor.

1. Ve vaší aplikaci z **sestavení** části a potom klikněte na tlačítko **entity** v levém panelu a pak vyberte **vytvořit novou entitu**.

2. V **Přidat entitu** dialogovém okně `BookTitle` v **název Entity** a vyberte **Pattern.any** jako **typ Entity**.
 
    ![Snímek obrazovky vytváření pattern.any entity](./media/add-entities/create-pattern-any-entity.png)

    Použití pattern.any entity, přidejte vzor na **vzory** stránky (v části **zvýšit výkon aplikace** část) se syntaxí správné složenou závorku, jako "pro **{BookTitle}** kdo je autorkou? ".

    Zobrazit [extrakce dat](luis-concept-data-extraction.md#patternany-entity-data) Další informace o extrahování Pattern.any entity z koncového bodu odpověď na dotaz JSON. Zkuste [vzor](luis-tutorial-pattern.md) kurzu se dozvíte další informace o tom, jak používat Pattern.any entity.

Pokud zjistíte, že váš model, pokud obsahuje Pattern.any, výpisy entity nesprávně, použijte [explicitní seznam](luis-concept-patterns.md#explicit-lists) abyste tento problém opravili. 

## <a name="add-role-to-pattern-based-entity"></a>Přidání role na základě vzoru entitu
Role je pojmenovaný podtypem typu entity na základě kontextu. Je srovnatelná s hodnotou [hierarchické](#add-hierarchical-entities) entity, ale role se používají v [vzory](luis-how-to-model-intent-pattern.md). 

Má například roviny lístek *původu Město* a *město cílové*, ale oba jsou měst. Služba LUIS zjistí, že oba jsou měst a můžete určit zdroj a cíl měst v kontextu pořadí slov a výběr slov. 

Syntaxe pro roli je **{name: Role název Entity}** kde je název entity následovaný dvojtečkou a potom název role. Například "Book lístek z {umístění: Origin} a {umístění: Destination}".

1. Ve vaší aplikaci z **sestavení** a potom vyberte **entity** na levém panelu.

2. Vyberte **Create new entity** (Vytvořit novou entitu). Zadejte název `Location`. Vyberte typ **jednoduché** a vyberte **Hotovo**

3. Vyberte **entity** na levém panelu, vyberte novou entitu **umístění** vytvořili v kroku 2.

4. V **název Role** textového pole zadejte název role `Origin` a zadejte. Přidejte druhý název role `Destination`. Jako příklad výlet roviny může mít původ a cílové město. Tyto dvě role jsou "Zdroj" a "Cíl".

    ![Snímek obrazovky přidání role zdroje do umístění entity](./media/add-entities/roles-enter-role-name-text.png)

    Zobrazit [extrakce dat](luis-concept-data-extraction.md) Další informace o extrahování role z koncového bodu odpověď na dotaz JSON. Projděte si kurz vzor získat další informace o tom, jak používat Pattern.any entity.

## <a name="add-list-entities"></a>Přidání seznamu entit
Seznam entit představují pevné, uzavřené sadu souvisejících slov ve vašem systému. 

Seznam entity drinky den před začátkem, máte dvě normalizované hodnoty: vod a soda pop. Každý normalizovaný název má synonym. Pro vody jsou synonyma paušální H20, automobilový průmysl. Synonyma pro soda pop, jsou ovoce, coly, zázvor. Není nutné znát všechny hodnoty při vytvoření entity. Můžete přidat více až po kontrole projevy uživatelů s synonym.

|Normalizovaný název|Synonyma|
|--|--|
|Vodohospodářství|H20, plynu, paušální|
|Soda pop|Ovoce, coly, zázvor|

1. Ve vaší aplikaci z **sestavení** části a potom klikněte na tlačítko **entity** v levém panelu a pak vyberte **vytvořit novou entitu**.

2. V **Přidat entitu** dialogovém okně `Drinks` v **název Entity** a vyberte **seznamu** jako **typ Entity**. Vyberte **Done** (Hotovo).
 
    ![Obrázek dialogového okna vytváření drinky den před začátkem seznam entit](./media/add-entities/menu-list-dialog.png)
  
3.  Na stránce seznamu entit umožňuje přidat normalizované názvy. V **hodnoty** textového pole zadejte položku pro seznam, jako například `Water` pro nápoje seznamu stiskněte klávesu Enter na klávesnici. 

    ![Snímek obrazovky nápojů entitou seznamu vodou Normalizovaná hodnota v textovém poli](./media/add-entities/entity-list-normalized-name.png)

4. Napravo od normalizovanou hodnotu **vody**, zadejte synonyma `h20`, `flat`, a `gas`, stisknutím klávesy Enter na klávesnici po každé položky.

    ![Snímek obrazovky nápojů seznamu entity s položkami synonymum pro vody zvýrazněnou](./media/add-entities/menu-list-synonyms.png)

5. Pokud chcete více normalizované položek pro seznam, vyberte **doporučujeme** zobrazíte možnosti z [sémantické slovníku](luis-glossary.md#semantic-dictionary).

    ![Snímek obrazovky nepijí entitou seznamu se zvýrazněnou povinných položek](./media/add-entities/entity-list-recommended-list.png)

6. Vyberte položku v seznamu doporučené a přidejte ho jako normalizované hodnoty, nebo vyberte **přidejte všechny** přidat všechny položky. 

    ![Snímek obrazovky nepijí seznamu entita s Doporučené položky z až a všechny zvýrazněným tlačítkem Přidat](./media/add-entities/list-entity-add-suggestions.png)

    Zobrazit [extrakce dat](luis-concept-data-extraction.md#list-entity-data) Další informace o extrahování seznam entit z koncového bodu odpověď na dotaz JSON. Zkuste [rychlý Start](luis-quickstart-intent-and-list-entity.md) získat další informace o tom, jak používat seznam entit.

## <a name="import-list-entity-values"></a>Import hodnoty seznamu entit
Hodnoty můžete importovat do existující entity seznamu.

 1. Na stránce entity seznamu vyberte **Import uvádí**.

 2. V **nové položky importu** dialogu **zvolit soubor** a vyberte soubor JSON, který obsahuje seznam.

    ![Snímek obrazovky importu seznam entit hodnoty vyskakovací dialogové okno](./media/add-entities/menu-list-import-json-dialog-with-file.png)

    >[!NOTE]
    >Služba LUIS importuje soubory s rozšíření ".json" pouze.

 3. Další informace o syntaxi seznamu podporovaných ve formátu JSON, vyberte **přečtěte si víc o seznam podporované syntaxe** dialogového okna rozbalte a zobrazit příklad povolené syntaxe. Do dialogového okna sbalit a skrýt syntaxe, vyberte název odkazu.

 4. Vyberte **Done** (Hotovo).

    Příklad platný kód json pro **barvy** entitou seznamu se zobrazí v následujícím kódu ve formátu JSON:

    ```
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

## <a name="edit-entity-name"></a>Upravit název entity
1. Na **entity** stránka seznamu, vyberte entitu v seznamu. Tím přejdete do **Entity** stránky.

2. Na **Entity** stránky, můžete upravit název entity tak, že vyberete ikonu úprav vedle názvu entity. Typ entity není upravovat. 

## <a name="delete-entity"></a>Odstranit entitu

Na **Entity** stránky, vyberte **odstranit Entity** tlačítko. Vyberte **Ok** v potvrzovací zprávě potvrďte odstranění.
 
![Stránka entity snímku obrazovky z umístění se zvýrazněným tlačítkem odstranit entitu](./media/add-entities/entity-delete.png)

>[!NOTE]
>* Odstraněním hierarchické entity se odstraní všechny jeho podřízené entity.
>* Odstranění složeného entity odstraní pouze složeného přeruší složené vztah, ale nedojde k odstranění entity, které ho tvoří.

## <a name="changing-entity-type"></a>Změna typu entity
Služba LUIS neumožňuje změnit typ entity, protože nebude vědět, co se má přidat nebo odebrat k vytvoření této entity. Chcete-li změnit typ, je lepší vytvořit novou entitu správného typu s mírně odlišný název. Po vytvoření entity v každé utterance, odeberte starý název s popiskem entity a přidejte nový název entity. Jakmile se všechny projevy mít relabeled, odstraňte staré entitu. 

## <a name="create-a-pattern-from-an-utterance"></a>Ze utterance společně tvoří masku
Zobrazit [přidat vzorek z existující utterance na stránce záměr nebo entity](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="search-utterances"></a>Projevy vyhledávání
Můžete vyhledávat a filtrovat projevy s ikonou lupy na panelu nástrojů. 

## <a name="train-your-app-after-changing-model-with-entities"></a>Trénování aplikace po změně modelu s entitami
Po přidání, úprava nebo odebrání entity, [trénování](luis-how-to-train.md) a [publikovat](luis-how-to-publish-app.md) vaší aplikace pro vaše změny ovlivnit dotazy koncový bod. 

## <a name="next-steps"></a>Další postup
Teď, když jste přidali projevy záměry a entity, budete mít základní aplikaci LUIS. Zjistěte, jak [trénování](luis-how-to-train.md), [testování](luis-interactive-test.md), a [publikovat](luis-how-to-publish-app.md) vaší aplikace.
 
