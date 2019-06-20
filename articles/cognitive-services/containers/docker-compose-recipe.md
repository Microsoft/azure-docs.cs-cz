---
title: Docker compose recepty kontejneru
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: IEvan
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: diberry
ms.openlocfilehash: 351c89d3dfbc066d7c94a9e6b99e399b07011043
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272928"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>Použití více kontejnerů v privátní síti pomocí Docker Compose

```docker-compose
version: '3.3'
services:
  forms:
    image:  "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: "https://westus2.api.cognitive.microsoft.com/"
       apiKey: 0e17f5e4a83c43bb9d7756289f0c5cf4
       FormRecognizer__ComputerVisionApiKey: 02a628714e9f4a6e970eb557fc780854
       FormRecognizer__ComputerVisionEndpointUri: "https://westcentralus.api.cognitive.microsoft.com/"
    volumes:
       - type: bind
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: 02a628714e9f4a6e970eb557fc780854
      billing: "https://westcentralus.api.cognitive.microsoft.com/"
    ports:
      - "5021:5000"
```
