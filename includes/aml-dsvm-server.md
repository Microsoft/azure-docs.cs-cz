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
ms.openlocfilehash: 12d1576c3bfbf96c0445fcd2a6f0bc37d6a68f11
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60753349"
---
1. Dokončení [rychlý start Python pro Azure Machine Learning](../articles/machine-learning/service/quickstart-create-workspace-with-python.md) vytvořit pracovní prostor.  Můžete přejít **použití poznámkového bloku** části, chcete-li.
1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
1. Přidejte konfigurační soubor pracovního prostoru pomocí některé z těchto metod:
    * Kopírovat **aml_config\config.json** soubor vytvořený pomocí tohoto rychlého startu požadovaných součástí na klonovaný adresář.
    * Vytvořit nový pracovní prostor pomocí kódu v [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) Poznámkový blok v naklonovaném adresáři.
1. Spusťte server poznámkového bloku v naklonovaném adresáři.
    
    ```shell
    jupyter notebook
    ```