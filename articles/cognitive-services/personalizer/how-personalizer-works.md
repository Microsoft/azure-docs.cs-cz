---
title: Jak přizpůsobovat funkce přizpůsobování
titleSuffix: Azure Cognitive Services
description: Přizpůsobování využívá Machine Learning ke zjištění, jakou akci použít v kontextu. Každá výuková smyčka má model, který je vyškolen výhradně na datech, která jste jim odeslali prostřednictvím volání Rank a disměna. Každá výuková smyčka je zcela nezávislá na sobě.
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: diberry
ms.openlocfilehash: e55ccb508760c4473f71245c183948219f31985c
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663914"
---
# <a name="how-personalizer-works"></a>Jak služba Personalizace funguje

Přizpůsobování využívá Machine Learning ke zjištění, jakou akci použít v kontextu. Každá výuková smyčka má model, který je vyškolen výhradně na datech, která jste jim odeslali prostřednictvím volání **Rank** a disměna. Každá výuková smyčka je zcela nezávislá na sobě. Vytvořte výukovou smyčku pro každou část nebo chování vaší aplikace, kterou chcete přizpůsobit.

Pro každou smyčku **volejte rozhraní API řazení s** použitím aktuálního kontextu s:

* Seznam možných akcí: položek obsahu, ze kterých se má vybrat hlavní akce
* Seznam [funkcí kontextu](concepts-features.md): kontextově relevantní data, jako je například uživatel, obsah a kontext.

Rozhraní API **pořadí** se rozhodne použít buď:

* _Zneužití_: Aktuální model, který určí nejlepší akci na základě minulých dat.
* _Prozkoumat_: Místo horní akce vyberte jinou akci.

API pro odměnu:

* Shromažďuje data pro výuku modelu pomocí zaznamenávání funkcí a odměňování skóre každého volání pořadí.
* Tato data používá k aktualizaci modelu na základě nastavení určeného v _zásadách Učení_.

## <a name="architecture"></a>Architektura

Následující obrázek ukazuje architekturu pro volání pořadí a odměny:

![alternativní text](./media/how-personalizer-works/personalization-how-it-works.png "Jak funguje přizpůsobení")

1. Přizpůsobování používá interní model AI k určení pořadí akce.
1. Služba rozhodne, zda má využít aktuální model nebo prozkoumat nové volby modelu.  
1. Výsledky hodnocení se odesílají do centra EventHub.
1. Když přizpůsobování obdrží odměnu, bude se tato měna odesílat do centra EventHub. 
1. Rozsah a měna jsou korelační.
1. Model AI se aktualizuje na základě výsledků korelace.
1. Odvozený modul se aktualizuje novým modelem. 

## <a name="research-behind-personalizer"></a>Výzkum za přizpůsobením

Přizpůsobování vychází z špičkové vědy a výzkumu v oblasti [posílení učení](concepts-reinforcement-learning.md) , včetně papírů, výzkumných aktivit a pokračujících oblastí průzkumu v Microsoft Research.

## <a name="terminology"></a>Terminologie

* **Výuková smyčka**: Výukovou smyčku můžete vytvořit pro každou část aplikace, která může být výhodou individuálního nastavení. Pokud máte více než jedno prostředí pro přizpůsobení, vytvořte smyčku pro každý z nich. 

* **Akce**: Akce jsou položky obsahu, jako jsou produkty nebo propagační materiály, ze kterých si můžete vybrat. Přizpůsobení zvolí hlavní akci, která se zobrazí uživatelům, označovanou jako _Akce odměňování_prostřednictvím rozhraní API pro řazení. Každá akce může mít funkce odeslané pomocí požadavku Rank.

* **Kontext**: Chcete-li zadat přesnější pořadí, zadejte informace o kontextu, například:
    * Váš uživatel.
    * Zařízení, na kterém jsou. 
    * Aktuální čas.
    * Další informace o aktuální situaci.
    * Historická data o uživateli nebo kontextu.

    Vaše konkrétní aplikace mohou mít různé kontextové informace. 

* **[Funkce](concepts-features.md)** : Jednotka informací o položce obsahu nebo kontextu uživatele.

* **Odměna**: Míra, kterou uživatel odpověděl na rozhraní API řazení, se vrátilo jako skóre mezi 0 a 1. Hodnota 0 až 1 je nastavená vaší obchodní logikou na základě toho, jak volba pomáhá dosáhnout vašich obchodních cílů přizpůsobení. 

* **Průzkum**: Služba pro přizpůsobení zkoumá, kdy místo vrácení nejlepší akce zvolí pro uživatele jinou akci. Služba přizpůsobeného přizpůsobování zabraňuje posunu, stagnation a přizpůsobení probíhajícímu uživatelskému chování, a to zkoumáním. 

* **Doba trvání experimentu**: Doba, po kterou služba přizpůsobeného přizpůsobování čeká na určitou odměnu od okamžiku, kdy pro danou událost proběhlo volání pořadí.

* **Neaktivní události**: Neaktivní událost je ta, kde se říkáte Rank, ale nejste si jistí, že uživatel uvidí výsledek z důvodu rozhodnutí klientské aplikace. Neaktivní události umožňují vytvořit a uložit výsledky přizpůsobení a pak se rozhodnout o jejich zrušení později, aniž by to ovlivnilo model strojového učení.

* **Model**: Model pro přizpůsobení zachycuje všechna data zjištěná v souvislosti s uživatelským chováním a získá školicí data z kombinace argumentů, které odesíláte do volání funkce Rank a disměna, a s chováním školení, které určila zásada učení. 

* **Zásady učení**: Způsob, jakým přizpůsobuje vlaky model každé události, bude stanoven pomocí některých meta parametrů, které mají vliv na to, jak algoritmy strojového učení fungují. Nové smyčky pro přizpůsobení začnou používat výchozí zásady učení, což může přinést střední výkon. Při spuštění [hodnocení](concepts-offline-evaluation.md)může přizpůsobovat nové zásady učení, které jsou speciálně optimalizované pro případy použití smyčky. Přizpůsobený přizpůsobování bude výrazně lepší díky zásadám optimalizovaným pro každou specifickou smyčku vygenerovanou během hodnocení.

## <a name="example-use-cases-for-personalizer"></a>Příklady případů použití pro přizpůsobování

* Vysvětlení záměru & zrušení nejasností: pomůžete uživatelům lepší zkušenosti, když jejich záměr není jasný, protože nabízí možnost přizpůsobení každému uživateli.
* Výchozí návrhy pro nabídky & možnosti: robotovi nabídne nejpravděpodobnější položku přizpůsobeným způsobem jako první krok, namísto prezentace nepracovní nabídky nebo seznamu alternativ.
* Roboty & tónové znaky: pro roboty, které se můžou lišit ve stylu tónů, podrobností a psaní, zvažte, že se tyto vlastnosti upraví různými způsoby.
* Obsah upozornění & oznámení: Rozhodněte, jaký text se má použít pro výstrahy, aby bylo možné uživatele zapojit.
* Oznámení & časování výstrah: máte přizpůsobené informace o tom, kdy posílat oznámení uživatelům, aby je mohli zapojit.

## <a name="checklist-for-applying-personalizer"></a>Kontrolní seznam pro použití přizpůsobeného přizpůsobování

Přizpůsobené přizpůsobování můžete použít v situacích:

* Pro vaši aplikaci máte cíl pro podnikání nebo použitelnost.
* V aplikaci máte místo, kde se pro účely kontextového rozhodování o tom, co Ukázat uživatelům, tento cíl vylepší.
* Nejlepší volba může a měla by se poznat z kolektivního chování uživatelů a celkové skóre odměňování.
* Používání strojového učení pro přizpůsobení se řídí [pokyny pro použití](ethics-responsible-use.md) a možnostmi pro váš tým.
* Rozhodnutí může být vyjádřeno jako hodnocení nejlepší možnosti ([Akce](concepts-features.md#actions-represent-a-list-of-options) z omezené sady možností.
* To, jak dobře fungovala možnost volby, je možné vypočítat pomocí obchodní logiky, měřením určitého aspektu chování uživatelů a jejich vyjádřením v čísle od-1 do 1.
* Skóre odměňování nepřináší příliš mnoho nenalezených nebo externích faktorů, konkrétně doba trvání experimentu je dostatečně nízká, aby bylo možné vypočítat skóre odměňování, zatímco je stále relevantní.
* Můžete vyjádřit kontext pro řazení jako slovník o nejméně 5 funkcích, o kterých byste si myslíte, že by vám pomohly vytvořit správnou volbu a že neobsahují osobně identifikovatelné osobní údaje.
* Máte informace o jednotlivých akcích, které jsou ve slovníku nejméně 5 atributů nebo funkcí, které si myslíte, že přizpůsobíte správnou volbu.
* Můžete uchovávat data dostatečně dlouho a shromažďovat tak historii nejméně 100 000 interakcí.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Požadavky na strojové učení pro použití přizpůsobení

Přizpůsobování je založeno na posílení učení, přístupu ke strojovém učení, které získá výuku na základě zpětné vazby, kterou udělíte. 

Přizpůsobené aplikace se seznámí nejlépe v situacích, kdy:
* V případě, že je problém v průběhu času (například předvolby zprávy nebo způsobu), existuje dostatek událostí, aby zůstaly nad optimálním přizpůsobením. Přizpůsobený modul se přizpůsobí nepřetržité změně reálného světa, ale výsledky nebudou optimální, pokud není dostatek událostí a data, která by se dala zjistit a usadit na nové vzory. Měli byste zvolit případ použití, který je často k dispozici. Zvažte možnost Hledat případy použití, které se vyskytují minimálně 500 časů za den.
* Kontext a akce mají dostatek funkcí pro usnadnění učení.
* Pro pořadí podle volání je k dispozici méně než 50 akcí.
* Nastavení uchovávání dat umožňují přizpůsobování shromažďování dostatečného množství dat, aby bylo možné provádět vyhodnocení a optimalizaci zásad offline. Obvykle se jedná o minimálně 50 000 datových bodů.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Použití přizpůsobení ve webové aplikaci

Přidání smyčky do webové aplikace zahrnuje:

* Určete, jaké prostředí se má přizpůsobit, jaké akce a funkce máte, jaké kontextové funkce se mají použít, a jaké možnosti, které nastavíte.
* Přidejte odkaz na sadu SDK přizpůsobení ve vaší aplikaci.
* Až budete připraveni k přizpůsobení, zavolejte rozhraní API pro řazení.
* Uložte ID události. Později se vám pošle protiměna pomocí API pro odměnu.
1. Pokud jste si jisti, že uživatel viděl vaši personalizovanou stránku, zavolejte aktivovat pro událost.
1. Počkejte na výběr uživatelem seřazeného obsahu. 
1. K určení, jak dobře existoval výstup rozhraní API řazení, zavolejte API pro odměnu.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Použití přizpůsobení s robotem chatu

V tomto příkladu se dozvíte, jak použít přizpůsobení k vytvoření výchozího návrhu místo odeslání uživatele v několika nabídkách nebo volbách pokaždé, když.

* Získat [kód](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) pro tuto ukázku.
* Nastavte své řešení robot. Ujistěte se, že jste publikovali aplikaci LUIS. 
* Spravujte volání API pro řazení a měnu pro robota.
    * Přidejte kód pro správu zpracování záměru LUIS. Pokud se jako nejvyšší záměr vrátí **žádná položka None** nebo je skóre nejvyšší záměru pod prahovou hodnotou vaší obchodní logiky, pošlete seznam záměrů, aby bylo možné seřadit záměr.
    * Zobrazit seznam záměrů pro uživatele jako volitelných odkazů s prvním záměrem z odpovědi rozhraní API řazení
    * Zachyťte výběr uživatele a odešlete ho v volání API pro odměnu. 

### <a name="recommended-bot-patterns"></a>Doporučené vzory robotů

* Zajistěte volání rozhraní API pro přizpůsobování při každém nutnosti nejednoznačnosti, a to na rozdíl od ukládání výsledků do mezipaměti pro každého uživatele. Výsledek jednoznačného záměru se může v průběhu času pro jednu osobu změnit a povolit rozhraní API řazení pro zkoumání odchylek zrychlí celkové učení.
* Vyberte interakci, která je společná pro mnoho uživatelů, abyste měli k dispozici dostatek dat pro přizpůsobení. Úvodní otázky můžou být například lepší, než menší vyjasnění v grafu konverzace, ke kterým se může dostat jenom pár uživatelů.
* Použijte volání rozhraní API pro řazení, pokud chcete povolit konverzace "první návrh je pravá", kde se uživateli zobrazí výzva "líbí se vám X?" nebo "znamenali jste X?" a uživatel si ho může jenom potvrdit; na rozdíl od poskytnutí možností uživateli, kde musí zvolit z nabídky. Například uživatel: "chtěl bych seřadit robot" bot: "Přejete si Double espresso?". Tímto způsobem je signál pro odměnu také silný, protože se vztahuje přímo k jednomu návrhu.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Používání přizpůsobeného řešení s řešením doporučení

Pomocí nástroje pro doporučení můžete vyfiltrovat velký katalog na několik položek, které se pak dají odeslat do rozhraní API řazení jako 30 možných akcí.

Můžete použít moduly pro doporučení s přizpůsobením:

* Nastavte [řešení doporučení](https://github.com/Microsoft/Recommenders/). 
* Při zobrazení stránky volejte model doporučení, který získá krátký seznam doporučení.
* Zavolejte přizpůsobení a určete tak výstup řešení doporučení.
* Pošlete nám svůj názor na vaši akci uživatele pomocí volání API pro odměnu.


## <a name="pitfalls-to-avoid"></a>Nástrah, abyste se vyhnuli

* Nepoužívejte přizpůsobené chování, u kterého není individuální chování zjištěné u všech uživatelů, ale místo toho, aby je bylo možné zapamatovat pro konkrétní uživatele, nebo pochází ze seznamu alternativ konkrétního uživatele. Například použití přizpůsobeného modulu k navržení prvního Pizza objednávky ze seznamu 20 možných položek nabídky je užitečné, ale kontakt na volání ze seznamu kontaktů uživatelů, když se vyžaduje pomáhat s péči o dítě (například "Grandma"), není objekt, který je přizpůsobitelný napříč vaše uživatelská základna.


## <a name="adding-content-safeguards-to-your-application"></a>Přidání ochrany obsahu do aplikace

Pokud vaše aplikace umožňuje velké odchylky v obsahu zobrazeného uživatelům a některý z těchto obsahů může být pro některé uživatele nebezpečný nebo nevhodný, měli byste předem naplánovat, aby se zajistilo, že jsou zavedená správná ochranná opatření, aby se uživatelům zabránilo v nepřijatelném zobrazení. sušin. Nejlepším způsobem, jak implementovat ochranu, je: Nejlepším způsobem, jak implementovat ochranu, je:
    * Získejte seznam akcí, které je potřeba seřadit.
    * Vyfiltrujte ty, které nejsou pro cílovou skupinu životaschopné.
    * Tyto životaschopné akce je možné seřadit pouze.
    * Zobrazí uživateli horní seřazenou akci.

V některých architekturách může být výše uvedená sekvence těžká implementace. V takovém případě existuje alternativní přístup k implementaci ochrany po hodnocení, ale je třeba provést zřízení, takže akce, které spadají mimo ochranu, se nepoužijí ke školení modelu přizpůsobeného objektu pro přizpůsobení.

* Získejte seznam akcí, které se mají seřadit, a Naučte se deaktivovat.
* Akce pořadí.
* Zkontroluje, jestli je akce nejvyšší úrovně životaschopná.
    * Pokud je nejdůležitější akce životaschopná, aktivujte pro toto pořadí učení a pak ho Zobrazte uživateli.
    * Pokud akce nejvyšší úrovně není životaschopná, nepovolujte si výuku tohoto hodnocení a rozhodněte se pomocí vlastní logiky nebo alternativních přístupů k tomu, co se uživateli zobrazí. I v případě, že použijete druhou možnost, která je v pořadí, neaktivuje se učení pro toto hodnocení.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>Ověření adekvátní efektivity přizpůsobeného přizpůsobování

Efektivitu přizpůsobeného přizpůsobování můžete pravidelně monitorovat prováděním [offline hodnocení](how-to-offline-evaluation.md) .

## <a name="next-steps"></a>Další postup

Pochopení, [kde můžete použít](where-can-you-use-personalizer.md)přizpůsobování.
Provést [vyhodnocení offline](how-to-offline-evaluation.md)
