---
title: Použijte Visual Studio Code s
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak nainstalovat Azure Machine Learning pro Visual Studio Code a vytvoření jednoduchého experimentu v Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 898fd7d0416d1e1b27a25cabf524e72a7de44fde
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269582"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Začínáme s Azure Machine Learning pro Visual Studio Code

V tomto článku se dozvíte víc o instalaci **Azure Machine Learning pro Visual Studio Code** rozšíření a vytvoření prvního experimentu pomocí služby Azure Machine Learning ve Visual Studio Code (VS Code).

Pomocí rozšíření Azure Machine Learning v sadě Visual Studio code pomocí služby Azure Machine Learning pro přípravu dat, trénování a test modely strojového učení na cílových výpočetních prostředí místní a vzdálené, nasadit tyto modely a sledovat, vlastní metriky a experimentů.

## <a name="prerequisite"></a>Požadavek


+ Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree) ještě dnes.

+ Musí být nainstalované Visual Studio Code. VS Code je zdroj jednoduchý, ale výkonný editor kódu, který běží na vaší ploše. Obsahuje integrovanou podporu pro Python a další.  [Informace o instalaci VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Nainstalujte Python 3.5 nebo vyšší](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>Instalace Azure Machine Learning pro rozšíření VS Code

Při instalaci **Azure Machine Learning** rozšíření, dvě další rozšíření jsou automaticky nainstalovány (Pokud máte přístup k Internetu). Jsou [účet Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) rozšíření a [Python pro Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-python.python) rozšíření

Pro práci s Azure Machine Learning, musíme proměnit Python IDE VS Code. Práce s [Pythonu ve Visual Studio Code](https://code.visualstudio.com/docs/languages/python), vyžaduje rozšíření Python pro Microsoft, které se automaticky nainstaluje s rozšířením Azure Machine Learning. Rozšíření je vynikající integrované vývojové prostředí VS Code a funguje pro všechny operační systémy s širokou škálu interpretů Pythonu. Využívá všechny VS Code výkon k poskytování automatické dokončování a technologie IntelliSense, linting, ladění a testování, spolu se schopností snadno přepínat mezi prostředími Pythonu, včetně virtuální a prostředí conda. Přečtěte si tento návod, jak úpravy, spuštění a ladění kódu v Pythonu, najdete v článku [kurz Pythonu Hello World](https://code.visualstudio.com/docs/python/python-tutorial)

**Chcete-li nainstalovat rozšíření Azure Machine Learning:**

1. Spusťte VS Code.

1. V prohlížeči navštivte: [Azure Machine Learning pro Visual Studio Code (Preview)](https://aka.ms/vscodetoolsforai) rozšíření

1. V této webové stránce klikněte na tlačítko **nainstalovat**. 

1. Na kartě rozšíření, klikněte na tlačítko **nainstalovat**.

1. Vítejte v kartě se otevře v nástroji VS Code pro rozšíření a Azure symbol je přidán do panelu aktivit.

   ![Azure ikonu na panelu aktivit Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. V dialogovém okně klikněte na tlačítko **Sign In** a postupujte podle pokynů na obrazovce výzva k ověření pomocí Azure. 
   
   Rozšíření Azure Account, která byla nainstalována spolu s Azure Machine Learning pro rozšíření VS Code, umožňuje ověření pomocí účtu Azure. Zobrazit seznam příkazů v [rozšíření Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) stránky.

> [!Tip] 
> Podívejte se [IntelliCode rozšíření pro VS Code (preview)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode poskytuje sadu funkcí s asistencí AI pro IntelliSense v jazyce Python, jako je například odvození nejrelevantnější auto dokončování podle aktuální kontext kódu.

## <a name="install-the-sdk"></a>Instalace sady SDK

1. Ujistěte se, že je nainstalovaná a rozpoznaná službou VS Code Python 3.5 nebo vyšší. Pokud vám jeho instalaci, pak znovu spusťte VS Code a vyberte interpret Pythonu pomocí pokynů na adrese https://code.visualstudio.com/docs/python/python-tutorial.

1. V nástroji VS Code, otevřete paletu příkazů **Ctrl + Shift + P**.

1. Typ 'nainstalujte sadu Azure ML SDK"najít pip nainstalovat příkaz pro sadu SDK. Místní privátní prostředí Python se vytvoří, která má požadavky Visual Studio Code pro práci s Azure Machine Learning.

   ![nainstalovat sadu SDK Azure Machine Learning pro Python](./media/vscode-tools-for-ai/install-sdk.png)

1. V okně integrovaného terminálu zadejte určený interpret Pythonu nebo mohli dostat **Enter** určený interpret Pythonu vaše výchozí.

   ![Vyberte interpret](./media/vscode-tools-for-ai/python.png)

## <a name="get-started-with-azure-machine-learning"></a>Začínáme se službou Azure Machine Learning

Před zahájením školení a nasazení modelů strojového učení pomocí VS Code, je potřeba vytvořit [pracovního prostoru služby Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) v cloudu, které obsahují vaše modely a prostředky. Zjistěte, jak vytvořit a vytvoření prvního experimentu v daném pracovním prostoru.

1. Klepněte na ikonu na panelu aplikace Visual Studio Code aktivit Azure. Zobrazí se na bočním panelu Azure Machine Learning.

   [![boční panel](./media/vscode-tools-for-ai/CreateNewWorkspace.gif)](./media/vscode-tools-for-ai/CreateNewWorkspace.gif#lightbox)

1. Klikněte pravým tlačítkem na vaše předplatné Azure a vyberte **vytvořit pracovní prostor**. Zobrazí se seznam. Animovaný obrázek název předplatného je bezplatná zkušební verze a pracovní prostor je "TeamWorkspace". 

1. Ze seznamu vyberte existující skupinu prostředků nebo vytvořte novou pomocí Průvodce v paletu příkazů.

1. V poli zadejte jedinečný a jasný název pro nový pracovní prostor. Na snímcích obrazovky je pracovní prostor s názvem "TeamWorkspace".

1. Stiskněte enter a vytvoří se nový pracovní prostor. Zobrazí se ve stromu níže název předplatného.

1. Klikněte pravým tlačítkem na uzel Experiment a vyberte **vytvoření experimentu** v místní nabídce.  Experimenty udržovat přehled o spuštěních pomocí Azure Machine Learning.

1. Do pole zadejte název svého experimentu. Na snímcích obrazovky je experimentu s názvem "Mnist ručně".
 
1. Stiskněte enter a se vytvoří nový experiment. Zobrazí se ve stromu níže název pracovního prostoru.

1. Klikněte pravým tlačítkem na název experimentu a zvolte **připojit složky Experiment**. Tato složka by měla obsahovat vaše místní skriptů Pythonu. Složka se pak propojí s experiment v cloudu. 

   Teď každá experimentu běží s experimentu tak, aby všechny klíčové metriky se uloží v historii experimentů a modelů, které tréninku získat automaticky nahráli do Azure Machine Learning a ukládají s protokoly a metriky experimentu.

   [![Připojit do složky ve VS Code](./media/vscode-tools-for-ai/CreateNewExperimentandFolder.gif)](./media/vscode-tools-for-ai/CreateNewExperimentandFolder.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Používání klávesových zkratek

Oblíbili VS Code funkce Azure Machine Learning v nástroji VS Code jsou přístupná z klávesnice. Nejdůležitější kombinace kláves znát je kombinace kláves Ctrl + Shift + P, kterým se zobrazí paletu příkazů. Tady máte přístup ke všem funkcí VS Code, včetně klávesové zkratky pro většinu běžných operací.

[![Klávesové zkratky pro Azure Machine Learning pro VS Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Další postup

Můžete teď Visual Studio Code pro práci s Azure Machine Learning.

Zjistěte, jak [vytvořit cílových výpočetních prostředí, trénovat a nasazovat modely ve službě Visual Studio Code](how-to-vscode-train-deploy.md).
