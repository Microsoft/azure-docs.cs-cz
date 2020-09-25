---
title: Typy entit – LUIS
description: Entita extrahuje data z utterance uživatele v předpokládaném modulu runtime. _Volitelným_a sekundárním účelem je zvýšit předpověď záměru nebo jiných entit pomocí entity jako funkce.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398d18642052726af4d4920443bad515ec0b5bef
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316558"
---
# <a name="extract-data-with-entities"></a>Extrakce dat s entitami

Entita extrahuje data z utterance uživatele v předpokládaném modulu runtime. _Volitelným_a sekundárním účelem je zvýšit předpověď záměru nebo jiných entit pomocí entity jako funkce.

Existuje několik typů entit:

* [entita strojového učení](reference-entity-machine-learned-entity.md) – jedná se o primární entitu. Před použitím jiných entit byste měli navrhnout schéma s tímto typem entity.
* Nestrojové učení, které se používá jako požadovaná [funkce](luis-concept-feature.md) – pro přesné shody textu, porovnávání vzorů nebo zjišťování podle předem připravených entit
* [Vzor. any](#patternany-entity) – pro extrakci textu volných formulářů, jako jsou názvy knih ze [vzoru](reference-entity-pattern-any.md)

entity strojového učení poskytují nejširší škálu voleb pro extrakci dat. Entity bez strojového učení fungují podle textu a používají se jako [požadovaná funkce](#design-entities-for-decomposition) pro entitu nebo záměr strojového učení.

## <a name="entities-represent-data"></a>Entity reprezentují data

Entity jsou data, která chcete z utterance vyžádat, jako jsou názvy, data, názvy produktů nebo libovolná velká skupina slov. Utterance může zahrnovat mnoho entit nebo žádný vůbec. Klientská aplikace _může_ k provedení úlohy potřebovat data.

Entity musí být označeny konzistentně ve všech školicích projevy pro každý záměr v modelu.

 Můžete definovat vlastní entity nebo používat předem připravené entity k ukládání času pro běžné koncepty, jako je [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinální](luis-reference-prebuilt-ordinal.md)číslo, [e-mail](luis-reference-prebuilt-email.md)a [telefonní číslo](luis-reference-prebuilt-phonenumber.md).

|Promluva|Entita|Data|
|--|--|--|
|Koupit 3 lístky do Praha|Předem připravené číslo<br>Cíl|3<br>New York|


### <a name="entities-are-optional-but-recommended"></a>Entity jsou volitelné, ale Doporučené

I když jsou požadované [záměry](luis-concept-intent.md) , jsou entity volitelné. Pro každý koncept ve vaší aplikaci nemusíte vytvářet entity, ale jenom pro ty, které klientská aplikace potřebuje k datům nebo které entita funguje jako pomocný parametr nebo signál k jiné entitě nebo záměru.

Jak se vyvíjí vaše aplikace a identifikují se nové potřeby dat, můžete do modelu LUIS přidat vhodné entity později.

<a name="entity-compared-to-intent"></a>

## <a name="entity-represents-data-extraction"></a>Entita představuje extrakci dat

Entita představuje koncept dat _uvnitř utterance_. Záměr klasifikuje _celou utterance_.

Vezměte v úvahu následující čtyři projevy:

|Promluva|Předpokládané záměr|Extrahované entity|Vysvětlení|
|--|--|--|--|
|Nápověda|Nápověda|-|Nic k extrakci.|
|Poslat něco|sendSomething|-|Nic k extrakci. Model nemá požadovanou funkci pro extrakci `something` v tomto kontextu a není uveden žádný příjemce.|
|Poslat Bobovi a|sendSomething|`Bob`, `present`|Model se extrahuje `Bob` tak, že se přidá požadovaná funkce předem sestavené entity `personName` . Entita strojového učení se použila k extrakci `present` .|
|Poslat Bobovi pole čokolády|sendSomething|`Bob`, `box of chocolates`|Tyto dvě důležité části dat a byly `Bob` `box of chocolates` extrahovány entitami strojového učení.|

## <a name="label-entities-in-all-intents"></a>Označení entit ve všech záměrech

Entity extrahují data bez ohledu na předpokládaný záměr. Ujistěte se, že jste ve všech záměrech projevy popisky _All_ . `None`Záměru chybějícího označení entity způsobuje nejasnost, i když pro ostatní záměry existovalo ještě mnohem více školení projevy.

## <a name="design-entities-for-decomposition"></a>Entity návrhu pro rozložení

entity strojového učení umožňují návrh schématu aplikace pro rozložení a rozdělení velkého konceptu na subentity.

Návrh pro dekompozici umožňuje, aby LUIS vrátilo v klientské aplikaci hlubokou míru překladu entit. Díky tomu se vaše klientská aplikace může soustředit na obchodní pravidla a ponechání rozlišení dat LUIS.

Entita strojového učení se aktivuje na základě kontextu získaného prostřednictvím příkladu projevy.

[**entity strojového učení**](tutorial-machine-learned-entity.md) jsou extraktory na nejvyšší úrovni. Dílčí entity jsou podřízené entity entit strojového učení.

## <a name="effective-machine-learned-entities"></a>Efektivní entity pro strojové učení

Chcete-li efektivně sestavovat počítač se zjištěnými entitami:

* Štítky by měly být v souladu s těmito záměry. To zahrnuje i projevy, které zadáte do záměru **none** , který obsahuje tuto entitu. V opačném případě model nebude moci určit sekvence efektivně.
* Pokud jste získali entitu počítač s podentitami, ujistěte se, že různé objednávky a varianty entit a podentit jsou uvedeny v popisku projevy. Vzorový příklad projevy by měl zahrnovat všechny platné formuláře a zahrnout entity, které se zobrazí a jsou chybějící a také přeobjednány v rámci utterance.
* Měli byste se vyhnout přebudování entit na velmi pevně danou sadu. K překrytí **dojde,** když model nebude správně generalizovat a jedná se o běžný problém v modelech strojového učení. To znamená, že aplikace nebude správně fungovat na nových datech. V takovém případě byste měli měnit příklad projevy, aby se aplikace mohla zobecnit nad rámec omezených příkladů, které zadáte. Měli byste změnit různé podentity s dostatečnou změnou modelu tak, aby se místo pouze v zobrazených příkladech promyslelo více konceptů.

## <a name="effective-prebuilt-entities"></a>Efektivní předem připravené entity

Pro sestavování efektivních entit, které extrahují společná data, například těch, které poskytují [předem připravené entity](luis-reference-prebuilt-entities.md), doporučujeme následující postup.

Vylepšete extrakci dat tak, že jako funkci přinesete vlastní data do entity. Tímto způsobem se všechny další štítky z vašich dat učí v kontextu, kde ve vaší aplikaci existují jména osob.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typy entit

Podentita na nadřazený objekt by měla být entita strojového učení. Subentita může jako [funkci](luis-concept-feature.md)použít entitu bez strojového učení.

Vyberte entitu na základě toho, jak by měla být data extrahována a jak by měla být reprezentována po extrakci.

|Typ entity|Účel|
|--|--|
|[**Strojové učení**](tutorial-machine-learned-entity.md)|Extrahujte vnořená a složitá data získaná z příkladů s popisky. |
|[**Seznamu**](reference-entity-list.md)|Seznam položek a jejich synonym, které byly extrahovány s **přesnou shodou textu**|
|[**Vzor. any**](#patternany-entity)|Entita, kde najít konec entity, je obtížné určit, protože entita má volnou formu. K dispozici pouze ve [vzorcích](luis-concept-patterns.md).|
|[**Předem připravených**](luis-reference-prebuilt-entities.md)|Vyškolený pro extrakci konkrétního druhu dat, jako je adresa URL nebo e-mail. Některé z těchto předem vytvořených entit jsou definované v otevřeném zdrojovém projektu pro [rozpoznávání – textový](https://github.com/Microsoft/Recognizers-Text) projekt. Pokud vaše konkrétní jazyková verze nebo entita není aktuálně podporována, přispívat k projektu.|
|[**Regulární výraz**](reference-entity-regular-expression.md)|Používá regulární výraz pro **přesnější shodu textu**.|


## <a name="extraction-versus-resolution"></a>Extrakce versus rozlišení

Entity extrahují data, když se data zobrazí v utterance. Entity nemění ani neřeší data. Entita neposkytne žádné řešení, pokud je text platnou hodnotou pro entitu nebo ne.

Existují způsoby, jak převést na extrakci řešení, ale měli byste si uvědomit, že tato možnost omezuje schopnost aplikace, aby byla odolná proti změnám a chybám.

Entity seznamu a regulární výrazy (s odpovídajícími texty) lze použít jako [požadované funkce pro dílčí](luis-concept-feature.md#required-features) entitu a která slouží jako filtr pro extrakci. Tuto možnost byste měli pečlivě používat, aby nebránila tomu, aby aplikace mohla předpovídat.

## <a name="extracting-contextually-related-data"></a>Extrahování kontextově souvisejících dat

Utterance může obsahovat dva nebo více výskytů entity, kde význam dat je založen na kontextu v rámci utterance. Příkladem je utterance pro rezervaci letu, který má dvě zeměpisná umístění, původ a cíl.

`Book a flight from Seattle to Cairo`

Tato dvě umístění je třeba extrahovat způsobem, který klientská aplikace ví o typu každého umístění, aby bylo možné dokončit nákup lístku.

Chcete-li extrahovat původ a cíl, vytvořte dvě podentity jako součást entity Machine-Learning objednávky lístku. Pro každou z podentit vytvořte požadovanou funkci, která používá geographyV2.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>Omezení entit pomocí požadovaných funkcí

Další informace o [požadovaných funkcích](luis-concept-feature.md)

## <a name="patternany-entity"></a>Entita Pattern.any

Vzor. any je k dispozici pouze ve [vzoru](luis-concept-patterns.md).

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>Překročení omezení aplikací pro entity

Pokud potřebujete víc, než je [limit](luis-limits.md#model-limits), obraťte se na podporu. Provedete to tak, že shromáždíte podrobné informace o vašem systému, přejdete na web [Luis](luis-reference-regions.md#luis-website) a pak vyberete **Podpora**. Pokud vaše předplatné Azure zahrnuje služby podpory, obraťte se na [technickou podporu Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status-and-errors"></a>Stav a chyby předpovědi entity

Portál LUIS se zobrazí, když má entita jinou předpověď entit než entita, kterou jste vybrali pro příklad utterance. Toto jiné skóre vychází z aktuálně vyškolených modelů. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="Portál LUIS se zobrazí, když má entita jinou předpověď entit než entita, kterou jste vybrali pro příklad utterance.":::

Chybový text je zvýrazněný v příkladu utterance a příklad utterance čára má na pravé straně indikátor chyby, který se zobrazuje jako červený trojúhelník. 

Tyto informace slouží k vyřešení chyb entity pomocí jedné nebo několika z následujících možností:
* Zvýrazněný text je označený jako nepopisekný. Oprava, kontrola, správné a přeučení. 
* Vytvořte [funkci](luis-concept-feature.md) pro entitu, která vám usnadní identifikaci konceptu entity.
* Přidejte k entitě další [Příklady projevy](luis-concept-utterance.md) a Label.
* [Přečtěte si přehled aktivních kurzů](luis-concept-review-endpoint-utterances.md) pro všechny projevy přijaté na koncovém bodu předpovědi, které vám pomůžou identifikovat koncept entity.

## <a name="next-steps"></a>Další kroky

Seznamte se s koncepty dobré [projevy](luis-concept-utterance.md).

Další informace o tom, jak přidat entity do aplikace LUIS, najdete v tématu věnovaném [Přidání entit](luis-how-to-add-entities.md) .

Viz [kurz: extrakce strukturovaných dat ze utterance uživatelů pomocí entit strojového učení v Language Understanding (Luis)](tutorial-machine-learned-entity.md) , kde se dozvíte, jak extrahovat strukturovaná data z utterance pomocí entity Machine-Learning.

