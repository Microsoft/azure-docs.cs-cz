---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 01/25/2019
ms.openlocfilehash: 6c93d1243db1b3c4277a54cf71e10f6bbc648d26
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846018"
---
- Sada Azure Machine Learning SDK pro Python je nainstalována. Pokyny v [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) použijte k následujícím akcím:


1. Pokyny v [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) použijte k následujícím akcím:
    * Vytvoření prostředí Miniconda [vytváření a Správa pracovních prostorů služby Azure Machine Learning]
    * Instalace sady Azure Machine Learning SDK pro Python

1. Vytvořte [pracovní prostor služby Azure Machine Learning](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Zapsat soubor [konfiguračního souboru](../articles/machine-learning/service/how-to-configure-environment.md#workspace) (**aml_config/config. JSON**).

1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Spusťte server poznámkového bloku v naklonovaném adresáři.

    ```shell
    jupyter notebook
    ```