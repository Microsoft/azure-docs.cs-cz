---
title: 'Kurz: Vytvořte svůj první experiment ML: Instalace'
titleSuffix: Azure Machine Learning service
description: V této sérii kurzů dokončíte kompletní kroky, které vám pomohou začít se sadou Azure Machine Learning Python SDK spuštěnou v poznámkových blocích Jupyter.  První část pokrývá vytvoření prostředí serveru cloudového poznámkového bloku a vytvoření pracovního prostoru pro správu experimentů a modelů strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: 21da8dce7a77a086381bd21dc0129b614fa65ad3
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70138418"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment"></a>Kurz: Začněte vytvářet první experiment v ML

V tomto kurzu dokončíte kompletní kroky, abyste mohli začít se sadou Azure Machine Learning Python SDK spuštěnou v poznámkových blocích Jupyter. Tento kurz je **první částí série kurzů se dvěma částmi**a popisuje nastavení a konfiguraci prostředí Python a také vytvoření pracovního prostoru pro správu experimentů a modelů strojového učení. [**Druhá část**](tutorial-1st-experiment-sdk-train.md) sestavení na této stránce umožňuje výuku více modelů strojového učení a zavedení procesu správy modelů pomocí Azure Portal i sady SDK.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte pracovní prostor Machine Learning, který chcete použít v dalším kurzu.
> * Vytvořte cloudový virtuální počítač Azure Machine Learning s Jupyter poznámkovým blokem s nainstalovanou a předem nakonfigurovanou sadou Python SDK.

## <a name="prerequisites"></a>Požadavky

Jediným předpokladem pro tento kurz je předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze služby Azure Machine Learning](https://aka.ms/AMLFree) dnes

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pracovní prostor je základní prostředek v cloudu, který používáte k experimentování, výukě a nasazení modelů strojového učení. Přijedná se o vaše předplatné Azure a skupinu prostředků k snadno spotřebovanému objektu v sadě SDK. Pokud již máte pracovní prostor služby Azure Machine Learning, přejděte k [Další části](#azure). V opačném případě vytvořte nyní.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Vytvoření serveru cloudového poznámkového bloku

V tomto příkladu se v pracovním prostoru používá cloudový notebook pro instalaci bez předkonfigurovaného a předem nakonfigurovaného prostředí. Použijte [vlastní prostředí](how-to-configure-environment.md#local) , pokud dáváte přednost kontrole prostředí, balíčků a závislostí.

Z pracovního prostoru vytvoříte cloudový prostředek, abyste mohli začít používat Jupyter poznámkové bloky. Tento prostředek je cloudový virtuální počítač se systémem Linux, který je předem nakonfigurován se všemi potřebnými ke spuštění služby Azure Machine Learning.

1. Otevřete pracovní prostor v [Azure Portal](https://portal.azure.com/).  Pokud si nejste jistí, jak váš pracovní prostor najít na portálu, přečtěte si téma Jak [Najít pracovní prostor](how-to-manage-workspace.md#view).

1. Na stránce pracovního prostoru v Azure Portal na levé straně vyberte **virtuální počítače poznámkových bloků** .

1. Vyberte **+ Nová** a vytvořte virtuální počítač poznámkového bloku.

     ![Výběr nového virtuálního počítače](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. Zadejte název vašeho virtuálního počítače. Potom vyberte **Vytvořit**.

    > [!NOTE]
    > Název virtuálního počítače poznámkového bloku musí mít délku 2 až 16 znaků. Platné znaky jsou písmena, číslice a znak-znaku.  Název musí být taky jedinečný v rámci vašeho předplatného Azure.

1. Počkejte, než se stav změnína spuštěno.

### <a name="launch-jupyter-web-interface"></a>Spustit webové rozhraní Jupyter

Po spuštění virtuálního počítače pomocí části **virtuální počítače poznámkového bloku** spusťte webové rozhraní Jupyter.

1. Vyberte **Jupyter** ve sloupci **identifikátor URI** pro váš virtuální počítač.

    ![Spuštění serveru Jupyter notebook](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

    Odkaz spustí svůj server poznámkového bloku a otevře webovou stránku Jupyter poznámkového bloku na nové kartě prohlížeče.  Tento odkaz bude fungovat jenom pro osobu, která virtuální počítač vytvořila. Každý uživatel pracovního prostoru musí vytvořit svůj vlastní virtuální počítač.

1. Na webové stránce poznámkového bloku Jupyter je hlavní název_složky vaším uživatelským jménem.  Vyberte tuto složku.

    > [!TIP]
    > Tato složka se nachází v [účtu úložiště](concept-workspace.md#resources) ve vašem pracovním prostoru, nikoli na samotném virtuálním počítači poznámkového bloku.  Virtuální počítač poznámkového bloku můžete odstranit a pořád zachovat veškerou práci.  Když později vytvoříte nový virtuální počítač s poznámkovým blokem, načte se tato složka. Pokud svůj pracovní prostor sdílíte s ostatními, uvidí vaši složku a zobrazí se jejich.

1. `samples-*` Otevřete podadresář a pak otevřete`tutorials/tutorial-1st-experiment-sdk-train.ipynb`

> [!Warning]
> Ujistěte se, že `tutorial-1st-experiment-sdk-train.ipynb` soubor otevřete , nikoli `.yml` soubor > stejným názvem. 

Ve **druhé části** kurzu spouštíte kód v `tutorial-1st-experiment-sdk-train.ipynb` nástroji pro výuku modelu strojového učení.

## <a name="end"></a>Vyčištění prostředků

Tuto část neprovádějte, pokud plánujete pokračovat v **části 2** tohoto kurzu.

### <a name="stop-the-notebook-vm"></a>Zastavení virtuálního počítače poznámkového bloku

Pokud jste použili server cloudového poznámkového bloku, zastavte virtuální počítač, pokud ho nepoužíváte ke snížení nákladů.

1. V pracovním prostoru vyberte **virtuální počítače poznámkového bloku**.

   ![Zastavení serveru VM](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. V seznamu vyberte virtuální počítač.

1. Vyberte **zastavit**.

1. Až budete chtít znovu použít server, vyberte **Spustit**.

### <a name="delete-everything"></a>Odstranit vše

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste dokončili tyto úkoly:

* Byl vytvořen pracovní prostor služby Azure Machine Learning Service.
* Vytvořili a nakonfigurovali jste v pracovním prostoru Server cloudového poznámkového bloku.

Pokračujte v **části 2** tohoto kurzu a Naučte se jednoduchý model strojového učení.

> [!div class="nextstepaction"]
> [Kurz: Výuka prvního modelu](tutorial-1st-experiment-sdk-train.md)
