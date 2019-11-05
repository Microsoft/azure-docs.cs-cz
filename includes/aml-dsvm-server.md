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
ms.openlocfilehash: 6bddfa048e4cb9cb7728c236417d4672df6f25b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476103"
---
1. [Vytvořte pracovní prostor Azure Machine Learning](../articles/machine-learning/service/how-to-manage-workspace.md).

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