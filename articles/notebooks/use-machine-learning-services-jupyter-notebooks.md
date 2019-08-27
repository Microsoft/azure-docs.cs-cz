---
title: Použití Azure Machine Learning Services v Azure poznámkové bloky
description: Přehled ukázkové poznámkové bloky pro Azure Machine Learning Services, který vám pomůže s poznámkovými bloky Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: c99f815bd58b03dbc43ba742577259be5638fef9
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035737"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>Použití Azure Machine Learning služby v poznámkovém bloku

Azure Notebooks je předem nakonfigurovaný s potřebným prostředím pro spolupráci se [službou Azure Machine Learning](/azure/machine-learning/service/). Ukázkový projekt můžete snadno naklonovat do svého účtu poznámkových bloků a prozkoumejte různé scénáře Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Vytvoření klonu ukázky ke svému účtu

1. Přihlaste se do [poznámkových bloků Azure](https://notebooks.azure.com/).
1. Vyberte **Moje projekty** a přejdete na řídicí panel projekty.
1. Kliknutím na tlačítko **nahrát úložiště GitHub** (šipka nahoru) otevřete místní nabídku **nahrát úložiště GitHub** .
1. V místní nabídce `Azure/MachineLearningNotebooks` zadejte do **úložiště GitHubu**název projektu, jako je například Azure Machine Learning služba , zadejte identifikátor v **ID projektu**, zrušte zaškrtnutí **veřejného** , pokud chcete, a pak vyberte  **Importujte**.

    ![Importovat ukázku Azure Machine Learning Poznámkový blok ke svému účtu poznámkové bloky](media/azureml-import-project.png)

1. Po jedné až dvou minutách poznámkových bloků Azure automaticky přejde na řídicí panel nový projekt.

## <a name="run-a-sample-notebook"></a>Spuštění poznámkového bloku vzorku

1. Vyberte **00 - configuration.ipynb** spuštění konfiguračního oddílu poznámkového bloku a postupujte podle jeho pokynů a vytvořte pracovní prostor služby Azure Machine Learning.

    - Protože poznámkových bloků Azure již obsahuje potřebné balíčky Pythonu, lze pouze spustit fragmentu kódu v kroku 2 požadavky na ověření verze sady SDK Azure ML.

1. Po dokončení konfigurace vyberte **01. Začínáme** s otevřením složky, která obsahuje třináct různých ukázkových poznámkových bloků, z nichž každá je zřejmá.

## <a name="next-steps"></a>Další kroky

Dokumentace ke službě Azure Machine Learning Services obsahuje celou řadu dalších prostředků, které vás provedou práce pomocí služby Machine Learning v rámci poznámkových bloků:

- [Rychlé zprovoznění: Začněte s Azure Machine Learning pomocí Pythonu](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [#1 kurzu: Výuka modelu klasifikace obrázků pomocí služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [#2 kurzu: Nasazení modelu klasifikace imagí ve službě Azure Container instance (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Kurz: Školení modelu klasifikace pomocí automatizovaného strojového učení ve službě Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Také naleznete v dokumentaci k [Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
