---
title: Použití Azure Machine Learning v Azure Notebooks
description: Přehled ukázkových poznámkových bloků pro Azure Machine Learning, které můžete použít s Azure Notebooks.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: e3c4fbdf35bf7ea1f4dddbceb9d7235c67ed13a4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277460"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Použití Azure Machine Learning v poznámkovém bloku

Azure Notebooks je předem nakonfigurovaný s potřebným prostředím pro práci s [Azure Machine Learning](/azure/machine-learning/service/). Ukázkový projekt můžete snadno naklonovat do svého účtu poznámkových bloků a prozkoumejte různé scénáře Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Vytvoření klonu ukázky ke svému účtu

1. Přihlaste se do [poznámkových bloků Azure](https://notebooks.azure.com/).
1. Vyberte **Moje projekty** a přejdete na řídicí panel projekty.
1. Kliknutím na tlačítko **nahrát úložiště GitHub** (šipka nahoru) otevřete místní nabídku **nahrát úložiště GitHub** .
1. V místní nabídce zadejte `Azure/MachineLearningNotebooks` do **úložiště GitHubu**, v části **název projektu** zadejte název projektu, jako je například "Azure Machine Learning", zadejte identifikátor v **ID projektu**, zrušte zaškrtnutí **veřejného** , pokud chcete, a pak vyberte **importovat**.

    ![Importovat ukázku Azure Machine Learning Poznámkový blok ke svému účtu poznámkové bloky](media/azureml-import-project.png)

1. Po jedné až dvou minutách poznámkových bloků Azure automaticky přejde na řídicí panel nový projekt.

## <a name="run-a-sample-notebook"></a>Spuštění poznámkového bloku vzorku

1. Vyberte **00 - configuration.ipynb** spuštění konfiguračního oddílu poznámkového bloku a postupujte podle jeho pokynů a vytvořte pracovní prostor služby Azure Machine Learning.

    - Protože poznámkových bloků Azure již obsahuje potřebné balíčky Pythonu, lze pouze spustit fragmentu kódu v kroku 2 požadavky na ověření verze sady SDK Azure ML.

1. Po dokončení konfigurace vyberte **01. Začínáme** s otevřením složky, která obsahuje třináct různých ukázkových poznámkových bloků, z nichž každá je zřejmá.

## <a name="next-steps"></a>Další kroky

Dokumentace k Azure Machine Learning obsahuje řadu dalších prostředků, které vás provedou prací s Machine Learning v rámci poznámkových bloků:

- [Rychlý start: Použití Pythonu k seznámení s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Kurz #1: výuka modelu klasifikace obrázků pomocí Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [#2. kurz: Nasazení modelu klasifikace image v Azure Container Instance (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Kurz: analýza modelu klasifikace pomocí automatizovaného strojového učení v Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Také naleznete v dokumentaci k [Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
