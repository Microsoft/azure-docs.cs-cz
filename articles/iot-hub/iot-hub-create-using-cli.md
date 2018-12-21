---
title: Vytvoření IoT Hubu pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Postup vytvoření služby Azure IoT hub pomocí Azure CLI.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: 90830c4e27e90af6c9d77509844696f64e4909f0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994764"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Vytvoření IoT hubu pomocí rozhraní příkazového řádku Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

V tomto článku se dozvíte, jak vytvořit IoT hubu pomocí rozhraní příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky

Pokud chcete dokončit tento návod, potřebujete předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Přihlaste se a v nastavení účtu Azure

Pokud používáte Azure CLI místně, namísto použití Cloud Shell, musíte se přihlásit ke svému účtu Azure.

Na příkazovém řádku, spusťte [přihlašovací příkaz](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

    ```azurecli
    az login
    ```

Postupujte podle pokynů pro ověření pomocí kódu a přihlaste se ke svému účtu Azure ve webovém prohlížeči.

## <a name="create-an-iot-hub"></a>Vytvořit IoT Hub

Pomocí rozhraní příkazového řádku Azure vytvořte skupinu prostředků a poté přidejte službu IoT hub.

1. Když vytvoříte službu IoT hub, třeba jej vytvořit ve skupině prostředků. Použijte existující skupinu prostředků, nebo spusťte následující příkaz [příkazu vytvořte skupinu prostředků](https://docs.microsoft.com/cli/azure/resource):
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > Předchozí příklad vytvoří skupinu prostředků v umístění Západní USA. Seznam dostupných umístění můžete zobrazit spuštěním tohoto příkazu: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Spusťte následující příkaz [příkaz pro vytvoření služby IoT hub](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) ve vaší skupině prostředků pomocí globálně jedinečný název pro službu IoT hub:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Předchozí příkaz vytvoří služby IoT hub S1 cenovou úroveň, pro které se vám účtuje. Další informace najdete v tématu [ceny služby Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Odeberte služby IoT Hub

Můžete použít Azure CLI pro [odstranit jednotlivé prostředky](https://docs.microsoft.com/cli/azure/resource), například služby IoT hub nebo odstranit skupinu prostředků a všechny její prostředky, včetně centra IoT.

K [odstranit centrum IoT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete), spusťte následující příkaz:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

K [odstranit skupinu prostředků](https://docs.microsoft.com/cli/azure/group#az-group-delete) a všechny její prostředky, spusťte následující příkaz:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Další postup

Další informace o používání služby IoT hub, najdete v následujících článcích:

* [Příručka vývojáře pro IoT Hub](iot-hub-devguide.md)
* [Pomocí webu Azure portal ke správě služby IoT Hub](iot-hub-create-through-portal.md)
