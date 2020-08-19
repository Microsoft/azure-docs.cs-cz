---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: a1ed4a9e123459a697f0894ff05430c63988a70b
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585405"
---
<!--Create a media services asset cLI-->

Následující příkaz rozhraní příkazového řádku Azure vytvoří nový prostředek Media Services. Nahraďte hodnoty `assetName` `amsAccountName` a `resourceGroup` hodnotami, se kterými právě pracujete.  

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```