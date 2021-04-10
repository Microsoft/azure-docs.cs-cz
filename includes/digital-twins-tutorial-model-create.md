---
author: baanders
description: zahrnutí souboru pro digitální vlákna Azure – pokyny pro model v kurzu příkazového řádku
ms.topic: include
ms.date: 3/5/2021
ms.author: baanders
ms.openlocfilehash: a94b9304ecd6c6630f6ad45652e76d2879bbc1b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463748"
---
1. **Aktualizujte číslo verze**, abyste označili, že poskytujete aktualizovanou verzi tohoto modelu. Provedete to tak, že změníte *1* na konci `@id` hodnoty na *2*. Bude fungovat i číslo větší než aktuální číslo verze.
1. **Upravit vlastnost**. Změňte název `Humidity` vlastnosti na *HumidityLevel* (nebo něco jiného, pokud chcete. Pokud používáte něco jiného než *HumidityLevel*, pamatujte na to, co jste použili, a místo *HumidityLevel* v průběhu tohoto kurzu ho budete používat.
1. **Přidejte vlastnost**. Pod `HumidityLevel` vlastností, která končí na řádku 15, vložte následující kód pro přidání `RoomName` vlastnosti do místnosti:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **Přidejte relaci**. Pod `RoomName` vlastnost, kterou jste právě přidali, vložte následující kód pro přidání možnosti pro tento typ vlákna do formuláře *obsahuje* relace s jinými nezávislostmi:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

Až skončíte, aktualizovaný model by se měl shodovat s tímto:

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

Před přechodem na. Nezapomeňte soubor uložit.