---
title: Spuštění skriptů strojového učení Pythonu – Azure Machine Learning Studio | Dokumentace Microsoftu
description: Jsou podrobněji popsány dále návrh zásadami pro podporu pro skripty Python v Azure Machine Learning Studio a scénáře základní informace o využití, možnosti a omezení.
keywords: Python strojového učení, balíčky pandas, pandas v pythonu, skriptů pythonu, spouštění skriptů pythonu
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: ee9eb764-0d3e-4104-a797-19fc29345d39
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 958dd91277a81a9082a4149d2b0026fc11bf882a
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52317581"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Spouštění skriptů strojového učení v Pythonu v nástroji Azure Machine Learning Studio

Toto téma popisuje principy návrhu základní aktuální podporu pro skripty Python ve službě Azure Machine Learning. Hlavní funkce k dispozici jsou také uvedeny, včetně:

- Spustit scénáře základní informace o využití
- skóre experiment ve webové službě
- Podpora pro import existujícího kódu
- Export vizualizace
- provést výběr funkcí jsou pod dohledem
- Vysvětlení některých omezení

[Python](https://www.python.org/) jsou nepostradatelným prostředkem v hrudníku nástroj řadu odborníků přes data. Obsahuje:

* Elegantní a stručné syntaxe, 
* Podpora víc platforem 
* rozsáhlé kolekci výkonných knihoven a 
* až po zralé vývojové nástroje. 

Python se používá ve všech fázích obvykle používaných v machine learning modelování pracovního postupu:

- pro příjem a zpracování 
- konstrukce funkcí
- Cvičení modelu 
- Ověření modelu
- nasazení modelů

Azure Machine Learning Studio podporuje skriptů Pythonu a vkládání do různých částí strojové učení experiment a také bezproblémově jejich publikování jako webové služby v Microsoft Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Principy návrhu skriptů Pythonu ve službě Machine Learning

Primární rozhraní pro Python v Azure Machine Learning Studio je prostřednictvím [Execute Python Script] [ execute-python-script] modulu je znázorněno na obrázku 1.

![image1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

Obrázek 1. **Execute Python Script** modulu.

[Execute Python Script] [ execute-python-script] modulu ve službě Azure ML Studio přijímá až tři vstupů a vytvoří až dva výstupy (probíranou v následující sekci), stejně jako jeho analogové R [spustit R Skript] [ execute-r-script] modulu. Ke spuštění kódu Pythonu se zadá do pole parametru jako speciálně s názvem vstupního bodu funkce volaná `azureml_main`. Tady jsou klíčové MSDL používaný k implementaci tohoto modulu:

1. *Musí být idiomatickou pro Python uživatele.* Většina uživatelů Python faktor svůj kód jako funkce uvnitř modulů. Proto uvedením spoustu spustitelné příkazy nejvyšší úrovně modulu je poměrně vzácný. V důsledku toho pole skript také využívá speciálně pojmenované funkce Pythonu na rozdíl od sekvenci příkazů. Objekty zveřejněné ve funkci, jako jsou typy standardní knihovny Pythonu [Pandas](http://pandas.pydata.org/) datových rámců a [NumPy](http://www.numpy.org/) pole.
2. *Musí mít vysokou věrností mezi místním a cloudovým spuštění.* Je back-endu ke spuštění kódu Pythonu na základě [Anaconda](https://store.continuum.io/cshop/anaconda/), často se využívá napříč platformami vědecké distribuci jazyka Python. Obsahuje blízko 200 nejběžnější balíčky Pythonu. Odborníci přes data proto můžete ladit a kvalifikovat svůj kód na místní prostředí Azure Machine Learning kompatibilní Anaconda. Použít existující vývojové prostředí, například [IPython](http://ipython.org/) Poznámkový blok nebo [Python Tools for Visual Studio](https://aka.ms/ptvs), spustíte ho v rámci experimentu Azure ML. `azureml_main` Vstupním bodem je vanilla funkce jazyka Python a proto *** dají se vytvářet bez kódu pro Azure ML konkrétní nebo nainstalovaná sada SDK.
3. *Musí být bez problémů sestavitelný s ostatními moduly Azure Machine Learning.* [Execute Python Script] [ execute-python-script] modul přijímá jako vstup a výstup, standardní datové sady Azure Machine Learning. Základní architektury transparentně a efektivně přemosťuje moduly runtime Azure ML a Python. Python je tak možné ve spojení s existující pracovní postupy Azure ML, včetně těch, které volají do R a SQLite. Výsledek, odborník přes data mohou vytvářet pracovní postupy, které:
   * pro data předběžného zpracování a čištění pomocí Pythonu a Pandas
   * informační kanál dat k transformaci SQL, spojování více datových sad funkcí formuláře
   * trénování modelů pomocí algoritmů ve službě Azure Machine Learning 
   * vyhodnocení a následného zpracování výsledků v R.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Základní informace o využití scénáře v ML skriptů Pythonu

V této části jsme některé základní použití zjišťování [Execute Python Script] [ execute-python-script] modulu. Vstupy pro modul Python jsou vystaveny jako Pandas datové rámce. Funkce musí vracet jednoho datového rámce Pandas zabalené uvnitř Python [pořadí](https://docs.python.org/2/c-api/sequence.html) jako řazené kolekce členů, seznam nebo pole NumPy. V první výstupní port modulu se pak vrátí první prvek toto pořadí. Toto schéma je znázorněno na obrázku 2.

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

Obrázek 2. Mapování ze vstupních portů, které mají parametry a vrátí hodnotu na výstupní port.

Podrobnější sémantika jak vstupních portů se namapují na parametry `azureml_main` funkce jsou uvedené v tabulce 1:

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabulka 1. Mapování portů vstupní parametry funkce.

Mapování mezi vstupní porty a parametry funkce je poziční:

- První připojených vstupního portu je namapována na první parametr funkce. 
- Druhého vstupu (Pokud je připojení) se mapuje na druhý parametr funkce.

Zobrazit *Python pro analýzu dat* (O'Reilly, 2012) podle západní McKinney pro další informace o Python Pandas a jak ji můžete použít k manipulaci s daty efektivně a účinně. 


## <a name="translation-of-input-and-output-types"></a>Překlad vstupní a výstupní typy 
Vstupní datové sady ve službě Azure ML se převedou na datové rámce v Pandas. Výstupní datové rámce převedou zpět do datové sady Azure ML. Následující převody jsou prováděny:

1. Řetězcové a číselné sloupce se převedou jako-je a chybějící hodnoty v datové sadě se převedou na hodnoty "NA" v Pandas. Na cestě zpět se stane stejný převod (hodnoty není k dispozici v Pandas se převedou na chybějící hodnoty v Azure ML).
2. Index vektory v Pandas nejsou podporované v Azure ML. Všechny snímky vstupní data v Pythonu – funkce mají vždy 64-bit číselný index od 0 do počtu řádků mínus 1. 
3. Datové sady Azure ML nemůže mít duplicitní názvy sloupců a názvy sloupců, které nejsou řetězce. Obsahuje-li výstupu datového rámce jiné než číselné sloupce, volá framework `str` na názvy sloupců. Stejně tak žádné duplicitní názvy sloupců jsou automaticky pozměnění – pomáhat zajistit, že jsou jedinečné názvy. Přípona (2) se přidá do první duplicitní, (3) na druhý duplicitní a tak dále.


## <a name="operationalizing-python-scripts"></a>Až po zprovoznění skriptů Pythonu

Žádné [Execute Python Script] [ execute-python-script] moduly používané v hodnoticí experimentu jsou volány při publikovat jako webovou službu. Obrázek 3 ukazuje například vyhodnocení experiment, který obsahuje kód pro vyhodnocení jeden výraz Pythonu. 

![image4](./media/execute-python-scripts/figure3a.png)

![image5](./media/execute-python-scripts/python-script-with-python-pandas.png)

Obrázek 3. Webová služba pro vyhodnocování výrazu Python.

Vytvořit z tohoto experimentu webové služby:

- přijímá jako vstup výraz Pythonu (jako řetězec)
- odešle k interpretu Pythonu 
- Vrátí tabulku obsahující výraz a vyhodnocený výsledek.


## <a name="importing-existing-python-script-modules"></a>Import existující moduly skriptu Pythonu

Běžným případem použití pro mnoho odborníky přes data je začlenit stávající skripty Python do experimenty Azure ML. Nemusíte mít, že veškerý kód zřetězit a vložit do jednoho skriptu pole [Execute Python Script] [ execute-python-script] modul přijímá soubor zip, který obsahuje moduly Pythonu na třetí vstupní port. Soubor je odblokujte rozhraním spuštění za běhu a obsah se přidají do knihovny cesta k interpretu Pythonu. `azureml_main` Vstupní bod funkce pak můžete importovat tyto moduly přímo.

Jako příklad vezměte v úvahu souboru Hello.py obsahující jednoduchou funkci "Hello, World".

![image6](./media/execute-python-scripts/figure4.png)

Obrázek 4. Uživatelem definované funkce v souboru Hello.py.

V dalším kroku vytvoříme soubor Hello.zip obsahující Hello.py:

![image7](./media/execute-python-scripts/figure5.png)

Obrázek 5. Soubor ZIP, který obsahuje uživatelský kód v Pythonu.

Nahrání souboru zip jako datovou sadu do Azure Machine Learning Studio. Pak vytvoření a spuštění experimentu, který používá kódu Python v souboru Hello.zip připojením na třetí vstupní port **Execute Python Script** modulu, jak je znázorněno na tomto obrázku.

![image8](./media/execute-python-scripts/figure6a.png)

![image9](./media/execute-python-scripts/figure6b.png)

Obrázek 6. Ukázkový experiment s uživatelský kód v Pythonu uloženo jako soubor zip.

Modul výstup ukazuje, že soubor zip nezabalené a které funkce `print_hello` byl spuštěn.
 
![image10](./media/execute-python-scripts/figure7.png)

Obrázek 7. Uživatelem definovaná funkce používá uvnitř [Execute Python Script] [ execute-python-script] modulu.


## <a name="working-with-visualizations"></a>Práce s vizualizacemi

Může být vrácen grafy vytvořené pomocí MatplotLib, který lze vizualizovat v prohlížeči [Execute Python Script][execute-python-script]. Ale vykreslení přesměrování automaticky do bitové kopie jsou při použití jazyka R. Proto uživatel musí explicitně uložit žádné grafy pro soubory PNG Pokud má být vrácena zpět do Azure Machine Learning. 

Ke generování bitových kopií z MatplotLib, musíte dokončit postup:

* Přepněte na back-end na "%{AGG/" od rendereru výchozí na základě Qt 
* Vytvořit nový objekt obrázek 
* Získejte na ose a generovat do ní všechna vykreslení 
* Uložit obrázek soubor PNG 

Tento proces je znázorněn na následujícím obrázku 8, která vytváří bodový diagram matice pomocí funkce scatter_matrix v Pandas.

![image1v](./media/execute-python-scripts/figure-v1-8.png)

Obrázek 8. Kód pro uložení hodnoty MatplotLib do bitové kopie.

Obrázek 9 ukazuje, že prostřednictvím druhý výstupní port, který experiment, který používá skript uvedený dříve k vrácení.

![image2v](./media/execute-python-scripts/figure-v2-9a.png) 

![image2v](./media/execute-python-scripts/figure-v2-9b.png) 

Obrázek 9. Vizualizace se grafy generované z kódu Pythonu.

Je možné vrátit více obrázky uložením do různých obrázků, modul runtime Azure Machine Learning vybere všechny bitové kopie a zřetězí za účelem vizualizace.


## <a name="advanced-examples"></a>Pokročilé příklady

Anaconda prostředí nainstalovaná ve službě Azure Machine Learning obsahuje běžné balíčky, jako je například NumPy, SciPy a Učte se Scikits. Tyto balíčky můžete efektivně použít pro různé úlohy zpracování dat v machine learning kanálu. Například následující experiment a skript ilustrují použití skupiny stromů studentů v Scikits další vypočítat skóre význam funkce pro datovou sadu. Skóre je možné provést výběr pod dohledem funkcí než jsou předány do jiného modelu ML.

Tady je funkce Python slouží k výpočtu skóre význam a pořadí funkcí podle skóre:

![image11](./media/execute-python-scripts/figure8.png)

Obrázek 10. Funkce pořadí funkcí podle skóre.
 
Následujícího experimentu pak vypočítá a vrátí skóre význam funkcí v datové sadě "Pima území v Indickém diabetem" ve službě Azure Machine Learning:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

Obrázek 11. Experimentujte pořadí funkcí v datové sadě Pima území v Indickém cukrovka.

## <a name="limitations"></a>Omezení
[Execute Python Script] [ execute-python-script] aktuálně má následující omezení:

1. *Pro spuštění v izolovaném prostoru.* Modulu runtime Pythonu je aktuálně v izolovaném prostoru a proto neumožňuje přístup k síti nebo do místního systému souborů trvalé způsobem. Všechny soubory, které jsou uloženy v místním počítači jsou izolované a odstranit po dokončení modulu. Kód Pythonu, nemá přístup k většině adresáře na počítači, na kterém poběží, s výjimkou v aktuálním adresáři a jeho podadresářích.
2. *Nedostatek sofistikované vývojářské a podporu ladění.* Modul Pythonu v současné době nepodporuje funkce integrovaného vývojového prostředí, jako je například technologie intellisense a ladění. Také pokud modul selže v době běhu, úplné trasování zásobníku Python je k dispozici. Ale je třeba zobrazit ve výstupu protokolu pro modul. Doporučujeme nyní vývoj a ladění skriptů v Pythonu v prostředí, jako je například IPython a poté ji naimportujte kód do modulu.
3. *Single – datový rámec výstupu.* Vstupní bod Python je povolená jenom k vrácení jednoho datového rámce jako výstup. Není aktuálně možné vrátit zpět do modulu runtime Azure Machine Learning libovolného Python objekty, jako jsou trénované modely přímo. Stejně jako [spustit skript jazyka R][execute-r-script], který má stejné omezení, je možné v mnoha případech pickle objekty do bajtového pole a vrátí, který v rámci datového rámce.
4. *Nemožnost k přizpůsobení instalace Pythonu*. V současné době je jediný způsob, jak přidat vlastní moduly Pythonu prostřednictvím mechanismu souboru zip je popsáno výše. Když je vhodná pro malé moduly, je náročné pro velké moduly (zvlášť ty s nativních knihoven DLL) nebo velký počet modulů. 

## <a name="conclusions"></a>Závěry
[Execute Python Script] [ execute-python-script] modul umožňuje odborník přes data začlenit existujícího kódu Pythonu do pracovních postupů hostované v cloudu strojové učení ve službě Azure Machine Learning a získat bez problémů zprovoznění je v rámci webové služby. Modul skriptu Pythonu přirozeně spolupracuje s ostatními moduly ve službě Azure Machine Learning. Modul lze použít pro celou řadu úloh od zkoumání dat předběžného zpracování a extrahování funkcí a potom k vyhodnocení a následné zpracování výsledků. Modul runtime back-end použít pro spuštění je založená na Anacondu, distribuci jazyka Python dobře otestovaný a široce používaným. Tento back-end usnadňuje můžete připojit stávající kód prostředky do cloudu.

Očekáváme, že poskytuje další funkce pro [Execute Python Script] [ execute-python-script] modulu, například funkce k trénování a zprovozňovat modely v Pythonu a lepší podporu pro vývoj a ladění kódu v nástroji Azure Machine Learning Studio.

## <a name="next-steps"></a>Další postup
Další informace naleznete ve [Středisku pro vývojáře Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
