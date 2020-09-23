---
title: Ladění & řešení potíží s kanály ML
titleSuffix: Azure Machine Learning
description: Ladění kanálů Azure Machine Learning v Pythonu Seznamte se s běžnými nástrah pro vývoj kanálů a tipů, které vám pomůžou ladit skripty před a během vzdáleného spouštění. Naučte se používat Visual Studio Code k interaktivnímu ladění kanálů strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python
ms.openlocfilehash: 616cdb1d0940ea6f64c3be3d687adaa9c2a98cc2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889964"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Ladění kanálů strojového učení a řešení souvisejících potíží

V tomto článku se dozvíte, jak ladit a řešit potíže s [kanály strojového učení](concept-ml-pipelines.md) v sadě [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) a v [Návrháři Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-designer). Informace jsou k dispozici v tématu Postupy:

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Následující tabulka obsahuje běžné problémy při vývoji kanálů s potenciálními řešeními.

| Problém | Možné řešení |
|--|--|
| Nejde předat data do `PipelineData` adresáře. | Ujistěte se, že jste ve skriptu vytvořili adresář, který odpovídá tomu, kde váš kanál očekává výstupní data kroku. Ve většině případů vstupní argument definuje výstupní adresář a pak adresář vytvoří explicitně. Použijte `os.makedirs(args.output_dir, exist_ok=True)` k vytvoření výstupního adresáře. V tomto [kurzu](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) najdete příklad ukázkového skriptu, který ukazuje tento vzor návrhu. |
| Chyby závislostí | Pokud ve svém vzdáleném kanálu dojde k chybám závislostí, které nevznikly při místním testování, potvrďte, že závislosti a verze vzdáleného prostředí odpovídají hodnotám ve vašem testovacím prostředí. (Viz [sestavování prostředí, ukládání do mezipaměti a opakované použití](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Dvojznačné chyby s cíli výpočtů | Zkuste odstranit a znovu vytvořit výpočetní cíle. Opětovné vytváření výpočetních cílů je rychlé a může vyřešit některé přechodné problémy. |
| Kanál nepoužívá znovu postup | Použití tohoto kroku je ve výchozím nastavení povolené, ale ujistěte se, že jste ho neaktivovali v kroku kanálu. Pokud je opětovné použití zakázané, `allow_reuse` parametr v kroku se nastaví na `False` . |
| Nenutně funguje kanál. | Aby se zajistilo, že kroky se spustí znovu jenom v případě, že se změní jejich podkladová data nebo skripty, oddělte adresáře zdrojového kódu pro každý krok. Pokud používáte stejný zdrojový adresář pro více kroků, může docházet k zbytečnému opakovanému spuštění. Použijte `source_directory` parametr v objektu kroku kanálu, který odkazuje na izolovaný adresář pro daný krok, a ujistěte se, že nepoužíváte stejnou `source_directory` cestu pro více kroků. |
| Krok zpomalující epochs nebo jiné chování smyček | Zkuste přepnout všechny zápisy souborů, včetně protokolování, od `as_mount()` do `as_upload()` . Režim **připojení** používá vzdálený virtualizovaný systém souborů a nahrává celý soubor pokaždé, když je připojen k. |

## <a name="debugging-techniques"></a>Techniky ladění

Existují tři hlavní techniky pro ladění kanálů: 

* Ladění jednotlivých kroků kanálu v místním počítači
* K izolaci a diagnostice zdroje problému použijte protokolování a Application Insights
* Připojení vzdáleného ladicího programu k kanálu běžícímu v Azure

### <a name="debug-scripts-locally"></a>Místní ladění skriptů

Jedním z nejběžnějších chyb v kanálu je, že se skript domény nespustí tak, jak má, nebo obsahuje běhové chyby ve vzdáleném výpočetním kontextu, které se obtížně ladí.

Samotné kanály se nedají spouštět místně, ale spuštěné skripty v izolaci na místním počítači vám umožní ladit rychleji, protože nemusíte čekat na proces sestavení výpočtů a prostředí. K tomu je potřeba nějaká vývojová práce:

* Pokud jsou vaše data v cloudovém úložišti dat, budete muset stáhnout data a zpřístupnit je vašemu skriptu. Použití malého vzorku vašich dat je dobrým způsobem, jak vyjímat modul runtime a rychle získat zpětnou vazbu k chování skriptu.
* Pokud se pokoušíte simulovat krok zprostředkujícího kanálu, možná budete muset ručně vytvořit typy objektů, které konkrétní skript očekává od předchozího kroku.
* Budete také muset definovat vlastní prostředí a replikovat závislosti definované ve vzdáleném výpočetním prostředí.

Jakmile budete mít Instalační program skriptu spuštěný v místním prostředí, je mnohem jednodušší provádět úlohy ladění, jako je:

* Připojení vlastní konfigurace ladění
* Pozastavení provádění a kontrola stavu objektu
* Zachycení typu nebo logických chyb, které se nezveřejňují do doby běhu

> [!TIP] 
> Jakmile ověříte, že je váš skript spuštěný podle očekávání, dobrým dalším krokem je spuštění skriptu v kanálu s jedním krokem předtím, než se pokusíte spustit v kanálu s více kroky.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Konfigurace, zápis a kontrola protokolů kanálu

Místní testování skriptů je skvělým způsobem, jak ladit hlavní fragmenty kódu a složitou logiku předtím, než začnete sestavovat kanál, ale v některých případech bude pravděpodobně nutné ladit skripty během samotného spuštění kanálu, zejména při diagnostice chování, ke kterému dojde během interakce mezi jednotlivými kroky kanálu. Doporučujeme, `print()` abyste ve svých skriptech použili možnost použití příkazů, abyste viděli stav objektu a očekávané hodnoty při vzdáleném spuštění, podobně jako při ladění kódu JavaScriptu.

### <a name="logging-options-and-behavior"></a>Možnosti a chování protokolování

Následující tabulka poskytuje informace o různých možnostech ladění pro kanály. Nejedná se o vyčerpávající seznam, protože další možnosti existují kromě pouze Azure Machine Learning, Pythonu a OpenCensus, které jsou zde uvedeny.

| Knihovna                    | Typ   | Příklad                                                          | Cíl                                  | Zdroje informací                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metrika | `run.log(name, val)`                                             | Uživatelské rozhraní portálu Azure Machine Learning             | [Jak sledovat experimenty](how-to-track-experiments.md)<br>[AzureML. Core. Run – třída](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Tisk/protokolování v Pythonu    | Protokol    | `print(val)`<br>`logging.info(message)`                          | Protokoly ovladačů, Návrhář Azure Machine Learning | [Jak sledovat experimenty](how-to-track-experiments.md)<br><br>[Protokolování Pythonu](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Protokol    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights – trasování                | [Ladění kanálů ve službě Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Nástroje pro export OpenCensus pro Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Kuchařka protokolování Pythonu](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Příklad možností protokolování

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer"></a>Návrhář služby Azure Machine Learning

Pro kanály vytvořené v Návrháři můžete soubor **70_driver_log** najít na stránce pro vytváření obsahu nebo na stránce s podrobnostmi o spuštění kanálu.

### <a name="enable-logging-for-real-time-endpoints"></a>Povolit protokolování pro koncové body v reálném čase

Aby bylo možné řešit a ladit koncové body v reálném čase v návrháři, je nutné povolit protokolování Application Insight pomocí sady SDK. Protokolování umožňuje řešit a ladit nasazení modelu a problémy s využitím. Další informace najdete v tématu [protokolování pro nasazené modely](how-to-enable-logging.md#logging-for-deployed-models). 

### <a name="get-logs-from-the-authoring-page"></a>Získat protokoly ze stránky pro vytváření obsahu

Když odešlete spuštění kanálu a zůstanete na stránce vytváření obsahu, můžete najít soubory protokolu vygenerované pro každý modul, když se každý modul dokončí.

1. Vyberte modul, který se dokončil na plátně pro tvorbu.
1. V pravém podokně modulu otevřete kartu  **výstupy + protokoly** .
1. Rozbalte pravé podokno a vyberte **70_driver_log.txt** pro zobrazení souboru v prohlížeči. Protokoly také můžete stahovat místně.

    ![Rozšířené podokno výstup v Návrháři](./media/how-to-debug-pipelines/designer-logs.png)? zobrazení = Azure-ml-py&Preserve-View = true)? zobrazení = Azure-ml-py&Preserve-View = true)

### <a name="get-logs-from-pipeline-runs"></a>Získání protokolů z spuštění kanálu

Soubory protokolů pro konkrétní spuštění můžete najít na stránce s podrobnostmi o spuštění kanálu, kterou najdete v části **kanály** nebo **experimenty** v nástroji Studio.

1. Vyberte běh kanálu vytvořený v návrháři.

    ![Stránka spuštění kanálu](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Vyberte modul v podokně náhledu.
1. V pravém podokně modulu otevřete kartu  **výstupy + protokoly** .
1. Rozbalením pravého podokna zobrazte soubor **70_driver_log.txt** v prohlížeči nebo vyberte soubor pro místní stažení protokolů.

> [!IMPORTANT]
> Chcete-li aktualizovat kanál na stránce s podrobnostmi o spuštění kanálu, je nutné **naklonovat** spuštění kanálu do nové konceptu kanálu. Spuštění kanálu je snímek kanálu. Je podobný souboru protokolu a nedá se změnit. 

## <a name="application-insights"></a>Application Insights
Další informace o použití knihovny Pythonu OpenCensus tímto způsobem najdete v této příručce: [ladění a řešení potíží s kanály strojového učení v Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Interaktivní ladění pomocí Visual Studio Code

V některých případech možná budete muset interaktivně ladit kód Pythonu, který se používá v kanálu ML. Pomocí Visual Studio Code (VS Code) a debugpy se můžete připojit ke kódu při jeho spuštění ve školicím prostředí. Další informace najdete [v příručce k interaktivnímu ladění v vs Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Další kroky

* Nápovědu k balíčku [AzureML-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) a balíčku [AzureML-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true) najdete v referenčních informacích k sadě SDK.

* Podívejte se na seznam [výjimek návrháře a kódů chyb](algorithm-module-reference/designer-error-codes.md).
