---
title: Použití rozhraní příkazového řádku Azure Digital zdvojené
titleSuffix: Azure Digital Twins
description: Podívejte se, jak začít s a používat rozhraní příkazového řádku Azure Digital zdvojené.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 53b20ded8e4b4a003beff1ef8489ecd9ff3451ac
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612800"
---
# <a name="use-the-azure-digital-twins-cli"></a>Použití rozhraní příkazového řádku Azure Digital zdvojené

Kromě správy instance digitálních vláken Azure v Azure Portal má služba Azure Digital disvlákna **rozhraní příkazového řádku (CLI)** , které můžete použít k provádění většiny hlavních akcí se službou, včetně těchto:
* Správa instance digitálních vláken Azure
* Správa modelů
* Správa digitálních vláken
* Správa dvojitých vztahů
* Konfigurace koncových bodů
* Správa [tras](concepts-route-events.md)
* Konfigurace [zabezpečení](concepts-security.md) prostřednictvím řízení přístupu na základě role (RBAC)

Příkazy digitálních vláken Azure jsou součástí [rozšíření Azure IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Referenční dokumentaci pro tyto příkazy můžete zobrazit jako součást `az iot` sady příkazů: [AZ DT](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest).

## <a name="deploy-and-validate"></a>Nasazení a ověření

Kromě všeobecně spravované instance je také rozhraní příkazového řádku užitečným nástrojem pro nasazení a ověřování.
* Příkazy roviny ovládacího prvku lze použít k zajištění opakovaného nebo automatizovaného nasazení nové instance.
* Příkazy roviny dat lze použít k rychlé kontrole hodnot ve vaší instanci a k ověření, že operace byly dokončeny podle očekávání.

## <a name="next-steps"></a>Další kroky

Alternativu k příkazům rozhraní příkazového řádku najdete v tématu Správa instance digitálních vláken Azure pomocí rozhraní API a sad SDK:
* [Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure](how-to-use-apis-sdks.md)
