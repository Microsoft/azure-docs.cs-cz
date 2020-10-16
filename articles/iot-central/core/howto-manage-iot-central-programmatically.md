---
title: Spravovat IoT Central programově | Microsoft Docs
description: Tento článek popisuje, jak vytvořit a spravovat IoT Central programově. Můžete zobrazit, upravit a odebrat aplikaci pomocí více jazykových sad SDK, jako je JavaScript, Python, C#, Ruby a přejít.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/19/2020
ms.topic: how-to
ms.openlocfilehash: 773793c7681890098fea1a37cc5b9912c0ecb75c
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122990"
---
# <a name="manage-iot-central-programmatically"></a>Správa IoT Central programově

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Místo vytváření a správy aplikací IoT Central na webu [azure IoT Central Správce aplikací](https://aka.ms/iotcentral) můžete spravovat aplikace programově pomocí sad Azure SDK. Mezi podporované jazyky patří JavaScript, Python, C#, Ruby a přejít.

## <a name="install-the-sdk"></a>Instalace sady SDK

V následující tabulce jsou uvedeny příkazy úložišť sady SDK a instalační příkazy balíčku:

| Úložiště sady SDK | Instalace balíčku |
| -------------- | ------------ |
| [Azure IotCentralClient SDK pro JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/iotcentral/arm-iotcentral) | `npm install @azure/arm-iotcentral` |
| [Sada SDK Microsoft Azure pro Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/iothub/azure-mgmt-iotcentral/azure/mgmt/iotcentral) | `pip install azure-mgmt-iotcentral` |
| [Sada Azure SDK pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/iotcentral/Microsoft.Azure.Management.IotCentral) | `dotnet add package Microsoft.Azure.Management.IotCentral` |
| [Sada SDK pro Microsoft Azure pro Ruby – Správa prostředků (Preview)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_iot_central/lib/2018-09-01/generated/azure_mgmt_iot_central) | `gem install azure_mgmt_iot_central` |
| [Azure SDK pro Javu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/iotcentral) | [Balíček Maven](https://search.maven.org/search?q=a:azure-mgmt-iotcentral) |
| [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/iotcentral/mgmt/2018-09-01/iotcentral) | [Verze balíčků](https://github.com/Azure/azure-sdk-for-go/releases) |

## <a name="samples"></a>ukázky

Úložiště [ukázek sady azure IoT Central ARM SDK](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) obsahuje ukázky kódu pro několik programovacích jazyků, které ukazují, jak vytvořit, aktualizovat, vypsat a odstranit aplikace Azure IoT Central.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak programově spravovat aplikace IoT Central Azure, je navržený další krok, kde najdete další informace o službě [Azure Resource Manager](../../azure-resource-manager/management/overview.md) .