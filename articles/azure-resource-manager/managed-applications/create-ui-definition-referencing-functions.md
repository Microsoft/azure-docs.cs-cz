---
title: Vytvoření referenčních funkcí pro definici uživatelského rozhraní
description: Popisuje funkce, které se mají použít při vytváření definic uživatelského rozhraní pro Azure Portal, které odkazují na jiné objekty.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87096949"
---
# <a name="createuidefinition-referencing-functions"></a>Funkce odkazující na CreateUiDefinition

Funkce, které se mají použít při odkazování na výstupy z vlastností nebo kontextu souboru CreateUiDefinition.

## <a name="basics"></a>práce

Vrátí výstupní hodnoty prvku, který je definován v kroku [základy](create-uidefinition-overview.md#basics) . Předat název prvku jako parametr této funkci.

Chcete-li získat výstupní hodnoty prvků v jiných krocích, použijte funkci [kroků ()](#steps) .

Následující příklad vrátí výstup elementu s názvem `clusterName` v kroku základy:

```json
"[basics('clusterName')]"
```

Vrácené hodnoty se liší v závislosti na typu načteného prvku.

## <a name="location"></a>location

Vrátí umístění vybrané v kroku základy nebo v aktuálním kontextu.

Následující příklad vrací hodnotu jako `"westus"` :

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

Vrátí podrobnosti o zdroji dat vybrané v kroku základy nebo v aktuálním kontextu.

Následující příklad:

```json
"[resourceGroup()]"
```

Vrátí následující vlastnosti:

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

Můžete získat libovolnou konkrétní hodnotu s zápisem tečky.

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>kroky

Vrátí prvky v zadaném kroku. Předat název kroku jako parametr této funkci. Od vrácených prvků lze získat konkrétní hodnoty vlastností.

Chcete-li získat výstupní hodnoty prvků v kroku základy, použijte funkci [základy ()](#basics) .

Následující příklad vrátí krok s názvem `vmParameters` . V tomto kroku je element s názvem `adminUsername` .

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>předplatné

Vrátí vlastnosti pro odběr vybraný v kroku základy nebo v aktuálním kontextu.

Následující příklad:

```json
"[subscription()]"
```

Vrátí následující vlastnosti:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>Další kroky

* Úvod k vývoji rozhraní portálu najdete v tématu [CreateUiDefinition.jspro prostředí pro vytváření spravované aplikace Azure](create-uidefinition-overview.md).
