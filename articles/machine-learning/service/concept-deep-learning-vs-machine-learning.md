---
title: Obsáhlý Learning a strojové učení
titleSuffix: Azure Machine Learning service
description: Přečtěte si o hloubkovém učení a strojovém učení a o tom, jak se obě koncepce vztahují na umělou analýzu. Obsáhlý Learning se dá použít ve scénářích, jako je odhalování podvodů, rozpoznávání hlasu a obličeje, analýza mínění a prognózování časových řad.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: 3b0546c42ddd5252b35d1a77d7b152733beec233
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240957"
---
# <a name="deep-learning-vs-machine-learning"></a>Obsáhlý Learning a strojové učení

Tento článek vám pomůže porovnat hloubkové učení a strojové učení. Dozvíte se, jak tyto dvě koncepce porovnávají a jak se vejdou do širší kategorie umělých inteligentních funkcí. Tento článek také popisuje, jak se dá využít obsáhlý Learning v reálných scénářích, jako je odhalování podvodů, rozpoznávání hlasu a obličeje, mínění Analytics a prognózy časových řad.

## <a name="deep-learning-machine-learning-and-ai"></a>Obsáhlý Learning, strojové učení a AI

![Diagram vztahů: AI a strojové učení vs. obsáhlý Learning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Zvažte následující definice pro pochopení hloubkového učení vs. strojové učení vs. AI

- **Obsáhlý Learning** je podmnožinou strojového učení založeného na umělých neuronovéch sítích. _Proces výuky_ je _hluboký_ , protože struktura umělých neuronové sítí sestává z několika vstupních, výstupních a skrytých vrstev. Každá vrstva obsahuje jednotky, které transformují vstupní data na informace, které může další vrstva použít pro určitou prediktivní úlohu. Díky této struktuře se může počítač seznámit s vlastním zpracováním dat.

- **Machine Learning** je podmnožinou umělých inteligentních funkcí, které využívají techniky (jako je obsáhlý Learning), která umožňuje počítačům používat prostředí pro zlepšení úloh. _Proces výuky_ je založený na následujících krocích:

   1. Data se zakládá do algoritmu. (V tomto kroku můžete k modelu zadat další informace, například prováděním extrakce funkcí.)
   1. Tato data slouží k učení modelu.
   1. Otestujte a nasaďte model.
   1. Využití nasazeného modelu k provedení automatizované prediktivní úlohy. (Jinými slovy, zavolejte a použijte nasazený model pro příjem předpovědi vrácených modelem.)

- **Umělá inteligentní funkce (AI)** je technika, která umožňuje počítačům napodobovat lidské inteligentní funkce. Zahrnuje Machine Learning. 
 
Je důležité pochopit vztah mezi AI, Machine Learningem a hlubokou výukou. Machine Learning je způsob, jak dosáhnout umělých inteligentních funkcí. Pomocí technik strojového učení a hloubkového učení můžete sestavovat počítačové systémy a aplikace, které budou často přidruženy k lidským inteligentním funkcím. Tyto úlohy zahrnují vizuální vnímání, rozpoznávání řeči, rozhodování a překlad jazyka.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Techniky hloubkového učení vs. Machine Learning 

Teď, když máte přehled o strojovém učení vs. hluboké učení, porovnáme tyto dvě techniky. Ve strojovém učení musí být algoritmus znám, jak zajistit přesnou předpověď tím, že se zaměříte na více informací (například provedením extrakce funkcí). V obsáhlém učení se algoritmus může naučit, jak zajistit přesnou předpověď prostřednictvím vlastního zpracování dat, a to díky umělé struktuře neuronové sítě.

Následující tabulka porovnává tyto dvě metody podrobněji:

| |Všechny Machine Learning |Jenom obsáhlý Learning|
|---|---|---|
|  **Počet datových bodů** | Může používat malé objemy dat poskytované uživateli. | Vyžaduje velké množství školicích dat, aby bylo možné provádět stručné závěry. |
|  **Závislosti hardwaru** | Může pracovat na méně koncových počítačích. Nepotřebuje velké množství výpočetního výkonu. | Závisí na vysoce koncových počítačích. Tím je v podstatě velký počet operací násobení matice. GPU může efektivně optimalizovat tyto operace. |
|  **Featurization proces** | Vyžaduje, aby funkce byly přesně identifikovány a vytvořeny uživateli. | Zjišťuje funkce na nejvyšší úrovni z dat a vytváří nové funkce sama o sobě. |
|  **Postup učení** | Rozdělí úkoly na malé kousky a pak kombinuje přijaté výsledky do jednoho závěru. | Vyřeší tento problém na konci. |
|  **Doba spuštění** | Provede poměrně krátkou dobu, od několika sekund až po několik hodin. | Vyučování trvá neobvykle dlouhou dobu, protože algoritmus hloubkového učení zahrnuje mnoho parametrů. |
|  **Výstup** | Výstupem je obvykle číselná hodnota, jako je například skóre nebo klasifikace. | Výstupem může být text, skóre, element nebo zvuk. |

## <a name="deep-learning-use-cases"></a>Případy použití hloubkového učení

Z důvodu umělé struktury Neuronové sítě je obsáhlý Learning v Excelu v identifikaci vzorů nestrukturovaných dat, jako jsou obrázky, zvuk, video a text. Z tohoto důvodu obsáhlé učení rychle transformuje spoustu odvětví, včetně zdravotní péče, energie, financí a dopravy. Tyto obory nyní přemýšlení o tradičních obchodních procesech. 

Některé z nejběžnějších aplikací pro obsáhlý Learning jsou popsané v následujících odstavcích.

### <a name="named-entity-recognition"></a>Rozpoznávání pojmenovaných entit

Jedno použití sítí s hloubkovým učením je rozpoznávání názvů entit, což je způsob, jak extrahovat určité typy informací z nestrukturovaných a neoznačených dat. Tyto informace mohou být osoby, místa, společnosti nebo věci. Informace pak mohou být uloženy ve strukturovaném schématu, aby bylo možné sestavit seznam adres nebo sloužit jako srovnávací test pro modul ověřování identity.

### <a name="object-detection"></a>Detekce objektů

Obsáhlý Learning se použil v mnoha případech použití detekce objektů. Detekce objektu se skládá ze dvou částí: klasifikace obrázku a pak lokalizace imagí. _Klasifikace_ obrázku identifikuje objekty obrázku, například auta nebo lidi. _Lokalizace_ obrázků poskytuje konkrétní umístění těchto objektů. 

Detekce objektů se už používá v oborech, jako jsou hry, maloobchodní prodej, cestovní ruch a osobní automobily.

### <a name="image-caption-generation"></a>Generování titulku obrázku

Podobně jako rozpoznávání obrázku v titulkování obrázků pro daný obrázek systém musí vygenerovat titulek, který popisuje obsah obrázku. Když můžete objekty v fotografiích detekovat a označovat, je dalším krokem změna těchto popisků na popisné a soudržné věty. Obecně platí, že systémy titulků obrázků využívají velmi velké konvoluční sítě neuronové ke zjišťování objektů v obrazových fotografiích a pak používají znovu aktuální neuronové síť (RNN), aby se štítky přepnuly na souvislé věty.

### <a name="machine-translation"></a>Strojový překlad

Strojový překlad používá slova, fráze nebo věty z jednoho jazyka a automaticky je překládá do jiného jazyka. Automatický překlad strojového překladu je delší dobu, ale obsáhlý Learning dosahuje působivých výsledků ve dvou konkrétních oblastech: automatický překlad textu (a převod řeči na text) a automatický překlad obrázků. 

Díky správné transformaci dat dokáže rozsáhlá síť pochopit textové, zvukové a vizuální signály. Strojový překlad lze použít k identifikaci fragmentů zvuku ve větších zvukových souborech a přepisovat mluveného slova nebo obrázku jako textu.

### <a name="text-analytics"></a>Analýza textu

Důležitým úkolem hloubkového učení je e-zjišťování. Společnosti využívají analýzu textu založené na hloubkovém učení ke zjišťování obchodu zasvěcených osob a dodržování předpisů pro státní správu. Fondy transakcí využívají Text Analytics k přechodu do rozsáhlých úložišť dokumentů, abyste získali přehled o budoucích investicích a mínění trhu. Případ použití pro analýzu textu na základě hloubkového učení se otáčí kolem své schopnosti analyzovat obrovské objemy textových dat a provádět analýzy nebo vracet agregace.

## <a name="artificial-neural-networks"></a>Umělé sítě neuronové

Umělé sítě neuronové se vytváří pomocí vrstev připojených uzlů. Modely hloubkového učení používají neuronové sítě, které mají velký počet vrstev. 

V následujících částech se prozkoumá nejoblíbenější umělá neuronové síť typologies.

### <a name="feedforward-neural-network"></a>Síť neuronové Feedforward

Síť neuronové Feedforward je nejzákladnější typ umělé neuronové sítě. V Feedforward síti jsou informace přenášeny pouze v jednom směru ze vstupní vrstvy do výstupní vrstvy. Feedforward neuronové Networks transformují vstup tím, že ho umístí prostřednictvím řady skrytých vrstev. Každá vrstva se skládá ze sady neurons a každá vrstva je plně připojená ke všem neurons ve vrstvě. Poslední plně připojená vrstva (výstupní vrstva) představuje vygenerované předpovědi.

### <a name="recurrent-neural-network"></a>Znovu aktuální neuronové síť

Opakující se neuronové sítě jsou široce využívanou umělou neuronovéou síť. Tyto sítě ukládají výstup vrstvy a předává je zpět do vstupní vrstvy, aby bylo možné odhadnout výsledek vrstvy. Opakující se neuronové sítě mají skvělé možnosti učení. Používají se často pro složité úlohy, jako je učení rukopisu a rozpoznávání jazyka.

### <a name="convolutional-neural-networks"></a>Konvoluční neuronové sítě

Síť neuronové konvoluční je zvláště efektivní umělá síť neuronové a představuje jedinečnou architekturu. Vrstvy jsou uspořádány do tří rozměrů: šířka, Výška a hloubka. Neurons v jedné vrstvě se připojí k neurons v následující vrstvě, ale pouze do malých oblastí neurons vrstvy. Konečný výstup je snížen na jeden vektor skóre pravděpodobnosti uspořádané podél rozměru hloubky. 

Sítě konvoluční neuronové se používaly v oblastech, jako je rozpoznávání obrazu a klasifikace.

## <a name="next-steps"></a>Další postup

Následující články ukazují, jak používat technologii pro hloubkové učení ve [službě Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/?WT.mc_id=docs-article-lazzeri):

- [Klasifikace rukou psaných číslic pomocí modelu TensorFlow](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)
- [Klasifikace rukou psaných číslic pomocí TensorFlow Estimator a Keras](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-keras?WT.mc_id=docs-article-lazzeri)
- [Klasifikace imagí pomocí modelu Pytorch](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)
- [Klasifikace rukou psaných číslic pomocí modelu zřetězení](https://docs.microsoft.com/en-us/azure/machine-learning/service/how-to-train-chainer?WT.mc_id=docs-article-lazzeri)
