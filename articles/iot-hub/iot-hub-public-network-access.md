---
title: Správa přístupu k veřejné síti pro službu Azure IoT Hub
description: Dokumentace k zakázání a povolení přístupu k veřejné síti pro IoT Hub
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: b225ad7e12a95b4dcbb8656f54f8a9e9562b2ee8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604691"
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

Pokud chcete zapnout přístup k veřejné síti, vyberte **všechny sítě** a pak **Uložit**.

## <a name="accessing-the-iot-hub-after-disabling-public-network-access"></a>Přístup k IoT Hub po zakázání přístupu k veřejné síti

Po zakázání přístupu k veřejné síti je IoT Hub k dispozici jenom prostřednictvím privátního [koncového bodu virtuální sítě pomocí privátního odkazu Azure](virtual-network-support.md).

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>IoT Hub koncový bod, IP adresu a porty po zakázání přístupu k veřejné síti

IoT Hub je víceklientské platforma jako služba (PaaS), takže různí zákazníci sdílejí stejný fond výpočetních, síťových a hardwarových prostředků úložiště. Názvy hostitelů IoT Hub se mapují na veřejný koncový bod s veřejně směrovatelný IP adresou přes Internet. Různí zákazníci sdílí tento IoT Hub veřejný koncový bod a zařízení IoT v různých sítích a místních sítích mají přístup k nim. 

Zakázání přístupu k veřejné síti se vynutilo u konkrétního prostředku IoT Hub a zajišťuje tak izolaci. Aby se služba udržovala aktivní pro jiné zákaznické prostředky pomocí veřejné cesty, zůstane jejich veřejný koncový bod přeložitelný, IP adresy zjistitelné a porty zůstanou otevřené. Nejedná se o příčinu obav, protože společnost Microsoft integruje více vrstev zabezpečení, aby zajistila úplnou izolaci mezi klienty. Další informace najdete v tématu věnovaném [izolaci ve veřejném cloudu Azure](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

## <a name="ip-filter"></a>Filtr IP adres 

Pokud je přístup k veřejné síti zakázán, všechna pravidla [filtru IP](iot-hub-ip-filtering.md) jsou ignorována. Důvodem je to, že všechny IP adresy z veřejného Internetu jsou blokované. Pokud chcete použít filtr IP adres, použijte možnost **vybrané rozsahy IP adres** .

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Oprava chyb s integrovaným koncovým bodem centra událostí

Došlo k chybě u IoT Hub, kde je [integrovaný koncový bod kompatibilního centra událostí](iot-hub-devguide-messages-read-builtin.md) i nadále přístupný prostřednictvím veřejného Internetu, když je přístup k síti k IoT Hub zakázán. Další informace o této chybě získáte v tématu [Zakázání přístupu k veřejné síti pro IoT Hub zakáže přístup k integrovanému koncovému bodu centra událostí](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).
