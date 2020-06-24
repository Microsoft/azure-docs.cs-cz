---
title: Použití rozhraní příkazového řádku Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Podívejte se, jak začít s a používat rozhraní příkazového řádku Azure Digital zdvojené.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 595ab06e618fd977b75a15d3acb9a9a339b6edce
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84725289"
---
# <a name="use-the-azure-digital-twins-cli"></a>Použití rozhraní příkazového řádku Azure Digital Twins

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

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
