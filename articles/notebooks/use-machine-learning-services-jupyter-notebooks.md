---
title: Pomocí Azure Machine Learning Services v poznámkových bloků Azure | Dokumentace Microsoftu
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
ms.openlocfilehash: fabd89f7591abafd68b318921fb7723f3eb7373d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855791"
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

- [Rychlý start: Použití Pythonu k seznámení s Azure Machine Learning](/azure/machine-learning/service/quickstart-create-workspace-with-python.md)
- [Kurz #1: Trénování modelu klasifikace obrázků pomocí služby Azure Machine Learning](/azure/machine-learning/service/tutorial-train-models-with-aml.md)
- [#2. kurz: Nasazení modelu klasifikace image v Azure Container Instance (ACI)](/azure/machine-learning/service/tutorial-deploy-models-with-aml.md)
- [Kurz: Vyškolíme model klasifikace pomocí automatizovaných strojové učení ve službě Azure Machine Learning](/azure/machine-learning/service/tutorial-auto-train-models.md)

Také naleznete v dokumentaci k [Azure Machine Learning SDK pro Python](/python/api/overview/azure/ml/intro.md?view=azure-ml-py).
