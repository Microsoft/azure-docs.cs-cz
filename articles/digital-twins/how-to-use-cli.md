---
title: Použití rozhraní příkazového řádku Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Podívejte se, jak začít s a používat rozhraní příkazového řádku Azure Digital zdvojené.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 118e137f06a49f2c125b1ca156877514d65af86f
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047059"
---
# <a name="use-the-azure-digital-twins-cli"></a>Použití rozhraní příkazového řádku Azure Digital Twins

Kromě správy instance digitálních vláken Azure v Azure Portal má služba Azure Digital disvlákna **rozhraní příkazového řádku (CLI)** , které můžete použít k provádění většiny hlavních akcí se službou, včetně těchto:
* Správa instance digitálních vláken Azure
* Správa modelů
* Správa digitálních vláken
* Správa dvojitých vztahů
* Konfigurace koncových bodů
* Správa [tras](concepts-route-events.md)
* Konfigurace [zabezpečení](concepts-security.md) prostřednictvím řízení přístupu na základě role (RBAC)

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>Použití (nasazení a ověření)

Kromě všeobecně spravované instance je také rozhraní příkazového řádku užitečným nástrojem pro nasazení a ověřování.
* Příkazy roviny ovládacího prvku lze použít k zajištění opakovaného nebo automatizovaného nasazení nové instance.
* Příkazy roviny dat lze použít k rychlé kontrole hodnot ve vaší instanci a k ověření, že operace byly dokončeny podle očekávání.

## <a name="get-the-extension"></a>Získat rozšíření

Příkazy digitálních vláken Azure jsou součástí [rozšíření Azure IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Úplný seznam příkazů a jejich použití můžete zobrazit jako součást Referenční dokumentace pro `az iot` sadu příkazů: [ *AZ DT* Command reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest).

Pomocí těchto kroků se můžete ujistit, že máte nejnovější verzi rozšíření. Tyto příkazy můžete spustit v [Azure Cloud Shell](../cloud-shell/overview.md) nebo v místním rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní příkazového řádku a jeho úplnou sadu příkazů prostřednictvím referenčních dokumentů:
* [*AZ DT* Command reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)