---
title: Dobrý příklad projevy-LUIS
description: Výrok představuje zadání od uživatele, které má aplikace interpretovat. Shromážděte fráze, které budou uživatelé zadávat. Zahrňte projevy, který je stejný jako stejný objekt, ale je vytvořen jinak v délce slova a v umístění slova.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 46004d81756809958e359c2a0b72c143599c2853
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706754"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Vysvětlení, co je pro vaši aplikaci LUIS dobré projevy

**Projevy** jsou vstupy od uživatele, který vaše aplikace potřebuje k interpretaci. Pro výuku LUIS k extrakci záměrů a entit z nich je důležité zachytit různé příklady projevy pro každý záměr. Aktivní učení nebo proces pokračujícího učení s novým projevy je základem pro kurzy strojového učení, které LUIS poskytuje.

Shromážděte projevyy, které se domníváte, že uživatelé vstoupí v úvahu. Zahrňte projevy, což znamená totéž, ale je tvořeno různými způsoby:

* Délka utterance – krátká, střední a dlouhá pro klienta – aplikace
* Délka slova a fráze
* Umístění slov – entita na začátku, uprostřed a na konci utterance
* Gramatika
* Pluralizace
* Způsobenému výpadky proudu
* Podstatné jméno a volba příkazu
* [Interpunkční znaménka](luis-reference-application-settings.md#punctuation-normalization) – dobrá škála pomocí správného, nesprávného a bez gramatiky

## <a name="how-to-choose-varied-utterances"></a>Jak zvolit různé projevy

Když začnete s prvním zprovozněním, [přidáte příklad projevy](./luis-how-to-add-entities.md) do modelu Luis. tady je několik principů, které je potřeba vzít v úvahu.

### <a name="utterances-arent-always-well-formed"></a>Projevy nejsou vždycky správně vytvořené

Může se jednat o větu, jako je například "lístek lístku na Paříž pro mě" nebo fragment věty, například "rezervace" nebo "pařížský let".  Uživatelé často vytvářejí pravopisné chyby. Při plánování aplikace zvažte, jestli před předáním vstupu do LUIS použít [Kontrola pravopisu Bingu](luis-tutorial-bing-spellcheck.md) k opravě vstupu uživatele.

Pokud neprovedete kontrolu pravopisu pro uživatele projevy, měli byste LUIS v projevy, které obsahují překlepy a pravopisné chyby.

### <a name="use-the-representative-language-of-the-user"></a>Použijte zástupce jazyka uživatele.

Při volbě projevy mějte na paměti, že si myslíte, že se jedná o běžný výraz nebo fráze nemusí být správná pro typického uživatele klientské aplikace. Nemusí mít prostředí v doméně. Buďte opatrní při použití podmínek nebo frází, které by měl uživatel vyslovit jenom v případě, že byli odborníkem.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Výběr proměnlivé terminologie i formulace

Zjistíte, že i když se rozhodnete vytvořit různé vzory vět, stále se opakují i některé slovníky.

Vezměte v úvahu tento příklad projevy:

|Ukázkové promluvy|
|--|
|Jak získám počítač?|
|Kde získám počítač?|
|Chci získat počítač, jak na něj mám přejít?|
|Kdy můžu počítač dělat?|

Základní pojem, *počítač*, není proměnlivý. Používejte alternativy, jako je stolní počítač, přenosný počítač, pracovní stanice nebo dokonce jenom počítač. LUIS dokáže inteligentně odvodit synonyma z kontextu, ale při vytváření projevy pro školení je vždy lepší je měnit.

## <a name="example-utterances-in-each-intent"></a>Příklad projevy v každém záměru

Každý záměr musí mít příklad projevy, nejméně 15. Pokud máte záměr, který nemá žádné ukázkové projevy, nebudete moci LUIS. Pokud máte záměr s jedním nebo velmi malým příkladem projevy, LUIS nemusí přesně předpovědět záměr.

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Přidat malé skupiny s 15 projevy pro každou iteraci vytváření

V každé iteraci modelu nepřidávejte velké množství projevy. Přidejte projevy do množství 15. Znovu proveďte [výuku](luis-how-to-train.md), [publikování](luis-how-to-publish-app.md)a [testování](luis-interactive-test.md) .

LUIS vytváří efektivní modely s projevy, které pečlivě vybral Autor modelu LUIS. Přidání příliš velkého počtu projevy není důležité, protože představuje nejasnost.

Je lepší začít s několika projevy a pak [zkontrolovat projevy Endpoint](luis-how-to-review-endpoint-utterances.md) za účelem správné předpovědi záměru a extrakci entit.

## <a name="utterance-normalization"></a>Normalizace utterance

Normalizace utterance je proces ignorování efektů typů textu, jako jsou interpunkční znaménka a diakritická znaménka během školení a předpovědi.

Nastavení normalizace utterance jsou ve výchozím nastavení vypnutá. Mezi tato nastavení patří:

* Formuláře aplikace Word
* Diakritiku
* Interpunkční znaménka

Pokud zapnete nastavení normalizace, skóre v **testovacím** podokně, dávkových testech a dotazech koncového bodu se změní pro všechna projevyá nastavení normalizace.

Když naklonete verzi na portálu LUIS, nastavení verze bude pokračovat v nové klonované verzi.

Nastavení verze nastavte prostřednictvím portálu LUIS, v části **Správa** na stránce **nastavení aplikace** nebo v [rozhraní API pro aktualizaci nastavení verze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings). Přečtěte si další informace o těchto normalizačních změnách v [odkazu](luis-reference-application-settings.md).

### <a name="word-forms"></a>Formuláře aplikace Word

Normalizace **tvarů slov** ignoruje rozdíly v slovech, které přesahují kořen.

<a name="utterance-normalization-for-diacritics-and-punctuation"></a>

### <a name="diacritics"></a>Diakritiku

Diakritická znaménka jsou značky nebo znaménka v rámci textu, jako například:

```
İ ı Ş Ğ ş ğ ö ü
```

### <a name="punctuation-marks"></a>Interpunkční značky
Normalizace **interpunkce** znamená, že před tím, než se vaše modely vyškole a ještě před dokončením dotazů na koncové body, se z projevy odeberou interpunkční znaménka.

Interpunkce je samostatný token v LUIS. Utterance, která obsahuje tečku na konci oproti utterance, která neobsahuje tečku na konci, je dvě samostatné projevy a může získat dvě různé předpovědi.

Pokud interpunkční znaménko není normalizováno, LUIS Neignoruje interpunkční znaménka ve výchozím nastavení, protože některé klientské aplikace mohou na tyto značky umístit význam. Ujistěte se, že váš příklad projevy použít interpunkční znaménka i bez interpunkce v případě, že oba styly vrátí stejné relativní skóre.

Ujistěte se, že model zpracovává interpunkční znaménka buď v příkladu projevy (má a nemá interpunkci), nebo ve [vzorech](luis-concept-patterns.md) , kde je snazší ignorovat interpunkci se speciální syntaxí: `I am applying for the {Job} position[.]`

Pokud interpunkční znaménko nemá v klientské aplikaci žádný zvláštní význam, zvažte [ignorování interpunkce](#utterance-normalization) normalizací interpunkce.

### <a name="ignoring-words-and-punctuation"></a>Ignorují se slova a interpunkční znaménka.

Chcete-li v vzorcích ignorovat určitá slova nebo interpunkční znaménka, použijte [vzor](luis-concept-patterns.md#pattern-syntax) s syntaxí _Ignore_ hranatých závorek, `[]` .

<a name="training-utterances"></a>

## <a name="training-with-all-utterances"></a>Školení se všemi projevy

Školení je všeobecně nedeterministické: předpověď utterance se může mírně lišit napříč verzemi nebo aplikacemi.
Nedeterministické školení můžete odebrat tak, že aktualizujete rozhraní API pro [Nastavení verze](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) s `UseAllTrainingData` použitím dvojice název/hodnota, aby se použila všechna školicí data.

## <a name="testing-utterances"></a>Testování projevy

Vývojáři by měli zahájit testování své aplikace LUIS pomocí skutečného provozu odesláním projevy na adresu URL [koncového bodu předpovědi](luis-how-to-azure-subscription.md) . Tyto projevy slouží ke zlepšení výkonu záměrů a entit s [kontrolou projevy](luis-how-to-review-endpoint-utterances.md). Testy odeslané pomocí podokna testování webu LUIS nejsou odesílány prostřednictvím koncového bodu, a proto nepřispívají k aktivnímu učení.

## <a name="review-utterances"></a>Zkontrolovat projevy

Po vyškolení, publikování a přijímání dotazů na [koncové body](luis-glossary.md#endpoint) [si prohlédněte projevy, který](luis-how-to-review-endpoint-utterances.md) navrhuje Luis. LUIS vybere koncový bod projevy, který má nízké skóre pro záměr nebo entitu.

## <a name="best-practices"></a>Osvědčené postupy

Projděte si [osvědčené postupy](luis-concept-best-practices.md) a použijte je jako součást běžného cyklu vytváření.

## <a name="label-for-word-meaning"></a>Označení pro wordový význam

Pokud je volba slova nebo uspořádání slov stejná, ale neznamená to stejné, nepište je entitou.

Následující projevy `fair` je slovo homograf. Je pravopisné, ale má jiný význam:

|Výrok|
|--|
|Jaký druh veletrhů se děje v oblasti Praha této léto?|
|Je aktuální hodnocení pro Seattle na veletrzích?|

Pokud jste chtěli, aby entita události vyhledala všechna data události, označte slovo `fair` v prvním utterance, ale ne za sekundu.


## <a name="next-steps"></a>Další kroky
Informace o výuce aplikace LUIS pro pochopení uživatelského projevy najdete v tématu věnovaném [Přidání ukázkového projevy](./luis-how-to-add-entities.md) .