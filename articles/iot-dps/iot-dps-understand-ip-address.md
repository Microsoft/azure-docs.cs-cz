---
title: Porozumění IP adrese instance služby IoT Device Provisioning (DPS) | Microsoft Docs
description: Naučte se, jak zadat dotaz na adresu služby IoT Device Provisioning (DPS) a její vlastnosti. IP adresa vaší instance DPS se může změnit během určitých scénářů, jako je například zotavení po havárii nebo regionální převzetí služeb při selhání.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "79284925"
---
# <a name="iot-hub-dps-ip-addresses"></a>IP adresy IoT Hub DPS

Předpony IP adres pro veřejné koncové body IoT Hub Device Provisioning Service (DPS) se pravidelně publikují v rámci [značky služby](../virtual-network/service-tags-overview.md) _AzureIoTHub_ . Tyto předpony IP adres můžete použít k řízení připojení mezi instancí IoT DPS a zařízeními nebo síťovými prostředky, aby bylo možné implementovat celou řadu cílů izolace sítě:

| Cíl | Přístup |
|------|----------|
| Zajistěte, aby zařízení a služby komunikovaly jenom s IoT Hubmi koncovými body DPS | Pomocí značky služby _AzureIoTHub_ zjistíte instance IoT Hub DPS. V nastavení brány firewall pro vaše zařízení a služby nakonfigurujte pravidla povolení pro tyto předpony IP adres. Nakonfigurujte pravidla pro vyřazení provozu do jiných cílových IP adres, se kterými nechcete komunikovat pomocí zařízení nebo služeb. |
| Zajistěte, aby koncový bod IoT Hub DPS přijímal připojení jenom z vašich zařízení a síťových prostředků. | Pomocí [funkce filtru IP adres](iot-dps-ip-filtering.md) IoT DPS můžete vytvořit pravidla filtru pro rozhraní API služby Device a DPS. Tato pravidla filtru se dají použít k povolení připojení jenom z vašich zařízení a IP adres síťových prostředků (viz část [omezení](#limitations-and-workarounds) ). | 




## <a name="best-practices"></a>Osvědčené postupy

* Při přidávání pravidel povolení v konfiguraci brány firewall zařízení je nejlepší poskytnout konkrétní [porty používané použitelnými protokoly](../iot-hub/iot-hub-devguide-protocols.md#port-numbers).

* Předpony IP adres instancí IoT DPS se mohou změnit. Tyto změny jsou pravidelně publikovány prostřednictvím značek služeb, než začne platit. Je proto důležité, abyste vytvořili procesy pro pravidelné načítání a používání nejnovějších značek služeb. Tento proces může být automatizovaný prostřednictvím [rozhraní API pro zjišťování značek služeb](../virtual-network/service-tags-overview.md#service-tags-on-premises). Rozhraní API pro zjišťování značek služeb je stále ve verzi Preview a v některých případech nemusí mít úplný seznam značek a IP adres. Až bude rozhraní API pro zjišťování všeobecně dostupné, zvažte použití [značek služby ve formátu JSON ke stažení](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Použijte *AzureIoTHub. [ název oblasti]* : Značka pro identifikaci předpon IP adres používaných koncovými body DPS v konkrétní oblasti. Aby se mohlo přihlédnout k zotavení po havárii datacentra nebo [regionálnímu převzetí služeb při selhání](../iot-hub/iot-hub-ha-dr.md), zajistěte, aby bylo povolené i připojení k předponám IP oblastí geografické dvojice instance DPS.

* Nastavení pravidel brány firewall pro instanci DPS může blokovat připojení potřebné ke spouštění příkazů Azure CLI a PowerShellu. Abyste se vyhnuli těmto problémům s připojením, můžete přidat pravidla povolení pro předpony IP adres vašich klientů, abyste mohli znovu povolit klientům CLI nebo PowerShellu komunikovat s vaší instancí DPS.  


## <a name="limitations-and-workarounds"></a>Omezení a alternativní řešení

* Funkce filtru protokolu IP DPS má omezení 100 pravidel. Tento limit je možné vydávat prostřednictvím požadavků Azure Customer Support. 

* Nakonfigurovaná [pravidla filtrování IP adres](iot-dps-ip-filtering.md) se aplikují jenom na koncové body DPS a ne na odkazovaných koncových bodech IoT Hub. Filtrování IP adres pro propojená centra IoT se musí nakonfigurovat samostatně. Další informace najdete v tématu [IoT Hub pravidla filtrování IP adres](../iot-hub/iot-hub-ip-filtering.md).

## <a name="support-for-ipv6"></a>Podpora protokolu IPv6 

Protokol IPv6 se v současnosti v IoT Hub nebo DPS nepodporuje.

## <a name="next-steps"></a>Další kroky

Další informace o konfiguracích IP adres s DPS najdete v těchto tématech:

* [Konfigurace filtrování IP](iot-dps-ip-filtering.md)
