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
ms.openlocfilehash: 09a3cc5a623be2ee5a9d50204f0902ca9f400a76
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857345"
---
1. [Vytvořte pracovní prostor služby Azure Machine Learning](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Přidejte konfigurační soubor pracovního prostoru do klonovaného adresáře pomocí některé z těchto metod:

    * V [Azure Portal](https://ms.portal.azure.com)v části **Přehled** pracovního prostoru vyberte **Stáhnout soubor config. JSON** . 

    ![Stažení souboru config. JSON](./media/aml-dsvm-server/download-config.png)

    * Vytvořte nový pracovní prostor pomocí kódu v poznámkovém bloku [Configuration. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) ve klonovaném adresáři.

1. Spusťte server poznámkového bloku v naklonovaném adresáři.

    ```shell
    jupyter notebook
    ```