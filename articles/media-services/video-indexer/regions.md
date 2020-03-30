---
title: Oblasti, ve kterých je video indexer k dispozici - Azure
titleSuffix: Azure Media Services
description: Tento článek popisuje oblasti Azure, ve kterých je k dispozici Azure Media Services Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: c91b38fcbfb9b517651adead010408425e519a82
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382745"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Oblasti Azure, ve kterých existuje Video Indexer

Rozhraní API indexeru videa obsahují parametr **umístění,** který byste měli nastavit na oblast Azure, do které by mělo být volání směrováno. Musí se jednat o [oblast Azure, ve které je k dispozici video indexer](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Umístění

Parametr **umístění** musí mít jako hodnotu kódový název oblasti Azure. Pokud používáte Video Indexer v režimu náhledu, měli byste dát *"zkušební"* jako hodnotu. V opačném případě chcete-li získat kódový název oblasti Azure, ve které se váš účet nachází, a na který by měl být směrován váš hovor, můžete v [azure cli](/cli/azure)spustit následující řádek :

```azurecli-interactive
az account list-locations
```

Po spuštění řádku uvedeného výše získáte seznam všech oblastí Azure. Přejděte do oblasti Azure, která má *displayName,* který hledáte, a použijte jeho hodnotu *názvu* pro parametr **umístění.**

Například pro oblast Azure West US 2 (zobrazeno níže) použijete "westus2" pro parametr **umístění.**

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Další kroky

- [Přizpůsobení jazykového modelu pomocí rozhraní API](customize-language-model-with-api.md)
- [Přizpůsobení modelu značek pomocí api](customize-brands-model-with-api.md)
- [Přizpůsobení modelu osoby pomocí api](customize-person-model-with-api.md)
