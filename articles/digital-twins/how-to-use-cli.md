---
title: Použití rozhraní příkazového řádku Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Podívejte se, jak začít s a používat rozhraní příkazového řádku Azure Digital zdvojené.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5037450d401153811899b8d769ca92af7ce4068e
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103772"
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

Sada příkazů se nazývá **AZ DT** a je součástí [rozšíření Azure IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Úplný seznam příkazů a jejich použití můžete zobrazit jako součást Referenční dokumentace pro `az iot` sadu příkazů: [ *AZ DT* Command reference](/cli/azure/dt).

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

Rozhraní příkazového řádku Azure CLI vás automaticky vyzve k instalaci tohoto rozšíření při prvním použití příkazu, který to vyžaduje.

Alternativně můžete pomocí následujícího příkazu nainstalovat rozšíření kdykoli (nebo ho aktualizovat, pokud se tak stane, že už máte starší verzi). Příkaz se dá spustit buď v [Azure Cloud Shell](../cloud-shell/overview.md) , nebo v místním rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
az extension add --upgrade -n azure-iot
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní příkazového řádku a jeho úplnou sadu příkazů prostřednictvím referenčních dokumentů:
* [*AZ DT* Command reference](/cli/azure/dt)