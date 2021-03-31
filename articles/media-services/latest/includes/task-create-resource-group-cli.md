---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88602390"
---
<!-- Create a resource group -->

Pomocí následujícího příkazu vytvořte skupinu prostředků. Vyberte zeměpisnou oblast, která se bude používat k ukládání médií a záznamů metadat pro váš Media Services účet. Tato oblast se použije ke zpracování a streamování vašeho média.

```azurecli
az group create --name amsResourceGroup --location westus2
```
