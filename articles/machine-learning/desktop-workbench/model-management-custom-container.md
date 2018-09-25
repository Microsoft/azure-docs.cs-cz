---
title: Přizpůsobení Image kontejneru pro nasazení modelů Azure ML | Dokumentace Microsoftu
description: Tento článek popisuje, jak přizpůsobit image kontejneru pro modely Azure Machine Learning
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: mldocs, raymondl
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: d4cfb7067510ec06df3319035dee5e2195cb2f9d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997535"
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Přizpůsobení image kontejneru pro modely Azure ML

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Tento článek popisuje, jak přizpůsobit image kontejneru pro modely Azure Machine Learning.  Azure ML Workbench nepoužívá kontejnery pro nasazení modelů strojového učení. Modely se nasazují společně s jejich závislostmi a Azure ML sestaví image z modelu, závislosti a přidružené soubory.

## <a name="how-to-customize-the-docker-image"></a>Postup přizpůsobení image Dockeru
Vlastní image Dockeru, který nasadí Azure ML pomocí:

1. A `dependencies.yml` souboru: ke správě závislostí, které jsou Instalovatelné z [PyPi]( https://pypi.python.org/pypi), můžete použít `conda_dependencies.yml` soubor z projektu aplikace Workbench, nebo si vytvořte svoje vlastní. Toto je tento přístup doporučuje pro závislosti Pythonu, které jsou Instalovatelné pip instalaci.

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
        
2. Docker kroky souboru: použití této možnosti přizpůsobení nasazené bitové kopii nainstalováním závislosti, které nelze nainstalovat z PyPi. 

   Soubor by měl obsahovat kroky instalace Dockeru jako souboru DockerFile. V souboru jsou povoleny následující příkazy: 

    RUN, ENV, ARG, LABEL, EXPOSE

   Příklad rozhraní příkazového řádku příkaz:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   Obrázek, Manifest a Service příkazy přijímají příznak soubor docker.

   Příklad souboru Docker kroky:
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> Základní image kontejnerů Azure ML je Ubuntu a nedá se změnit. Pokud chcete zadat jinou základní image, bude se ignorovat.

## <a name="next-steps"></a>Další postup
Teď, když jste přizpůsobili svou image kontejneru, můžete ho nasadit do clusteru pro použití ve velkém měřítku.  Podrobnosti o nastavení clusteru pro nasazení webové služby najdete v tématu [konfigurace správy modelu](deployment-setup-configuration.md). 
