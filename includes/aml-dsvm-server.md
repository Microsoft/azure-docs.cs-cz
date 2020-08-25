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
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "79486088"
---
1. [Vytvořte pracovní prostor Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Přidejte konfigurační soubor pracovního prostoru do klonovaného adresáře pomocí některé z těchto metod:

    * V [Azure Portal](https://ms.portal.azure.com)vyberte  **Stáhnout config.jsv** části **Přehled** v pracovním prostoru. 

    ![Stáhnout config.js](./media/aml-dsvm-server/download-config.png)

    * Vytvořte nový pracovní prostor pomocí kódu v poznámkovém bloku [Configuration. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) ve klonovaném adresáři.

1. Spusťte server poznámkového bloku v naklonovaném adresáři.

    ```bash
    jupyter notebook
    ```