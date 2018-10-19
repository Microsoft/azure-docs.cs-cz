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
ms.date: 07/27/2018
ms.openlocfilehash: 12f0f17a7c25696d2c8ff5b427f4b103617e5678
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430809"
---
V okně příkazového řádku nebo prostředí vytvořte a aktivujte prostředí správce balíčků conda s rozšířeními numpy a cython. V tomto příkladu se používá Python 3.6.

  + Ve Windows:
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       conda activate myenv
       ```

  + V Linuxu nebo macOS:
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       source activate myenv
       ```

Nainstalujte sadu SDK.
   ```sh 
   pip install azureml-sdk
   ```
