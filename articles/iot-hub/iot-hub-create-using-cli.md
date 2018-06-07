---
title: Vytvoření služby IoT Hub pomocí rozhraní příkazového řádku Azure (az.py) | Microsoft Docs
description: Postup vytvoření služby Azure IoT hub pomocí Azure CLI a platformy 2.0 (az.py).
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: 9f97775a5a49077a340efb0e3de14b7064db5fe4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632760"
---
# <a name="create-an-iot-hub-using-the-azure-cli-20"></a>Vytvoření služby IoT hub pomocí Azure CLI 2.0

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Úvod

Azure CLI 2.0 (az.py) můžete použít k vytváření a správě Azure IoT hubs prostřednictvím kódu programu. Tento článek ukazuje, jak používat Azure CLI 2.0 (az.py) k vytvoření služby IoT hub.

K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

* [Rozhraní příkazového řádku Azure (azure.js)](iot-hub-create-using-cli-nodejs.md) – rozhraní příkazového řádku pro modelem nasazení classic a resource správy.
* Azure CLI 2.0 (az.py) - nové generace rozhraní příkazového řádku pro model nasazení prostředků správy popsané v tomto článku.

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].
* [Rozhraní příkazového řádku Azure 2.0][lnk-CLI-install].

## <a name="sign-in-and-set-your-azure-account"></a>Přihlaste se a nastavit váš účet Azure

Přihlaste se ke svému účtu Azure a vyberte své předplatné.

1. Na příkazovém řádku spusťte [příkaz pro přihlášení][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Postupujte podle pokynů pro ověření pomocí kódu a přihlaste se ke svému účtu Azure ve webovém prohlížeči.

2. Pokud máte více předplatných Azure, přihlášením k Azure získáte přístup ke všem účtům Azure přidruženým k vašim přihlašovacím údajům. Pomocí následujícího [příkazu zobrazte výpis účtů Azure][lnk-az-account-command], které můžete použít:
    
    ```azurecli
    az account list 
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů pro vytvoření centra IoT. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="create-an-iot-hub"></a>Vytvoření služby IoT Hub

Pomocí rozhraní příkazového řádku Azure k vytvoření skupiny prostředků a poté přidejte služby IoT hub.

1. Když vytvoříte Centrum IoT, musíte ji vytvořit ve skupině prostředků. Použijte existující skupinu prostředků, nebo spusťte následující [příkaz pro vytvoření skupiny prostředků][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > Předchozí příklad vytvoří skupinu prostředků v umístění USA – západ. Seznam dostupných umístění můžete zobrazit spuštěním příkazu `az account list-locations -o table`.
    >
    >

2. Spusťte následující [příkaz pro vytvoření služby IoT hub] [ lnk-az-iot-command] ve vaší skupině prostředků pomocí globálně jedinečného názvu pro službu IoT hub:
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


> [!NOTE]
> Předchozí příkaz vytvoří služby IoT hub S1 cenovou úroveň, pro kterou se účtují. Další informace najdete v tématu [ceny služby Azure IoT Hub][lnk-iot-pricing].
>

## <a name="remove-an-iot-hub"></a>Odeberte služby IoT Hub

Můžete použít rozhraní příkazového řádku Azure pro [odstranit prostředek jednotlivých][lnk-az-resource-command], například služby IoT hub, nebo odstranit skupinu prostředků a všechny její prostředky, včetně všech centra IoT.

Pokud chcete odstranit centrum IoT, spusťte následující příkaz:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte následující příkaz:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Další postup
Další informace o vývoji pro Centrum IoT, naleznete v následujících článcích:

* [Příručka vývojáře pro službu IoT Hub][lnk-devguide]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Použití portálu Azure ke správě služby IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
