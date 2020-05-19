---
title: Typy entit – LUIS
description: Entita extrahuje data z utterance uživatele v předpokládaném modulu runtime. _Volitelným_a sekundárním účelem je zvýšit předpověď záměru nebo jiných entit pomocí entity jako funkce.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 9d8afd5a660b3af5556256835486e984d7d657bc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585636"
---
# <a name="extract-data-with-entities"></a>Extrakce dat s entitami

Entita extrahuje data z utterance uživatele v předpokládaném modulu runtime. _Volitelným_a sekundárním účelem je zvýšit předpověď záměru nebo jiných entit pomocí entity jako funkce.

Existuje několik typů entit:

* [Strojově naučená entita](reference-entity-machine-learned-entity.md)
* Nenáročné na počítač, který se používá jako požadovaná [funkce](luis-concept-feature.md) – pro přesné shody textu, porovnávání vzorů nebo zjišťování podle předem připravených entit
* [Vzor. any](#patternany-entity) – pro extrakci textu volných formulářů, jako jsou názvy knih ze [vzoru](reference-entity-pattern-any.md)

Entity, které se naučily počítačem, poskytují nejširší škálu voleb pro extrakci dat. Entity nenáročné na počítač fungují podle textu a používají se jako [povinná funkce](#design-entities-for-decomposition) pro určitou entitu nebo záměr, který se naučil stroji.

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

## <a name="entity-compared-to-intent"></a>Entita v porovnání s záměrem

Entita představuje koncept dat _uvnitř utterance_. Záměr klasifikuje _celou utterance_.

Vezměte v úvahu následující čtyři projevy:

|Promluva|Předpokládané záměr|Extrahované entity|Vysvětlení|
|--|--|--|--|
|Nápověda|Nápověda|-|Nic k extrakci.|
|Poslat něco|sendSomething|-|Nic k extrakci. Model nemá požadovanou funkci pro extrakci `something` v tomto kontextu a není uveden žádný příjemce.|
|Poslat Bobovi a|sendSomething|`Bob`, `present`|Model se extrahuje `Bob` tak, že se přidá požadovaná funkce předem sestavené entity `personName` . K extrakci se použila entita získaná počítačem `present` .|
|Poslat Bobovi pole čokolády|sendSomething|`Bob`, `box of chocolates`|Dvě důležité části dat `Bob` a byly `box of chocolates` extrahovány entitami, které se naučily strojově.|

## <a name="design-entities-for-decomposition"></a>Entity návrhu pro rozložení

Entity náročné na počítač umožňují návrh schématu aplikace pro rozložení a rozdělení velkého konceptu na subentity.

Návrh pro dekompozici umožňuje, aby LUIS vrátilo v klientské aplikaci hlubokou míru překladu entit. Díky tomu se vaše klientská aplikace může soustředit na obchodní pravidla a ponechání rozlišení dat LUIS.

Na základě kontextu, který se získal prostřednictvím příkladu projevy, se aktivují triggery entit počítače.

[**Entitami zjištěnými počítačem**](tutorial-machine-learned-entity.md) jsou extraktory na nejvyšší úrovni. Dílčí entity jsou podřízené entity entit strojového učení.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typy entit

Podřízenou entitou nadřazeného objektu by měla být entita získaná počítačem. Subentita může jako [funkci](luis-concept-feature.md)použít entitu, která není učená počítačem.

Vyberte entitu na základě toho, jak by měla být data extrahována a jak by měla být reprezentována po extrakci.

|Typ entity|Účel|
|--|--|
|[**Strojové učení**](tutorial-machine-learned-entity.md)|Extrahujte vnořená a složitá data získaná z příkladů s popisky. |
|[**Seznamu**](reference-entity-list.md)|Seznam položek a jejich synonym, které byly extrahovány s **přesnou shodou textu**|
|[**Vzor. any**](#patternany-entity)|Entita, kde najít konec entity, je obtížné určit, protože entita má volnou formu. K dispozici pouze ve [vzorcích](luis-concept-patterns.md).|
|[**Předem připravených**](luis-reference-prebuilt-entities.md)|Vyškolený pro extrakci konkrétního druhu dat, jako je adresa URL nebo e-mail. Některé z těchto předem vytvořených entit jsou definované v otevřeném zdrojovém projektu pro [rozpoznávání – textový](https://github.com/Microsoft/Recognizers-Text) projekt. Pokud vaše konkrétní jazyková verze nebo entita není aktuálně podporována, přispívat k projektu.|
|[**Regulární výraz**](reference-entity-regular-expression.md)|Používá regulární výraz pro **přesnější shodu textu**.|

## <a name="extracting-contextually-related-data"></a>Extrahování kontextově souvisejících dat

Utterance může obsahovat dva nebo více výskytů entity, kde význam dat je založen na kontextu v rámci utterance. Příkladem je utterance pro rezervaci letu, který má dvě zeměpisná umístění, původ a cíl.

`Book a flight from Seattle to Cairo`

Tato dvě umístění je třeba extrahovat způsobem, který klientská aplikace ví o typu každého umístění, aby bylo možné dokončit nákup lístku.

Chcete-li extrahovat původ a cíl, vytvořte dvě podentity jako součást entity náročné na strojové pořadí lístku. Pro každou z podentit vytvořte požadovanou funkci, která používá geographyV2.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>Omezení entit pomocí požadovaných funkcí

Další informace o [požadovaných funkcích](luis-concept-feature.md)

## <a name="patternany-entity"></a>Entita Pattern.any

Vzor. any je k dispozici pouze ve [vzoru](luis-concept-patterns.md).

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>Překročení omezení aplikací pro entity

Pokud potřebujete víc, než je [limit](luis-limits.md#model-limits), obraťte se na podporu. Provedete to tak, že shromáždíte podrobné informace o vašem systému, přejdete na web [Luis](luis-reference-regions.md#luis-website) a pak vyberete **Podpora**. Pokud vaše předplatné Azure zahrnuje služby podpory, obraťte se na [technickou podporu Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Stav předpovědi entity

Portál LUIS se zobrazí, když má entita jinou předpověď entit než entita, kterou jste vybrali pro příklad utterance. Toto jiné skóre vychází z aktuálně vyškolených modelů. Tyto informace slouží k řešení chyb školení pomocí jedné nebo několika z následujících možností:
* Vytvořte [funkci](luis-concept-feature.md) pro entitu, která vám usnadní identifikaci konceptu entity.
* Přidejte k entitě další [Příklady projevy](luis-concept-utterance.md) a Label.
* [Přečtěte si přehled aktivních kurzů](luis-concept-review-endpoint-utterances.md) pro všechny projevy přijaté na koncovém bodu předpovědi, které vám pomůžou identifikovat koncept entity.

## <a name="next-steps"></a>Další kroky

Seznamte se s koncepty dobré [projevy](luis-concept-utterance.md).

Další informace o tom, jak přidat entity do aplikace LUIS, najdete v tématu věnovaném [Přidání entit](luis-how-to-add-entities.md) .

Viz [kurz: extrakce strukturovaných dat ze utterance uživatelů pomocí entit strojového učení v tématu Language Understanding (Luis)](tutorial-machine-learned-entity.md) , kde se dozvíte, jak extrahovat strukturovaná data z utterance pomocí této entity, kterou se naučila počítač.

