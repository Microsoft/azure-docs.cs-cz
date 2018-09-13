---
title: Prognózování energie vyžádání časové řady | Dokumentace Microsoftu
description: Postup použití strojového učení k předpovědi časové řady vyžádání energie v aplikaci Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: ''
author: anta
manager: ireiter
editor: anta
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: 3f32c9048c4b2002e7672b46a4f39e86ffa7b98e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642899"
---
# <a name="energy-demand-time-series-forecasting"></a>Prognózování energie vyžádání časové řady


Prognózy časových řad je úkol předpovídat budoucí hodnoty v časově řazenou posloupnost pozorování. Problém je běžný a má aplikace, které v řadě odvětví. Například maloobchodního prodeje společnosti potřebují o prognózu prodeje budoucích produktů, abyste mohli efektivně uspořádat jejich dodavatelské podle potřeby. Podobně společnosti doručování balíčků musí odhadnout požadavky na svých služeb můžete plánovat požadavky pracovní síly a doručovací trasy předem. V mnoha případech můžou být významné finančních rizik nepřesné předpovědí. Prognózování je proto často důležitých obchodních aktivit.

Tato ukázka předvádí, jak prognózy časových řad lze provést prostřednictvím použití techniky machine learning. Projdete každý krok modelování procesů včetně:
- Přípravy dat až po vyčistit a naformátovat data;
- Vytvoření funkce modely strojového učení z dat nezpracovaná časových řad;
- Školení různých modelů strojového učení;
- Vyhodnocení modelů porovnáním jejich výkon v datové sadě nachází mimo testu; a,
- Až po zprovoznění nejvhodnějšího modelu, zpřístupní se tak prostřednictvím webové služby sloužící ke generování předpovědí na vyžádání.

Azure Machine Learning Workbench pomůcky procesu modelování při každém kroku: 
- Tato ukázka vysvětluje, jak můžete vytvořit aplikace Jupyter notebook prostředí – k dispozici přímo prostřednictvím aplikace Workbench – vývoj kódu v Pythonu jednodušší. Proces vývoje modelu lze vysvětlit ostatním uživatelům jasněji pomocí markdownu poznámky a grafů. Tyto poznámkové bloky můžete zobrazit, upravit a spustit přímo z aplikace Workbench.
- Trénované modely můžete jako trvalý a nahráli do úložiště objektů blob. To pomáhá udržovat přehled o objekty trénovaného modelu a ověřte, že jsou zachované a získat v případě potřeby mezi odborníky přes data.
- Metriky můžete protokolovat při provádění skriptu v jazyce Python, povolení mezi odborníky přes data chcete zaznamenat výsledky výkonu modelu.
- Aplikace workbench vytváří přizpůsobitelné tabulky protokolu metrik, které umožní snadno porovnat metriky výkonu modelu mezi odborníky přes data. Grafy se automaticky zobrazí tak nejvýkonnějšího modelu lze snadno identifikovat.
- A konečně Tato ukázka vysvětluje, jak může být trénovaného modelu mají zprovoznit nasazením webové služby v reálném čase.

## <a name="link-to-the-gallery-github-repository"></a>Propojit úložiště GitHub Galerie
Veřejného úložiště GitHub pro tento scénář reálného světa obsahuje veškeré materiály, včetně ukázek kódu, třeba v tomto příkladu:

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>Přehled případu použití

Tento scénář se zaměřuje na poptávku po energii Prognózování, kde je cílem předpovídat budoucí zátěž v energetické síti. Operátory nutné k udržování pořádku rovnováhu mezi do mřížky spotřeba energie a poskytnou energie je představuje klíčovou obchodní operaci pro společnosti, které v energetickém sektoru. Příliš mnoho power dodaná do mřížky může mít za následek plýtvání energie nebo technických chyb. Nicméně pokud není zadána příliš málo energie může vést k blackouts byste museli opustit zákazníků bez power. Obvykle operátory mřížky můžete využít krátkodobé rozhodnutí ke správě dodávek energie do mřížky a mějte Vyrovnávání zatížení. Přesné krátkodobou předpověď poptávky po energii je proto zásadní pro operátor tato rozhodnutí bez obav.

Tento scénář obsahuje podrobnosti o konstrukce strojového učení řešení Prognózování poptávky po energii. Řešení jsou trénovaná na veřejné datové sady z [New York nezávislé systému – operátor (NYISO)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument), jež pracuje power mřížky pro stav New Yorku. Datová sada obsahuje každou hodinu data o poptávce napájení pro New York City po dobu pěti let. Další datová sada služby obsahující počasí v New Yorku po hodinách za stejné období byla získána z [darksky.net](https://darksky.net).

## <a name="prerequisites"></a>Požadavky

- [Účtu Azure](https://azure.microsoft.com/free/) (bezplatné zkušební verze jsou k dispozici).
- Nainstalovaná kopie produktu [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) následující [Stručná Instalační příručka](../service/quickstart-installation.md) k instalaci a vytvoření pracovního prostoru.
- Tento příklad předpokládá, že používáte Azure ML Workbench ve Windows 10 s [modul Docker](https://www.docker.com/) místně nainstalovaný. Pokud používáte macOS, pokyny se z velké části stejný.
- Azure Machine Learning Operacionalizace nainstalovaný v nastavení prostředí pro místní nasazení a účet správy modelů vytvořili, jak je popsáno v tomto [průvodce](./model-management-configuration.md).
- Tato ukázka vyžaduje aktualizaci instalace Pandas verzi 0.20.3 nebo vyšší a nainstalujte balíček matplotlib. Klikněte na tlačítko *otevřít příkazový řádek* z *souboru* nabídky v aplikaci Workbench a spusťte následující příkazy pro instalaci těchto závislostí:

    ```
    conda install "pandas>=0.21.1"
    ```
    
## <a name="create-a-new-workbench-project"></a>Vytvořte nový projekt aplikace Workbench

Vytvořte nový projekt s použitím v tomto příkladu jako šablony:
1.  Otevřete Azure Machine Learning Workbench
2.  Na **projekty** stránky, klikněte na tlačítko **+** přihlášení a výběr **nový projekt**
3.  V **vytvořit nový projekt** podokně, zadejte informace pro nový projekt
4.  V **Hledat v šablonách projektů** pole vyhledávání zadejte "Energie vyžádání prognózy časových řad" a vyberte šablonu
5.  Klikněte na **Vytvořit**


## <a name="data-description"></a>Popis dat

Dvě datové sady jsou součástí této ukázky a se stahují pomocí `1-data-preparation.ipynb` Poznámkový blok: `nyc_demand.csv` a `nyc_weather.csv`.

**nyc_demand.csv** obsahuje každou hodinu energie vyžádání hodnoty pro města New York v letech 2012 až 2017. Data mají následující strukturu jednoduchý:

| Časové razítko | vyžádání |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

Vyžádání hodnoty jsou v megawatt hodin (MWh). Tady je graf poptávku po energii po dobu 7 dní od července 2017:

![Poptávku po energii](./media/scenario-time-series-forecasting/energy_demand.png  "poptávku po energii")

**nyc_weather.csv** obsahuje každou hodinu počasí hodnoty pro města New York v letech 2012 až 2017:

| Časové razítko | precip | temp
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46.13 |
| 2012-01-01 01:00:00 | 0.01 | 45.89 |
| 2012-01-01 02:00:00 | 0.05 | 45.04 |
| 2012-01-01 03:00:00 | 0.02 | 45.03 |

*precip* je míra procentuální úroveň srážek. *temp* (teplotní) hodnoty mají byla měřítka tak, aby se všechny hodnoty v intervalu [0, 100].

## <a name="scenario-structure"></a>Struktura scénář

Při prvním spuštění v Azure Machine Learning Workbench otevřete tento projekt, přejděte *soubory* podokna na levé straně. Následující soubory kódu jsou součástí této ukázce:
- `1-data-preparation.ipynb` – Tento poznámkový blok Jupyter stahuje a zpracovává data, aby byl připravený k modelování. Toto je první Poznámkový blok, ve kterém se spouští.
- `2-linear-regression.ipynb` – Tento poznámkový blok, trénovat trénování modelu lineární regrese na trénovací data.
- `3-ridge.ipynb` -trénovat ridge regresní model.
- `4-ridge-poly2.ipynb` -trénovat ridge regresní model na stupně 2 polynomické funkce.
- `5-mlp.ipynb` -trénovat vícevrstvých perceptron neuronové sítě.
- `6-dtree.ipynb` -trénovat modelu rozhodovací strom.
- `7-gbm.ipynb` -trénovat model posíleného strojového přechodu.
- `8-evaluate-model.py` – Tento skript načte trénovaného modelu a použije ho k vytvoření predikcí nachází mimo testovací datové sady. Vytvoří model metrik výkonu různých modelů lze porovnat.
- `9-forecast-output-exploration.ipynb` – Tento poznámkový blok se vytvoří vizualizace předpovědí vygenerovaných tímto modely strojového učení.
- `10-deploy-model.ipynb` – Tento poznámkový blok ukazuje, jak může být trénovaný model Prognózování mají zprovoznit v webové služby v reálném čase.
- `evaluate-all-models.py` – Tento skript vyhodnotí všechny trénované modely. Představuje alternativu ke spuštění `8-evaluate-model.py` jednotlivě pro každý Trénink modelu.

### <a name="1-data-preparation-and-cleaning"></a>1. Příprava dat a čištění

Spustit ukázky, nejprve klikněte na `1-data-preparation.ipynb` otevřete Poznámkový blok v režimu náhledu. Klikněte na ***spustit Server poznámkového bloku*** spuštění jádra Python a server poznámkového bloku Jupyter na svém počítači. Můžete buď spustit poznámkových bloků z v rámci aplikace Workbench, nebo můžete pomocí prohlížeče přejděte na [ http://localhost:8888 ](http://localhost:8888). Všimněte si, že je nutné změnit jádro *PROJECT_NAME místní*. Udělat z *jádra* nabídky do poznámkového bloku v rámci *změnu jádra*. Stisknutím klávesy ***shift + Enter*** spusťte kód v jednotlivých Poznámkový blok buněk, nebo klikněte na *spustit všechny* v *buňky* nabídky celý poznámkový blok spustit.

Poznámkový blok nejprve stáhne data z kontejneru objektů blob úložiště hostované v Azure. Data se pak ukládá ve vašem počítači v `AZUREML_NATIVE_SHARE_DIRECTORY`. Toto umístění je přístupná ze všech poznámkových bloků nebo skripty, které spustíte z aplikace Workbench proto je vhodné místo k uložení dat a trénované modely.

Po stažení data vyčistí poznámkového bloku dat vyplnění mezer v časové řadě a vyplněním chybějících hodnot pomocí interpolace. Čištění dat časových řad tímto způsobem maximalizuje množství dat, které jsou k dispozici pro trénování modelů.

Několik funkcí modelu jsou vytvořeny z vyčištěnou nezpracovaná data. Tyto funkce lze rozdělit do dvou skupin:

- **Čas řízené funkce** jsou odvozeny z *časové razítko* proměnné třeba *měsíc*, *dayofweek* a *hodinu*.
- **Opožděné funkce** jsou hodnoty času posunuta vyžádání nebo teploty hodnot. Tyto funkce mají zachytit podmíněné závislosti mezi po sobě jdoucích období v modelu.

Výsledné datové sady funkce pak lze při vytváření modelů prognózy.

### <a name="2-model-development"></a>2. Vývoj modelu

První modelování přístup může být model lineární regrese jednoduché. `2-linear-regression.ipynb` Poznámkový blok ukazuje, jak pro trénování modelu lineární regrese prognózy pro budoucí energii. Konkrétně se model vyškolit tak, aby předpovídat poptávku po energii jednu hodinu (*t + 1*) náskok před aktuální časové období (*t*). Ale můžete použít tento model rekurzivně "jednoduchý" v době testu ke generování předpovědi pro budoucí časová období, *t + n*.

Pro trénování modelu, se vytvoří prediktivní kanálu, který zahrnuje tři samostatné kroky:

- **Transformace dat**: požadovaných formátech pro vstupní data mohou lišit v závislosti na algoritmu strojového učení. V takovém případě trénování modelu lineární regrese vyžaduje zařazené do kategorií proměnné být zakódovaný, aby jeden hot.
- **A křížové ověření rutina**: často model strojového učení, budou mít jeden nebo více hyperparameters, které je třeba ladit pomocí služby experimentování ve službě. Křížového ověření je možné najít optimální sadou hodnot parametrů. Model je opakovaně školení a vyhodnocené na různých složení datové sady. Doporučené parametry jsou ty, které dosažení nejlepší výkon modelu při průměrovaný napříč přeložení křížového ověření. Tento proces je vysvětleno podrobněji `2-linear-regression.ipynb`.
- **Trénování modelu konečné**: model se trénuje na celé datové sadě pomocí sady nejlepší hyperparametrů.

Uvádíme řadu 6 různých modelů v poznámkových blocích číslem 2 – 7. Každý Poznámkový blok jiný model trénovat a uloží jej do `AZUREML_NATIVE_SHARE_DIRECTORY` umístění. Jakmile máte školení všechny modely vytvořené pro tento scénář, jsme můžete vyhodnotit a jejich porovnání jako jsou popsány v následující části.

### <a name="3-model-evaluation-and-comparison"></a>3. Porovnání a vyhodnocení modelů

Můžeme použít trénovaného modelu provádět předpovědi pro budoucí časová období. Doporučujeme vyhodnotit tyto modely v datové sadě nachází mimo testu. Vyhodnocením různé modely na stejné datové sady nezobrazený jsme poměrně porovnání jejich výkon a identifikovat nejlepší model. V tomto případě používáme rekurzivně trénovaný model pro předpověď několik kroků čas v budoucnosti. Konkrétně jsme generování prognózy až šest hodin, *t + 6* náskok před do aktuální hodiny *t*. Tyto předpovědi vyhodnocují skutečné poptávce zjištěnými pro jednotlivá časová období.

K vyhodnocení modelu, otevřete `8-evaluate-model.py` skriptu z *soubory* podokno v aplikaci Workbench. Zkontrolujte, že *konfigurace spustit* je nastavena na **místní** a pak zadejte název modelu do *argumenty* pole. Název modelu musí přesně odpovídat *název_modelu* proměnná nastavená na začátku Poznámkový blok, ve kterém model se trénuje. Zadejte například "linear_regression" k vyhodnocení modelu trénovaného lineární regrese. Můžete také po všechny modely mají školení, můžete si vyzkoušet je pomocí jednoho příkazu spuštěním `evaluate-all-models.py`. Ke spuštění tohoto skriptu, otevřete příkazový řádek z aplikace Workbench a spusťte následující příkaz:

```
python evaluate-all-models.py
```
`8-evaluate-model.py` Skript provádí následující operace:

- Načte kanálu trénovaného modelu z disku
- Vytváří předpovědi na základě testovací datové
- Vypočítá metriky výkonu modelu a protokoly je
- Uloží test predikcí datovou sadu do `AZUREML_NATIVE_SHARE_DIRECTORY` pro pozdější kontrolu a analýzy
- Uloží kanálu trénovaného modelu na *výstupy* složky.

> [!Note]
> Ukládání modelu, který má *výstupy* složky do účtu úložiště objektů Blob v Azure spojené s vaším účtem služby experimentování ve službě automaticky zkopíruje objekt modelu. To znamená, že vám bude vždycky se nepovedlo načíst objekt uložený modelu z objektu blob, i když změníte počítač nebo výpočetního kontextu.

Přejděte *historie běhů* podokně a kliknutím na `8-evaluate-model.py`. Zobrazí se grafy znázorňující několik metriky výkonu modelu:

- **ME**: znamenat chyba předpovědi. To může být interpretován jako průměrné posunu předpovědi.
- **MPE**: [znamenat procento chyba](https://en.wikipedia.org/wiki/Mean_percentage_error) (ME vyjádřené jako procentní podíl skutečné poptávce)
- **MSE**: [znamenat kvadratické chyby](https://en.wikipedia.org/wiki/Mean_squared_error)
- **RMSE**: střední kořenové spolehlivosti chyby (odmocninu MSE)
- **MAPE**: [znamenat absolutní procenta chyb](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**: [symetrický procento střední absolutní chyba](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**: MAPE směrný plán prognózy, ve kterém se rovná do predikce. poslední známá vyžádání hodnotu.
- **MdRAE**: medián relativní absolutní chyba. Střední poměr prognózy chyby do směrného plánu předpovídat chyby. Hodnotu menší než 1 znamená, že předpověď provádí lepší než směrného plánu.

Výše uvedené všechny metriky odkazovat *t + 1* prognózy. Kromě výše uvedených metriky, zobrazí se také sadu odpovídající metriky ve službě *_horizon* příponu. To je metrika průměrem za všechny tečky v oblasti prognózy z období *t + 1* období *t + 6*.

Pokud metriky se nezobrazují v oblasti grafu, klikněte na symbol ozubeného kolečka v pravém horním rohu. Ověřte, že je zaškrtnuté políčko metriky výkonu modelu, které vás zajímají. Metriky se také zobrazí v tabulce pod grafy. Tato tabulka je znovu, přizpůsobitelné kliknutím na symbol ozubeného kolečka. Seřadí tabulku podle metrika výkonu k identifikaci nejlepší model. Pokud vás zajímají zobrazuje, jak se liší od období prognózy výkonu *t + 1* k *t + 6*, klikněte na položku pro model v tabulce. Grafy znázorňující MAPE, MPE a MdRAE metrik prognózy období se zobrazí pod *vizualizace*.

Při vyhodnocování modelů prognóz, může být velmi užitečná k vizualizaci predikcí výstup. To pomáhá k určení, zda předpovědi vytvořený, zobrazí se realistické mezi odborníky přes data. Může také pomoct identifikovat problémy v prognózy, pokud například předpověď provádí špatně v určitých časových období. `9-forecast-output-exploration.ipynb` Poznámkového bloku budou vytvářet vizualizace předpovědí vygenerovaných testovací datové sady.

### <a name="4-deployment"></a>4. Nasazení

Nejlepší model můžete zprovozněný a nasadit jako webovou službu v reálném čase. Tuto webovou službu lze poté vyvolat ke generování předpovědí na vyžádání, jak bude k dispozici nová data. V tomto scénáři je potřeba vygenerovat každou hodinu k predikci poptávky pro energiích v následných hodinu nových předpovědí. K provedení této úlohy, mohou být webové služby nasazené, která má celou řadu funkcí pro příslušné hodiny časové období jako vstup a vrátí předpokládanou poptávku jako výstup.

V této ukázce webové služby nasazené do počítače s Windows 10. Zkontrolujte jste dokončili požadované kroky pro místní nasazení uvedené v tomto [– Příručka Začínáme](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md) pro rozhraní příkazového řádku Operacionalizace. Jakmile nastavíte místní prostředí a účet služby Správa modelů, spusťte `10-deploy-model.ipynb` Poznámkový blok jupyter nasadit také webovou službu.

## <a name="conclusion"></a>Závěr

Tento příklad ukazuje, jak sestavit začátku do konce časové řady řešení Prognózování pro účely prognózy poptávky po energii. Řada zásad prozkoumali v této ukázce je možné rozšířit na další prognóz scénáře a obory.

Tento scénář popisuje, jak můžete aplikaci Azure Machine Learning Workbench pomáhají při vývoji řešení reálného světa pomocí užitečných funkcí, jako je například prostředí poznámkového bloku Jupyter a možnosti protokolování metrik mezi odborníky přes data. Ukázka také provede čtečky o můžete zprovozněný a nasadit pomocí rozhraní příkazového řádku Azure Machine Learning Operacionalizace modelu. Po nasazení webového rozhraní API služby umožňuje vývojáři nebo datovými architekty model Prognózování integrovat širší datového kanálu.
