---
author: baanders
description: zahrnutí souboru pro nahrání modelu do instance digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 3/23/2021
ms.author: baanders
ms.openlocfilehash: 987409f070f34f0fd9ee212fab8cc57e889e0146
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950586"
---
Model vypadá takto:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Pokud chcete **Tento model nahrát do instance s dvojitou** silou, spusťte následující příkaz Azure CLI, který nahraje výše uvedený model jako vložený formát JSON. Můžete spustit příkaz v [Azure Cloud Shell](../articles/cloud-shell/overview.md) v prohlížeči nebo na svém počítači, pokud máte rozhraní příkazového řádku [nainstalované místně](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

> [!Note]
> Pokud používáte Cloud Shell v prostředí PowerShellu, může být nutné, aby se znaky uvozovek v vložených polích JSON vyhnuly jejich správné analýze. Zde je příkaz pro nahrání modelu s touto úpravou:
>
> Nahrát model:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```