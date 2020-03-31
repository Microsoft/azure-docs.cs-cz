---
title: Vytvoření služby IoT Hub pomocí azure cli | Dokumenty společnosti Microsoft
description: Zjistěte, jak pomocí příkazů Azure CLI vytvořit skupinu prostředků a pak vytvořit centrum IoT ve skupině prostředků. Přečtěte si také, jak centrum odebrat.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e7253747d95917e34725cc7c6c034b1306f65e50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284717"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Vytvoření centra IoT pomocí azure cli

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Tento článek ukazuje, jak vytvořit centrum IoT pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto návodu potřebujete předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Přihlaste se a nastavte si účet Azure

Pokud používáte Azure CLI místně místo pomocí Cloud Shell, musíte se přihlásit ke svému účtu Azure.

Na příkazovém řádku spusťte [příkaz pro přihlášení](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Postupujte podle pokynů pro ověření pomocí kódu a přihlaste se ke svému účtu Azure ve webovém prohlížeči.

## <a name="create-an-iot-hub"></a>Vytvořit IoT Hub

Pomocí azure cli vytvořit skupinu prostředků a pak přidat centrum IoT.

1. Při vytváření centra IoT je nutné jej vytvořit ve skupině prostředků. Použijte existující skupinu prostředků, nebo spusťte následující [příkaz pro vytvoření skupiny prostředků](https://docs.microsoft.com/cli/azure/resource):
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > Předchozí příklad vytvoří skupinu prostředků v umístění USA – západ. Seznam dostupných umístění můžete zobrazit spuštěním tohoto příkazu: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Spuštěním následujícího [příkazu vytvořte centrum IoT hub](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) ve vaší skupině prostředků pomocí globálně jedinečného názvu služby IoT hub:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Předchozí příkaz vytvoří centrum IoT v cenové úrovni S1, za kterou se vám účtují. Další informace najdete v [tématu Azure IoT Hub ceny](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Odebrání centra IoT Hub

Pomocí azure cli můžete [odstranit jednotlivé prostředky](https://docs.microsoft.com/cli/azure/resource), jako je například služby IoT hub, nebo odstranit skupinu prostředků a všechny její prostředky, včetně všech center IoT.

Chcete-li [odstranit centrum IoT ,](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete)spusťte následující příkaz:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Chcete-li [odstranit skupinu prostředků](https://docs.microsoft.com/cli/azure/group#az-group-delete) a všechny její prostředky, spusťte následující příkaz:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Další kroky

Další informace o používání centra IoT hub, najdete v následujících článcích:

* [Průvodce vývojáři ioT Hubu](iot-hub-devguide.md)
* [Správa služby IoT Hub pomocí portálu Azure](iot-hub-create-through-portal.md)
