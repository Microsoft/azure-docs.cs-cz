---
title: Pomocí nástroje Visual Studio Code pro AI rozšíření pomocí služby Azure Machine Learning
description: Další informace o Visual Studio Code Tools pro AI a jak spustit trénovací a nasazení strojového učení a hloubkového učení modely se službou Azure Machine Learning v nástroji VS Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: a298a49c5bdf28b30da05c1b6296fc211ec14c1d
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713142"
---
# <a name="vs-code-tools-for-ai-get-started-with-azure-machine-learning-from-visual-studio-code"></a>Nástroje VS Code pro AI: Začínáme s Azure Machine Learning z Visual Studio Code

V tomto článku se dozvíte o rozšíření Visual Studio Code (VS Code) **Tools pro AI**a jak spustit trénovací a nasazení strojového učení a hloubkového učení modely se službou Azure Machine Learning v nástroji VS Code.

Tools for AI rozšíření Visual Studio Code pomocí služby Azure Machine Learning pro přípravu dat, trénování a test ML modely pro místní a vzdálené používání cílových výpočetních prostředí, nasadit tyto modely a sledovat, vlastní metriky a experimentů.

## <a name="prerequisite"></a>Požadavek

+ Musí být nainstalované Visual Studio Code. VS Code je zdroj jednoduchý, ale výkonný editor kódu, který běží na vaší ploše. Obsahuje integrovanou podporu pro Python a další.  [Informace o instalaci VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Nainstalujte Python 3.5 nebo vyšší](https://www.anaconda.com/download/).

+ Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLfree) před tím, než začnete.

## <a name="install-vs-code-tools-for-ai-extension"></a>Instalace nástroje VS Code pro AI rozšíření

Při instalaci **Tools pro AI** rozšíření, dvě další rozšíření jsou automaticky nainstalovány (Pokud máte přístup k Internetu). Jsou [účet Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) rozšíření a [Python pro Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-python.python) rozšíření

Pro práci s Azure Machine Learning, musíme proměnit Python IDE VS Code. Práce s [Pythonu ve Visual Studio Code](https://code.visualstudio.com/docs/languages/python), vyžaduje rozšíření Python pro Microsoft, který získá instaluje s nástroji Tools pro AI automaticky. Rozšíření je vynikající integrované vývojové prostředí VS Code a funguje pro všechny operační systémy s širokou škálu interpretů Pythonu. Využívá všechny VS Code výkon k poskytování automatické dokončování a technologie IntelliSense, linting, ladění a testování, spolu se schopností snadno přepínat mezi prostředími Pythonu, včetně virtuální a prostředí conda. Přečtěte si tento návod, jak úpravy, spuštění a ladění kódu v Pythonu, najdete v článku [kurz Pythonu Hello World](https://code.visualstudio.com/docs/python/python-tutorial)

**Instalace nástrojů pro rozšíření AI:**

1. Spusťte VS Code.

1. V prohlížeči, navštivte: https://aka.ms/vscodetoolsforai. 

1. V této webové stránce klikněte na tlačítko **nainstalovat**. 

1. Na kartě rozšíření, klikněte na tlačítko **nainstalovat**.

1. Vítejte v kartě se otevře v nástroji VS Code pro rozšíření a Azure symbol je přidán do panelu aktivit.

   ![Azure ikonu na panelu aktivit Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. V dialogovém okně klikněte na tlačítko **Sign In** a postupujte podle pokynů na obrazovce výzva k ověření pomocí Azure. 
   
   Rozšíření Azure Account, která byla nainstalována spolu s VS Code Tools pro AI, umožňuje ověření pomocí účtu Azure. Zobrazit seznam příkazů v [rozšíření Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) stránky.

> [!Tip] 
> Podívejte se [IntelliCode rozšíření pro VS Code (preview)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode poskytuje sadu funkcí s asistencí AI pro IntelliSense v jazyce Python, jako je například odvození nejrelevantnější auto dokončování podle aktuální kontext kódu.

## <a name="install-the-sdk"></a>Instalace sady SDK

1. Ujistěte se, že je nainstalovaná a rozpoznaná službou VS Code Python 3.5 nebo vyšší. Pokud vám jeho instalaci, pak znovu spusťte VS Code a vyberte interpret Pythonu pomocí pokynů na adrese https://code.visualstudio.com/docs/python/python-tutorial.

1. V nástroji VS Code, otevřete paletu příkazů **Ctrl + Shift + P**.

1. Typ 'nainstalujte sadu Azure ML SDK"najít pip nainstalovat příkaz pro sadu SDK. Místní privátní prostředí Python se vytvoří, která má požadavky Visual Studio Code pro práci s Azure Machine Learning.
   ![Instalace](./media/vscode-tools-for-ai/install-sdk.png)

1. V okně integrovaného terminálu zadejte určený interpret Pythonu nebo mohli dostat **Enter** určený interpret Pythonu vaše výchozí.

   ![Instalace](./media/vscode-tools-for-ai/python.png)

## <a name="get-started-with-azure-machine-learning"></a>Začínáme se službou Azure Machine Learning

Před zahájením školení a nasazování modelů ML používáte VS Code, je potřeba vytvořit [pracovního prostoru služby Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace) v cloudu, které obsahují vaše modely a prostředky. Zjistěte, jak vytvořit a vytvoření prvního experimentu v daném pracovním prostoru.

1. Klepněte na ikonu na panelu aplikace Visual Studio Code aktivit Azure. Azure: Machine Learning postranního panelu se zobrazí.

   ![Instalace](./media/vscode-tools-for-ai/createworkspace.gif)

1. Klikněte pravým tlačítkem na vaše předplatné Azure a vyberte **vytvořit pracovní prostor**. Zobrazí se seznam. Animovaný obrázek název předplatného je "OpenMind Studio" a pracovní prostor je "MyWorkspace". 

1. Ze seznamu vyberte existující skupinu prostředků nebo vytvořte novou pomocí Průvodce v paletu příkazů.

1. V poli zadejte jedinečný a jasný název pro nový pracovní prostor. Na snímcích obrazovky je pracovní prostor s názvem "MyWorkspace".

1. Stiskněte enter a vytvoří se nový pracovní prostor. Zobrazí se ve stromu níže název předplatného.

1. Klikněte pravým tlačítkem na název pracovního prostoru a vyberte **vytvoření experimentu** v místní nabídce.  Experimenty udržovat přehled o spuštěních pomocí Azure Machine Learning.

1. Do pole zadejte název svého experimentu. Na snímcích obrazovky je experimentu s názvem "Mnist ručně".
 
1. Stiskněte enter a se vytvoří nový experiment. Zobrazí se ve stromu níže název pracovního prostoru.

1. Klikněte pravým tlačítkem na název experimentu a zvolte **připojit místní složky**. Tato složka by měla obsahovat vaše místní skriptů Pythonu. Složka se pak propojí s experiment v cloudu. 

   Teď každá experimentu běží s experimentu tak, aby všechny klíčové metriky se uloží v historii experimentů a modelů, které tréninku získat automaticky nahráli do Azure Machine Learning a ukládají s protokoly a metriky experimentu.

   [![Připojit do složky ve VS Code](./media/vscode-tools-for-ai/attachfolder.gif)](./media/vscode-tools-for-ai/attachfolder.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Používání klávesových zkratek

Oblíbili VS Code funkce Azure Machine Learning v nástroji VS Code jsou přístupná z klávesnice. Nejdůležitější kombinace kláves znát je kombinace kláves Ctrl + Shift + P, kterým se zobrazí paletu příkazů. Tady máte přístup ke všem funkcí VS Code, včetně klávesové zkratky pro většinu běžných operací.

[![Klávesové zkratky pro VS Code Tools pro AI](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Další postup

Můžete teď Visual Studio Code pro práci s Azure Machine Learning.

Zjistěte, jak [vytvořit cílových výpočetních prostředí, trénovat a nasazovat modely ve službě Visual Studio Code](how-to-vscode-train-deploy.md).