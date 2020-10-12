---
title: Správa přístupu k veřejné síti pro službu Azure IoT Hub
description: Dokumentace k zakázání a povolení přístupu k veřejné síti pro IoT Hub
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85937520"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Správa přístupu k veřejné síti ve službě IoT Hub

Pokud chcete omezit přístup jenom na [soukromý koncový bod pro službu IoT Hub ve vaší virtuální](virtual-network-support.md)síti, zakažte přístup k veřejné síti. K tomu použijte Azure Portal nebo `publicNetworkAccess` rozhraní API. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Vypnout přístup k veřejné síti pomocí Azure Portal

1. Navštívit [Azure Portal](https://portal.azure.com)
2. Přejděte do svého centra IoT.
3. V nabídce na levé straně vyberte **síť** .
4. V části "povolení přístupu k veřejné síti k" vyberte **zakázáno** .
5. Vyberte **Uložit**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Obrázek znázorňující Azure Portal, kde vypnout přístup k veřejné síti" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Pokud chcete zapnout přístup k veřejné síti, vyberte **povoleno**a pak **Uložit**.

## <a name="ip-filter"></a>Filtr IP adres 

Pokud je přístup k veřejné síti zakázán, všechna pravidla [filtru IP](iot-hub-ip-filtering.md) jsou ignorována. Důvodem je to, že všechny IP adresy z veřejného Internetu jsou blokované. Pokud chcete použít filtr IP adres, použijte možnost **vybrané rozsahy IP adres** .

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Oprava chyb s integrovaným koncovým bodem centra událostí

Došlo k chybě u IoT Hub, kde je [integrovaný koncový bod kompatibilního centra událostí](iot-hub-devguide-messages-read-builtin.md) i nadále přístupný prostřednictvím veřejného Internetu, když je přístup k síti k IoT Hub zakázán. Další informace o této chybě získáte v tématu [Zakázání přístupu k veřejné síti pro IoT Hub zakáže přístup k integrovanému koncovému bodu centra událostí](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).