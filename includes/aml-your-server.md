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
ms.openlocfilehash: 8d21e41ad487ad17598f2320fab5eebae02309e8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66123186"
---
1. Postupujte podle pokynů v [vytvořit pracovní prostor služby Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) na:
    * Vytvoření prostředí Miniconda
    * Nainstalujte aplikaci Azure Machine Learning sady SDK pro Python
    * Vytvoření pracovního prostoru
    * Zápis do souboru konfigurace pracovního prostoru (**aml_config/config.json**).
    
1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Přidejte konfigurační soubor pracovního prostoru pomocí některé z těchto metod:
    * Kopírovat **aml_config/config.json** soubor vytvořený v kroku 1 na klonovaný adresář.

    * V [webu Azure portal](https://ms.portal.azure.com)vyberte **stáhnout config.json** z **přehled** části pracovního prostoru. 

    ![Stáhněte si config.json](./media/aml-dsvm-server/download-config.png)

    * Vytvořit nový pracovní prostor pomocí kódu v [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Spusťte server poznámkového bloku v naklonovaném adresáři.
    
    ```shell
    jupyter notebook
    ```