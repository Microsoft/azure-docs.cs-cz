---
title: Správa ioT central z Azure CLI | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak vytvořit a spravovat aplikaci IoT Central pomocí cli. Aplikaci můžete zobrazit, upravit a odebrat pomocí funkce vpořádku.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: df24a2dc6e9bd058a2f8b1355b8760653ed3128a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365519"
---
# <a name="manage-iot-central-from-azure-cli"></a>Správa IoT Central z Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Místo vytváření a správy aplikací IoT Central na webu [správce aplikací Azure IoT Central](https://aka.ms/iotcentral) můžete ke správě aplikací použít Azure [CLI.](/cli/azure/)

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud dáváte přednost spuštění azure cli na místním počítači, najdete [v tématu instalace příkazového příkazového příkazu k Azure](/cli/azure/install-azure-cli). Při spuštění Azure CLI místně, použijte příkaz **az přihlášení** k přihlášení do Azure před pokusem příkazy v tomto článku.

> [!TIP]
> Pokud potřebujete spustit příkazy příkazu příkazu příkazu příkazu příkazu v jiném předplatném Azure, přečtěte si informace [o změně aktivního předplatného](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription).

## <a name="install-the-extension"></a>Instalace rozšíření

Příkazy v tomto článku jsou součástí rozšíření **azure-iot** CLI. Chcete-li nainstalovat rozšíření, spusťte následující příkaz:

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>Vytvoření aplikace

Pomocí příkazu [az iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create) a ioT Central application in your Azure subscription. Například:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview@1.0.0" \
  --display-name "My Custom Display Name"
```

Tyto příkazy nejprve vytvořit skupinu prostředků v oblasti východní USA pro aplikaci. Následující tabulka popisuje parametry použité s příkazem **az iotcentral app create:**

| Parametr         | Popis |
| ----------------- | ----------- |
| resource-group    | Skupina prostředků, která obsahuje aplikaci. Tato skupina prostředků musí již existovat ve vašem předplatném. |
| location          | Ve výchozím nastavení tento příkaz používá umístění ze skupiny prostředků. V současné době můžete vytvořit aplikaci IoT Central v **oblastech Zeměpisné**oblasti Austrálie , Asie **a Tichomoří**, **Evropa**, **Spojené státy**, **Velká Británie**a **Japonsko.** |
| jméno              | Název aplikace na webu Azure Portal. |
| Subdoménu         | Subdoména v adrese URL aplikace. V příkladu je `https://mysubdomain.azureiotcentral.com`adresa URL aplikace . |
| Sku               | V současné době můžete použít **buď ST1** nebo **ST2**. Viz [Azure IoT Central pricing](https://azure.microsoft.com/pricing/details/iot-central/). |
| šablona          | Šablona aplikace, která má být používána. Další informace najdete v následující tabulce. |
| zobrazovaný název      | Název aplikace zobrazený v uživatelském uživatelském nastavení. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Zobrazení vašich aplikací

Pomocí příkazu [az iotcentral app list](/cli/azure/iotcentral/app#az-iotcentral-app-list) můžete uvést aplikace IoT Central a zobrazit metadata.

## <a name="modify-an-application"></a>Úprava aplikace

Pomocí příkazu [aktualizace aplikace az iotcentral](/cli/azure/iotcentral/app#az-iotcentral-app-update) aktualizujte metadata aplikace IoT Central. Chcete-li například změnit zobrazovaný název aplikace:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Odebrání aplikace

Pomocí příkazu [delete aplikace az iotcentral](/cli/azure/iotcentral/app#az-iotcentral-app-delete) odstraňte aplikaci IoT Central. Například:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili spravovat aplikace Azure IoT Central z Azure CLI, tady je další krok:

> [!div class="nextstepaction"]
> [Správa vaší aplikace](howto-administer.md)
