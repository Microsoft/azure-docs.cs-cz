---
title: Ladění a řešení potíží s kanály strojového učení
titleSuffix: Azure Machine Learning
description: Ladit a řešit potíže s kanály strojového učení v sadě Azure Machine Learning SDK pro Python. Seznamte se s běžnými nástrah pro vývoj kanálů a tipů, které vám pomůžou ladit skripty před a během vzdáleného spouštění.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 10/01/2019
ms.openlocfilehash: 50593741e185a146c5a376c34da959063198e7d0
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813805"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Ladění a řešení potíží s kanály strojového učení

V tomto článku se dozvíte, jak ladit a řešit potíže s [kanály strojového učení](concept-ml-pipelines.md) v sadě [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Následující části poskytují přehled běžných nástrah při vytváření kanálů a různé strategie pro ladění kódu, který běží v kanálu. Následující tipy použijte, pokud máte potíže se spuštěním kanálu podle očekávání. 

## <a name="testing-scripts-locally"></a>Místní testování skriptů

Jedním z nejběžnějších chyb v kanálu je to, že připojený skript (skript pro čištění dat, hodnoticí skript atd.) neběží tak, jak je zamýšlený, nebo obsahuje běhové chyby ve vzdáleném výpočetním kontextu, které se obtížně ladí ve vašem pracovním prostoru v Azure Portal. 

Samotné kanály se nedají spouštět místně, ale testování částí vaše základní skripty představuje snadný způsob, jak zajistit, aby se skripty prováděly tak, co očekáváte v izolaci, aniž byste čekali na celou dobu trvání spuštění kanálu. K tomu je potřeba nějaká vývojová práce:

* Pokud jsou vaše data v cloudovém úložišti dat, budete muset stáhnout data a zpřístupnit je vašemu skriptu. Použití malého vzorku vašich dat je dobrým způsobem, jak vyjímat modul runtime a rychle získat zpětnou vazbu k chování skriptu.
* Pokud se pokoušíte simulovat krok zprostředkujícího kanálu, možná budete muset ručně vytvořit typy objektů, které konkrétní skript očekává od předchozího kroku.
* Budete také muset definovat vlastní prostředí a replikovat závislosti definované ve vzdáleném výpočetním prostředí.

Jakmile budete mít Instalační program skriptu spuštěný v místním prostředí, je mnohem jednodušší provádět úlohy ladění, jako je:

* Připojení vlastní konfigurace ladění
* Pozastavení provádění a kontrola stavu objektu
* Zachycení typu nebo logických chyb, které se nezveřejňují do doby běhu

## <a name="debugging-scripts-from-remote-context"></a>Ladění skriptů ze vzdáleného kontextu

Místní testování skriptů je skvělým způsobem, jak ladit hlavní fragmenty kódu a složitou logiku předtím, než začnete sestavovat kanál, ale v některých případech bude pravděpodobně nutné ladit skripty během samotného spuštění kanálu, zejména při diagnostice chování, ke kterému dojde během interakce mezi jednotlivými kroky kanálu. Pro zobrazení stavu objektu a očekávaných hodnot během vzdáleného spouštění doporučujeme použít možnost použití `print()`, podobně jako při ladění kódu JavaScriptu.

Soubor protokolu `70_driver_log.txt` obsahuje: 

* Všechny tištěné příkazy během provádění skriptu
* Trasování zásobníku pro skript 

Pokud chcete najít tento a další soubory protokolů na portálu, nejdřív klikněte na kanál ve svém pracovním prostoru.

![Stránka kanály na portálu](./media/how-to-debug-pipelines/pipeline-1.png)

Přejděte do nadřazeného spuštění kanálu.

![Nadřazené spuštění kanálů](./media/how-to-debug-pipelines/pipeline-2.png)

Klikněte na ID spuštění pro konkrétní krok.

![Stránka kanály na portálu](./media/how-to-debug-pipelines/pipeline-3.png)

Přejděte na kartu **protokoly** . Další protokoly obsahují informace o procesu sestavení image prostředí a kroku přípravy skriptu.

![Stránka kanály na portálu](./media/how-to-debug-pipelines/pipeline-4.png)

> [!TIP]
> Spuštění *publikovaných kanálů* najdete na kartě **kanály** v pracovním prostoru na portálu. Spuštění pro *nepublikované kanály* se dá najít v **experimentech**.

## <a name="troubleshooting-tips"></a>Tipy poradce při potížích

Následující tabulka obsahuje běžné problémy při vývoji kanálů s potenciálními řešeními.

| Problém | Možné řešení |
|--|--|
| Nelze předat data do adresáře `PipelineData`. | Ujistěte se, že jste ve skriptu vytvořili adresář, který odpovídá tomu, kde váš kanál očekává výstupní data kroku. Ve většině případů vstupní argument definuje výstupní adresář a pak adresář vytvoří explicitně. K vytvoření výstupního adresáře použijte `os.makedirs(args.output_dir, exist_ok=True)`. V tomto [kurzu](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) najdete příklad ukázkového skriptu, který ukazuje tento vzor návrhu. |
| Chyby závislostí | Pokud jste vytvořili a otestovali skripty lokálně, ale při spuštění ve vzdálené výpočetní službě v kanálu zjistíte problémy se závislostmi, ujistěte se, že vaše závislosti a verze prostředí COMPUTE odpovídají vašemu testovacímu prostředí. |
| Dvojznačné chyby s cíli výpočtů | Odstranění a opětovné vytváření výpočetních cílů může vyřešit určité problémy s cíli výpočtů. |
| Kanál nepoužívá znovu postup | Použití tohoto kroku je ve výchozím nastavení povolené, ale ujistěte se, že jste ho neaktivovali v kroku kanálu. Pokud je použití zakázáno, parametr `allow_reuse` v kroku se nastaví na hodnotu `False`. |
| Nenutně funguje kanál. | Aby se zajistilo, že se kroky spustí znovu jenom v případě, že se změní jejich podkladová data nebo skripty, oddělte adresáře pro každý krok. Pokud používáte stejný zdrojový adresář pro více kroků, může docházet k zbytečnému opakovanému spuštění. Použijte parametr `source_directory` u objektu kroku kanálu, který odkazuje na izolovaný adresář pro daný krok, a ujistěte se, že nepoužíváte stejnou cestu `source_directory` pro více kroků. |

## <a name="next-steps"></a>Další kroky

* Nápovědu k balíčku [AzureML-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) a balíčku [AzureML-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) najdete v referenčních informacích k sadě SDK.

* Postupujte podle [podrobného kurzu](tutorial-pipeline-batch-scoring-classification.md) použití kanálů pro dávkové vyhodnocování.