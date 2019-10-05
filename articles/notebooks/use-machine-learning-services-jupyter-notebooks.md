---
title: Použití služeb Azure Machine Learning Services v Azure Notebooks
description: Přehled ukázkových poznámkových bloků pro Azure Machine Learning služby, které můžete použít s Azure Notebooks.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: f591758fa6e51c420a090aa62d5160320fe15fe8
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973034"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>Použití Azure Machine Learning služby v poznámkovém bloku

Azure Notebooks je předem nakonfigurovaný s potřebným prostředím pro spolupráci se [službou Azure Machine Learning](/azure/machine-learning/service/). Ukázkový projekt můžete snadno klonovat do účtu poznámkových bloků a prozkoumat celou řadu scénářů Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Naklonujte ukázku na svůj účet.

1. Přihlaste se [Azure Notebooks](https://notebooks.azure.com/).
1. Vyberte **Moje projekty** a přejdete na řídicí panel projekty.
1. Kliknutím na tlačítko **nahrát úložiště GitHub** (šipka nahoru) otevřete místní nabídku **nahrát úložiště GitHub** .
1. V místní nabídce zadejte `Azure/MachineLearningNotebooks` v **úložišti GitHubu**, v části **název projektu** zadejte název projektu, jako je například "Azure Machine Learning služba", zadejte identifikátor v **ID projektu**, zrušte zaškrtnutí políčka **Public** , pokud chcete, a pak vyberte **importovat.** .

    ![Import ukázky Azure Machine Learning poznámkového bloku do účtu vašich poznámkových bloků](media/azureml-import-project.png)

1. Po minutě nebo dvou Azure Notebooks vás automaticky přejde na řídicí panel nového projektu.

## <a name="run-a-sample-notebook"></a>Spuštění ukázkového poznámkového bloku

1. Vyberte **00-Configuration. ipynb** , aby se spustila konfigurační oddíl poznámkového bloku, a postupujte podle pokynů a vytvořte pracovní prostor Azure Machine Learning.

    - Vzhledem k tomu, že Azure Notebooks již obsahuje nezbytné balíčky Pythonu, můžete pouze spustit fragment kódu v kroku 2 požadavků pro ověření verze sady Azure ML SDK.

1. Po dokončení konfigurace vyberte **01. Začínáme** s otevřením složky, která obsahuje třináct různých ukázkových poznámkových bloků, z nichž každá je zřejmá.

## <a name="next-steps"></a>Další kroky

Dokumentace ke službě Azure Machine Learning Services obsahuje řadu dalších prostředků, které vás provedou při práci se službou Machine Learning v rámci poznámkových bloků:

- [Rychlý Start: použití Pythonu pro začátek Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Kurz #1: výuka modelu klasifikace obrázků pomocí služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Kurz #2: nasazení modelu klasifikace imagí ve službě Azure Container instance (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Kurz: výuka modelu klasifikace pomocí automatizovaného strojového učení ve službě Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Podívejte se také na dokumentaci k sadě [Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
