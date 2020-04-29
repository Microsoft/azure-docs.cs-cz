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
ms.openlocfilehash: 2504245a14eee5308e046148f8073cf34ffa8143
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79486088"
---
1. [Vytvořte pracovní prostor Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Přidejte konfigurační soubor pracovního prostoru do klonovaného adresáře pomocí některé z těchto metod:

    * V [Azure Portal](https://ms.portal.azure.com)v části **Přehled** pracovního prostoru vyberte **Stáhnout soubor config. JSON** . 

    ![Stažení souboru config. JSON](./media/aml-dsvm-server/download-config.png)

    * Vytvořte nový pracovní prostor pomocí kódu v poznámkovém bloku [Configuration. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) ve klonovaném adresáři.

1. Spusťte server poznámkového bloku v naklonovaném adresáři.

    ```bash
    jupyter notebook
    ```