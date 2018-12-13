---
title: Použití Azure Machine Learning Services v Azure poznámkové bloky
description: Přehled ukázkové poznámkové bloky pro Azure Machine Learning Services, který vám pomůže s poznámkovými bloky Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 1508ccc151aaafd7e0c0b46345c4dcb1a5b7b03c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083661"
---
# <a name="use-azure-machine-learning-services-in-a-notebook"></a>Použití služby Azure Machine Learning v poznámkovém bloku

Vybavená předem nakonfigurovaným rozhraním prostředí potřebné pro práci s Azure Notebooks [Azure Machine Learning Services](/azure/machine-learning/service/). Ukázkový projekt můžete snadno naklonovat do svého účtu poznámkových bloků a prozkoumejte různé scénáře Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Vytvoření klonu ukázky ke svému účtu

1. Přihlaste se do [poznámkových bloků Azure](https://notebooks.azure.com/).
1. Vyberte **projekty** přejít na řídicí panel projektů.
1. Vyberte **nahrát úložiště GitHub se vzorovými** (na šipku nahoru) tlačítko Otevřít **nahrát úložiště Github** automaticky otevíraného okna.
1. V místní nabídce zadejte `Azure/MachineLearningNotebooks` v **úložiště GitHub**, zadejte název projektu v **název projektu** jako "Služby Azure ML", zadejte identifikátor v **ID projektu**, zrušte zaškrtnutí políčka **veřejné** Pokud chcete, můžete vybrat **Import**.

    ![Importovat ukázku Azure Machine Learning Poznámkový blok ke svému účtu poznámkové bloky](media/azureml-import-project.png)

1. Po jedné až dvou minutách poznámkových bloků Azure automaticky přejde na řídicí panel nový projekt.

## <a name="run-a-sample-notebook"></a>Spuštění poznámkového bloku vzorku

1. Vyberte **00 - configuration.ipynb** spuštění konfiguračního oddílu poznámkového bloku a postupujte podle jeho pokynů a vytvořte pracovní prostor služby Azure Machine Learning.

    - Protože poznámkových bloků Azure již obsahuje potřebné balíčky Pythonu, lze pouze spustit fragmentu kódu v kroku 2 požadavky na ověření verze sady SDK Azure ML.

1. Po dokončení konfigurace vyberte **spouštěná 01.getting** přejděte do složky obsahující třináct různé ukázkové poznámkové bloky, z nichž každý je zřejmých.

## <a name="next-steps"></a>Další postup

Dokumentace ke službě Azure Machine Learning Services obsahuje celou řadu dalších prostředků, které vás provedou práce pomocí služby Machine Learning v rámci poznámkových bloků:

- [Rychlý start: Použití Pythonu k seznámení s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Kurz #1: Trénování modelu klasifikace obrázků pomocí služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [#2. kurz: Nasazení modelu klasifikace image v Azure Container Instance (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Kurz: Vyškolíme model klasifikace pomocí automatizovaných strojové učení ve službě Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Také naleznete v dokumentaci k [Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
