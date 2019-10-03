---
title: Princip IP adresy vašeho centra IoT Hub | Microsoft Docs
description: Zjistěte, jak zadat dotaz na IP adresu služby IoT Hub a její vlastnosti. IP adresa vašeho centra IoT se může změnit během určitých scénářů, jako je například zotavení po havárii nebo regionální převzetí služeb při selhání.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 7af40404550fb78af891563d8256f23620781b24
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841538"
---
# <a name="understanding-the-ip-address-of-your-iot-hub"></a>Porozumění IP adrese vašeho centra IoT Hub

IP adresa vašeho centra IoT je veřejný koncový bod s vyrovnáváním zatížení pro vaše centrum, nikoli vyhrazenou IP adresu. Adresa je určena rozsahem síťových adres oblasti Azure, ve které je nasazená. Tato IP adresa se může změnit bez předchozího upozornění. Aktualizace sítě Datacenter, zotavení po havárii Datacenter nebo regionální převzetí služeb při selhání v rámci služby IoT Hub může změnit vaši IP adresu služby IoT Hub. Další informace o Azure IoT Hub regionálním převzetí služeb při selhání a dostupnosti najdete v tématu [IoT Hub vysoké dostupnosti a zotavení po havárii](iot-hub-ha-dr.md) .

IP adresa vašeho centra IoT se po převzetí služeb při selhání do jiné oblasti změní. Tuto funkci můžete otestovat pomocí kurzu [provedení ručního převzetí služeb IoT Hub](tutorial-manual-failover.md).

## <a name="discover-your-iot-hub-ip-address"></a>Zjištění IP adresy služby IoT Hub

Vaši IP adresu IoT Hub můžete zjistit pomocí zpětného vyhledávání DNS na CNAME ([*IoT-Hub-Name*]. Azure-Devices.NET). Pomocí nástroje **nslookup** můžete ověřit IP adresu instance služby IoT Hub:

```cmd/sh
nslookup {YourIoTHubName}.azure-devices.net
```

Tato IP adresa se může změnit bez předchozího upozornění. Ve scénáři převzetí služeb při selhání nebo zotavení po havárii budete muset v sekundární oblasti dotazovat IP adresu služby IoT Hub.

## <a name="outbound-firewall-rules-for-iot-hub"></a>Odchozí pravidla brány firewall pro IoT Hub

Pokuste se vytvořit pravidla a filtrování brány firewall na základě názvu hostitele nebo domény hostitele centra IoT. Pokud povolíte odchozí přenosy jenom na konkrétní adresy, můžete pravidelně dotazovat IP adresy služby IoT Hub a aktualizovat pravidla brány firewall.

## <a name="support-for-ipv6"></a>Podpora protokolu IPv6 

Protokol IPv6 se v této IoT Hub v tuto chvíli nepodporuje.