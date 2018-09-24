---
title: Článek Rychlý start pro Visual Studio Code Tools pro Machine Learning v Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak začít s používáním sady Visual Studio Code Tools pro službu Machine Learning, od vytvoření experimentu a trénování modelu až po zprovoznění webové služby.
services: machine-learning
author: chris-lauren
ms.author: clauren
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/15/2017
ROBOTS: NOINDEX
ms.openlocfilehash: a2c4455735523b6b726dedb13151627d8ce70f22
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991755"
---
# <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools pro AI

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Sada Visual Studio Code Tools pro AI představuje rozšíření pro vývoj sloužící k sestavení, testování a nasazení řešení hloubkového učení a AI. Bezproblémově se integruje se službou Azure Machine Learning a poskytuje zejména zobrazení historie spuštění, podrobnosti o výkonu předchozích trénování a vlastní metriky. Nabízí zobrazení průzkumníka ukázek, který umožňuje procházení ukázek a spuštění nového projektu s využitím architektury [Microsoft Cognitive Toolkit](http://www.microsoft.com/cognitive-toolkit) (dříve označovaná jako CNTK), [Google TensorFlow](https://www.tensorflow.org) a dalších architektur hloubkového učení. Navíc poskytuje průzkumníka cílových výpočetních prostředí, který umožňuje odesílat úlohy k trénování modelů ve vzdálených prostředích, jako je služba Azure Virtual Machines nebo servery s Linuxem a GPU. 
 
## <a name="getting-started"></a>Začínáme 
Abyste mohli začít, musíte nejprve stáhnout a nainstalovat [Visual Studio Code](https://code.visualstudio.com/Download). Jakmile budete mít otevřený nástroj Visual Studio Code, proveďte následující kroky:
1. Klikněte na ikonu rozšíření na panelu aktivit. 
2. Vyhledejte Visual Studio Code Tools pro AI. 
3. Klikněte na tlačítko **Nainstalovat**. 
4. Po dokončení instalace klikněte na tlačítko **Znovu načíst**. 

Po opětovném načtení nástroje Visual Studio Code se rozšíření aktivuje. [Další informace o instalaci rozšíření](https://code.visualstudio.com/docs/editor/extension-gallery).

## <a name="exploring-project-samples"></a>Zkoumání ukázek projektů
Součástí sady Visual Studio Code Tools pro AI je průzkumník ukázek. Průzkumník ukázek usnadňuje vyhledávání ukázek a jejich zkoušení pomocí jenom několika kliknutí. Průzkumníka otevřete následujícím způsobem:   
1. Otevřete paletu příkazů (Zobrazení > **Paleta příkazů** nebo **Ctrl + Shift + P**).
2. Zadejte AI Sample. 
3. Zobrazí se doporučení položky AI: Open Azure ML Sample Explorer (AI: Otevřít průzkumníka ukázek pro Azure ML). Vyberte ji a stiskněte Enter. 

Alternativně můžete kliknout na ikonu průzkumníka ukázek.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Vytvoření nového projektu v průzkumníku ukázek 
Můžete procházet různé ukázky a získávat k nim více informací. Pusťme se do procházení, dokud nenajdeme ukázku Klasifikace Iris. Pokud chcete vytvořit nový projekt založený na této ukázce, postupujte následovně:
1. Klikněte na tlačítko Nainstalovat v ukázce projektu a všimněte si zobrazených výzev s příkazy, které vás provedou postupem vytvoření nového projektu. 
2. Vyberte název projektu, například Iris.
3. Zvolte cestu ke složce, ve které chcete svůj projekt vytvořit, a stiskněte Enter. 
4. Vyberte existující pracovní prostor a stiskněte Enter.

Projekt se pak vytvoří.

> [!TIP]
> Pro přístup k prostředku Azure budete muset být přihlášeni. V integrovaném terminálu zadejte az login a postupujte podle pokynů. 

## <a name="submitting-experiment-with-the-new-project"></a>Odeslání experimentu s použitím nového projektu
Když je nový projekt otevřený ve Visual Studio Code, odešleme úlohu do našeho jiného cílového výpočetního prostředí (místní a virtuální počítač s Dockerem).
Sada Visual Studio Code Tools pro AI poskytuje několik způsobů odeslání experimentu. 
1. Místní nabídka (kliknutí pravým tlačítkem) – **AI: Submit Job** (AI: Odeslat úlohu).
2. Z palety příkazů – AI: Submit Job (AI: Odeslat úlohu).
3. Alternativně můžete příkaz spustit přímo pomocí integrovaného terminálu s použitím příkazů Azure CLI pro službu Machine Learning.

Otevřete soubor iris_sklearn.py, klikněte pravým tlačítkem a vyberte **AI: Submit Job** (AI: Odeslat úlohu).
1. Vyberte vaši platformu: Azure Machine Learning.
2. Vyberte vaši konfiguraci spuštění: Docker-Python.

> [!NOTE]
> Pokud odesíláte úlohu poprvé, zobrazí se zpráva „No Machine Learning configuration found, creating...“ (Nebyla nalezena konfigurace služby Machine Learning, probíhá vytváření...). Otevře se soubor JSON, který uložte (**Ctrl + S**).

Po odeslání úlohy se v integrovaném terminálu zobrazí průběh spuštění. 

## <a name="view-list-of-jobs"></a>Zobrazení seznamu úloh
Po odeslání úloh můžete zobrazit seznam úloh z historie spuštění.
1. Otevřete paletu příkazů (Zobrazení > **Paleta příkazů** nebo **Ctrl + Shift + P**).
2. Zadejte AI List.
3. Zobrazí se doporučení položky AI: List Jobs (AI: Seznam úloh). Vyberte ji a stiskněte Enter.

Otevře se zobrazení seznamu úloh, ve kterém se zobrazí všechna spuštění a nějaké související informace.

## <a name="view-job-details"></a>Zobrazení podrobností o úloze
Když je zobrazení seznamu úloh pořád otevřené, klikněte na první spuštění v seznamu.
Pokud chcete zobrazit podrobné informace o výsledcích úlohy, klikněte na nejvyšší **ID úlohy**. 

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Postup konfigurace služby Azure Machine Learning pro práci s integrovaným vývojovým prostředím](./how-to-configure-your-IDE.md)
