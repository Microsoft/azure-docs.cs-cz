---
author: baanders
description: zahrnutí souboru pro nahrání modelu do instance digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: add49cabaece1187cb9bcda3a94c92feb08b2439
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304289"
---
Model vypadá takto:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Pokud chcete **Tento model nahrát do instance s dvojitou** silou, spusťte následující příkaz Azure CLI, který nahraje výše uvedený model jako vložený formát JSON. Příkaz můžete spustit v [Azure Cloud Shell](../articles/cloud-shell/overview.md) v prohlížeči (použijte prostředí **bash** ), nebo na svém počítači, pokud máte rozhraní příkazového řádku [nainstalované místně](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```