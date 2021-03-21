---
title: Vytvoření IoT Hub pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se používat příkazy rozhraní příkazového řádku Azure k vytvoření skupiny prostředků a pak ve skupině prostředků vytvořit službu IoT Hub. Také se dozvíte, jak odebrat centrum.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e9757b94330f889869080a0e961ab2728cee86f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659909"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Vytvoření služby IoT Hub pomocí Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

V tomto článku se dozvíte, jak vytvořit centrum IoT pomocí Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>Vytvořit IoT Hub

Pomocí Azure CLI vytvořte skupinu prostředků a pak přidejte IoT Hub.

1. Když vytvoříte centrum IoT, musíte ho vytvořit ve skupině prostředků. Použijte existující skupinu prostředků, nebo spusťte následující [příkaz pro vytvoření skupiny prostředků](/cli/azure/resource):
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > Předchozí příklad vytvoří skupinu prostředků v umístění USA – západ. Seznam dostupných umístění můžete zobrazit spuštěním tohoto příkazu: 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. Spuštěním následujícího [příkazu vytvořte IoT Hub](/cli/azure/iot/hub#az-iot-hub-create) ve skupině prostředků pomocí globálně jedinečného názvu pro vaše centrum IoT:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Předchozí příkaz vytvoří centrum IoT v cenové úrovni S1, pro kterou se fakturuje. Další informace najdete v tématu [ceny služby Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Odebrání IoT Hub

Pomocí Azure CLI můžete [odstranit jednotlivý prostředek](/cli/azure/resource), jako je třeba Centrum IoT, nebo odstranit skupinu prostředků a všechny její prostředky, včetně všech rozbočovačů IoT.

Pokud chcete [Odstranit centrum IoT](/cli/azure/iot/hub#az-iot-hub-delete), spusťte následující příkaz:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Pokud chcete [Odstranit skupinu prostředků](/cli/azure/group#az-group-delete) a všechny její prostředky, spusťte následující příkaz:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Další kroky

Další informace o používání služby IoT Hub najdete v následujících článcích:

* [IoT Hub příručka pro vývojáře](iot-hub-devguide.md)
* [Správa IoT Hub pomocí Azure Portal](iot-hub-create-through-portal.md)