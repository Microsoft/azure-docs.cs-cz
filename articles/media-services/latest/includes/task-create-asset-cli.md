---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: e16e3dc6788fb30a87a78fd6d6077087f461cfa7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512799"
---
<!--Create a media services asset CLI-->

Následující příkaz rozhraní příkazového řádku Azure vytvoří nový prostředek Media Services. Nahraďte hodnoty `assetName` `amsAccountName` a `resourceGroup` hodnotami, se kterými právě pracujete.  

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
