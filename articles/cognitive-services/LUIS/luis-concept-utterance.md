---
title: Dobrý příklad projevy - LUIS
description: Výrok představuje zadání od uživatele, které má aplikace interpretovat. Shromážděte fráze, které si myslíte, že uživatelé zadají. Zahrnout projevy, které znamenají totéž, ale jsou konstruovány odlišně v délce slova a umístění slova.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d851082a4ec4a003619826eeffd4f4b856a67824
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382293"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Pochopit, jaké dobré projevy jsou pro vaši aplikaci LUIS

**Projevy** jsou vstup od uživatele, který vaše aplikace potřebuje interpretovat. Chcete-li trénovat LUIS extrahovat záměry a entity z nich, je důležité zachytit různé příklad projevy pro každý záměr. Aktivní učení nebo proces dalšího trénování na nové projevy, je nezbytné pro inteligenci naučil počítače, které poskytuje LUIS.

Shromažďujte projevy, které podle vás uživatelé zadají. Zahrnout projevy, které znamenají totéž, ale jsou konstruovány různými způsoby:

* Délka utterance - krátká, střední a dlouhá pro vaši klientskou aplikaci
* Délka slova a fráze
* Umístění slova – entita na začátku, uprostřed a na konci utterance
* Gramatika
* Pluralizace
* Vyplývající
* Volba nití a slovesa
* [Interpunkce](luis-reference-application-settings.md#punctuation-normalization) - dobrá odrůda pomocí správné, nesprávné a bez gramatiky

## <a name="how-to-choose-varied-utterances"></a>Jak si vybrat různé projevy

Při prvním spuštění [přidáním příklad projevy](luis-how-to-add-example-utterances.md) do modelu LUIS, zde jsou některé zásady mít na paměti.

### <a name="utterances-arent-always-well-formed"></a>Projevy nejsou vždy dobře tvarované

Může to být věta, jako "Rezervujte si letenku do Paříže pro mě", nebo fragment věty, jako "Rezervace" nebo "Pařížský let".  Uživatelé často dělají pravopisné chyby. Při plánování aplikace zvažte, zda používáte [kontrolu pravopisu Bingu](luis-tutorial-bing-spellcheck.md) k opravě vstupu uživatele před jeho předáním službě LUIS.

Pokud nechcete pravopisu uživatele projevy, měli byste trénovat LUIS na projevy, které obsahují překlepy a překlepy.

### <a name="use-the-representative-language-of-the-user"></a>Použití reprezentativního jazyka uživatele

Při výběru projevy, uvědomte si, že to, co si myslíte, že je běžný termín nebo frázi nemusí být správné pro typického uživatele klientské aplikace. Nemusí mít zkušenosti s doménou. Buďte opatrní při používání termínů nebo frází, které by uživatel řekl pouze v případě, že by byl odborníkem.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Vyberte si pestrou terminologii a frázování

Zjistíte, že i když se budete snažit vytvořit různé věty vzory, budete stále opakovat nějakou slovní zásobu.

Vezměte v uvedenépříklad projevy:

|Ukázkové promluvy|
|--|
|Jak získám počítač?|
|Kde získám počítač?|
|Chci si pojít počítač, jak na to mám jít?|
|Kdy můžu mít počítač?|

Základní termín zde, "počítač", není pestrý. Používejte alternativy, jako je stolní počítač, notebook, pracovní stanice nebo dokonce jen stroj. Služba LUIS můžete inteligentně odvodit synonyma z kontextu, ale při vytváření projevy pro školení, je vždy lepší je měnit.

## <a name="example-utterances-in-each-intent"></a>Příklad projevy v každém záměru

Každý záměr musí mít příklad projevy, alespoň 15. Pokud máte záměr, který nemá žádné příklad projevy, nebude možné trénovat LUIS. Pokud máte záměr s jedním nebo velmi málo příklad projevy, LUIS nemusí přesně předpovědět záměr.

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Přidání malých skupin 15 projevy pro každou vývojová iterace

V každé iteraci modelu nepřidávejte velké množství promluv. Přidejte projevy v množství 15. [Vlak](luis-how-to-train.md), [publikovat](luis-how-to-publish-app.md)a [znovu testovat.](luis-interactive-test.md)

Služba LUIS vytváří efektivní modely s projevy, které jsou pečlivě vybrány autorem modelu LUIS. Přidání příliš mnoho projevy není cenné, protože zavádí zmatek.

Je lepší začít s několika projevy, pak [zkontrolujte projevy koncového bodu](luis-how-to-review-endpoint-utterances.md) pro správnou předpověď záměru a extrakce entity.

## <a name="utterance-normalization"></a>Normalizace utterance

Utterance normalizace je proces ignorování účinků interpunkce a diakritiky během trénování a predikce. Pomocí [nastavení aplikace](luis-reference-application-settings.md) můžete řídit, jak normalizace utterance ovlivňuje předpovědi utterance.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Normalizace utterance pro diakritiku a interpunkci

Normalizace utterance je definována při vytváření nebo importu aplikace, protože se jedná o nastavení v souboru JSON aplikace. Nastavení normalizace utterance jsou ve výchozím nastavení vypnuta.

Diakritika jsou značky nebo znaky v textu, například:

```
İ ı Ş Ğ ş ğ ö ü
```

Pokud vaše aplikace zapne normalizaci, skóre v podokně **Test,** dávkové testy a dotazy koncového bodu se změní pro všechny projevy pomocí diakritiky nebo interpunkce.

Zapněte normalizaci utterance pro diakritiku nebo interpunkci do souboru aplikace LUIS JSON v parametru. `settings`

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

Normalizace **interpunkce** znamená, že před vaše modely získat trénované a před vaše dotazy koncového bodu získat předpovědět, interpunkce bude odebrána z projevy.

Normalizace **diakritiky** nahradí znaky diakritikou v projevech běžnými znaky. Například: `Je parle français` `Je parle francais`se stane .

Normalizace neznamená, že neuvidíte interpunkci a diakritiku v příkladu projevy nebo předpověď odpovědi, pouze to, že budou ignorovány během školení a předpověď.

### <a name="punctuation-marks"></a>Interpunkční znaménka

Interpunkce je samostatný token v LUIS. Utterance, který obsahuje tečku na konci versus utterance, která neobsahuje tečku na konci jsou dvě samostatné projevy a může získat dvě různé předpovědi.

Pokud interpunkce není normalizována, LUIS neignoruje interpunkční znaménka ve výchozím nastavení, protože některé klientské aplikace může umístit význam na tyto značky. Ujistěte se, že vaše promluvy příkladu používají interpunkci a žádné interpunkce, aby oba styly vrátit stejné relativní skóre.

Ujistěte se, že model zpracovává interpunkci buď v příkladu promluvy (s a nemají interpunkci) nebo ve [vzorcích,](luis-concept-patterns.md) kde je snazší ignorovat interpunkci se speciální syntaxí:`I am applying for the {Job} position[.]`

Pokud interpunkce nemá žádný konkrétní význam v klientské aplikaci, zvažte [ignorování interpunkce](#utterance-normalization) normalizací interpunkce.

### <a name="ignoring-words-and-punctuation"></a>Ignorování slov a interpunkce

Pokud chcete ignorovat určitá slova nebo interpunkci ve vzorcích, použijte [vzorek](luis-concept-patterns.md#pattern-syntax) s `[]` _ignore_ syntaxí hranatých závorek .

## <a name="training-utterances"></a>Promluvy školení

Školení je obecně nedeterministické: utterance předpověď se může mírně lišit mezi verzemi nebo aplikacemi.
Nedeterministické školení můžete odebrat aktualizací rozhraní API `UseAllTrainingData` pro nastavení [verze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) pomocí dvojice název/hodnota tak, aby bylo možné použít všechna trénovací data.

## <a name="testing-utterances"></a>Testování promluv

Vývojáři by měli začít testovat jejich luis aplikace s reálným provozem odesláním projevy na adresu URL [koncového bodu předpověď.](luis-how-to-azure-subscription.md) Tyto projevy se používají ke zlepšení výkonu záměry a entity s [review projevy](luis-how-to-review-endpoint-utterances.md). Testy odeslané pomocí podokna testování webu LUIS nejsou odesílány prostřednictvím koncového bodu, a proto nepřispívají k aktivnímu učení.

## <a name="review-utterances"></a>Kontrola promluv

Po trénování modelu, publikování a přijímání dotazů [koncového bodu](luis-glossary.md#endpoint) [zkontrolujte projevy](luis-how-to-review-endpoint-utterances.md) navržené luis. Služba LUIS vybere projevy koncového bodu, které mají nízké skóre pro záměr nebo entitu.

## <a name="best-practices"></a>Osvědčené postupy

Projděte si [osvědčené postupy](luis-concept-best-practices.md) a aplikujte je jako součást svého pravidelného cyklu vytváření.

## <a name="label-for-word-meaning"></a>Popisek významu slova

Pokud je volba slova nebo uspořádání slov stejné, ale neznamená to též, neoznačte jej entitou.

Následující projevy, slovo `fair` je homograph. To je napsáno stejné, ale má jiný význam:

|Promluva|
|--|
|Jaký druh krajských veletrhů se letos v létě konají v oblasti Seattlu?|
|Je aktuální hodnocení pro recenzi Seattle veletrhu?|

Pokud jste chtěli, aby entita události `fair` nalezla všechna data události, označte slovo v prvním utterance, ale ne ve druhém.


## <a name="next-steps"></a>Další kroky
Viz [Přidání příklad projevy](luis-how-to-add-example-utterances.md) pro informace o školení aplikace LUIS pochopit projevy uživatelů.

