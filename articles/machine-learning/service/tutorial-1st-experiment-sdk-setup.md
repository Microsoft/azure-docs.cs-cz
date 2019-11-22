---
title: 'Kurz: vytvoření prvního experimentu v ML'
titleSuffix: Azure Machine Learning
description: V tomto kurzu se naučíte, jak začít používat sadu SDK Azure Machine Learning Python spuštěnou v poznámkových blocích Jupyter.  V části 1 vytvoříte pracovní prostor, ve kterém budete spravovat experimenty a modely ML.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/25/2019
ms.openlocfilehash: d2247ba965c5e6db6e2da0218859d207d2fc61b8
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276628"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Kurz: Začínáme s vytvářením prvního experimentu ML pomocí sady Python SDK
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto kurzu dokončíte kompletní kroky, abyste mohli začít se sadou Azure Machine Learning Python SDK spuštěnou v poznámkových blocích Jupyter. Tento kurz je **první částí série kurzů se dvěma částmi**a popisuje nastavení a konfiguraci prostředí Python a také vytvoření pracovního prostoru pro správu experimentů a modelů strojového učení. [**Druhá část**](tutorial-1st-experiment-sdk-train.md) sestavení na této stránce umožňuje výuku více modelů strojového učení a zavedení procesu správy modelů pomocí Azure Machine Learning studia i sady SDK.

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvořte [pracovní prostor Azure Machine Learning](concept-workspace.md) pro použití v dalším kurzu.
> * Naklonujte Poznámkový blok kurzů do složky v pracovním prostoru.
> * Vytvořte cloudový virtuální počítač s poznámkovým blokem s nainstalovanou a předem nakonfigurovanou sadou Azure Machine Learning Python SDK.


Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Azure Machine Learning pracovní prostor je základní prostředek v cloudu, který používáte k experimentování, výuce a nasazování modelů strojového učení. Přijedná se o vaše předplatné Azure a skupinu prostředků k snadno spotřebovanému objektu ve službě. 

Pracovní prostor můžete vytvořit prostřednictvím Azure Portal, webové konzoly pro správu prostředků Azure. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Poznamenejte si svůj **pracovní prostor** a **předplatné**. Budete je potřebovat, abyste se ujistili, že vytvoříte experiment na správném místě. 


## <a name="azure"></a>Naklonování složky poznámkového bloku

V tomto příkladu se v pracovním prostoru používá cloudový notebook pro instalaci bez předkonfigurovaného a předem nakonfigurovaného prostředí. Použijte [vlastní prostředí](how-to-configure-environment.md#local) , pokud dáváte přednost kontrole prostředí, balíčků a závislostí.

Dokončili jste následující postup experimentování a spouštění v sadě Azure Machine Learning Studio, konsolidované rozhraní, které zahrnuje nástroje strojového učení, které slouží k provádění scénářů pro datové vědy v rámci všech úrovní dovedností.

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/).

1. Vyberte své předplatné a pracovní prostor, který jste vytvořili.

1. Na levé straně vyberte **poznámkové bloky** .

1. Otevřete složku **ukázky** .

1. Otevřete složku **Python** .

1. Otevřete složku s číslem verze.  Toto číslo představuje aktuální vydání sady Python SDK.

1. Na pravé straně složky **kurzy** vyberte **"..."** a pak vyberte **klonovat**.

    ![Klonovat složku](media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. Seznam složek zobrazuje každého uživatele, který přistupuje k pracovnímu prostoru.  Vyberte složku, do které se má naklonovat složka s **kurzy** .

## <a name="a-nameopenopen-the-cloned-notebook"></a><a name="open">otevřít Klonovaný Poznámkový blok

1. V části **uživatelské soubory** otevřete složku a otevřete složku klonované **kurzy** .

    ![Otevřít složku s kurzy](media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Poznámkové bloky můžete zobrazit ve složce **Samples** , ale nemůžete z nich spustit Poznámkový blok.  Pokud chcete spustit Poznámkový blok, ujistěte se, že jste v části **soubory uživatelů** otevřeli naklonované verze poznámkového bloku.
    
1. Ve složce s **kurzy** vyberte soubor **kurz – 1st-experiment-SDK-ipynb** .

1. Na horním panelu vyberte virtuální počítač poznámkového bloku, který chcete použít ke spuštění poznámkového bloku. Tyto virtuální počítače jsou předem nakonfigurované se všemi potřebnými ke spuštění Azure Machine Learning. Můžete vybrat virtuální počítač vytvořený libovolným uživatelem pracovního prostoru. 

1. Pokud se nenašly žádné virtuální počítače, vyberte **+ Přidat virtuální počítač** a vytvořte virtuální počítač. 

    1. Když vytváříte virtuální počítač, zadejte název.  Název musí mít 2 až 16 znaků. Platné znaky jsou písmena, číslice a znak a musí být také jedinečné v rámci předplatného Azure.

    1. Potom vyberte **Vytvořit**. Nastavení virtuálního počítače může trvat přibližně 5 minut.

1. Jakmile je virtuální počítač k dispozici, zobrazí se na horním panelu nástrojů.  Tento Poznámkový blok teď můžete spustit buď pomocí rutiny **Spustit vše** na panelu nástrojů, nebo pomocí **SHIFT + ENTER** v buňkách kódu poznámkového bloku.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste dokončili tyto úkoly:

* Byl vytvořen Azure Machine Learning pracovní prostor.
* Vytvořili a nakonfigurovali jste v pracovním prostoru Server cloudového poznámkového bloku.

V **druhé části** kurzu spustíte kód v `tutorial-1st-experiment-sdk-train.ipynb`, abyste mohli naučit model strojového učení. 

> [!div class="nextstepaction"]
> [Kurz: výuka prvního modelu](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Pokud neplánujete tuto část tohoto kurzu nebo žádné jiné kurzy, měli byste [zastavit virtuální počítač se serverem cloudového poznámkového bloku](tutorial-1st-experiment-sdk-train.md#clean-up-resources) , pokud ho nepoužíváte ke snížení nákladů.


