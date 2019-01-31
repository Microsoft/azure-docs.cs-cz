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
ms.openlocfilehash: 18ba86ce7876ba8275eb4853e4fc9ea0f35fa186
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302183"
---
1. Dokončení [rychlý start Python pro Azure Machine Learning](../articles/machine-learning/service/quickstart-create-workspace-with-python.md) instalace sady SDK a vytvoření pracovního prostoru.  Můžete přejít **použití poznámkového bloku** části, chcete-li.
1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Přidejte konfigurační soubor pracovního prostoru pomocí některé z těchto metod:
    * Kopírovat **aml_config\config.json** soubor vytvořený pomocí tohoto rychlého startu požadovaných součástí na klonovaný adresář.
    * Vytvořit nový pracovní prostor pomocí kódu v [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
1. Spusťte server poznámkového bloku v naklonovaném adresáři.
    
    ```shell
    jupyter notebook
    ```