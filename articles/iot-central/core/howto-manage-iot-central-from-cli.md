---
title: Správa IoT Central z Azure CLI | Microsoft Docs
description: Tento článek popisuje, jak vytvořit a spravovat IoT Central aplikaci pomocí rozhraní příkazového řádku. Aplikaci můžete zobrazit, upravit a odebrat pomocí rozhraní příkazového řádku.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 80f91849a35bcb94b934941a204d4503ddda376b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278858"
---
# <a name="manage-iot-central-from-azure-cli"></a>Správa IoT Central z Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Místo vytváření a správy aplikací IoT Central na webu [azure IoT Central Správce aplikací](https://aka.ms/iotcentral) můžete ke správě aplikací použít [Azure CLI](/cli/azure/) .

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud raději spustíte Azure CLI na místním počítači, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli). Když Azure CLI spouštíte místně, přihlaste se k Azure pomocí příkazu **AZ Login** , abyste se mohli pokusit použít příkazy v tomto článku.

## <a name="create-an-application"></a>Vytvoření aplikace

Pomocí příkazu [AZ iotcentral App Create](/cli/azure/iotcentral/app#az-iotcentral-app-create) vytvořte aplikaci IoT Central ve vašem předplatném Azure. Příklad:

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

Tyto příkazy nejprve vytvoří skupinu prostředků v umístění východní USA pro danou aplikaci. Následující tabulka popisuje parametry používané pomocí příkazu **AZ iotcentral App Create** :

| Parametr         | Popis |
| ----------------- | ----------- |
| resource-group    | Skupina prostředků, která obsahuje aplikaci. Tato skupina prostředků už musí existovat ve vašem předplatném. |
| location          | Ve výchozím nastavení tento příkaz používá umístění ze skupiny prostředků. V současné době můžete vytvořit aplikaci IoT Central v **USA**, **Austrálii**, **Asie a Tichomoří**nebo v **Evropě** . |
| jméno              | Název aplikace v Azure Portal. |
| subdomény         | Subdoména v adrese URL aplikace V příkladu je adresa URL aplikace https://mysubdomain.azureiotcentral.com. |
| skladové               | V současné době jediná hodnota je **S1** (úroveň Standard). Viz [ceny za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| Šablony          | Šablona aplikace, která se má použít Další informace najdete v následující tabulce: |
| zobrazované jméno      | Název aplikace, jak se zobrazuje v uživatelském rozhraní. |

**Šablony aplikací**

| Název šablony            | Popis |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Vytvoří prázdnou aplikaci, kterou můžete naplnit vlastními šablonami zařízení a zařízeními. |
| iotc-demo@1.0.0          | Vytvoří aplikaci, která zahrnuje již vytvořenou šablonu zařízení pro chladící prodejní automat. Pomocí této šablony můžete začít zkoumat Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Vytvoří aplikaci s připravenými šablonami zařízení pro připojení zařízení MXChip nebo Raspberry Pi. Tuto šablonu použijte, pokud jste vývojář zařízení experimentovat s některým z těchto zařízení. |

> [!NOTE]
> Šablona **aplikace Preview** je aktuálně dostupná jenom v oblastech **Evropa** a **USA** .

## <a name="view-your-applications"></a>Zobrazení vašich aplikací

Pomocí příkazu [AZ iotcentral App list](/cli/azure/iotcentral/app#az-iotcentral-app-list) můžete zobrazit seznam aplikací IoT Central a zobrazovat metadata.

## <a name="modify-an-application"></a>Úprava aplikace

Pomocí příkazu [AZ iotcentral App Update](/cli/azure/iotcentral/app#az-iotcentral-app-update) aktualizujte metadata aplikace IoT Central. Například pro změnu zobrazovaného názvu vaší aplikace:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Odebrání aplikace

Pomocí příkazu [AZ iotcentral App Delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) odstraňte aplikaci IoT Central. Příklad:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat aplikace Azure IoT Central z Azure CLI, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Správa aplikace](howto-administer.md)
