---
title: Postup konfigurace Azure Machine Learning Workbench pro práci s IDE?  | Dokumenty Microsoft
description: Příručka ke konfiguraci Azure Machine Learning Workbench pro práci s vaší IDE.
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 8a4bb8d825f89b08e9b7514d67ae2d2512cceec9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Postup konfigurace Azure Machine Learning Workbench pro práci s IDE 

Azure Machine Learning Workbench může být nakonfigurováno pro práci s oblíbených Python integrovaného vývojového prostředí (integrované vývojové prostředí). Umožňuje vývojové prostředí vědecké účely smooth data přesun mezi přípravy dat, vytváření kódu, spustit sledování a operationalization. Aktuálně jsou podporované integrovaného vývojového prostředí:
- Microsoft Visual Studio Code 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>Konfigurace workbench
1. Klikněte na **souboru** nabídce v horní části levého horního rohu aplikace. 
2. Vyberte **konfigurace projektu IDE** možnost plovoucím panelem 
3. Zadejte `VS Code` nebo `PyCharm` v **název** pole (název je volitelný)
4. Zadejte umístění k IDE spustitelný soubor (kompletní s názvem spustitelného souboru a rozšíření) **cesta spuštění**

### <a name="default-install-path-for-visual-studio-code"></a>Výchozí cestu instalace pro Visual Studio Code  

* Windows 32-bit- `C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* Windows 64-bit- `C:\Program Files\Microsoft VS Code\Code.exe`
* systému macOS - vyberte .app cestu, například `/Applications/Visual Studio Code.app`, a aplikace připojí zbytek cesty pro vás. Úplná cesta ke spustitelnému souboru, ve výchozím nastavení je `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`. Pokud jste provedli `Shell Command: Install 'code' command in PATH` příkazů v produktu VS Code, pak můžete taky odkazovat VS Code skript v `/usr/local/bin/code`

### <a name="default-install-path-for-pycharm"></a>Výchozí instalační cesta pro PyCharm 

* Windows 32-bit - `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* Windows 64-bit - `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* systému macOS - vyberte .app cestu, například "/ aplikace/PyCharm CE.app" a aplikace připojí zbytek cesty pro vás. Úplná cesta ke spustitelnému souboru, ve výchozím nastavení je `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`. Můžete také zjistit PyCharm ve složce Koš `/usr/local/bin/charm`

## <a name="open-project-in-ide"></a>Otevřete projekt v IDE 
Po dokončení konfigurace projektu Azure Machine Learning můžete otevřít tak, že otevřete **soubor** nabídky v Azure Machine Learning Workbench, pak klikněte na **otevřít projekt (< IDE_Name >)**. Tato akce otevře aktuální aktivní projekt v nakonfigurovaných rozhraní IDE. _Poznámka: Pokud nejste v projektu **otevřeného projektu (< IDE_Name >)** bude zakázán._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Konfigurace integrovaného terminálu ve Visual Studio Code

### <a name="windows"></a>Windows 
Budeme mít přepsat výchozí prostředí jako cmd místo prostředí PowerShell. Po kliknutí na tlačítko na **otevřeného projektu (< IDE_Name >)**, zobrazí se dotaz: 

_Povolíte prostředí shell: `C:\windows\System32\cmd.exe` (definovanou jako nastavení pracovního prostoru) ke spuštění v terminálu?_

Odpověď `yes` umožňující konfiguraci prostředí bezproblémově pracovat s rozhraní příkazového řádku Azure ML Workbench.

### <a name="mac"></a>Mac
Ke spuštění `az` příkaz pomocí Visual Studio Code integrované terminálu v systému Mac, je nutné ručně nastavit `PATH` na stejnou hodnotu jako `PATH` v projektu `.vscode/settings.json` souboru pod klíčem `terminal.integrated.env.osx`. Můžete tak učinit spuštěním následujícího příkazu v terminálu: `PATH=<PATH in .vscode/settings>`
