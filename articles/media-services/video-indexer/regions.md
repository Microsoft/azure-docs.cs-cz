---
title: Oblasti, ve kterých je Video Indexer k dispozici – Azure
titleSuffix: Azure Media Services
description: Tento článek pojednává o oblastech Azure, ve kterých je k dispozici Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 6ebdb22f50efbefc695f9752c6e6fc333571828c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530929"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Oblasti Azure, ve kterých Video Indexer existuje

Rozhraní Video Indexer API obsahují parametr **Location** , který byste měli nastavit na oblast Azure, do které se má volání směrovat. Musí se jednat o [oblast Azure, ve které je video indexer k dispozici](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Umístění

`location`Parametr musí mít jako svou hodnotu název kódu oblasti Azure. Pokud používáte Video Indexer v režimu náhledu, měli byste ho umístit `"trial"` jako hodnotu. `trial` je výchozí hodnota pro `location` parametr. Jinak, pokud chcete získat název kódu oblasti Azure, ve které je váš účet, a na to, že se má vaše volání směrovat, můžete použít Azure Portal nebo spustit příkaz rozhraní příkazového [řádku Azure CLI](/cli/azure) .

### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k webu [Video Indexer](https://www.videoindexer.ai/).
1. V pravém horním rohu stránky vyberte **uživatelské účty** .
1. V pravém horním rohu Najděte umístění vašeho účtu.  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="Umístění":::
    
###  <a name="cli-command"></a>CLI – příkaz

```azurecli-interactive
az account list-locations
```

Po spuštění výše uvedeného řádku získáte seznam všech oblastí Azure. Přejděte do oblasti Azure s názvem *DisplayName* , který hledáte, a použijte jeho *název* pro parametr **Location** .

Například pro oblast Azure Západní USA 2 (zobrazenou níže) budete pro parametr **Location** používat "westus2".

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
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
