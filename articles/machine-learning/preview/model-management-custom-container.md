---
title: Přizpůsobení bitové kopie kontejneru, používá pro nasazení Azure ML modely | Microsoft Docs
description: Tento článek popisuje, jak přizpůsobit image kontejner pro modely Azure Machine Learning
services: machine-learning
author: tedway
ms.author: tedway, raymondl
manager: mwinkle
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ms.openlocfilehash: 858c8933565aeeb22dc1b685082dab2c6481737b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Přizpůsobení bitové kopie kontejneru, použít pro modely Azure ML

Tento článek popisuje, jak lze přizpůsobit image kontejner pro modely Azure Machine Learning.  Azure ML Workbench používá kontejnery pro nasazování modelů machine learning. Modely jsou nasazeny společně s jejich závislosti a Azure ML vytvoří bitovou kopii z modelu, závislosti a přidružené soubory.

## <a name="how-to-customize-the-docker-image"></a>Postup přizpůsobení bitové kopie Docker
Přizpůsobení bitové kopie Docker, která Azure ML se nasazuje pomocí:

1. Soubor depenencies.yml: ke správě závislosti, které se instalovat z [úložiště PyPi]( https://pypi.python.org/pypi), můžete použít soubor conda_dependencies.yml z projektu Workbench, nebo vytvořit vlastní. Jedná se o postup doporučujeme pro instalaci Python závislosti, které pip instalovat.

   Příklad rozhraní příkazového řádku příkaz:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> -c amlconfig\conda_dependencies.yml
   ```

   Příklad souboru conda_dependencies: 
   ```yaml
   name: project_environment
   dependencies:
      - python=3.5.2
      - scikit-learn
      - ipykernel=4.6.1
      
      - pip:
        - azure-ml-api-sdk==0.1.0a11
        - matplotlib
   ```
        
2. Kroky Docker souboru: použití této možnosti přizpůsobení do nasazené bitové kopie nainstalováním závislosti, které nelze nainstalovat z úložiště PyPi. 

   Soubor musí zahrnovat kroky instalace Docker jako soubor Docker. V souboru jsou povoleny následující příkazy: 

    RUN, ENV, ARG, LABEL, EXPOSE

   Příklad rozhraní příkazového řádku příkaz:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   Bitové kopie, Manifest a služba příkazy přijmout příznak docker souboru.

   Příklad souboru Docker kroky:
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> Pro základní bitovou kopii kontejnery Azure ML je Ubuntu a nedá se změnit. Pokud zadáte jiný základní image, se budou ignorovat.

## <a name="next-steps"></a>Další postup
Teď, když jste přizpůsobili image kontejneru, můžete nasadit do clusteru s podporou pro použití ve velkém měřítku.  Podrobnosti o nastavení clusteru s podporou pro nasazení webu služby najdete v tématu [modelu Management Configuration](deployment-setup-configuration.md). 
