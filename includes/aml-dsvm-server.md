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
ms.openlocfilehash: 02ef0d6c7c8ddc7088938d9c8ea379e3b97f3045
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158725"
---
1. [Vytvořit pracovní prostor služby Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md).

1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Přidejte konfigurační soubor pracovního prostoru do naklonovaného adresáře pomocí některé z těchto metod:

    * V [webu Azure portal](https://ms.portal.azure.com)vyberte **stáhnout config.json** z **přehled** části pracovního prostoru. 

    ![Stáhněte si config.json](./media/aml-dsvm-server/download-config.png)

    * Vytvořit nový pracovní prostor pomocí kódu v [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) Poznámkový blok v naklonovaném adresáři.


1. Spusťte server poznámkového bloku v naklonovaném adresáři.
    
    ```shell
    jupyter notebook
    ```