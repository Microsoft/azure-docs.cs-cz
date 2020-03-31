---
title: Hloubkové učení vs. strojové učení
titleSuffix: Azure
description: Zjistěte, jak hluboké učení souvisí se strojovým učením a umělou inteligencí. Hloubkové učení se používá ve scénářích, jako je detekce podvodů, hlasové & rozpoznávání obličeje, analýza mínění a prognózy časových řad.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 03/05/2020
ms.openlocfilehash: bcacf4ff66e114f65ac75f0aadd1564875f15f62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501363"
---
# <a name="deep-learning-vs-machine-learning"></a>Hloubkové učení vs. strojové učení

Tento článek vám pomůže porovnat hloubkové učení vs. strojové učení. Dozvíte se, jak se tyto dva pojmy porovnávají a jak zapadají do širší kategorie umělé inteligence. Článek také popisuje, jak lze hluboké učení použít na reálné scénáře, jako je detekce podvodů, rozpoznávání hlasu a obličeje, analýza mínění a prognózy časových řad.

## <a name="deep-learning-machine-learning-and-ai"></a>Hluboké učení, strojové učení a umělá aule

![Schéma vztahů: AI vs. strojové učení vs. hloubkové učení](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Zvažte následující definice, abyste pochopili hloubkové učení vs. strojové učení vs. Umělá umělá va:

- **Hluboké učení** je podmnožina strojového učení, která je založena na umělých neuronových sítích. _Proces učení_ je _hluboký,_ protože struktura umělých neuronových sítí se skládá z více vstupních, výstupních a skrytých vrstev. Každá vrstva obsahuje jednotky, které transformují vstupní data na informace, které může další vrstva použít pro určitou prediktivní úlohu. Díky této struktuře se stroj může učit prostřednictvím vlastního zpracování dat.

- **Strojové učení** je podmnožina umělé inteligence, která využívá techniky (například hluboké učení), které umožňují strojům využívat zkušenosti ke zlepšení úkolů. _Proces učení_ je založen na následujících krocích:

   1. Vsumí data do algoritmu. (V tomto kroku můžete modelu poskytnout další informace, například provedením extrakce prvků.)
   1. Tato data slouží k trénování modelu.
   1. Otestujte a nasaďte model.
   1. Využijte nasazený model k automatické prediktivní úlohy. (Jinými slovy volání a použití nasazeného modelu k získání předpovědí vrácených modelem.)

- **Umělá inteligence (AI)** je technika, která umožňuje počítačům napodobovat lidskou inteligenci. Zahrnuje strojové učení. 
 
Je důležité porozumět vztahu mezi umělou ai, strojovým učením a hloubkovým učením. Strojové učení je způsob, jak dosáhnout umělé inteligence. Pomocí strojového učení a techniky hlubokého učení můžete vytvářet počítačové systémy a aplikace, které provádějí úkoly, které jsou běžně spojeny s lidskou inteligencí. Mezi tyto úkoly patří rozpoznávání obrázků, rozpoznávání řeči a překlad jazyka.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Techniky hlubokého učení vs. strojové učení 

Nyní, když máte přehled o strojovém učení vs. hluboké učení, porovnejme tyto dvě techniky. Ve strojovém učení je třeba algoritmus stižení, jak provést přesnou předpověď tím, že spotřebuje více informací (například provedením extrakce funkce). V hlubokém učení se algoritmus může naučit, jak provést přesnou předpověď prostřednictvím vlastního zpracování dat díky struktuře umělé neuronové sítě.

Následující tabulka porovnává dvě techniky podrobněji:

| |Veškeré strojové učení |Pouze hluboké učení|
|---|---|---|
|  **Počet datových bodů** | Můžete použít malé množství dat, aby předpovědi. | Je třeba použít velké množství dat školení, aby předpovědi. |
|  **Hardwarové závislosti** | Může pracovat na low-end strojích. Nepotřebuje velké množství výpočetního výkonu. | Záleží na špičkových strojích. Ze své podstaty provádí velké množství operací násobení matice. GPU může efektivně optimalizovat tyto operace. |
|  **Proces featurizace** | Vyžaduje, aby funkce byly přesně identifikovány a vytvořeny uživateli. | Učí se funkce na vysoké úrovni z dat a vytváří nové funkce sám. |
|  **Přístup k učení** | Rozdělí proces učení na menší kroky. Potom kombinuje výsledky z každého kroku do jednoho výstupu. | Prochází procesem učení tím, že řeší problém na základě end-to-end. |
|  **Doba spouštění** | Trvá poměrně málo času na trénink, od několika sekund až po několik hodin. | Obvykle trvá dlouhou dobu trénovat, protože algoritmus hlubokého učení zahrnuje mnoho vrstev. |
|  **Výstup** | Výstup je obvykle číselná hodnota, jako je skóre nebo klasifikace. | Výstup může mít více formátů, jako je text, skóre nebo zvuk. |

## <a name="deep-learning-use-cases"></a>Případy použití v hlubokém učení

Díky struktuře umělé neuronové sítě vyniká hluboké učení v identifikaci vzorů v nestrukturovaných datech, jako jsou obrázky, zvuk, video a text. Z tohoto důvodu hluboké učení rychle transformuje mnoho průmyslových odvětví, včetně zdravotnictví, energetiky, financí a dopravy. Tato odvětví nyní přehodnocují tradiční obchodní procesy. 

Některé z nejběžnějších aplikací pro hluboké učení jsou popsány v následujících odstavcích.

### <a name="named-entity-recognition"></a>Rozpoznávání pojmenovaných entit

Rozpoznávání pojmenovaných entit je metoda hlubokého učení, která přebírá část textu jako vstup a transformuje ji do předem zadané třídy. Tyto nové informace mohou být PSČ, datum, ID produktu. Informace pak mohou být uloženy ve strukturovaném schématu k sestavení seznamu adres nebo sloužit jako měřítko pro modul ověřování identity.

### <a name="object-detection"></a>Detekce objektů

Hloubkové učení bylo použito v mnoha případech použití detekce objektů. Detekce objektů se skládá ze dvou částí: klasifikace obrázků a pak lokalizace obrazu. _Klasifikace_ obrázků identifikuje objekty obrázku, například auta nebo osoby. _Lokalizace_ obrazu poskytuje konkrétní umístění těchto objektů. 

Detekce objektů se již používá v odvětvích, jako jsou hry, maloobchod, cestovní ruch a samořídící automobily.

### <a name="image-caption-generation"></a>Generování titulků obrázku

Podobně jako rozpoznávání obrázků musí systém v titulkování obrázků pro daný obrázek vygenerovat titulek, který popisuje obsah obrázku. Když můžete rozpoznat a označit objekty na fotografiích, dalším krokem je přeměnit tyto popisky na popisné věty. 

Aplikace titulků obrázků obvykle používají konvoluční neuronové sítě k identifikaci objektů v obraze a pak pomocí opakující se neuronové sítě proměnit popisky na konzistentní věty.

### <a name="machine-translation"></a>Strojový překlad

Strojový překlad bere slova nebo věty z jednoho jazyka a automaticky je překládá do jiného jazyka. Strojový překlad existuje již dlouhou dobu, ale hluboké učení dosahuje působivých výsledků ve dvou specifických oblastech: automatický překlad textu (a překlad řeči na text) a automatický překlad obrázků.

S příslušnou transformací dat může neuronová síť porozumět textovým, zvukovým a vizuálním signálům. Strojový překlad lze použít k identifikaci úryvků zvuku ve větších zvukových souborech a k přepsání mluveného slova nebo obrázku jako textu.

### <a name="text-analytics"></a>Analýza textu

Analýza textu založená na metodách hloubkového učení zahrnuje analýzu velkého množství textových dat (například lékařských dokumentů nebo příjmů z výdajů), rozpoznání vzorů a vytváření organizovaných a výstižných informací z nich.

Společnosti používají hluboké učení k provádění textové analýzy k detekci obchodování zasvěcených osob a dodržování vládních předpisů. Dalším běžným příkladem je pojistný podvod: analýza textu se často používá k analýze velkého množství dokumentů, aby se rozpoznala pravděpodobnost, že pojistná pohledávka bude podvod. 

## <a name="artificial-neural-networks"></a>Umělé neuronové sítě

Umělé neuronové sítě jsou tvořeny vrstvami připojených uzlů. Modely hlubokého učení používají neuronové sítě, které mají velký počet vrstev. 

Následující části zkoumají nejoblíbenější umělé neuronové sítě typologie.

### <a name="feedforward-neural-network"></a>Feedforward neuronová síť

Feedforward neuronová síť je nejzákladnější typ umělé neuronové sítě. V síti výsuvů se informace přesouvají pouze jedním směrem ze vstupní vrstvy do výstupní vrstvy. Feedforward neuronové sítě transformovat vstup tím, že prostřednictvím řady skrytých vrstev. Každá vrstva se skládá ze sady neuronů, a každá vrstva je plně připojen ke všem neuronům ve vrstvě před. Poslední plně připojená vrstva (výstupní vrstva) představuje generované předpovědi.

### <a name="recurrent-neural-network"></a>Opakující se neuronová síť

Opakující se neuronové sítě jsou široce používaná umělá neuronová síť. Tyto sítě ukládají výstup vrstvy a vracejí ji zpět do vstupní vrstvy, aby pomohly předpovědět výsledek vrstvy. Opakující se neuronové sítě mají skvělé schopnosti učení. Jsou široce používány pro složité úkoly, jako je prognóza časových řad, učení rukopisu a rozpoznávání jazyka.

### <a name="convolutional-neural-networks"></a>Konvoluční neuronové sítě

Konvoluční neuronová síť je obzvláště účinná umělá neuronová síť a představuje jedinečnou architekturu. Vrstvy jsou uspořádány do tří rozměrů: šířka, výška a hloubka. Neurony v jedné vrstvě se nepřipojují ke všem neuronům v další vrstvě, ale pouze k malé oblasti neuronů vrstvy. Konečný výstup je snížen na jeden vektor pravděpodobnosti skóre, uspořádané podél hloubkové kóty. 

Konvoluční neuronové sítě byly použity v oblastech, jako je rozpoznávání videa, rozpoznávání obrazu a doporučující systémy.

## <a name="next-steps"></a>Další kroky

Následující články ukazují, jak používat technologii hlubokého učení v [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/?WT.mc_id=docs-article-lazzeri):

- [Klasifikovat ručně psané číslice pomocí modelu TensorFlow](https://docs.microsoft.com/azure/machine-learning/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)

- [Klasifikovat ručně psané číslice pomocí odhadu TensorFlow a Keras](https://docs.microsoft.com/azure/machine-learning/how-to-train-keras?WT.mc_id=docs-article-lazzeri)

- [Klasifikovat obrázky pomocí modelu Pytorch](https://docs.microsoft.com/azure/machine-learning/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)

- [Klasifikovat ručně psané číslice pomocí modelu Chainer](https://docs.microsoft.com/azure/machine-learning/how-to-train-chainer?WT.mc_id=docs-article-lazzeri)

Také použijte [algoritmus strojového učení Cheat Sheet](../synapse-analytics/sql-data-warehouse/cheat-sheet.md) zvolit algoritmy pro váš model.
