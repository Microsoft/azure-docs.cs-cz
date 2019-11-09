---
title: Oblasti, ve kterých je Video Indexer k dispozici – Azure
titleSuffix: Azure Media Services
description: Tento článek pojednává o oblastech Azure, ve kterých je Video Indexer k dispozici.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b67c89e02091d57b4f2dc901005c2dae36deafc2
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838201"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Oblasti Azure, ve kterých Video Indexer existuje

Rozhraní Video Indexer API obsahují parametr **Location** , který byste měli nastavit na oblast Azure, do které se má volání směrovat. Musí se jednat o [oblast Azure, ve které je video indexer k dispozici](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Umístění

Parametru **Location** se musí jako jeho hodnota přidávat název kódu oblasti Azure. Pokud používáte Video Indexer v režimu náhledu, měli byste jako hodnotu vložit *"zkušební"* . Jinak, pokud chcete získat název kódu oblasti Azure, ve které je váš účet, a na to, že se má vaše volání směrovat, můžete v [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)spustit následující řádek:

```bash
az account list-locations
```

Po spuštění výše uvedeného řádku získáte seznam všech oblastí Azure. Přejděte do oblasti Azure s názvem *DisplayName* , který hledáte, a použijte jeho *název* pro parametr **Location** .

Například pro oblast Azure Západní USA 2 (zobrazenou níže) budete pro parametr **Location** používat "westus2".

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
- [Přizpůsobení modelu značek pomocí rozhraní API](customize-brands-model-with-api.md)
- [Přizpůsobení modelu osoby pomocí rozhraní API](customize-person-model-with-api.md)
