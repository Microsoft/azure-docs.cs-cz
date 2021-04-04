---
title: Kde a jak používat uživatelsky přizpůsobování
description: Přizpůsobování se dá použít v každé situaci, kdy vaše aplikace může vybrat správnou položku, akci nebo produkt k zobrazení, aby bylo lépe lepší, dosáhli jsme lepších obchodních výsledků nebo zlepšili produktivitu.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 87179950c33a6facb59ce499aa0ae393e53fb37f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "91777220"
---
# <a name="where-and-how-to-use-personalizer"></a>Kde a jak používat přizpůsobené přizpůsobování

Přizpůsobené možnosti můžete použít v jakékoli situaci, kdy aplikace potřebuje vybrat správnou akci (obsah), aby se zajistilo lepší, dosáhnout lepších obchodních výsledků nebo zvýšit produktivitu.

Individuální nastavení používá k výběru akce (obsahu), která se má zobrazit uživateli, možnost posílení učení. Výběr se může výrazně lišit v závislosti na množství, kvalitě a distribuci dat odesílaných službě.

## <a name="example-use-cases-for-personalizer"></a>Příklady případů použití pro přizpůsobování

* **Vysvětlení záměru & zrušení nejasností**: pomůžete uživatelům lepší zkušenosti, když jejich záměr není jasný, protože nabízí možnost, která je přizpůsobená.
* **Výchozí návrhy** pro nabídky & možnosti: robotovi nabídne nejpravděpodobnější položku přizpůsobeným způsobem jako první krok, namísto prezentace nepracovní nabídky nebo seznamu alternativ.
* **Znaky robota & tónové**: pro roboty, které se můžou lišit ve stylu tónů, podrobností a psaní, zvažte, že se tyto vlastnosti liší.
* **Obsah upozornění & oznámení**: Rozhodněte, jaký text se má použít pro výstrahy, aby bylo možné uživatele zapojit.
* **Oznámení & časování výstrah**: máte přizpůsobené informace o tom, kdy posílat oznámení uživatelům, aby je mohli zapojit.


## <a name="expectations-required-to-use-personalizer"></a>Požadované očekávání k použití přizpůsobeného přizpůsobování

Přizpůsobené přizpůsobování můžete použít v situacích, kdy splňujete nebo můžete implementovat následující pokyny.

|Pokyn|Vysvětlení|
|--|--|
|Obchodní cíl|Pro vaši aplikaci máte cíl pro podnikání nebo použitelnost.|
|Content|V aplikaci máte místo, kde se pro účely kontextového rozhodování o tom, co Ukázat uživatelům, tento cíl vylepší.|
|Množství obsahu|Pro každé volání máte méně než 50 akcí.|
|Agregovaná data|Nejlepší volba může a měla by se poznat z kolektivního chování uživatelů a celkové skóre odměňování.|
|Etické použití|Používání strojového učení pro přizpůsobení se řídí [pokyny pro použití](ethics-responsible-use.md) a volbami, které jste zvolili.
|Nejlepší jedna možnost|Kontextové rozhodnutí může být vyjádřeno jako hodnocení nejlepší možnosti (akce) z omezené sady možností.|
|Výsledek skóre|To, jak dobře se vybraná volba rozpracované pro vaši aplikaci může určit měřením určitého aspektu chování uživatelů, a jejím vyjádřením ve _[skóre odměňování](concept-rewards.md)_.|
|Příslušné časování|Skóre odměňování nepřináší příliš mnoho nenalezených nebo externích faktorů. Doba trvání experimentu je dostatečně nízká, aby bylo možné vypočítat skóre odměňování, zatímco je stále relevantní.|
|Dostatečné funkce kontextu|Můžete vyjádřit kontext pro řazení jako seznam alespoň 5 [funkcí](concepts-features.md) , o kterých se domníváte, že vám pomůžete vybrat správnou volbu a nezahrnuje uživatelsky identifikovatelné informace specifické pro uživatele.|
|Dostatečné funkce pro akce|Máte informace o jednotlivých možnostech obsahu, _akcích_, jako seznam o nejméně 5 [funkcích](concepts-features.md) , které si myslíte, že přizpůsobíte správnou volbu.|
|Denní data|V případě, že je problém v průběhu času (například předvolby zprávy nebo způsobu), existuje dostatek událostí, aby zůstaly nad optimálním přizpůsobením. Přizpůsobený modul se přizpůsobí nepřetržité změně reálného světa, ale výsledky nebudou optimální, pokud není dostatek událostí a data, která by se dala zjistit a usadit na nové vzory. Měli byste zvolit případ použití, který je často k dispozici. Zvažte možnost Hledat případy použití, které se vyskytují minimálně 500 časů za den.|
|Historická data|Vaše aplikace může uchovávat data dostatečně dlouho a shromažďovat tak historii nejméně 100 000 interakcí. To umožňuje přizpůsobovat shromažďování dostatečného množství dat pro offline vyhodnocení a optimalizaci zásad.|

**Nepoužívejte** Přizpůsobené, kde individuální chování není něco, co je možné zjistit u všech uživatelů. Například použití přizpůsobeného nástroje k navržení prvního Pizza objednávky ze seznamu 20 možných položek nabídky je užitečné, ale kontakt na volání ze seznamu kontaktů uživatelů při vyžadování pomoci s vychovatelí (například "Grandma") není objekt, který je přizpůsobitelný napříč vaší uživatelskou základnou.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Použití přizpůsobení ve webové aplikaci

Přidávání výukových smyček do webové aplikace zahrnuje:

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
* Použijte volání rozhraní API pro řazení, pokud chcete povolit konverzace "první návrh je pravá", kde se uživateli zobrazí výzva "líbí se vám X?" nebo "znamenali jste X?" a uživatel si ho může jenom potvrdit; na rozdíl od poskytnutí možností uživateli, kde musí zvolit z nabídky. Například uživatel: "chtěl bych seřadit bot" robot: "chcete mít dvojitou espresso?". Tímto způsobem je signál pro odměnu také silný, protože se vztahuje přímo k jednomu návrhu.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Používání přizpůsobeného řešení s řešením doporučení

Mnohé společnosti využívají moduly doporučení, marketingové a týmové nástroje, segmentování skupin a clusteringu, filtrování pro spolupráci a další způsoby, jak doporučit produkty od velkého katalogu zákazníkům.

[Úložiště GitHubu, které doporučuje Microsoft](https://github.com/Microsoft/Recommenders) , poskytuje příklady a osvědčené postupy pro vytváření systémů doporučení, které se poskytují jako Jupyter poznámkové bloky. Poskytuje pracovní příklady pro přípravu dat, sestavování modelů, vyhodnocování, ladění a zprovozňováníí pro doporučení pro mnohé běžné přístupy, včetně xDeepFM, správní rady, ALS, MKP, DKN.

Přizpůsobování může pracovat s modulem doporučení, když je přítomen.

* Moduly doporučení využívají velké množství položek (například 500 000) a doporučují podmnožinu (například prvních 20) ze stovek nebo tisíců možností.
* Přizpůsobený modul přebírá malý počet akcí s velkým množstvím informací o nich a řadí je v reálném čase pro daný bohatý kontext, zatímco většina modulů pro doporučení používá jenom několik atributů o uživatelích, produktech a jejich interakcích.
* Přizpůsobený modul je navržený tak, aby se samostatně seznámil s uživatelskými preferencemi, což vám poskytne lepší výsledky, při kterých se rychle mění obsah, jako jsou novinky, živé události, živý obsah komunity, obsah s denními aktualizacemi nebo sezónní obsah.

Běžným použitím je pořídit výstup modulu doporučení (například prvních 20 produktů pro určitého zákazníka) a použít ho jako vstupní akce pro přizpůsobené aplikace.

## <a name="adding-content-safeguards-to-your-application"></a>Přidání ochrany obsahu do aplikace

Pokud vaše aplikace umožňuje velké odchylky v obsahu zobrazeného uživatelům a některý z těchto obsahů může být pro některé uživatele nebezpečný nebo nevhodný, měli byste předem naplánovat, aby se zajistilo, že jsou zavedená správná ochranná opatření, která uživatelům brání v zobrazení nepřijatelného obsahu. Nejlepším způsobem, jak implementovat ochranu, je:
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


## <a name="next-steps"></a>Další kroky

[Etika & odpovědná za použití](ethics-responsible-use.md).
