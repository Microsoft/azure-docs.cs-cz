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
ms.openlocfilehash: e11b1c8c19db73ace069d4bfda3516cdbbceaa02
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476088"
---
1. Pomocí pokynů v [sadě Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) nainstalujte sadu Azure Machine Learning SDK pro Python.

1. Vytvořte [pracovní prostor Azure Machine Learning](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Zapsat soubor [konfiguračního souboru](../articles/machine-learning/service/how-to-configure-environment.md#workspace) (**aml_config/config. JSON**).

1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Spusťte server poznámkového bloku v naklonovaném adresáři.

    ```shell
    jupyter notebook
    ```
