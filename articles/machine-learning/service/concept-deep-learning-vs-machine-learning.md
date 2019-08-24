---
title: Obsáhlý Learning a strojové učení
titleSuffix: Azure Machine Learning service
description: Přečtěte si o vztahu mezi hloubkovým učením a strojovým učením a o tom, jak se vejdou do kategorie umělal Intelligence. Tento článek popisuje, jak obsáhlý Learning řeší scénáře, jako je odhalování podvodů, rozpoznávání hlasu a obličeje, mínění Analytics a prognózy časových řad.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: 5301be493ef22fc2d74cc337d88b04caf391fdaa
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982808"
---
# <a name="deep-learning-vs-machine-learning"></a>Obsáhlý Learning a strojové učení

Tento článek vám pomůže pochopit vztah mezi hloubkovým učením a strojovým učením. Dozvíte se, jak tyto dvě koncepce porovnávají a jak se vejdou do širší kategorie umělých inteligentních funkcí. Nakonec tento článek popisuje, jak se dá využít obsáhlý Learning v reálných scénářích, jako je odhalování podvodů, rozpoznávání hlasu a obličeje, analýza mínění a prognózování časových řad.

## <a name="how-do-deep-learning-machine-learning-and-ai-relate-to-one-another"></a>Jak se na sebe navzájem souvisí obsáhlý Learning, Machine Learning a AI?

![Diagram vztahů: AI a strojové učení vs. obsáhlý Learning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Aby bylo možné pochopit rozdíl mezi hloubkovým učením a strojovým učením vs. Seznamte se s každým z těchto polí studia:

- **Obsáhlý Learning** je podmnožinou strojového učení založeného na umělých neuronovéch sítích, které umožňují počítači vlak sám. V tomto případě se _proces učení_ nazývá _hluboko_, protože struktura umělých neuronové sítí sestává z několika vstupních, výstupních a skrytých vrstev. Každá vrstva obsahuje jednotky, které transformují vstupní data na informace, které může další vrstva použít k provedení určité prediktivní úlohy. Díky této struktuře se může počítač seznámit s vlastním zpracováním dat.

- **Machine Learning** je podmnožinou umělých inteligentních funkcí, které zahrnují techniky (jako je obsáhlý Learning), které usnadňují vylepšení počítačů při práci. V tomto případě je _proces učení_ založený na následujících krocích:

    1. Podávání algoritmu s daty poskytnutím dalších informací (například při extrakci funkcí).
    2. Tato data slouží k učení modelu.
    3. Otestujte a nasaďte model.
    4. Využití nasazeného modelu k provedení určité automatizované prediktivní úlohy.

- **Umělá inteligentní funkce (AI)** je technika, která umožňuje počítačům napodobovat lidské inteligentní funkce. Zahrnuje Machine Learning. Je důležité pochopit vztah mezi _AI a strojovým učením vs. obsáhlý Learning_: Machine Learning je způsob, jak dosáhnout umělých analytických nástrojů, což znamená, že používání strojového učení a technik hloubkového učení vám umožní sestavovat počítačové systémy a aplikace, které můžou provádět úlohy běžně spojené s lidskými inteligentními funkcemi, jako je třeba vizuální vnímání, rozpoznávání řeči, rozhodování a překlad mezi jazyky.

## <a name="comparison-of-deep-learning-vs-machine-learning"></a>Porovnání hloubkového učení a strojového učení

Teď, když máte přehled o strojovém učení vs. hluboké učení, Porovnejte tyto dvě postupy. V případě strojového učení musí být algoritmus nápomocen, jak zajistit přesnou předpověď tím, že mu poskytne další informace; vzhledem k tomu, že v případě hloubkového učení je algoritmus schopný zjistit, že se jedná o vlastní zpracování dat, a to díky umělé neuronové síťové struktuře.

Následující tabulka porovnává dvě metody podrobněji:

| |Všechny Machine Learning |Jenom obsáhlý Learning|
|---|---|---|
|  **Počet datových bodů** | Může používat malé objemy dat poskytované uživateli. | Vyžaduje velké množství školicích dat, aby bylo možné provádět stručné závěry. |
|  **Závislosti hardwaru** | Může pracovat na méně koncových počítačích. Nepotřebuje velký objem výpočetního výkonu. | Závisí na vysoce koncových počítačích. Tím je v podstatě velký počet operací násobení matice. Tyto operace je možné efektivně optimalizovat pomocí GPU. |
|  **Featurization proces** | Vyžaduje, aby se funkce přesně identifikovaly a vytvořily uživatelé. | Zjišťuje funkce na nejvyšší úrovni z dat a vytváří nové funkce sám o sobě. |
|  **Postup učení** | Rozdělí úkoly na malé kousky a potom sloučí přijaté výsledky do jednoho závěru. | Vyřeší problém na konci. |
|  **Doba spuštění** | Poměrná prodleva mezi několika sekundami a několika hodinami v rozsahu | Vybírá neobvykle dlouhou dobu, protože existuje mnoho parametrů v rámci algoritmu hloubkového učení. |
|  **Výstup** | Výstupem je obvykle číselná hodnota, například skóre nebo klasifikace. | Výstupem může být cokoli ze skóre, textu, prvku nebo zvuku. |

## <a name="deep-learning-use-cases-what-problems-does-it-solve"></a>Případy použití hloubkového učení: Jaké problémy řeší?

Z důvodu umělé struktury Neuronové sítě je obsáhlý Learning v Excelu v identifikaci vzorů nestrukturovaných dat, jako jsou obrázky, zvuk, video a text. Z tohoto důvodu se rychle transformují spousty oborů, včetně zdravotní péče, energie, umožňuje, přepravy a dalších, a přepřemýšlejí se na tradiční obchodní procesy. Některé z nejběžnějších aplikací pro obsáhlý Learning jsou popsané v následujících odstavcích.

### <a name="named-entity-recognition"></a>Rozpoznávání pojmenovaných entit

Jedno použití sítí s hloubkovým učením je rozpoznávání názvů, což je způsob, jak extrahovat z nestrukturovaných, neoznačených dat určité typy informací, jako jsou lidé, místa, společnosti nebo věci. Tyto informace pak mohou být uloženy ve strukturovaném schématu, aby bylo možné sestavit seznam adres nebo sloužit jako srovnávací test pro modul ověřování identity.

### <a name="object-detection"></a>Detekce objektů

Obsáhlý Learning se použil v mnoha případech použití detekce objektů. Rozpoznávání objektů je ve skutečnosti proces dvou částí: klasifikace obrázku a pak lokalizace imagí. Klasifikace obrázku určuje, co jsou objekty v imagi, jako je auto nebo osoba, zatímco lokalizace imagí poskytuje konkrétní umístění těchto objektů. Detekce objektů se už používá v oblasti her, maloobchodu, turistiky a vlastních hnacích automobilů.

### <a name="image-caption-generation"></a>Generování titulku obrázku

Podobně jako u úlohy rozpoznávání obrázků je titulek obrázku v případě, že je pro daný obrázek v systému vygenerován titulek, který popisuje obsah obrázku. Jakmile můžete detekovat objekty v fotografiích a generovat pro tyto objekty popisky, uvidíte, že v dalším kroku jsou tyto popisky převedené do souvislého popisu věty. Obecně platí, že systémy zahrnují použití velmi velkých konvolučních sítí pro detekci objektů v fotografiích a pak znovu aktuální neuronové sítě (RNN), aby popisky přepnuly na soudržnou větu.

### <a name="machine-translation"></a>Strojový překlad

Strojový překlad používá slova, fráze nebo věty z jednoho jazyka a automaticky je překládá do jiného jazyka. Automatické překlad strojového překladu je delší dobu, ale obsáhlý Learning dosahuje horních výsledků ve dvou konkrétních oblastech: automatický převod textu (a převod řeči na text) a automatický překlad obrázků. Díky správné transformaci dat je rozsáhlá síť schopná pochopit textové, zvukové a vizuální signály. Strojový překlad lze použít k identifikaci fragmentů zvuku ve větších zvukových souborech a přepisovat mluveného slova nebo obrázku jako textu.

### <a name="text-analytics"></a>Analýza textu

Jeden důležitý úkol, který může obsáhlý Learning provádět, je e-Discovery. Společnosti využívají analýzu textu na základě obsáhlého učení pro zjišťování obchodování v programu Insider a dodržování předpisů pro státní správu. Fondy transakcí využívají Text Analytics k přechodu do rozsáhlých úložišť dokumentů pro získání přehledů o budoucích investicích a mínění trhu. Případ použití pro analýzu textu na základě obsáhlého učení se otáčí kolem své schopnosti analyzovat obrovské objemy textových dat, aby se prováděly analýzy nebo vydávaly agregace.

## <a name="what-are-artificial-neural-networks"></a>Co jsou umělé sítě neuronové?

Umělé sítě neuronové se vytváří pomocí vrstev připojených uzlů. Modely hloubkového učení používají sítě neuronové s velkým množstvím vrstev. Nejoblíbenější umělá neuronové síť typologies je popsána níže.

### <a name="feedforward-neural-network"></a>Síť neuronové Feedforward

Síť neuronové Feedforward je nejzákladnější typ umělé neuronové sítě, ve které jsou informace přenášeny pouze v jednom směru ze vstupní vrstvy do výstupní vrstvy. Feedforward neuronové Networks transformují vstup tím, že ho umístí prostřednictvím řady skrytých vrstev. Každá vrstva se skládá ze sady neurons, kde je každá vrstva plně připojená ke všem neurons ve vrstvě. Nakonec je k dispozici poslední plně připojená vrstva – výstupní vrstva, která představuje vygenerovanou předpovědi.

### <a name="recurrent-neural-network"></a>Znovu aktuální neuronové síť

Opakující se neuronové sítě jsou široce používaný typ umělé neuronové sítě, který funguje na principu uložení výstupu vrstvy a jejich krmení zpátky do vstupní vrstvy, která vám usnadní předpověď výsledku vrstvy. Tyto neuronové sítě mají větší možnosti učení a jsou široce používané pro složitější úkoly, jako je například psaní rukou nebo rozpoznávání jazyka.

### <a name="convolutional-neural-networks"></a>Konvoluční neuronové sítě

Síť neuronové konvoluční je obzvláště efektivní typ umělé neuronové sítě, která prezentuje jedinečnou architekturu. Nejprve jsou vrstvy uspořádány ve třech rozměrech: šířka, Výška a hloubka. Neurons v jedné vrstvě se navíc nepřipojí ke všem neurons v následující vrstvě, ale pouze do malých oblastí. Nakonec bude konečný výstup snížen na jeden vektor skóre pravděpodobnosti uspořádané podél rozměru hloubky. Tyto sítě neuronové byly použity v oblastech, jako je například rozpoznávání obrázku a klasifikace.

## <a name="next-steps"></a>Další kroky

V následujících článcích se dozvíte, jak používat technologii pro hloubkové učení ve [službě Azure Machine Learning](/azure/machine-learning/service/):

- [Klasifikace ručně psaných číslic pomocí modelu TensorFlow](how-to-train-tensorflow.md)
- [Klasifikace rukou psaných číslic pomocí TensorFlow Estimator a Keras](how-to-train-keras.md)
- [Klasifikace imagí pomocí modelu Pytorch](how-to-train-pytorch.md)
- [Klasifikace ručně psaných číslic pomocí modelu zřetězení](how-to-train-pytorch.md)
