---
title: "Článek Rychlý start pro Visual Studio Tools pro Machine Learning v Azure | Dokumentace Microsoftu"
description: "Tento článek popisuje, jak začít s používáním sady Visual Studio Tools pro službu Machine Learning, od vytvoření experimentu a trénování modelu až po zprovoznění webové služby."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/15/2017
ms.openlocfilehash: bbcb2ea5a7ceeb976f590393608cc29c67d9a49e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2018
---
# <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools pro AI
Sada Visual Studio Tools pro AI představuje rozšíření pro vývoj sloužící k sestavení, testování a nasazení řešení hloubkového učení a AI. Bezproblémově se integruje se službou Azure Machine Learning a poskytuje zejména zobrazení historie spuštění, podrobnosti o výkonu předchozích trénování a vlastní metriky. Nabízí zobrazení průzkumníka ukázek, který umožňuje procházení ukázek a spuštění nového projektu s využitím architektury [Microsoft Cognitive Toolkit](http://www.microsoft.com/en-us/cognitive-toolkit) (dříve označovaná jako CNTK), [Google TensorFlow](https://www.tensorflow.org) a dalších architektur hloubkového učení. Navíc poskytuje průzkumníka cílových výpočetních prostředí, který umožňuje odesílat úlohy k trénování modelů ve vzdálených prostředích, jako je služba Azure Virtual Machines nebo servery s Linuxem a GPU. Poskytuje také snazší přístup k [Azure Batch AI (Preview)](https://docs.microsoft.com/azure/batch-ai/).
 
## <a name="getting-started"></a>Začínáme 
Abyste mohli začít, musíte nejprve stáhnout a nainstalovat sadu [Visual Studio](https://www.visualstudio.com/downloads/). Jakmile budete mít otevřenou sadu Visual Studio, proveďte následující kroky:
1. V sadě Visual Studio klikněte na řádek nabídek a vyberte Rozšíření a aktualizace...
2. Klikněte na kartu Online a vyberte Hledat na Visual Studio Marketplace.
3. Vyhledejte Visual Studio pro AI. 
3. Klikněte na tlačítko **Stáhnout**. 
4. Po instalaci restartujte sadu Visual Studio. 

Po opětovném načtení sady Visual Studio se rozšíření aktivuje. [Další informace o vyhledání rozšíření](hhttps://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions).

> [!NOTE]
> Visual Studio Tools pro AI vyžaduje sadu Visual Studio 2015 nebo 2017 v edici Professional nebo Enterprise. Verzi pro Apple OSX nepodporuje. 


## <a name="exploring-project-samples"></a>Zkoumání ukázek projektů
Součástí sady Visual Studio Tools pro AI je průzkumník ukázek. Průzkumník ukázek usnadňuje vyhledávání ukázek a jejich zkoušení pomocí jenom několika kliknutí. Průzkumníka otevřete následujícím způsobem:   
1. Na řádku nabídek klikněte na **Nástroje AI**.
2. Klikněte na Galerie Azure Machine Learning.

Otevře se karta se všemi ukázkami pro Azure ML.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Vytvoření nového projektu v průzkumníku ukázek 
Můžete procházet různé ukázky a získávat k nim více informací. Pusťme se do procházení, dokud nenajdeme ukázku Klasifikace Iris. Pokud chcete vytvořit nový projekt založený na této ukázce, postupujte následovně:
1. Klikněte na tlačítko **Nainstalovat** v ukázce projektu. Otevře se nové dialogové okno. 
2. Vyberte skupinu prostředků, účet a pracovní prostor.
3. Typ projektu můžete nechat jako Obecný.
4. Zadejte cestu k projektu a název projektu a pak stiskněte Enter. 
5. Otevře se dialogové okno s výzvou k uložení projektu, klikněte na Uložit. 

Po dokončení se nový projekt otevře v nové instanci sady Visual Studio. 

> [!TIP]
> Pro přístup k prostředku Azure musíte být přihlášeni. V integrovaném terminálu zadejte az login a postupujte podle pokynů. 

## <a name="submitting-experiment-with-the-new-project"></a>Odeslání experimentu s použitím nového projektu
Když je nový projekt otevřený v sadě Visual Studio, odešlete úlohu do cílového výpočetního prostředí (místní nebo virtuální počítač s Dockerem).
Úlohu odešlete následujícím způsobem: 
1. V průzkumníku řešení klikněte pravým tlačítkem na soubor, který chcete odeslat, a vyberte **Nastavit jako spouštěcí soubor**.
2. Vyberte název projektu, klikněte pravým tlačítkem a vyberte **Odeslat úlohu...**
3. Otevře se nové dialogové okno, ve kterém můžete zvolit cluster (nebo cílové výpočetní prostředí) pro spuštění vašeho skriptu.
4. Klikněte na **Odeslat**.

Po odeslání úlohy se v integrovaném terminálu zobrazí průběh spuštění.

## <a name="view-list-of-jobs"></a>Zobrazení seznamu úloh
Po odeslání úlohy můžete zobrazit seznam úloh z historie spuštění.
1. V **Průzkumníku serveru** klikněte na **Nástroje AI**.
2. Pak vyberte **Azure Machine Learning**.
3. Klikněte na nabídku **Úlohy**.

V Průzkumníku úloh se zobrazí seznam všech odeslaných experimentů pro tento projekt. 

## <a name="view-job-details"></a>Zobrazení podrobností o úloze
Když je zobrazení Průzkumníku úloh otevřené, klikněte na první spuštění v seznamu.
Tím se načtou panely Souhrn úlohy, Protokoly a Výstupy.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Postup konfigurace služby Azure Machine Learning pro práci s integrovaným vývojovým prostředím](./how-to-configure-your-IDE.md)
