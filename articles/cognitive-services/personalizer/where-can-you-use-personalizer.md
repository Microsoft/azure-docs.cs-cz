---
title: Kde a jak používat - Personalizace
description: Personalizátor lze použít v každé situaci, kdy vaše aplikace může vybrat správnou položku, akci nebo produkt k zobrazení - s cílem zlepšit zážitek, dosáhnout lepších obchodních výsledků nebo zvýšit produktivitu.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 63e66315898242beb5da59927e8d506e6f2cff78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219321"
---
# <a name="where-and-how-to-use-personalizer"></a>Kde a jak používat personalista

Použijte personalizátor v každé situaci, kdy vaše aplikace potřebuje vybrat správnou akci (obsah) pro zobrazení - s cílem zlepšit zážitek, dosáhnout lepších obchodních výsledků nebo zvýšit produktivitu.

Personalizátor používá strojové učení k výběru akce (obsahu), která má uživateli zobrazit. Výběr se může výrazně lišit v závislosti na množství, kvalitě a distribuci dat odeslaných do služby.

## <a name="example-use-cases-for-personalizer"></a>Příklad případů použití pro personalizace

* **Záměr objasnění & rozdvojení**: pomozte uživatelům mít lepší zážitek, když jejich záměr není jasné tím, že poskytuje možnost, která je personalizovaná.
* **Výchozí návrhy** pro nabídky & možnosti: nechat robota navrhnout nejpravděpodobnější položku personalizovaným způsobem jako první krok namísto prezentace neosobní nabídky nebo seznamu alternativ.
* **Bot rysy & tón**: pro roboty, které se mohou lišit tón, výmluvnost, a styl psaní, zvažte různé tyto vlastnosti.
* **Oznámení & obsah upozornění**: rozhodněte se, jaký text použít pro výstrahy, aby se uživatelé více zapojili.
* **Oznámení & časování výstrah**: máte personalizované informace o tom, kdy mají posílat oznámení uživatelům, aby je více zapojili.


## <a name="expectations-required-to-use-personalizer"></a>Očekávání nutná k používání personalisty

Personalista můžete použít v situacích, kdy se setkáte nebo můžete implementovat následující pokyny.

|Pokyn|Vysvětlení|
|--|--|
|Obchodní cíl|Máte obchodní nebo použitelný cíl pro vaši aplikaci.|
|Obsah|Máte místo ve vaší aplikaci, kde kontextové rozhodnutí o tom, co ukázat uživatelům zlepší tento cíl.|
|Množství obsahu|Na jedno volání můžete zařadit méně než 50 akcí.|
|Agregovaná data|Nejlepší volbou může a měli bychom se naučit z kolektivního chování uživatelů a celkové odměny skóre.|
|Etické použití|Použití strojového učení pro přizpůsobení se řídí [pokyny pro zodpovědné používání](ethics-responsible-use.md) a volbami, které jste zvolili.
|Nejlepší možnost|Kontextové rozhodnutí lze vyjádřit jako nejlepší možnost (akce) z omezeného souboru možností.|
|Výsledek skóre|Jak dobře hodnocená volba fungovala pro vaši aplikaci, může být určena měřením některých aspektů chování uživatelů a jeho vyjádřením v _[skóre odměny](concept-rewards.md)_.|
|Příslušné načasování|Skóre odměny nepřináší příliš mnoho matoucích nebo vnějších faktorů. Doba trvání experimentu je dostatečně nízká, že skóre odměny lze vypočítat, dokud je stále relevantní.|
|Dostatečné kontextové funkce|Kontext pro hodnocení můžete vyjádřit jako seznam alespoň 5 [funkcí,](concepts-features.md) o kterých si myslíte, že by pomohly učinit správnou volbu, a které neobsahují identifikovatelné informace specifické pro uživatele.|
|Dostatečné akční funkce|Máte informace o každé volbě obsahu, _akci_, jako seznam alespoň 5 [funkcí,](concepts-features.md) o kterých si myslíte, že pomohou personalistovi učinit správnou volbu.|
|Denní data|Je tu dost událostí, aby zůstali na vrcholu optimální personalizace, pokud problém driftuje v průběhu času (například preference ve zprávách nebo módě). Personalizák se přizpůsobí neustálým změnám v reálném světě, ale výsledky nebudou optimální, pokud nebude dostatek událostí a dat, ze kterých se můžete učit, abyste objevili nové vzory a usadili se na nových vzorcích. Měli byste zvolit případ použití, který se stane dost často. Zvažte hledání případů použití, které se dějí nejméně 500krát denně.|
|Historická data|Aplikace může uchovávat data dostatečně dlouho a shromažďovat historii alespoň 100 000 interakcí. To umožňuje personalista shromažďovat dostatek dat k provádění offline hodnocení a optimalizace zásad.|

**Nepoužívejte personalizace,** kde personalizované chování není něco, co lze zjistit u všech uživatelů. Například použití personalistu k navržení první objednávky pizzy ze seznamu 20 možných položek nabídky je užitečné, ale který kontakt, který má volat ze seznamu kontaktů uživatelů, když vyžaduje pomoc s péčí o děti (například "Babička"), není něco, co je přizpůsobitelné v rámci vaší uživatelské základny.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Jak používat personalizace ve webové aplikaci

Přidání výukové smyčky do webové aplikace zahrnuje:

* Určete, jaké prostředí chcete přizpůsobit, jaké akce a funkce máte, jaké kontextové funkce použít a jakou odměnu nastavíte.
* Přidejte odkaz na sadu SDK individuálního nastavení ve vaší aplikaci.
* Zavolejte rozhraní Rank API, až budete připraveni přizpůsobit se.
* Uložte id události. Odměnu s rozhraním Reward API odešlete později.
1. Pokud si budete jistý, že uživatel vaši personalizovanou stránku uvidí, zavolejte aktivovat událost.
1. Počkejte na výběr uživatele hodnoceného obsahu.
1. Volání Bonus API určit, jak dobře výstup Rank API udělal.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Jak používat Personalizace s chat bot

V tomto příkladu uvidíte, jak pomocí individuálního nastavení vytvořit výchozí návrh namísto odeslání uživatele dolů řadu nabídek nebo volby pokaždé.

* Získat [kód](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) pro tuto ukázku.
* Nastavte si řešení bot. Nezapomeňte publikovat aplikaci LUIS.
* Správa Rank a Odměna API hovory pro bot.
    * Přidejte kód pro správu zpracování záměru LUIS. Pokud **none** je vrácena jako hlavní záměr nebo nejvyšší záměr skóre je pod prahovou hodnotou obchodní logiky, odešlete seznam záměrů personalistu pořadí záměry.
    * Zobrazit seznam záměru pro uživatele jako volitelné odkazy s prvním záměrem je nejvyšší hodnocené záměr z odpovědi rozhraní API pořadí.
    * Zachyťte výběr uživatele a odešlete jej do volání rozhraní API pro odměňování.

### <a name="recommended-bot-patterns"></a>Doporučené vzory botů

* Proveďte volání rozhraní API hodnocení personalizátoru pokaždé, když je potřeba rozdvojení, na rozdíl od ukládání výsledků do mezipaměti pro každého uživatele. Výsledek nejednoznačného záměru se může v průběhu času změnit pro jednu osobu a povolení rozhraní API pořadí prozkoumat odchylky urychlí celkové učení.
* Zvolte interakci, která je společná pro mnoho uživatelů, abyste měli dostatek dat k přizpůsobení. Například úvodní otázky mohou být vhodnější než menší vysvětlení hluboko v konverzačním grafu, ke kterým se může dostat pouze několik uživatelů.
* Pomocí volání rozhraní API rank povolte konverzace "první návrh je správný", kde se uživateli zeptá na otázku "Chcete x?" nebo "Myslel jsi X?" a uživatel může jen potvrdit; na rozdíl od poskytování možností uživateli, kde si musí vybrat z nabídky. Například Uživatel: "Chtěl bych si objednat kávu" Bot: "Chtěli byste dvojité espresso?". Tímto způsobem je signál odměny také silný, protože se přímo jedná o jeden návrh.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Jak používat personalista s řešením doporučení

Mnoho společností používá moduly doporučení, marketingové nástroje a nástroje pro kampaně, segmentaci publika a shlukování, kolaborativní filtrování a další prostředky k doporučení produktů z velkého katalogu zákazníkům.

[Úložiště GitHub společnosti Microsoft Recommenders](https://github.com/Microsoft/Recommenders) poskytuje příklady a osvědčené postupy pro vytváření doporučených systémů, které jsou k dispozici jako poznámkové bloky Jupyter. Poskytuje pracovní příklady pro přípravu dat, vytváření modelů, vyhodnocení, ladění a zprovoznění modulů doporučení pro mnoho běžných přístupů, včetně xDeepFM, SAR, ALS, RbM, DKN.

Personalizátář může pracovat s moduldoporučení, když je přítomen.

* Moduly doporučení berou velké množství položek (například 500 000) a doporučují podmnožinu (například prvních 20) ze stovek nebo tisíců možností.
* Personalizár má malý počet akcí se spoustou informací o nich a řadí je v reálném čase pro daný bohatý kontext, zatímco většina doporučení motory používají pouze několik atributů o uživatelích, produktech a jejich interakcích.
* Personalizátor je navržen tak, aby autonomně prozkoumával uživatelské preference po celou dobu, což přinese lepší výsledky tam, kde se obsah rychle mění, jako jsou zprávy, živé události, živý komunitní obsah, obsah s denními aktualizacemi nebo sezónní obsah.

Běžné použití je vzít výstup modulu doporučení (například top 20 produktů pro určitého zákazníka) a použít jako vstupní akce pro personalista.

## <a name="adding-content-safeguards-to-your-application"></a>Přidání zabezpečení obsahu do aplikace

Pokud vaše aplikace umožňuje velké odchylky v obsahu zobrazeném uživatelům a některé z těchto obsahů mohou být pro některé uživatele nebezpečné nebo nevhodné, měli byste plánovat předem, abyste se ujistili, že jsou zavedena správná ochranná opatření, která zabrání uživatelům v tom, aby viděli nepřijatelné Obsah. Nejlepší způsob zavedení ochranných opatření je:
    * Získejte seznam akcí k hodnocení.
    * Odfiltrujte ty, které nejsou životaschopné pro publikum.
    * Pouze pořadí těchto životaschopných akcí.
    * Zobrazí uživateli nejvyšší hodnocenou akci.

V některých architekturách výše uvedené sekvence může být obtížné implementovat. V takovém případě existuje alternativní přístup k provádění záruk po pořadí, ale je třeba přijmout ustanovení, aby se k trénování modelu personalisty nepoužívala opatření, která nespadají do ochranného opatření.

* Získejte seznam akcí k hodnocení s deaktivovaným učením.
* Pořadí akce.
* Zkontrolujte, zda je horní akce životaschopná.
    * Pokud je nejvyšší akce životaschopná, aktivujte výuku pro tuto hodnost a pak ji ukažte uživateli.
    * Pokud není nejvyšší akce životaschopná, neaktivujte učení pro toto hodnocení a rozhodněte se pomocí vlastní logiky nebo alternativních přístupů, co chcete uživateli zobrazit. Dokonce i když použijete druhou nejlepší možnost hodnocení, neaktivujte učení pro toto hodnocení.


## <a name="next-steps"></a>Další kroky

[Etické & odpovědné používání](ethics-responsible-use.md).