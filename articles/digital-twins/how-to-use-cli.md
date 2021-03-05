---
title: Použití rozhraní příkazového řádku Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Podívejte se, jak začít s a používat rozhraní příkazového řádku Azure Digital zdvojené.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a24b8b18dd109f1d8ed5acaa7de55ce5a3cc1eb9
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201103"
---
# <a name="use-the-azure-digital-twins-cli"></a>Použití rozhraní příkazového řádku Azure Digital Twins

Kromě správy instance digitálních vláken Azure v Azure Portal má digitální vlákna Azure **sadu příkazů pro [Azure CLI](/cli/azure/what-is-azure-cli)** , kterou můžete použít k provádění většiny hlavních akcí se službou, včetně těchto:
* Správa instance digitálních vláken Azure
* Správa modelů
* Správa digitálních vláken
* Správa dvojitých vztahů
* Konfigurace koncových bodů
* Správa [tras](concepts-route-events.md)
* Konfigurace [zabezpečení](concepts-security.md) prostřednictvím řízení přístupu na základě role Azure (Azure RBAC)

Sada příkazů se nazývá **AZ DT** a je součástí [rozšíření Azure IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Úplný seznam příkazů a jejich použití můžete zobrazit jako součást Referenční dokumentace pro `az iot` sadu příkazů: [ *AZ DT* Command reference](/cli/azure/ext/azure-iot/dt).

## <a name="uses-deploy-and-validate"></a>Použití (nasazení a ověření)

Kromě všeobecně spravované instance je také rozhraní příkazového řádku užitečným nástrojem pro nasazení a ověřování.
* Příkazy roviny ovládacího prvku lze použít k zajištění opakovaného nebo automatizovaného nasazení nové instance.
* Příkazy roviny dat lze použít k rychlé kontrole hodnot ve vaší instanci a k ověření, že operace byly dokončeny podle očekávání.

## <a name="get-the-command-set"></a>Získat sadu příkazů

Příkazy digitálních vláken Azure jsou součástí [rozšíření Azure IoT pro Azure CLI (Azure-IoT)](https://github.com/Azure/azure-iot-cli-extension), takže pomocí těchto kroků se ujistěte, že máte nejnovější `azure-iot` rozšíření s příkazy **AZ DT** .

### <a name="cli-version-requirements"></a>Požadavky na verzi rozhraní příkazového řádku

Pokud používáte Azure CLI s PowerShellem, balíček rozšíření vyžaduje, aby vaše verze Azure CLI byla **2.3.1** nebo vyšší.

Verzi rozhraní příkazového řádku Azure CLI můžete zjistit pomocí tohoto příkazu rozhraní příkazového řádku:
```azurecli
az --version
```

Pokyny k instalaci nebo aktualizaci Azure CLI na novější verzi najdete v tématu Instalace rozhraní příkazového [*řádku Azure CLI*](/cli/azure/install-azure-cli).

### <a name="get-the-extension"></a>Získat rozšíření

Pomocí těchto kroků se můžete ujistit, že máte nejnovější verzi `azure-iot` rozšíření. Tyto příkazy můžete spustit v [Azure Cloud Shell](../cloud-shell/overview.md) nebo v místním rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní příkazového řádku a jeho úplnou sadu příkazů prostřednictvím referenčních dokumentů:
* [*AZ DT* Command reference](/cli/azure/ext/azure-iot/dt)