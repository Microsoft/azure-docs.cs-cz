---
title: Vytvoření IoT Hub pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se používat příkazy rozhraní příkazového řádku Azure k vytvoření skupiny prostředků a pak ve skupině prostředků vytvořit službu IoT Hub. Také se dozvíte, jak odebrat centrum.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: 69372e4c212e2ce81bcd4c91d460aa191a1d3476
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087843"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Vytvoření služby IoT Hub pomocí Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

V tomto článku se dozvíte, jak vytvořit centrum IoT pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto postupu potřebujete předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Přihlaste se a nastavte svůj účet Azure.

Pokud používáte Azure CLI místně místo používání Cloud Shell, musíte se přihlásit ke svému účtu Azure.

Na příkazovém řádku spusťte [příkaz pro přihlášení](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Postupujte podle pokynů pro ověření pomocí kódu a přihlaste se ke svému účtu Azure ve webovém prohlížeči.

## <a name="create-an-iot-hub"></a>Vytvořit IoT Hub

Pomocí Azure CLI vytvořte skupinu prostředků a pak přidejte službu IoT Hub.

1. Když vytvoříte centrum IoT, musíte ho vytvořit ve skupině prostředků. Použijte existující skupinu prostředků, nebo spusťte následující [příkaz pro vytvoření skupiny prostředků](https://docs.microsoft.com/cli/azure/resource):
    
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

2. Spuštěním následujícího příkazu vytvořte ve skupině prostředků [Centrum IoT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) s použitím globálně jedinečného názvu pro vaše centrum IoT:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Předchozí příkaz vytvoří centrum IoT v cenové úrovni S1, pro kterou se fakturuje. Další informace najdete v tématu [ceny služby Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Odebrání IoT Hub

Pomocí Azure CLI můžete [odstranit jednotlivý prostředek](https://docs.microsoft.com/cli/azure/resource), jako je třeba Centrum IoT, nebo odstranit skupinu prostředků a všechny její prostředky, včetně všech rozbočovačů IoT.

Pokud chcete [Odstranit centrum IoT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete), spusťte následující příkaz:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Pokud chcete [Odstranit skupinu prostředků](https://docs.microsoft.com/cli/azure/group#az-group-delete) a všechny její prostředky, spusťte následující příkaz:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Další kroky

Další informace o používání služby IoT Hub najdete v následujících článcích:

* [IoT Hub příručka pro vývojáře](iot-hub-devguide.md)
* [Správa IoT Hub pomocí Azure Portal](iot-hub-create-through-portal.md)
