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
ms.date: 03/05/2020
ms.openlocfilehash: ff449626ce528cfe0218a95330a567303c547e5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "79486020"
---
1. Pomocí pokynů v [sadě Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)  nainstalujte sadu Azure Machine Learning SDK pro Python.

1. Vytvořte [pracovní prostor Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Napište soubor  [konfiguračního souboru](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.jszapnutý**).

1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Spusťte server poznámkového bloku v naklonovaném adresáři.

    ```bash
    jupyter notebook
    ```