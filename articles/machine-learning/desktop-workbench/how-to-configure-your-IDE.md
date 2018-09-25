---
title: Jak nakonfigurovat aplikaci Azure Machine Learning Workbench pro práci s integrovaným vývojovým prostředím?  | Dokumenty Microsoft
description: Tento průvodce konfiguruje aplikaci Azure Machine Learning Workbench pro práci s vaším prostředím IDE.
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 8158d6faee5ec4d28f0c7e16963fc3d78392b857
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978949"
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Jak nakonfigurovat aplikaci Azure Machine Learning Workbench pro práci s integrovaným vývojovým prostředím 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Azure Machine Learning Workbench můžete nakonfigurovat pro práci s oblíbenými integrovanými vývojovými prostředími Pythonu (integrované vývojové prostředí). Umožňuje vývojové prostředí vědy smooth data přesouvání mezi přípravy dat, vytváření kódu, sledování spuštění a operacionalizace. V současné době jsou podporované Integrovaná vývojová prostředí:
- Microsoft Visual Studio Code 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>Konfigurace aplikace workbench
1. Klikněte na **souboru** nabídky v horním levém horním rohu aplikace. 
2. Vyberte **konfigurace projektu IDE** možnost informační rámeček 
3. Zadejte `VS Code` nebo `PyCharm` v **název** pole (název je volitelný)
4. Zadejte umístění v integrovaném vývojovém prostředí spustitelný soubor (dokončeno s názvem spustitelného souboru a přípony) **cesta provedení**

### <a name="default-install-path-for-visual-studio-code"></a>Výchozí instalační cesty pro Visual Studio Code  

* Windows 32-bit `C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* Windows 64-bit `C:\Program Files\Microsoft VS Code\Code.exe`
* macOS – vyberte .app cestu, například `/Applications/Visual Studio Code.app`, a aplikace se připojí zbývající část cesty za vás. Úplná cesta ke spustitelnému souboru ve výchozím nastavení je `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`. Pokud jste spustili `Shell Command: Install 'code' command in PATH` příkaz ve VS Code, pak je také možné odkazovat skript VS Code na `/usr/local/bin/code`

### <a name="default-install-path-for-pycharm"></a>Výchozí instalační cesty pro PyCharm 

* Windows 32-bit - `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* Windows 64-bit - `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* macOS – vyberte .app cestu, například "/ Applications/PyCharm CE.app" a zbývající část cesty pro vás připojí aplikace. Úplná cesta ke spustitelnému souboru ve výchozím nastavení je `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`. Můžete také zjistit PyCharm ve složce bin `/usr/local/bin/charm`

## <a name="open-project-in-ide"></a>Otevřít projekt v integrovaném vývojovém prostředí 
Po dokončení konfigurace projektu aplikace Azure Machine Learning můžete otevřít tak, že otevřete **souboru** nabídky v aplikaci Azure Machine Learning Workbench klikněte **otevřít projekt (< IDE_Name >)**. Tato akce otevře aktuální aktivní projekt v nakonfigurovaných integrovaném vývojovém prostředí. _Poznámka: Pokud si nejste v projektu **otevřeného projektu (< IDE_Name >)** se deaktivuje._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Konfigurace integrovaného terminálu ve Visual Studio Code

### <a name="windows"></a>Windows 
Budeme mít přepsat výchozí prostředí bude cmd místo prostředí PowerShell. Po kliknutí na na **otevřeného projektu (< IDE_Name >)**, zobrazí se dotaz: 

_Povolit prostředí: `C:\windows\System32\cmd.exe` (posunu definovaná jako nastavení pracovního prostoru) spustit v terminálu?_

Odpověď `yes` umožňující konfiguraci prostředí bez problémů pracovat s rozhraním příkazového řádku Azure ML Workbench.

### <a name="mac"></a>Mac
Ke spuštění `az` příkaz pomocí Visual Studio Code integrovaný terminál na počítači Mac, je nutné ručně nastavit `PATH` na stejnou hodnotu jako `PATH` v projektu `.vscode/settings.json` souboru pod klíčem `terminal.integrated.env.osx`. Můžete to provést spuštěním následujícího příkazu v terminálu: `PATH=<PATH in .vscode/settings>`
