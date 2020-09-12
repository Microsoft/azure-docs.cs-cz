---
title: Použití rozhraní příkazového řádku Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Podívejte se, jak začít s a používat rozhraní příkazového řádku Azure Digital zdvojené.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2c642b2441d1f30c31e707a237732e028f548ac5
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298177"
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

Příkazy digitálních vláken Azure jsou součástí [rozšíření Azure IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Referenční dokumentaci pro tyto příkazy můžete zobrazit jako součást `az iot` sady příkazů: [AZ DT](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest).

Pomocí těchto kroků se můžete ujistit, že máte nejnovější verzi rozšíření. Tyto příkazy můžete spustit v [Azure Cloud Shell](../cloud-shell/overview.md) nebo v místním rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Další kroky

Alternativu k příkazům rozhraní příkazového řádku najdete v tématu Správa instance digitálních vláken Azure pomocí rozhraní API a sad SDK:
* [*Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure*](how-to-use-apis-sdks.md)
