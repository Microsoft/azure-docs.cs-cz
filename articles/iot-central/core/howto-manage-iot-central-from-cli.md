---
title: Správa IoT Central z Azure CLI | Microsoft Docs
description: Tento článek popisuje, jak vytvořit a spravovat IoT Central aplikaci pomocí rozhraní příkazového řádku. Aplikaci můžete zobrazit, upravit a odebrat pomocí rozhraní příkazového řádku.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: 6496e944d30724fe9e8db7168f9c9cb1552dcd1b
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2021
ms.locfileid: "98203320"
---
# <a name="manage-iot-central-from-azure-cli"></a>Správa IoT Central z Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Místo vytváření a správy aplikací IoT Central na webu [azure IoT Central Správce aplikací](https://aka.ms/iotcentral) můžete ke správě aplikací použít [Azure CLI](/cli/azure/) .

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Pokud potřebujete spustit příkazy rozhraní příkazového řádku v jiném předplatném Azure, přečtěte si téma [Změna aktivního předplatného](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription&preserve-view=true).

## <a name="create-an-application"></a>Vytvoření aplikace

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Pomocí příkazu [AZ IoT Central App Create](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-create&preserve-view=true) vytvořte aplikaci IoT Central ve vašem předplatném Azure. Příklad:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Tyto příkazy nejprve vytvoří skupinu prostředků v oblasti východní USA pro aplikaci. Následující tabulka popisuje parametry používané pomocí příkazu **AZ IoT Central App Create** :

| Parametr         | Popis |
| ----------------- | ----------- |
| resource-group    | Skupina prostředků, která obsahuje aplikaci. Tato skupina prostředků už musí existovat ve vašem předplatném. |
| location          | Ve výchozím nastavení tento příkaz používá umístění ze skupiny prostředků. V současné době můžete vytvořit aplikaci IoT Central v oblastech **Austrálie**, **Asie a Tichomoří**, **Evropa**, **USA**, **Spojené království** a **Japonsko** . |
| name              | Název aplikace v Azure Portal. |
| subdomény         | Subdoména v adrese URL aplikace V tomto příkladu je adresa URL aplikace `https://mysubdomain.azureiotcentral.com` . |
| skladové               | V současné době můžete použít buď **ST1** nebo **ST2**. Viz [ceny za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| šablona          | Šablona aplikace, která se má použít Další informace najdete v následující tabulce. |
| zobrazované jméno      | Název aplikace, jak se zobrazuje v uživatelském rozhraní. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Zobrazení vašich aplikací

Pomocí příkazu [AZ IoT Central App list](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-list&preserve-view=true) můžete zobrazit seznam aplikací IoT Central a zobrazovat metadata.

## <a name="modify-an-application"></a>Úprava aplikace

Pomocí příkazu [AZ IoT Central App Update](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-update&preserve-view=true) aktualizujte metadata aplikace IoT Central. Například pro změnu zobrazovaného názvu vaší aplikace:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Odebrání aplikace

Pomocí příkazu [AZ IoT Central App Delete](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-delete&preserve-view=true) odstraňte aplikaci IoT Central. Příklad:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat aplikace Azure IoT Central z Azure CLI, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Správa vaší aplikace](howto-administer.md)
