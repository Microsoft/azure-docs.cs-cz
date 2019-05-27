---
title: Spravovat z příkazového řádku Azure IoT Central | Dokumentace Microsoftu
description: Spravujte z příkazového řádku Azure IoT Central.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/07/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 9e5d842cece316bc9c53e1e8583f40a0f222b91d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66151961"
---
# <a name="manage-iot-central-from-azure-cli"></a>Spravovat z příkazového řádku Azure IoT Central

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Místo vytváření a správu aplikací IoT Central z IoT Central [Správce aplikací](https://aka.ms/iotcentral) stránku, použijte [rozhraní příkazového řádku Azure](/cli/azure/) ke správě vašich aplikací.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete na svém místním počítači spusťte rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli). Když spustíte Azure CLI místně, použijte **az login** příkaz pro přihlášení k Azure, než se pokusíte příkazy v tomto článku.

## <a name="create-an-application"></a>Vytvoření aplikace

Použití [az iotcentral aplikace vytvoří](/cli/azure/iotcentral/app#az-iotcentral-app-create) příkaz k vytvoření aplikace IoT Central ve vašem předplatném Azure. Příklad:

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
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Tyto příkazy nejdřív vytvořte skupinu prostředků na východě USA oblast pro aplikaci. Následující tabulka popisuje parametry používané s **az iotcentral aplikace vytvoří** příkaz:

| Parametr         | Popis |
| ----------------- | ----------- |
| resource-group    | Skupina prostředků, která obsahuje aplikaci. Tato skupina prostředků musí existovat ve vašem předplatném. |
| location          | Tento příkaz používá ve výchozím umístění, ze skupiny prostředků. V současné době můžete vytvořit aplikace IoT Central **USA – východ**, **USA – západ**, **Severní Evropa**, nebo **západní Evropa** oblastech. |
| name              | Název aplikace na webu Azure Portal. |
| subdomain         | Subdoména v adrese URL aplikace. V tomto příkladu je adresa URL aplikace https://mysubdomain.azureiotcentral.com. |
| SKU               | V současné době je jediná hodnota **S1** (úrovně standard). Zobrazit [ceny Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| šablona          | Šablony aplikace používat. Další informace najdete v tématu v následující tabulce: |
| Zobrazovaný název      | Název aplikace, jak se zobrazuje v uživatelském rozhraní. |

**Šablony aplikací**

| Název šablony            | Popis |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Vytvoří prázdnou aplikaci, kterou můžete naplnit vlastními šablonami zařízení a zařízeními. |
| iotc-demo@1.0.0          | Vytvoří aplikaci, která zahrnuje již vytvořenou šablonu zařízení pro chladící prodejní automat. Pomocí této šablony můžete začít zkoumat Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Vytvoří aplikaci s připravenými šablonami zařízení pro připojení zařízení MXChip nebo Raspberry Pi. Tuto šablonu použijte, pokud jste vývojář zařízení experimentovat s některou z těchto zařízení. |

## <a name="view-your-applications"></a>Zobrazení vašich aplikací

Použití [seznamu aplikací iotcentral az](/cli/azure/iotcentral/app#az-iotcentral-app-list) příkaz pro zobrazení seznamu aplikací IoT Central a zobrazení metadat.

## <a name="modify-an-application"></a>Upravit aplikace

Použití [aktualizaci aplikace iotcentral az](/cli/azure/iotcentral/app#az-iotcentral-app-update) příkazu pro aktualizaci metadat aplikace IoT Central. Chcete-li například změnit zobrazovaný název aplikace:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Odebrání aplikace

Použití [az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) příkaz pro odstranění aplikace IoT Central. Příklad:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak spravovat Azure IoT Central aplikace z Azure CLI, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Spravovat aplikaci](howto-administer.md)
