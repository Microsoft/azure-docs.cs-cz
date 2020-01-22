---
title: Ladění a řešení potíží s kanály strojového učení
titleSuffix: Azure Machine Learning
description: Ladit a řešit potíže s kanály strojového učení v sadě Azure Machine Learning SDK pro Python. Seznamte se s běžnými nástrah pro vývoj kanálů a tipů, které vám pomůžou ladit skripty před a během vzdáleného spouštění.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 12/12/2019
ms.openlocfilehash: 991f7ebf51be5f805a8b12fa0af0fefeff0ef582
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76309553"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Ladění a řešení potíží s kanály strojového učení
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak ladit a řešit potíže s [kanály strojového učení](concept-ml-pipelines.md) v sadě [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) a v [Návrháři Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-designer).


## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Ladění a řešení potíží v sadě Azure Machine Learning SDK
Následující části poskytují přehled běžných nástrah při vytváření kanálů a různé strategie pro ladění kódu, který běží v kanálu. Následující tipy použijte, pokud máte potíže se spuštěním kanálu podle očekávání. 
### <a name="testing-scripts-locally"></a>Místní testování skriptů

Jedním z nejběžnějších chyb v kanálu je to, že připojený skript (skript pro čištění dat, hodnoticí skript atd.) neběží tak, jak je zamýšlený, nebo obsahuje běhové chyby ve vzdáleném výpočetním kontextu, které se obtížně ladí ve vašem pracovním prostoru na Azure Machine. Learning Studio. 

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

### <a name="debugging-scripts-from-remote-context"></a>Ladění skriptů ze vzdáleného kontextu

Místní testování skriptů je skvělým způsobem, jak ladit hlavní fragmenty kódu a složitou logiku předtím, než začnete sestavovat kanál, ale v některých případech bude pravděpodobně nutné ladit skripty během samotného spuštění kanálu, zejména při diagnostice chování, ke kterému dojde během interakce mezi jednotlivými kroky kanálu. Ve skriptech v kroku doporučujeme použití `print()`ch příkazů, abyste viděli stav objektu a očekávané hodnoty během vzdáleného spuštění, podobně jako při ladění kódu JavaScriptu.

Soubor protokolu `70_driver_log.txt` obsahuje: 

* Všechny tištěné příkazy během provádění skriptu
* Trasování zásobníku pro skript 

Chcete-li najít tento a další soubory protokolu na portálu, nejprve klikněte na spuštění kanálu ve vašem pracovním prostoru.

![Stránka seznamu spuštění kanálu](./media/how-to-debug-pipelines/pipelinerun-01.png)

Přejděte na stránku s podrobnostmi o spuštění kanálu.

![Stránka s podrobnostmi o spuštění kanálu](./media/how-to-debug-pipelines/pipelinerun-02.png)

Pro konkrétní krok klikněte na modul. Přejděte na kartu **protokoly** . Další protokoly obsahují informace o procesu sestavení image prostředí a kroku přípravy skriptu.

![Karta protokol stránky podrobností spuštění kanálu](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Spuštění *publikovaných kanálů* najdete na kartě **koncové body** v pracovním prostoru. Spuštění pro *nepublikované kanály* se dá najít v **experimentech** nebo **kanálech**.

### <a name="troubleshooting-tips"></a>Tipy poradce při potížích

Následující tabulka obsahuje běžné problémy při vývoji kanálů s potenciálními řešeními.

| Problém | Možné řešení |
|--|--|
| Nejde předat data do adresáře `PipelineData`. | Ujistěte se, že jste ve skriptu vytvořili adresář, který odpovídá tomu, kde váš kanál očekává výstupní data kroku. Ve většině případů vstupní argument definuje výstupní adresář a pak adresář vytvoří explicitně. K vytvoření výstupního adresáře použijte `os.makedirs(args.output_dir, exist_ok=True)`. V tomto [kurzu](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) najdete příklad ukázkového skriptu, který ukazuje tento vzor návrhu. |
| Chyby závislostí | Pokud jste vytvořili a otestovali skripty lokálně, ale při spuštění ve vzdálené výpočetní službě v kanálu zjistíte problémy se závislostmi, ujistěte se, že vaše závislosti a verze prostředí COMPUTE odpovídají vašemu testovacímu prostředí. |
| Dvojznačné chyby s cíli výpočtů | Odstranění a opětovné vytváření výpočetních cílů může vyřešit určité problémy s cíli výpočtů. |
| Kanál nepoužívá znovu postup | Použití tohoto kroku je ve výchozím nastavení povolené, ale ujistěte se, že jste ho neaktivovali v kroku kanálu. Pokud je zakázané opětovné použití, parametr `allow_reuse` v kroku se nastaví na `False`. |
| Nenutně funguje kanál. | Aby se zajistilo, že se kroky spustí znovu jenom v případě, že se změní jejich podkladová data nebo skripty, oddělte adresáře pro každý krok. Pokud používáte stejný zdrojový adresář pro více kroků, může docházet k zbytečnému opakovanému spuštění. Použijte parametr `source_directory` u objektu kroku kanálu, který odkazuje na izolovaný adresář pro daný krok, a ujistěte se, že nepoužíváte stejnou `source_directory` cestu pro více kroků. |

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer"></a>Ladění a řešení potíží v Návrháři Azure Machine Learning

Tato část poskytuje přehled o řešení potíží s kanály v návrháři.
Pro kanály vytvořené v Návrháři můžete najít **soubory protokolu** na stránce vytváření obsahu nebo na stránce s podrobnostmi o spuštění kanálu.

### <a name="access-logs-from-the-authoring-page"></a>Přístup k protokolům ze stránky pro vytváření obsahu

Když odešlete spuštění kanálu a zůstanete na stránce vytváření obsahu, můžete najít soubory protokolů vygenerované pro každý modul.

1. Vyberte libovolný modul na plátně pro tvorbu obsahu.
1. V podokně vlastnosti přejdete na kartu **protokoly** .
1. Vyberte soubor protokolu `70_driver_log.txt`

    ![Vytváření protokolů modulu stránky](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Přístup k protokolům z spuštění kanálu

Soubory protokolu konkrétních spuštění můžete také najít na stránce s podrobnostmi o spuštění kanálu v částech **kanály** nebo **experimenty** .

1. Vyberte běh kanálu vytvořený v návrháři.
    Stránka spuštění kanálu ![](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. V podokně náhledu vyberte libovolný modul.
1. V podokně vlastnosti přejdete na kartu **protokoly** .
1. Vyberte soubor protokolu `70_driver_log.txt`

## <a name="next-steps"></a>Další kroky

* Nápovědu k balíčku [AzureML-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) a balíčku [AzureML-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) najdete v referenčních informacích k sadě SDK.

* Podívejte se na seznam [výjimek návrháře a kódů chyb](algorithm-module-reference/designer-error-codes.md).
