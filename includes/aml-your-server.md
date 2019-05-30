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
ms.openlocfilehash: ce8b117a3cbe0e3a5c4265729ccf5c0264241013
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391783"
---
1. Postupujte podle pokynů v [vytvořit pracovní prostor služby Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) můžete provádět následující:
    * Vytvoření prostředí Miniconda
    * Nainstalujte aplikaci Azure Machine Learning sady SDK pro Python
    * Vytvoření pracovního prostoru
    * Zápis do souboru konfigurace pracovního prostoru (**aml_config/config.json**).

1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Spusťte server poznámkového bloku v naklonovaném adresáři.

    ```shell
    jupyter notebook
    ```