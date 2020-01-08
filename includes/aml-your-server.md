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
ms.date: 09/26/2019
ms.openlocfilehash: 1260ee32c267a2652ad02e74c3978654bf2f9669
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529778"
---
1. Pomocí pokynů v [sadě Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) nainstalujte sadu Azure Machine Learning SDK pro Python.

1. Vytvořte [pracovní prostor Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Napište soubor [konfiguračního souboru](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.JSON**).

1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Spusťte server poznámkového bloku v naklonovaném adresáři.

    ```shell
    jupyter notebook
    ```
