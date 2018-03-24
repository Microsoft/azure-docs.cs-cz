---
title: Spuštění skriptů Python machine learning | Microsoft Docs
description: Obsahuje přehled návrhu zásadami pro Podpora skriptů Python v Azure Machine Learning a základní informace o využití scénáře, možnosti a omezení.
keywords: Python machine learningu pandas, python pandas, skriptů python, spuštění skriptů python
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: ee9eb764-0d3e-4104-a797-19fc29345d39
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 0efa2a7604b41b791ed3717b7f2b667b7181085d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Spouštění skriptů strojového učení v Pythonu v nástroji Azure Machine Learning Studio

Toto téma popisuje zásady designu základní aktuální podporu pro skripty jazyka Python v Azure Machine Learning. Hlavní možnosti poskytované jsou také uvedeny, včetně:

- spuštění scénáře základní informace o využití
- stanovení skóre experimentu ve webové službě
- Podpora pro import existujícího kódu
- Export vizualizace
- proveďte výběr pod dohledem funkce
- pochopení určitá omezení

[Python](https://www.python.org/) je nepostradatelným prostředkem nástroj hrudník mnoho datových vědců. Obsahuje:

* Elegantní a stručným syntaxe, 
* Podpora více platforem 
* velká kolekce výkonné knihovny, a 
* Vyspělá vývojové nástroje. 

Python se používá ve všech fázích obvykle používaných v machine learning modelování pracovního postupu:

- ingestování dat a zpracování 
- Funkce vytváření
- model školení 
- ověření modelu
- nasazení modelů

Azure Machine Learning Studio podporuje vnoření skriptů Python do různých částí počítače učení experiment a také bezproblémově publikováním jako webové služby v Microsoft Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Principy návrhu skriptů Python v Machine Learning

Uživatelského rozhraní jazyka Python v Azure Machine Learning Studio je prostřednictvím [Execute Python Script] [ execute-python-script] modulu na obrázku 1.

![image1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

Obrázek 1. **Execute Python Script** modulu.

[Execute Python Script] [ execute-python-script] modulu v Azure ML Studio přijímá až tři vstupy a vytváří až dvě výstupy (popsané v následující části), jako je jeho analogovým R [spustit skript jazyka R] [ execute-r-script] modulu. Kód jazyka Python, který bude proveden zadání do pole parametr jako speciálně pojmenovanou vstupní bod volaná funkce `azureml_main`. Tady jsou klíčové zásady použít k implementaci tento modul:

1. *Musí být idiomatickou pro Python uživatele.* Většina uživatelů Python dvoufaktorového svůj kód jako funkce uvnitř modulů. Proto uvedení spoustu spustitelné příkazy v modulu nejvyšší úrovně je relativně vzácné. Pole skript v důsledku toho trvá speciálně pojmenovanou funkce Python oproti právě posloupnost příkazy. Objekty zveřejněné ve funkci, jako jsou typy standardní knihovny jazyka Python [Pandas](http://pandas.pydata.org/) datových rámců a [NumPy](http://www.numpy.org/) pole.
2. *Musí mít zachováním mezi místním a cloudovým spuštěních.* Back-end používají ke spouštění kódu Python je založena na [Anaconda](https://store.continuum.io/cshop/anaconda/), nejběžněji používané scientific distribuci jazyka Python a platformy. Se dodává s blízko 200 nejběžnější balíčků Python. Proto můžete datových vědců ladění a kvalifikaci svůj kód na jejich místním prostředí Azure Machine Learning kompatibilní Anaconda. Použít existující vývojové prostředí, například [IPython](http://ipython.org/) poznámkového bloku nebo [Python Tools pro Visual Studio](http://aka.ms/ptvs), ke spuštění v rámci Azure ML experimentu. `azureml_main` Vstupní bod je vanilla Python funkce a tak *** tvořit bez kódu pro konkrétní Azure ML nebo sadu SDK nainstalovat.
3. *Musí být bezproblémově složení s další moduly Azure Machine Learning.* [Execute Python Script] [ execute-python-script] modul přijímá jako vstupy a výstupy, standardní datové sady Azure Machine Learning. Základní architektury transparentně a efektivně přemosťuje moduly runtime Azure ML a Python. Proto Python můžete použít ve spojení s stávajících pracovními postupy Azure ML, včetně těch, které volají do R a SQLite. Ve výsledku vědecký pracovník data tvoří pracovních který:
   * použití jazyka Python a Pandas pro data předem zpracování a čištění
   * informační kanál dat SQL transformace, připojení více datových sad do formuláře funkce
   * cvičení modelů pomocí algoritmech v Azure Machine Learning 
   * vyhodnocení a po zpracování pomocí R.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Základní informace o využití scénáře v ML skriptů Python

V této části jsme některé základní použití zjišťování [Execute Python Script] [ execute-python-script] modulu. Vstupy pro modul Python jsou zveřejněné jako Pandas datové rámce. Funkce musí vracet jeden snímek dat Pandas zabalené uvnitř Python [pořadí](https://docs.python.org/2/c-api/sequence.html) například řazené kolekce členů, seznamu nebo NumPy pole. V první výstupní port modulu je pak vrácen první prvek toto pořadí. Toto schéma je znázorněno na obrázku 2.

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

Obrázek 2. Mapování vstupní porty, které se parametry a vrátí hodnotu na výstupní port.

Podrobnější sémantiku jak získat vstupních portů mapované na parametry `azureml_main` funkce jsou uvedené v tabulce 1:

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabulka 1. Mapování vstupních portů na parametry funkce.

Mapování mezi vstupních portů a parametry funkce je poziční:

- První připojené vstupní port je namapována na první parametr funkce. 
- Druhý vstup (Pokud je připojeno) je namapována na druhý parametr funkce.

V tématu *Python pro analýzu dat* (O'Reilly, 2012) podle západní McKinney pro další informace o Python Pandas a na tom, jak lze použít k manipulaci s daty efektivnější. 


## <a name="translation-of-input-and-output-types"></a>Překlad vstupní a výstupní typů 
Vstupní datové sady v Azure ML se převedou na datové rámce v Pandas. Výstupní datové rámce převedou zpět do Azure ML datové sady. Jsou prováděny následující převody:

1. Řetězec a číselné sloupce se převedou jako-je a chybějící hodnoty v datové sadě jsou převedeny na hodnoty "NA" v Pandas. Dojde k stejné převodu na zpět způsob (chybějící hodnoty v Azure ML se převedou NA hodnoty v Pandas).
2. Index vektorů v Pandas nejsou podporovány v Azure ML. Všechny snímky vstupní data ve funkci Python mít vždy 64-bit index číselné od 0 do počet řádků minus 1. 
3. Datové sady Azure ML nemůže mít duplicitní názvy sloupců a názvy sloupců, které nejsou typu řetězec. Pokud již výstupní data rámečku obsahuje jiné než číselné sloupce, volá framework `str` na názvy sloupců. Stejně tak všechny duplicitní názvy sloupců jsou automaticky pozměnění zajistit, že jsou jedinečné názvy. Přípona (2) se přidá do první duplicitní, (3) na druhý duplicitní a tak dále.


## <a name="operationalizing-python-scripts"></a>Zprovozňování skriptů Python

Všechny [Execute Python Script] [ execute-python-script] moduly používané v vyhodnocování experimentu jsou volány při publikovat jako webovou službu. Obrázek 3 ukazuje například vyhodnocování experimentu, který obsahuje kód pro vyhodnocení jeden výraz Python. 

![image4](./media/execute-python-scripts/figure3a.png)

![image5](./media/execute-python-scripts/python-script-with-python-pandas.png)

Obrázek 3. Webové služby pro vyhodnocení výrazu jazyka Python.

Webové služby vytvořené z tohoto experimentu:

- přijímá jako vstup výraz Python (jako řetězec)
- odešle ji do překladač Pythonu 
- Vrátí tabulku obsahující ve výrazu a vyhodnotí výsledek.


## <a name="importing-existing-python-script-modules"></a>Import existující moduly skriptu jazyka Python

Běžné případ použití pro mnoho datových vědců je do Azure ML experimenty zahrnout stávající skriptů Python. Místo nutnosti, že veškerý kód zřetězených a vložit do jednoho skriptu pole, [Execute Python Script] [ execute-python-script] modul přijímá soubor zip, který obsahuje moduly jazyka Python na třetí vstupní port. Soubor je rozbalené rámcem provádění za běhu a obsah se přidají do knihovny cestu překladač Pythonu. `azureml_main` Vstupní bod funkce můžete poté importovat tyto moduly přímo.

Jako příklad zvažte soubor Hello.py obsahující jednoduché funkci "Hello, World".

![image6](./media/execute-python-scripts/figure4.png)

Obrázek 4. Uživatelem definované funkce v souboru Hello.py.

V dalším kroku vytvoříme soubor Hello.zip, který obsahuje Hello.py:

![image7](./media/execute-python-scripts/figure5.png)

Obrázek 5. Soubor ZIP obsahující uživatelem definované kód Python.

Nahrajte soubor zip jako datové sady do Azure Machine Learning Studio. Pak vytvořte a spusťte experimentu, který používá Python kód v souboru Hello.zip připojením k třetí vstupní port **Execute Python Script** modulu, jak je vidět na tomto obrázku.

![image8](./media/execute-python-scripts/figure6a.png)

![image9](./media/execute-python-scripts/figure6b.png)

Obrázek 6. Ukázkový experiment s uživatelem definovaný kód Python uloženo jako soubor zip.

Výstup modulu ukazuje, že byl soubor zip nezabalené a které funkce `print_hello` byl spuštěn.
 
![image10](./media/execute-python-scripts/figure7.png)

Na obrázku 7. Uživatelem definované funkce používá uvnitř [Execute Python Script] [ execute-python-script] modulu.


## <a name="working-with-visualizations"></a>Práce s vizualizacemi

Pozemků vytvořený MatplotLib, který můžete vizualizovat v prohlížeči může být vrácen pouze [Execute Python Script][execute-python-script]. Ale pozemků nejsou automaticky přesměrována na Image jako při použití R. Proto uživatel musí explicitně uložte jakékoli pozemků do soubory PNG Pokud mají být vráceny zpět do Azure Machine Learning. 

Ke generování bitových kopií z MatplotLib, musíte provést následující postup:

* Přepněte back-end "%{AGG/" z výchozí na základě rt vykreslování 
* Vytvořit nový objekt obrázku 
* získat ose a generovat všechny pozemků do ní 
* Uložit obrázek na soubor PNG 

Tento proces je znázorněn na následující obrázek 8, která vytvoří pomocí funkce scatter_matrix v Pandas bodové vykreslení matice.

![image1v](./media/execute-python-scripts/figure-v1-8.png)

Obrázek 8. Kód k uložení MatplotLib obrázky do bitové kopie.

Obrázek 9 ukazuje, že experimentu, který používá skript uvedený výše vrátit ukazuje zeměpisný prostřednictvím druhý výstupní port.

![image2v](./media/execute-python-scripts/figure-v2-9a.png) 

![image2v](./media/execute-python-scripts/figure-v2-9b.png) 

Obrázek 9. Vizualizace pozemků generované z kódu jazyka Python.

Je možné vrátit více útvarů jejich uložením do různých obrázků, modul runtime Azure Machine Learning převezme všechny bitové kopie a je zřetězí pro vizualizaci.


## <a name="advanced-examples"></a>Pokročilé příklady

Anaconda prostředí nainstalován v Azure Machine Learning obsahuje běžné balíčků, jako jsou NumPy, SciPy a další Scikits. Tyto balíčky můžete efektivně použít pro různé úlohy zpracování dat v machine learning kanálu. Jako příklad následující experiment a skript ilustrují použití inteligentních algoritmů komplet v Scikits informace k výpočtu skóre důležitosti funkce pro datovou sadu. Skóre lze použít k provedení před se předány do jiného modelu ML výběr pod dohledem funkce.

Tady je funkce Python slouží k výpočtu skóre důležitosti a pořadí funkcí podle skóre:

![image11](./media/execute-python-scripts/figure8.png)

Obrázek 10. Funkce rank funkcí podle skóre.
  Následující experimentu pak vypočítá a vrátí skóre důležitosti funkcí v datové sadě "Pima indickém Diabetes" v Azure Machine Learning:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

Obrázek 11. Experiment rank funkcí v datové sadě Pima indickém Diabetes.

## <a name="limitations"></a>Omezení
[Execute Python Script] [ execute-python-script] aktuálně má následující omezení:

1. *V izolovaném prostoru provádění.* Modul Python runtime je aktuálně v izolovaném prostoru a v důsledku toho neumožňuje přístup k síti nebo k místnímu systému souborů trvalé způsobem. Všechny soubory, které jsou uloženy v místním počítači jsou izolované a odstranit po dokončení modulu. Kód jazyka Python nemá přístup k většině adresářů v počítači, na kterém běží na, s výjimkou aktuálního adresáře a jejích podadresářů.
2. *Chybí sofistikované vývoji a ladění podpory.* Modul Python aktuálně nepodporuje IDE funkce jako je například technologie intellisense a ladění. Úplné trasování zásobníku Python je k dispozici také v případě selhání modulu za běhu. Je však nutné ji zobrazit v protokolu výstup modulu. Doporučujeme aktuálně vyvíjet a ladit Python skripty v prostředí, jako je například IPython a pak importovat kód do modulu.
3. *Single – datový výstup rámce.* Vstupní bod Python je povolená jenom vrátit jednoho datového rámce jako výstup. Není aktuálně možné vrátit zpět do Azure Machine Learning runtime libovolný Python objekty, například trénované modely přímo. Jako [spustit skript jazyka R][execute-r-script], který má stejné omezení, je možné v mnoha případech okurky objekty do bajtového pole a pak, vraťte uvnitř datové rámce.
4. *Neschopnost Python instalaci přizpůsobit*. V současné době je jediný způsob, jak přidat vlastní moduly jazyka Python přes mechanismus soubor zip popsané výše. To je vhodná pro malé moduly, je nevhodnou pro velké moduly (především těch s nativních knihoven DLL) nebo velký počet modulů. 

## <a name="conclusions"></a>Závěrů
[Execute Python Script] [ execute-python-script] modulu umožňuje data vědecký pracovník začlenit existující kód Python do pracovních hostovaných v cloudu machine learning v Azure Machine Learning a bezproblémově zprovoznit je jako součást webové služby. Modul Python skriptu přirozeně spolupracuje s dalších modulů v Azure Machine Learning. Modul můžete použít pro celou řadu úloh z zkoumání dat předběžné zpracování a funkce extrakce a pak vyhodnocení a po zpracování výsledků. Modul runtime back-end používá pro spuštění je založena na Anacondu, distribuci jazyka Python dobře otestované a často používaný. Tento back-end zjednodušuje pro vás k prostředkům integrovaného existující kód do cloudu.

Očekáváme, že poskytuje další funkce k [Execute Python Script] [ execute-python-script] modulu například umožňuje trénování a zprovoznit modely v Pythonu a přidat lepší podporu pro vývoj a ladění kódu v Azure Machine Learning Studio.

## <a name="next-steps"></a>Další postup
Další informace naleznete ve [Středisku pro vývojáře Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
