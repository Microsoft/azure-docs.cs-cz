---
title: Ve kterých je k dispozici – Video Indexer oblastí Azure
titlesuffix: Azure Media Services
description: Tento článek pojednává o oblasti Azure, ve kterých k Video indexeru k dispozici.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/08/2018
ms.author: anzaman
ms.openlocfilehash: 02872afed03494962d37e76ba0d8da524fb7b3a8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285372"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Oblasti Azure, ve kterých existuje Video Indexer

Rozhraní API pro video Indexer obsahovat **umístění** parametr, který byste měli nastavit na základě oblasti Azure, ke které se mají směrovat volání. Musí se jednat [oblast Azure, ve kterém je k dispozici funkce Video Indexer](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Umístění

**Umístění** parametr musí být uveden název kódu oblasti Azure jako svou hodnotu. Pokud používáte v režimu náhledu Video Indexer, měli byste umístit *"zkušební verze"* jako hodnotu. V opačném případě název kódu oblasti Azure, které váš účet a že vaše volání by měl směrovat na získáte spuštěním následující řádek [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```bash
az account list-locations
```

Po spuštění řádku vidíte výše, získání seznamu všech oblastech Azure. Přejděte do oblasti Azure, který má *displayName* hledáte a použít jeho *název* hodnota **umístění** parametru.

Například pro oblasti Azure USA – západ 2 (zobrazené níže), který použijete "westus2" **umístění** parametru.

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

## <a name="next-steps"></a>Další postup

- [Přizpůsobení jazykového modelu pomocí rozhraní API](customize-language-model-with-api.md)
- [Upravit značky modelu s použitím rozhraní API](customize-brands-model-with-api.md)
- [Přizpůsobení modelu osoba s použitím rozhraní API](customize-person-model-with-api.md)