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
ms.openlocfilehash: 8d8b314965253dc00b39d0b068b1d6fb3e4aa471
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60753343"
---
1. Postupujte podle pokynů v [vytvořit pracovní prostor služby Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) Miniconda prostředí vytvořit, vytvořit pracovní prostor a zápis do souboru konfigurace pracovního prostoru (**aml_config/config.json**) .

1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Přidejte konfigurační soubor pracovního prostoru pomocí některé z těchto metod:
    * Kopírovat **aml_config/config.json** soubor vytvořený pomocí tohoto rychlého startu požadovaných součástí na klonovaný adresář.
    * Vytvořit nový pracovní prostor pomocí kódu v [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
1. Spusťte server poznámkového bloku v naklonovaném adresáři.
    
    ```shell
    jupyter notebook
    ```