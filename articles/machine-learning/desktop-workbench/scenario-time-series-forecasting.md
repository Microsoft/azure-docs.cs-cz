---
title: Energie vyžádání časové řady prognózy | Microsoft Docs
description: Jak se má použít strojového učení předpověď časové řady vyžádání energie v nástroji Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: anta
manager: ireiter
editor: anta
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: 19e62364599ec9f1d0571e6f6e35205b0bb93b4b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832529"
---
# <a name="energy-demand-time-series-forecasting"></a>Vytváření prognóz energie vyžádání časové řady


Vytváření prognóz řady čas je úkol předpovídat budoucí hodnoty v pořadí řazení podle času pozorování. Je častých problémů a má aplikace v mnoha odvětví. Například prodejní společností muset prognózy prodeje budoucí produktu tak mohou efektivně uspořádat jejich řetězců potřeby. Podobně balíček doručení společnosti potřebují k zjištění přibližné hodnoty poptávka po jejich služby, takže se můžete naplánovat požadavky pracovníků a doručovací trasy předem. V mnoha případech může být výrazné finanční rizika nesprávné prognózy. Proto prognózy je často kritické obchodní činnosti.

Tento příklad ukazuje, jak časové řady prognózy lze provést prostřednictvím použití machine learning techniky. Projdete každý krok modelování procesu, včetně:
- Příprava dat a vyčištění dat;
- Vytváření funkcí pro strojové učení modely z času nezpracovaná data řady;
- Cvičení různé modely machine learning;
- Vyhodnocení modelů tak, že porovnáte výkonu na uchovávat out testovací datové; a,
- Zprovozňování nejlepší model zpřístupnění prostřednictvím webové služby ke generování prognózy na vyžádání.

Azure Machine Learning Workbench pomůcky procesu modelování u každého kroku: 
- Příklad ukazuje, jak provádět Jupyter poznámkového bloku prostředí - dostupné přímo z prostřednictvím Workbench – vývoj kód Python jednodušší. Proces vývoje modelu lze vysvětlit ostatním efektivněji pomocí markdownu poznámky a grafy. Tyto poznámkových bloků můžete prohlížet, upravovat a provést přímo z nástroje Workbench.
- Trénované modely můžete trvalé a nahrané do úložiště objektů blob. To pomáhá vědecký pracovník dat mít přehled o objektech trained model a ověřte, že jsou zachované a v případě potřeby dá načíst.
- Metriky lze protokolovat při provádění skriptu jazyka Python, povolení vědecký pracovník data chcete zaznamenat výkonu skóre modelu.
- Nástroje workbench vytvoří přizpůsobitelné tabulky protokolu metrik povolení vědecký pracovník dat snadno porovnat metriky výkonu modelu. Grafy se automaticky zobrazí tak nejvýkonnější modelu lze snadno identifikovat.
- Nakonec příklad ukazuje, jak může být modulu trained model operationalized nasazením v webovou službu v reálném čase.

## <a name="link-to-the-gallery-github-repository"></a>Propojit s úložišti GitHub Galerie
Veřejné úložiště GitHub pro tento scénář skutečných obsahuje všechny materiály, včetně ukázky kódu, potřebné pro tento příklad:

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>Přehled případu použití

Tento scénář se zaměřuje na vyžádání energie prognózy, kde je cílem předpovídat budoucí zatížení energie mřížky. Je klíčové podnikové operace pro společnosti, které v odvětví energie jako operátory musí udržovat jemné rovnováhu mezi k mřížce spotřeba energie a energie zadaný na ni. Příliš mnoho výpadku mřížky napájení může způsobit odpady energie nebo technické chyb. Ale pokud je zadáno příliš málo energie může vést k blackouts, ponechat zákazníky bez napájení. Operátory mřížky obvykle může trvat krátkodobé rozhodnutí ke správě přívod energie k mřížce a ponechat v Vyrovnávání zatížení. Proto je nezbytné pro operátor při rozhodování s jistotou již přesné krátkodobé prognózy spotřeby energie.

Tento scénář podrobné informace o vytváření strojového učení na energii na vyžádání prognózy řešení. Řešení je trénink na veřejné datové sady z [New Yorku nezávislé systému – operátor (NYISO)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument), který funguje power mřížky pro stav New Yorku. Datová sada zahrnuje každou hodinu power vyžádání data pro New Yorku během období 5 let. Další datové sadě služby obsahující každou hodinu počasí v New Yorku ve stejném období čas byl získán z [darksky.net](https://darksky.net).

## <a name="prerequisites"></a>Požadavky

- [Účet Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
- Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) následující [Průvodce instalací úvodní](../service/quickstart-installation.md) k instalaci programu a vytvořit pracovní prostor.
- Tato ukázka předpokládá, že používáte Azure ML Workbench ve Windows 10 s [modulu Docker](https://www.docker.com/) místně nainstalován. Pokud používáte systému macOS, tyto pokyny jsou z velké části stejný.
- Azure Machine Learning Operationalization nainstalované s nastavit prostředí pro místní nasazení a správu účet modelu vytvořit, jak je popsáno v tomto [průvodce](./model-management-configuration.md).
- Tato ukázka je nutné aktualizovat instalace Pandas verzi 0.20.3 nebo vyšší a nainstalujte matplotlib. Klikněte na tlačítko *spusťte příkazový řádek* z *souboru* nabídky na Workbench a spusťte následující příkazy pro instalaci tyto závislosti:

    ```
    conda install "pandas>=0.21.1"
    ```
    
## <a name="create-a-new-workbench-project"></a>Vytvoření nového projektu Workbench

Vytvořte nový projekt v tomto příkladu jako šablona:
1.  Otevřete Azure Machine Learning Workbench
2.  Na **projekty** klikněte na tlačítko **+** přihlásit a vybrat **nový projekt**
3.  V **vytvořit nový projekt** podokně, vyplňte informace pro nový projekt
4.  V **šablony projektů vyhledávání** vyhledávacího pole zadejte "Energie vyžádání časové řady prognózy" a vyberte šablonu
5.  Klikněte na **Vytvořit**


## <a name="data-description"></a>Popis dat

Dvě datové sady jsou k dispozici s Tato ukázka a staženy pomocí `1-data-preparation.ipynb` Poznámkový blok: `nyc_demand.csv` a `nyc_weather.csv`.

**nyc_demand.csv** obsahuje každou hodinu na energii na vyžádání hodnoty pro New Yorku let 2012 2017. Data má následující jednoduché strukturu:

| časové razítko | vyžádání |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

Vyžádání hodnoty jsou v hodinách megawatt (MWh). Níže je graf energie poptávky po dobu 7 dní v červenci 2017:

![Spotřeby energie](./media/scenario-time-series-forecasting/energy_demand.png  "spotřeby energie")

**nyc_weather.csv** obsahuje každou hodinu počasí hodnoty pro New Yorku v průběhu let 2012 2017:

| časové razítko | precip | dočasné
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46.13 |
| 2012-01-01 01:00:00 | 0.01 | 45.89 |
| 2012-01-01 02:00:00 | 0.05 | 45.04 |
| 2012-01-01 03:00:00 | 0.02 | 45.03 |

*precip* se rozumí míra procento úroveň srážení. *dočasné* hodnoty (teplotní) mají byla změní měřítko obrázku tak, aby se všechny hodnoty v intervalu [0, 100].

## <a name="scenario-structure"></a>Struktura scénář

Při otevření tohoto projektu v Azure Machine Learning Workbench poprvé, přejděte na *soubory* podokna na levé straně. Následující soubory kódu jsou k dispozici s této ukázce:
- `1-data-preparation.ipynb` -Tento poznámkový blok Jupyter stáhne a zpracovává data připraven k modelování. Toto je první poznámkového bloku, které budete spouštět.
- `2-linear-regression.ipynb` -Tento poznámkový blok nastaví model lineární regrese na Cvičná data.
- `3-ridge.ipynb` -soupravy ridge regresní model.
- `4-ridge-poly2.ipynb` -soupravy ridge regresní model na úroveň 2 polynomické funkce.
- `5-mlp.ipynb` -soupravy vícevrstvých perceptron neuronové sítě.
- `6-dtree.ipynb` -Nastaví model stromu rozhodnutí.
- `7-gbm.ipynb` -Nastaví model přechodu boosted počítače.
- `8-evaluate-model.py` -Tento skript načte modulu trained model a pomocí něj provádět předpovědi na datovou sadu testovací uchovávat na více systémů. Tak je možné porovnávat výkon odlišnými modely vyvolá metriky vyhodnocení modelu.
- `9-forecast-output-exploration.ipynb` -Tento poznámkový blok vytváří vizualizace prognózy generované strojového učení modelů.
- `10-deploy-model.ipynb` -Tento poznámkový blok ukazuje, jak může být modulu trained model prognózy operationalized ve webové službě v reálném čase.
- `evaluate-all-models.py` -Tento skript vyhodnotí všechny trénované modely. Nabízí alternativu ke spuštění `8-evaluate-model.py` samostatně pro každou cvičení modelu.

### <a name="1-data-preparation-and-cleaning"></a>1. Příprava dat a čištění

Chcete-li spustit ukázku, nejprve klikněte na `1-data-preparation.ipynb` poznámkového bloku otevřete v režimu preview. Klikněte na ***spuštění serveru poznámkového bloku*** spustit server poznámkového bloku Jupyter a Python jádra na váš počítač. Buď můžete spustit poznámkových bloků z v rámci nástroje Workbench, nebo můžete použít prohlížeč tak, že přejdete do [ http://localhost:8888 ](http://localhost:8888). Všimněte si, že musí změnit jádra k *název_projektu místní*. Můžete to provést z *jádra* nabídky v poznámkovém bloku pod *změnu jádra*. Stiskněte klávesu ***shift + Enter*** spustit kód v jednotlivých Poznámkový blok buněk, nebo kliknutím na tlačítko *spustit všechny* v *buňky* nabídky ke spuštění celý poznámkový blok.

Poznámkového bloku nejprve stáhne data z kontejneru úložiště objektů blob hostované v Azure. Data jsou pak uloženy na váš počítač v `AZUREML_NATIVE_SHARE_DIRECTORY`. Toto umístění je přístupná ze všech poznámkových bloků nebo skripty, které spustíte z nástroje Workbench proto je vhodná k uložení dat a trénované modely.

Po stažení data vyčistí poznámkového bloku dat vyplnění mezer v časové řady a vyplněním chybějících hodnot pomocí interpolace. Čištění data řady čas tímto způsobem maximalizuje množství dat, které jsou k dispozici pro trénování modely.

Několik funkcí modelu jsou vytvořené pomocí vyčištěnými nezpracovaná data. Tyto funkce lze rozdělit do dvou skupin:

- **Čas řízené funkce** jsou odvozeny od *časové razítko* proměnná například *měsíc*, *dayofweek* a *hodinu*.
- **Tepelně izolováno funkce** jsou hodnoty času zapuštěno skutečných hodnot na vyžádání nebo teploty. Tyto funkce zaměřte se na zachycení podmíněného závislosti mezi po sobě jdoucích časových období v modelu.

Výsledná datová sada funkce pak lze při vývoji modelů prognózy.

### <a name="2-model-development"></a>2. Vývoj pro model

Prvním přístupem modelování může být jednoduchý lineární regresní model. `2-linear-regression.ipynb` Poznámkového bloku ukazuje, jak k natrénování modelu prognózy lineární regrese pro budoucí spotřeby energie. Konkrétně bude Trénink modelu k předvídání energie vyžádání jednu hodinu (*t + 1*) před aktuální časové období (*t*). Však tento model rekurzivní "jednoduchý" můžete použít během testu ke generování prognózy pro budoucí časová období, *t + n*.

K natrénování modelu, se vytvoří prediktivní kanálu, který zahrnuje tři kroky odlišné:

- **Transformace dat**: požadované formáty pro vstupní data, se může lišit v závislosti na algoritmu strojového učení. V takovém případě model lineární regrese vyžaduje kategorií proměnné horkou jeden kódovaný.
- **A křížové ověření rutiny**: často model strojového učení bude mít jeden nebo více hyperparameters, které je třeba vyladit prostřednictvím experimenty. Křížového ověření můžete použít k vyhledání optimální sadu hodnot parametrů. Model je opakovaně vycvičena a vyhodnocené na různých složení datové sady. Nejlepších parametrů jsou ty, které dosažení nejlepší výkon modelu při průměrem složení křížového ověření. Tento proces je vysvětlené podrobněji v `2-linear-regression.ipynb`.
- **Cvičení konečné modelu**: cvičení modelu na celou datovou sadu, pomocí doporučené sady hyperparameters.

Jsme zahrnuli řadu 6 odlišnými modely v poznámkových bloků číslem 2 – 7. Každý poznámkového bloku soupravy jiného modelu a uloží jej do `AZUREML_NATIVE_SHARE_DIRECTORY` umístění. Jakmile máte Trénink všechny modely vytvořených pro tento scénář, jsme vyhodnotit a porovnejte je jako jsou popsány v další části.

### <a name="3-model-evaluation-and-comparison"></a>3. Porovnání a vyhodnocení modelu

Používáme můžete modulu trained model. Chcete-li prognózy pro budoucí časová období. Doporučujeme vyhodnotit tyto modely na datovou sadu testovací uchovávat na více systémů. Vyhodnocením odlišnými modely na stejné neviditelný datovou sadu jsme poměrně porovnat jejich výkonu a identifikovat nejlepší modelu. V tomto scénáři použijeme rekurzivně trained model, aby šlo vytvořit prognózu více kroků čas do budoucna. Konkrétně se vygeneruje prognózy až šest hodin, *t + 6* před do aktuální hodiny *t*. Tyto předpovědi vyhodnocují před skutečným vyžádání zjištěnými pro každé časové období.

Abyste mohli vyhodnotit model, otevřete `8-evaluate-model.py` skript z *soubory* podokno nástroje Workbench. Zkontrolujte, zda *konfigurace spustit* je nastaven na **místní** a pak zadejte název modelu do *argumenty* pole. Název modelu musí odpovídat přesně *název_modelu* proměnná nastavená na začátku poznámkového bloku, ve kterém je cvičení modelu. Zadejte například "linear_regression" Vyhodnotit model vyškolení lineární regrese. Alternativně po mít cvičena všechny modely, je možné vyhodnotit jejich všechny pomocí jednoho příkazu spuštěním `evaluate-all-models.py`. Pokud chcete spustit tento skript, otevřete příkazový řádek z nástroje Workbench a spusťte následující příkaz:

```
python evaluate-all-models.py
```
`8-evaluate-model.py` Skript provede následující operace:

- Načte kanál trained model z disku
- Vytváří předpovědi na testovací datové sady
- Vypočítá metriky výkonu modelu a protokolů je
- Uloží test předpovědi datové sady do `AZUREML_NATIVE_SHARE_DIRECTORY` pro pozdější kontroly a analýzy
- Uloží kanálu trained model, který má *výstupy* složky.

> [!Note]
> Ukládání modelu, který má *výstupy* objekt modelu složky automaticky zkopíruje do účtu úložiště objektů Blob Azure, které jsou spojené s vaším účtem experimenty. To znamená, že bude vždy možné načíst objekt uložené modelu z objektu blob, i když změníte počítače nebo výpočetní kontextu.

Přejděte na *historii běhů* panelu a klikněte na `8-evaluate-model.py`. Zobrazí se grafy znázorňující několik metriky výkonu modelu:

- **Poslat mi**: znamená chybu prognózy. To jde interpretovat jako Průměrná odchylka prognózy.
- **MPE**: [znamenat procento chyba](https://en.wikipedia.org/wiki/Mean_percentage_error) (ME vyjádřené jako procentní podíl aktuální)
- **MSE**: [znamenat kvadratická chyba](https://en.wikipedia.org/wiki/Mean_squared_error)
- **RMSE**: střední kořenové spolehlivosti chyby (druhou odmocninu MSE)
- **MAPE**: [znamenat absolutní procento chyb](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**: [symetrický procento střední absolutní chyba](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**: MAPE směrný plán prognózy, ve kterém předpovědi rovná hodnotě poslední známé vyžádání.
- **MdRAE**: medián relativní absolutní chyba. Střední poměr prognózy chyby do směrného plánu prognózy chyby. Hodnotu menší než 1 znamená, prognózy fungují lépe než směrného plánu.

Všechny metriky výše odkazovat *t + 1* prognózy. Kromě výše uvedených metriky, zobrazí se také sadu odpovídající metriky s *_horizon* příponu. Toto je metrika průměrem za všechny tečky v prognózy rozsahu z období *t + 1* na dobu *t + 6*.

Pokud metriky se nezobrazují v oblasti grafu, klikněte na symbol ozubené kolečko v horním pravém rohu. Ujistěte se, zda jsou zrušena zaškrtnutí metriky výkonu modelu, které vás zajímají. Metriky se také zobrazí v tabulce grafy. Tato tabulka je opět přizpůsobitelné se kliknutím na symbol zařízení. Seřadí tabulku podle metriky výkonu k identifikaci nejlepší modelu. Pokud vás zajímá zobrazuje, jak prognózy výkon se liší od období *t + 1* k *t + 6*, klikněte na položku pro model v tabulce. Zobrazení MAPE grafy, metriky MPE a MdRAE napříč období prognózy jsou uvedeny v části *vizualizace*.

Při vyhodnocování modelů prognózy, může být velmi užitečná k vizualizaci výstupních předpovědi. To pomáhá vědecký pracovník dat k určení, zda prognózy vytváří se zobrazí realistické. Může také pomoci zjistit problémy v předpovědi, pokud například prognózy provede špatně v určitých časových období. `9-forecast-output-exploration.ipynb` Poznámkového bloku způsobí vizualizace prognózy vygenerované testovací datové sady.

### <a name="4-deployment"></a>4. Nasazení

Nejlepší modelu můžete operationalized nasazením jako webovou službu v reálném čase. Této webové služby mohou být vyvolány pak ke generování prognózy na vyžádání, jakmile nová data k dispozici. V tomto scénáři nové prognózy je třeba vytvořit každou hodinu k předvídání vyžádání energie v následujících hodin. K provedení této úlohy, může být webová služba nasazení, který přijímá pole funkce pro zadané časové období jako vstup hodiny a vrátí předpokládaných vyžádání jako výstup.

V této ukázce webové služby se nasadí do počítače s Windows 10. Ujistěte se, dokončení požadovaných kroků pro místní nasazení uvedené v tomto [Příručka Začínáme](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md) pro rozhraní příkazového řádku Operationalization. Jakmile jste nastavili místním prostředí a účet správy modelu, spustit `10-deploy-model.ipynb` poznámkového bloku nasadit webovou službu.

## <a name="conclusion"></a>Závěr

Tento příklad znázorňuje, jak stavět začátku do konce časové řady prognózy řešení pro účely prognózy spotřeby energie. Řada zásad prozkoumali v této ukázce lze rozšířit do jiných prognózy scénáře a odvětví.

Tento scénář popisuje, jak Azure Machine Learning Workbench může být užitečné vědecký pracovník dat při vývoji řešení skutečných s užitečných funkcí, jako jsou prostředí poznámkového bloku Jupyter a možnosti metriky protokolování. Ukázka také provede čtečky na tom, jak model můžete operationalized a nasazují pomocí rozhraní příkazového řádku Azure Machine Learning Operationalization. Po nasazení, webového rozhraní API služby umožňuje vývojářům nebo technici data integrovat do kanálu data širší model prognózy.
