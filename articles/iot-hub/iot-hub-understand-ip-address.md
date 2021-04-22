---
title: Princip IP adresy vašeho centra IoT Hub | Microsoft Docs
description: Zjistěte, jak zadat dotaz na IP adresu služby IoT Hub a její vlastnosti. IP adresa vašeho centra IoT se může změnit během určitých scénářů, jako je například zotavení po havárii nebo regionální převzetí služeb při selhání.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 7d807a15d358bd621baedbff253f0c731e43ed26
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874166"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub IP adresy

Předpony IP adres IoT Hub veřejných koncových bodů se pravidelně publikují v rámci [značky služby](../virtual-network/service-tags-overview.md) _AzureIoTHub_ .

> [!NOTE]
> Pro zařízení nasazená v místních sítích Azure IoT Hub podporuje integraci připojení virtuální sítě s privátními koncovými body. Další informace najdete v tématu [Podpora pro virtuální síť IoT Hub](./virtual-network-support.md) .


Tyto předpony IP adres můžete použít k řízení připojení mezi IoT Hub a zařízeními nebo síťovými prostředky za účelem implementace nejrůznějších cílů izolace sítě:

| Cíl | Příslušné scénáře | Přístup |
|------|-----------|----------|
| Zajistěte, aby zařízení a služby komunikovaly jenom s IoT Hubmi koncovými body | Zasílání zpráv ze [zařízení na Cloud](./iot-hub-devguide-messaging.md)a z [cloudu do zařízení](./iot-hub-devguide-messages-c2d.md) , [přímých metod](./iot-hub-devguide-direct-methods.md), [vláken zařízení a modulů](./iot-hub-devguide-device-twins.md) a [datových proudů zařízení](./iot-hub-device-streams-overview.md) | Pomocí značek služby _AzureIoTHub_ a _EventHub_ vyhledejte IoT Hub a předpony IP adres centra událostí a nakonfigurujte pro tyto předpony IP adres pravidla povolení pro tyto předpony IP adres. Zařaďte provoz do jiných cílových IP adres, se kterými nechcete, aby zařízení nebo služby komunikovaly. |
| Ujistěte se, že koncový bod zařízení IoT Hub přijímá připojení jenom z vašich zařízení a síťových prostředků. | Zasílání zpráv ze [zařízení na Cloud](./iot-hub-devguide-messaging.md)a z [cloudu do zařízení](./iot-hub-devguide-messages-c2d.md) , [přímých metod](./iot-hub-devguide-direct-methods.md), [vláken zařízení a modulů](./iot-hub-devguide-device-twins.md) a [datových proudů zařízení](./iot-hub-device-streams-overview.md) | Pomocí IoT Hub [funkce filtru IP](iot-hub-ip-filtering.md) adres povolíte připojení z vašich zařízení a IP adres síťových prostředků (viz část [omezení](#limitations-and-workarounds) ). | 
| Zajistěte, aby byly prostředky vlastních koncových bodů tras (účty úložiště, Service Bus a centra událostí) dostupné jenom ze síťových prostředků. | [Směrování zpráv](./iot-hub-devguide-messages-d2c.md) | Postupujte podle pokynů k prostředkům v omezení připojení (například prostřednictvím [pravidel brány firewall](../storage/common/storage-network-security.md), [privátních odkazů](../private-link/private-endpoint-overview.md)nebo [koncových bodů služby](../virtual-network/virtual-network-service-endpoints-overview.md)). pomocí značek služby _AzureIoTHub_ můžete zjistit IoT Hub předpony IP adres a přidat pravidla povolení pro tyto předpony IP adres v konfiguraci brány firewall prostředku (viz část [omezení](#limitations-and-workarounds) ). |



## <a name="best-practices"></a>Osvědčené postupy

* Při přidávání pravidel povolení v konfiguraci brány firewall zařízení je nejlepší poskytnout konkrétní [porty používané použitelnými protokoly](./iot-hub-devguide-protocols.md#port-numbers).

* Předpony IP adres centra IoT Hub se mohou měnit. Tyto změny jsou pravidelně publikovány prostřednictvím značek služeb, než začne platit. Je proto důležité, abyste vytvořili procesy pro pravidelné načítání a používání nejnovějších značek služeb. Tento proces může být automatizovaný prostřednictvím [rozhraní API pro zjišťování značek služeb](../virtual-network/service-tags-overview.md#service-tags-on-premises). Všimněte si, že rozhraní API pro zjišťování značek služeb je stále ve verzi Preview, ale v některých případech nemusí mít úplný seznam značek a IP adres. Až bude rozhraní API pro zjišťování všeobecně dostupné, zvažte použití [značek služby ve formátu JSON ke stažení](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Použijte *AzureIoTHub. [ název oblasti]* : Značka pro identifikaci předpon IP adres používaných koncovými body centra IoT Hub v konkrétní oblasti. Aby se mohlo přihlédnout k zotavení po havárii datacentra, nebo k [místnímu převzetí služeb při selhání](iot-hub-ha-dr.md) , je povolené taky připojení k PŘEDPONám IP v oblasti geografického páru IoT Hub.

* Nastavení pravidel firewallu v IoT Hub může blokovat připojení potřebné ke spouštění příkazů Azure CLI a PowerShellu na základě vašeho IoT Hub. Pokud se tomu chcete vyhnout, můžete přidat pravidla povolení pro předpony IP adres klientů pro opětovné povolení komunikace klientů CLI nebo PowerShell ke komunikaci s vaším IoT Hub.  


## <a name="limitations-and-workarounds"></a>Omezení a alternativní řešení

* Funkce filtru IP IoT Hub má omezení 100 pravidel. Tento limit je možné vydávat prostřednictvím požadavků Azure Customer Support. 

* Nakonfigurovaná [pravidla filtrování IP adres](iot-hub-ip-filtering.md) se aplikují jenom na koncové body IP adres IoT Hub a ne na integrovaném koncovém bodu centra událostí služby IoT Hub. Pokud budete také vyžadovat, aby se filtry IP používaly v centru událostí, kde jsou uložené vaše zprávy, můžete tak učinit vlastní prostředek centra událostí, kde můžete nakonfigurovat požadovaná pravidla filtrování IP adres přímo. Pokud to chcete udělat, musíte zřídit vlastní prostředek centra událostí a nastavit [směrování zpráv](./iot-hub-devguide-messages-d2c.md) pro posílání zpráv k tomuto prostředku místo integrovaného centra událostí IoT Hub. A nakonec jak je popsáno v tabulce výše, aby bylo možné povolit funkci směrování zpráv, musíte také povolit připojení z předpon IP adres IoT Hub k vašemu zřízenému prostředku centra událostí.

* Při směrování na účet úložiště je možné povolit provoz z prefixů IP adres IoT Hub jenom v případě, že je účet úložiště v jiné oblasti jako váš IoT Hub.

## <a name="support-for-ipv6"></a>Podpora protokolu IPv6 

Protokol IPv6 se v této IoT Hub v tuto chvíli nepodporuje.
