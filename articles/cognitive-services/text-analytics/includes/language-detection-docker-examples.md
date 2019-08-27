---
title: Příklady Rozpoznávání jazyka kontejnerů Docker
titleSuffix: Azure Cognitive Services
description: Příklady Rozpoznávání jazyka kontejnerů Docker
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: 4d737dea4c78034382f431662a624c9295f3a354
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051127"
---
## <a name="language-detection-container-docker-examples"></a>Příklady Rozpoznávání jazyka kontejnerů Docker

Následující příklady Docker jsou pro kontejner Rozpoznávání jazyka.

### <a name="basic-example"></a>Základní příklad 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Příklad protokolování 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
  ```
