---
title: 'Kurz: vytvoření prvního experimentu ML: nastavení'
titleSuffix: Azure Machine Learning
description: V této sérii kurzů dokončíte kompletní kroky, které vám pomohou začít se sadou Azure Machine Learning Python SDK spuštěnou v poznámkových blocích Jupyter.  První část pokrývá vytvoření prostředí serveru cloudového poznámkového bloku a vytvoření pracovního prostoru pro správu experimentů a modelů strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/25/2019
ms.openlocfilehash: 3bbda22689bb330acc836173162a64b840f1bbd8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828036"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Kurz: Začínáme s vytvářením prvního experimentu ML pomocí sady Python SDK

V tomto kurzu dokončíte kompletní kroky, abyste mohli začít se sadou Azure Machine Learning Python SDK spuštěnou v poznámkových blocích Jupyter. Tento kurz je **první částí série kurzů se dvěma částmi**a popisuje nastavení a konfiguraci prostředí Python a také vytvoření pracovního prostoru pro správu experimentů a modelů strojového učení. [**Druhá část**](tutorial-1st-experiment-sdk-train.md) sestavení na této stránce umožňuje výuku více modelů strojového učení a zavedení procesu správy modelů pomocí Azure Portal i sady SDK.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte [pracovní prostor Azure Machine Learning](concept-workspace.md) pro použití v dalším kurzu.
> * Naklonujte Poznámkový blok kurzů do složky v pracovním prostoru.
> * Vytvořte cloudový virtuální počítač Azure Machine Learning s Jupyter poznámkovým blokem s nainstalovanou a předem nakonfigurovanou sadou Python SDK.

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Azure Machine Learning pracovní prostor je základní prostředek v cloudu, který používáte k experimentování, výuce a nasazování modelů strojového učení. Přijedná se o vaše předplatné Azure a skupinu prostředků k snadno spotřebovanému objektu ve službě. 

Pracovní prostor můžete vytvořit prostřednictvím Azure Portal, webové konzoly pro správu prostředků Azure. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Poznamenejte si svůj **pracovní prostor** a **předplatné**. Budete je potřebovat, abyste se ujistili, že vytvoříte experiment na správném místě. 


## <a name="azure"></a>Naklonování složky poznámkového bloku

V tomto příkladu se v pracovním prostoru používá cloudový notebook pro instalaci bez předkonfigurovaného a předem nakonfigurovaného prostředí. Použijte [vlastní prostředí](how-to-configure-environment.md#local) , pokud dáváte přednost kontrole prostředí, balíčků a závislostí.

Provedete následující kroky experimentování a spuštění na úvodní stránce pracovního prostoru (Preview), konsolidovaném rozhraní, které zahrnuje nástroje strojového učení, k provádění scénářů pro datové vědy u všech úrovní dovedností.

1. Přihlaste se na [úvodní stránku pracovního prostoru](https://ml.azure.com/).

1. Vyberte své předplatné a pracovní prostor, který jste vytvořili.

1. Na levé straně vyberte **poznámkové bloky a soubory** .

1. Otevřete složku **ukázky** .

1. Na pravé straně složky **kurzy** vyberte **"..."** a pak vyberte **klonovat**.

    ![Klonovat složku](media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. Pro každého uživatele, který přistupuje k pracovnímu prostoru, se zobrazí složka.  Vyberte složku pro naklonování složky **kurzu** .

## <a name="a-nameopenselect-a-vm-to-run-the-notebook"></a><a name="open">Select virtuálního počítače pro spuštění poznámkového bloku

1. V části **uživatelské soubory** otevřete složku a otevřete složku klonované **kurzy** .

    ![Otevřít složku s kurzy](media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Poznámkové bloky můžete zobrazit ve složce **Samples** , ale nemůžete z nich spustit Poznámkový blok.  Pokud chcete spustit Poznámkový blok, ujistěte se, že jste v části **soubory uživatelů** otevřeli naklonované verze poznámkového bloku.
    
1. Ve složce s **kurzy** vyberte soubor **kurz – 1st-experiment-SDK-ipynb** .

1. Na horním panelu vyberte virtuální počítač poznámkového bloku, který chcete použít ke spuštění poznámkového bloku. Tyto virtuální počítače jsou předem nakonfigurované se všemi potřebnými ke spuštění Azure Machine Learning. Můžete vybrat virtuální počítač vytvořený libovolným uživatelem pracovního prostoru. 

1. Pokud se nenašly žádné virtuální počítače, vyberte **+ Nový virtuální počítač** a vytvořte virtuální počítač.

    ![Vytvoření virtuálního počítače](media/tutorial-1st-experiment-sdk-setup/no-vm.png)

    1. Když vytváříte virtuální počítač, zadejte název.  Název musí mít 2 až 16 znaků. Platné znaky jsou písmena, číslice a znak a musí být také jedinečné v rámci předplatného Azure.

    1. Potom vyberte **Vytvořit**. Nastavení virtuálního počítače může trvat přibližně 5 minut.

1. Jakmile je virtuální počítač k dispozici, zobrazí se na horním panelu nástrojů.  Tento Poznámkový blok teď můžete spustit buď pomocí rutiny **Spustit vše** na panelu nástrojů, nebo pomocí **SHIFT + ENTER** v buňkách kódu poznámkového bloku.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste dokončili tyto úkoly:

* Byl vytvořen Azure Machine Learning pracovní prostor.
* Vytvořili a nakonfigurovali jste v pracovním prostoru Server cloudového poznámkového bloku.

V **druhé části** kurzu spustíte kód v `tutorial-1st-experiment-sdk-train.ipynb` pro výuku modelu Machine Learning. 

> [!div class="nextstepaction"]
> [Kurz: výuka prvního modelu](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Pokud neplánujete tuto část tohoto kurzu nebo žádné jiné kurzy, měli byste [zastavit virtuální počítač se serverem cloudového poznámkového bloku](tutorial-1st-experiment-sdk-train.md#clean-up-resources) , pokud ho nepoužíváte ke snížení nákladů.


