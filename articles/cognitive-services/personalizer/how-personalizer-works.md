---
title: Jak funguje Personalizer - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer pomocí strojového učení chcete zjistit, jaká akce se má použít v kontextu. Každé smyčce learning má model, který se trénuje výhradně na datech, která jste odeslali do něj pomocí pořadí a volá potřebu. Každý studijní smyčky je zcela nezávisle na sobě navzájem.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: d0a0a6101d876493188426d19f2fa845d20ee274
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055492"
---
# <a name="how-personalizer-works"></a>Jak služba Personalizace funguje

Personalizer pomocí strojového učení chcete zjistit, jaká akce se má použít v kontextu. Každé smyčce learning se model, který se trénuje výhradně na datech, která jste odeslali do ho přes **pořadí** a **Reward** volání. Každý studijní smyčky je zcela nezávisle na sobě navzájem. Vytvoření smyčky učení pro každou část nebo chování aplikace, které chcete přizpůsobit.

Pro každou smyčku **volání rozhraní API pořadí s** založenou na aktuálním kontextu se:

* Seznam možných akcí: obsah položky, ze kterého se má vybrat hlavní akce.
* Seznam [funkce kontextu](concepts-features.md): kontextově relevantní data, jako je například uživatel, obsah a kontextu.

**Pořadí** rozhraní API se rozhodne použít buď:

* _Ochrana Exploit_: Aktuální model rozhodnout nejvhodnějších akcí, které na základě posledních dat.
* _Prozkoumejte_: Vyberte jinou akci místo hlavní akce.

**Reward** rozhraní API:

* Shromažďuje data pro trénování modelu zaznamenáváním funkce a potřebu skóre každé pořadí volání.
* Tato data používá k aktualizaci modelu na základě zadané v nastavení _Learning zásad_.

## <a name="architecture"></a>Architektura

Následující obrázek ukazuje architekturu tok volání hodnocení a potřebu volání:

![alternativní text](./media/how-personalizer-works/personalization-how-it-works.png "jak funguje individuálního nastavení")

1. Personalizer používá interní modelů AI k určení pořadí v akci.
1. Služba rozhodne, zda aktuální model zneužití nebo prozkoumat nové možnosti pro model.  
1. Výsledek hodnocení se odešle do centra událostí.
1. Potřebu přijetí Personalizer potřebu se odešle do centra událostí. 
1. Počet rozměrů a potřebu se korelují.
1. AI dojde k aktualizaci modelu na základě výsledků korelace.
1. Modul odvození se aktualizuje s novým modelem. 

## <a name="research-behind-personalizer"></a>Výzkum za Personalizer

Personalizer je založeno na špičkové vědy a výzkumu v oblasti [Zpětnovazebnému učení](concepts-reinforcement-learning.md) včetně papíry, výzkumu a probíhající oblasti průzkum v Microsoft Research.

## <a name="terminology"></a>Terminologie

* **Učení smyčky**: Pro každou součást aplikace, který může přinést přizpůsobení může vytvořit smyčku učení. Pokud máte více než jednom prostředí můžete přizpůsobit, vytvořte pro každou smyčku. 

* **Akce**: Akce jsou položky obsahu, jako je například produktů nebo propagační akce lze vybírat. Personalizer vybere hlavní akce se zobrazí uživatelům, označované jako _odměna v rámci akce_, prostřednictvím rozhraní API pořadí. Každá akce může mít funkce odeslanou s požadavkem pořadí.

* **Kontext**: Poskytnout přesnější pořadí, zadejte informace o kontextu, například:
    * Vaše uživatele.
    * Zařízení, které se nachází na. 
    * Aktuální čas.
    * Další data o aktuální situaci.
    * Historická data o uživateli nebo kontext.

    Konkrétní aplikace může mít jiné kontextové informace. 

* **[Funkce](concepts-features.md)** : Jednotka informace o obsahu položky nebo kontextu uživatele.

* **Potřebu**: Míra jak uživatel odpověděl na rozhraní API pořadí vrácená akce, jako skóre mezi 0 a 1. Hodnota 0 až 1 je nastavena pomocí obchodní logiky a podle jak volba pomohl dosáhnout vašich obchodních cílů individuálního nastavení. 

* **Zkoumání**: Služba Personalizer k procházení, když místo vrácení nejvhodnějších akcí, zvolí jinou akci pro daného uživatele. Služba Personalizer zabraňuje odchylek stagnation a můžete přizpůsobit chování probíhající uživatelů, ve kterých. 

* **Doba trvání experimentu**: Množství času, které služba Personalizer čeká potřebu, od chvíle, kdy pořadí volání se naplnilo pro tuto událost.

* **Neaktivní události**: Neaktivní události je jedním kde volána pořadí, ale nejste jisti, že uživatel uvidí někdy výsledek z důvodu rozhodnutí klientské aplikace. Neaktivní události a umožňují vytvářet a ukládat výsledky přizpůsobení, potom se rozhodnete zahodíte později bez dopadu na modelu strojového učení.

* **Model**: Personalizer model zachytí všechna data se dozvěděli o chování uživatelů, získávání trénovací data z kombinace argumentů, které odesíláte do hodnocení a potřebu volání a s školení chování určené zásady učení. 

## <a name="example-use-cases-for-personalizer"></a>Příklad případy použití pro Personalizer

* Upřesnění záměru a odstraňování mnohoznačností: pomoc při jejich záměr je mají vaši uživatelé lepší nerušte zaškrtnutí políčka tím, že poskytuje možnost, která je přizpůsobit jednotlivým uživatelům.
* Výchozí návrhy nabídky a možnosti: mají bot navrhnout nejpravděpodobnější položky individuálním způsobem jako první krok, namísto prezentace na impersonal nabídky nebo seznam alternativ.
* Vlastnosti bot & tón: pro roboty, tón, úroveň podrobností a styl psaní se může lišit, zvažte různé tyto vlastnosti individuální způsoby.
* Obsah oznámení & upozornění: Rozhodněte, jaký text, který má použít pro výstrahy k zapojení více uživatelů.
* & Oznámení výstrah časování: zajistit přizpůsobený learning nad tím, kdy k odesílání oznámení uživatelům a zapojte je informace.

## <a name="checklist-for-applying-personalizer"></a>Kontrolní seznam pro použití Personalizer

Personalizer můžete použít v situacích, kde:

* Máte podnikový proces nebo použitelnosti cíle pro vaši aplikaci.
* Místo, kde máte ve své aplikaci, ve kterém rozhodování kontextové toho, jak zobrazit uživatelům zlepší tohoto cíle.
* O nejlepší volbu můžete a měly být přeneseny prostřednictvím kolektivní uživatele chování a součet reward skóre.
* Použití služby machine learning pro přizpůsobení následuje [pokyny pro využívání](ethics-responsible-use.md) a možnosti pro váš tým.
* Rozhodnutí může být vyjádřený jako nejlepší hodnocení ([akce](concepts-features.md#actions-represent-a-list-of-options) z omezenou sadu možností.
* Jak dobře pracoval široké možnosti volby můžete počítají tak, že obchodní logiky a měření některé aspekty chování uživatelů a jeho vyjádření v číslo v rozsahu od -1 a 1.
* Skóre reward nebude přinesli příliš mnoho zkreslení nebo vnější faktory, konkrétně trvání experimentu je dostatečně nízko, skóre reward můžete vypočítat, i když je stále relevantní.
* Kontext pro řád můžete vyjádřit jako slovník nejméně 5 funkcí, které si myslíte, že by pomohl učinit správnou volbu a, která nezahrnuje identifikovatelné osobní údaje.
* Máte informace o každé akce jako slovník atributů nejméně 5 nebo funkce, které si myslíte, že vám pomůže Personalizer učinit správnou volbu.
* Data můžete uchovávat dlouho dostatek historie alespoň 100 000 interakcí hromadit.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Strojové učení důležité informace týkající se použití Personalizer

Personalizer vychází zpětnovazební učení, přístup k sadě machine learning, která získá vedená zpětnou vazbu, že jí přiřadit. 

Personalizer se dozvíte, nejlépe v situacích, kde:
* Není k dispozici dostatek události pro získání přehledu optimální individuálního nastavení, pokud se problém drifts v čase (například předvolby v zprávy nebo způsobem). Průběžné změny v reálném světě se bude přizpůsobovat personalizer, ale výsledky nebudou optimální, pokud není k dispozici dostatek události a data z se naučíte, jak vyhledat a vyrovnejte na nová schémata. Měli byste vybrat, ke které dochází často případu použití. Vezměte v úvahu hledáte případy použití, ke kterým dochází aspoň 500 časy za den.
* Kontext a akce mají dostatek funkce pro usnadnění vzdělávání.
* Nejsou k dispozici méně než 50 akcí pro řazení za volání.
* Nastavení uchovávání dat povolit Personalizer shromažďovat dostatek dat k provedení offline vyhodnocení a optimalizace zásad. Toto je obvykle nejméně 50 000 datových bodů.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Jak používat Personalizer ve webové aplikaci

Přidání smyčky do webové aplikace zahrnuje:

* Určete, které prostředí můžete přizpůsobit, jaké akce a funkce budete mít, jaké funkce kontextu použít, a jaký rewards nastavíte.
* Přidejte odkaz na SDK přizpůsobení v aplikaci.
* Volání rozhraní API Rank, když budete chtít přizpůsobit.
* Store ID události. Odešlete reward s rozhraním API potřebu.
1. Zavolejte aktivovat události, jakmile máte jistotu, že uživatel viděl vlastní stránky.
1. Počkejte, výběru uživatelem seřazený obsahu. 
1. Volání rozhraní API Reward k určení, jak dobře nebyla výstup rozhraní API pro řazení.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Jak používat Personalizer s chatovací robot

V tomto příkladu uvidíte, jak používat námět výchozí místo abyste odesílali pokaždé, když uživatel dolů řadu nabídky a možnosti přizpůsobení.

* Získejte [kód](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) pro tuto ukázku.
* Nastavení řešení v robota. Ujistěte se, že můžete publikovat svoji aplikaci LUIS. 
* Správa pořadí a volání rozhraní API Reward pro robota.
    * Přidejte kód pro správu služby LUIS záměru zpracování. Pokud **žádný** se vrátí jako hlavní záměr nebo skóre hlavní záměr je pod prahovou hodnotou vaši obchodní logiku, odesílání příkazů do Personalizer na pořadí seznamu příkazů.
    * Zobrazte seznam záměru uživatele jako volitelnou odkazy s prvním cílem jsou záměr seřazené horní z odpovědi rozhraní API pořadí.
    * Zachycení výběru uživatele a odeslat do volání rozhraní API potřebu. 

### <a name="recommended-bot-patterns"></a>Vzorů robota doporučené

* Pokaždé, když je potřeba odstraňování mnohoznačností, na rozdíl od ukládání do mezipaměti výsledky pro jednotlivé uživatele volat rozhraní API Personalizer pořadí. Výsledek odstraňování záměr může postupem času změnit pro jednu osobu a umožňuje rozhraní API pořadí a prozkoumejte odchylky zrychlí celkové učení.
* Zvolte, které jsou společné s mnoha uživateli, takže máte dostatek dat. Chcete-li upravit interakce. Například úvodní otázky může být lepší přizpůsobí než menší vyjasnění hluboko v grafu konverzace, který se může zobrazit pouze několik uživatelů k.
* Použití volání rozhraní API pořadí k povolení "první návrh představuje pravé" konverzace, kde uživatel získá dotaz "Chcete X?" nebo "Neměli jste na mysli X?" a právě můžete ověřit uživatele; na rozdíl od poskytuje možnosti pro uživatele, kde musíte vybrat z nabídky. Například uživatel: "Chci pořadí kávu" Bot: "Způsobem double espresso?". Tímto způsobem reward signál je také silné jak se vztahuje přímo na jeden návrh.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Jak používat Personalizer řešení doporučení

Filtrovat velká katalogu pro několik položek, které mohou být poskytovány jako 30 možných akcí odeslaného do rozhraní API pořadí použijte doporučovací modul.

Generátory doporučení můžete použít s Personalizer:

* Nastavit [řešení doporučení](https://github.com/Microsoft/Recommenders/). 
* Při zobrazení stránky, vyvolejte Model doporučení zobrazíte krátký seznam doporučení.
* Volání přizpůsobení řadit výstup řešení doporučení.
* Pošlete názor na akci uživatele se volání rozhraní API potřebu.


## <a name="pitfalls-to-avoid"></a>Aby se zabránilo nástrahy

* Nepoužívejte Personalizer kde přizpůsobená chování není něco, co můžete zjistit přes všechny uživatele, ale spíše něco, co by měl být zapamatovaných pro konkrétní uživatele, nebo pochází z konkrétního uživatele seznamu možností. Například použití Personalizer navrhnout první pizza pořadí ze seznamu 20 položek nabídky je to možné je užitečné, ale kontaktu pro volání z uživatelů při vyžadování nápovědu péče (například "Grandma") není něco, co je přizpůsobitelný napříč seznam kontaktů svou uživatelskou základnu.


## <a name="adding-content-safeguards-to-your-application"></a>Přidání obsahu chrání pro vaši aplikaci

Pokud vaše aplikace umožňuje pro velké rozdíly v obsahu pro uživatele a některé z uvedené, že obsah může být nebezpečné nebo nevhodnou pro některé uživatele, měli byste naplánovat napřed Ujistěte se, že správný bezpečnostní opatření se používá k zabránění uživatelům v zobrazení nemůže být přijata obsah. Nejlepší model implementovat bezpečnostní opatření je: Nejlepší model implementovat bezpečnostní opatření je:
    * Získání seznamu akcí na pořadí.
    * Filtrování, které nejsou použitelné pro cílovou skupinu.
    * Pouze pořadí tyto akce přijatelné.
    * Zobrazte horní seřazené akce uživatele.

V některých architekturách může být obtížně se implementují výše uvedené pořadí. V takovém případě je alternativním přístupem k implementaci bezpečnostní opatření po hodnocení, ale i ustanovení je potřeba provést akce, které spadá mimo zabezpečení se používá pro trénování modelu Personalizer proto.

* Získání seznamu akcí na pořadí, službou learning deaktivuje.
* RANK – akce.
* Zkontrolujte, jestli je hlavní akce přijatelné.
    * Pokud je hlavní akce přijatelné, aktivujte učení pro toto pořadí a pak si ukážeme uživateli.
    * Pokud hlavní akce není přijatelné, není aktivovat učení pro toto pořadí a rozhodněte prostřednictvím vlastní logiku nebo alternativní postupy, jak zobrazit uživateli. I v případě, že použijete možnost seřazený druhé nejlepší, Neaktivujte učení pro toto pořadí.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>Ověřuje se, jestli odpovídající efektivitu Personalizer

Můžete sledovat efektivitu Personalizer pravidelně provedením [offline hodnocení](how-to-offline-evaluation.md)

## <a name="next-steps"></a>Další postup

Vysvětlení [ve kterém mohou používat Personalizer](where-can-you-use-personalizer.md).