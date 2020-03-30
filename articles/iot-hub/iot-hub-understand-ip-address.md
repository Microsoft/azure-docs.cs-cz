---
title: Principy IP adresy vašeho centra IoT hub | Dokumenty společnosti Microsoft
description: Zjistěte, jak se dotazovat na IP adresu služby IoT hub a její vlastnosti. IP adresa vašeho centra IoT hub se může změnit během určitých scénářů, jako je zotavení po havárii nebo místní převzetí služeb při selhání.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c609f2a3843481442e97061739a806de60a680b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367563"
---
# <a name="iot-hub-ip-addresses"></a>IP adresy centra IoT

Předpony IP adres veřejných koncových bodů služby IoT Hub se pravidelně publikují pod [značkou služby](../virtual-network/service-tags-overview.md) _AzureIoTHub_ .

> [!NOTE]
> Pro zařízení, která jsou nasazená v místních sítích, Azure IoT Hub podporuje integraci připojení virtuální sítě s privátními koncovými body. Další informace najdete [v tématu Podpora služby IoT Hub pro virtuální sítě.](./virtual-network-support.md#ingress-connectivity-to-iot-hub-using-private-endpoints)


Tyto předpony IP adres můžete použít k řízení připojení mezi službou IoT Hub a vašimi zařízeními nebo síťovými prostředky za účelem implementace různých cílů izolace sítě:

| Cíl | Použitelné scénáře | Přístup |
|------|-----------|----------|
| Zajistěte, aby vaše zařízení a služby komunikovaly jenom s koncovými body služby IoT Hub | [Zasílání zpráv mezi zařízeními](./iot-hub-devguide-messaging.md)a [cloud-to-device,](./iot-hub-devguide-messages-c2d.md) [přímé metody](./iot-hub-devguide-direct-methods.md), [dvojčata zařízení a modulů](./iot-hub-devguide-device-twins.md) a [datové proudy zařízení](./iot-hub-device-streams-overview.md) | Pomocí značek služeb _AzureIoTHub_ a _EventHub_ můžete zjistit ioT hub a předpony IP adres Centra událostí a odpovídajícím způsobem nakonfigurovat pravidla ALLOW na nastavení brány firewall vašich zařízení a služeb pro předpony IP adres; přetažením provozu na jiné cílové ADRESY IP, se kterými nechcete, aby zařízení nebo služby komunikovaly. |
| Zajistěte, aby koncový bod zařízení služby IoT Hub přijímaný připojení pouze z vašich zařízení a síťových prostředků. | [Zasílání zpráv mezi zařízeními](./iot-hub-devguide-messaging.md)a [cloud-to-device,](./iot-hub-devguide-messages-c2d.md) [přímé metody](./iot-hub-devguide-direct-methods.md), [dvojčata zařízení a modulů](./iot-hub-devguide-device-twins.md) a [datové proudy zařízení](./iot-hub-device-streams-overview.md) | Pomocí [funkce filtru IP](iot-hub-ip-filtering.md) služby IoT Hub můžete povolit připojení ze zařízení a adres IP síťových prostředků (viz část [omezení).](#limitations-and-workarounds) | 
| Zajistěte, aby byly vlastní prostředky koncových bodů vašich tras (účty úložiště, service bus a centra událostí) dostupné pouze z prostředků vaší sítě. | [Směrování zpráv](./iot-hub-devguide-messages-d2c.md) | Postupujte podle pokynů k omezení připojení (například prostřednictvím [pravidel brány firewall](../storage/common/storage-network-security.md), [privátních propojení](../private-link/private-endpoint-overview.md)nebo [koncových bodů služby);](../virtual-network/virtual-network-service-endpoints-overview.md) pomocí značek služby _AzureIoTHub_ zjišťujte předpony IP adres služby IoT Hub a přidejte pravidla PO PRO TYTO IP předpony v konfiguraci brány firewall vašeho prostředku (viz část [Omezení).](#limitations-and-workarounds) |



## <a name="best-practices"></a>Osvědčené postupy

* Při přidávání pravidel ALLOW do konfigurace brány firewall zařízení je nejlepší poskytnout konkrétní [porty používané příslušnými protokoly](./iot-hub-devguide-protocols.md#port-numbers).

* Předpony IP adres centra IoT hub se mohou změnit. Tyto změny jsou pravidelně zveřejňovány prostřednictvím značek služby, než se projeví. Je proto důležité, abyste vyvinuli procesy pro pravidelné načítání a používání nejnovějších značek služeb. Tento proces lze automatizovat prostřednictvím [rozhraní API pro zjišťování značek služeb](../virtual-network/service-tags-overview.md#service-tags-on-premises). Všimněte si, že rozhraní API pro zjišťování značek služby je stále ve verzi preview a v některých případech nemusí vytvořit úplný seznam značek a ADRES IP. Dokud nebude rozhraní API pro zjišťování obecně dostupné, zvažte použití [značek služeb ve formátu JSON ke stažení](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Použijte *AzureIoTHub.[ název oblasti]* k identifikaci předpon IP používaných koncovými body centra IoT v určité oblasti. Chcete-li účet pro zotavení po havárii datového centra nebo [místní převzetí služeb při selhání](iot-hub-ha-dr.md) zajistit připojení k IP předpony vaší oblasti geo-pair služby IoT Hub je také povolena.

* Nastavení pravidel brány firewall v ioT hubu může blokovat připojení potřebné ke spuštění příkazů Azure CLI a PowerShell proti vašemu IoT Hubu. Chcete-li tomu zabránit, můžete přidat pravidla PO PRO IP adresy vašich klientů a znovu povolit klientům CLI nebo PowerShell ke komunikaci s vaším centrem IoT Hub.  


## <a name="limitations-and-workarounds"></a>Omezení a řešení

* Funkce filtru IP centra IoT Hub má limit 10 pravidel. Tento limit a lze zvýšit prostřednictvím požadavků prostřednictvím zákaznické podpory Azure. 

* Nakonfigurovaná [pravidla filtrování IP](iot-hub-ip-filtering.md) adres se použijí jenom na koncových bodech IP centra IoT hubu a ne na integrovaném koncovém bodu centra událostí centra IoT hubu. Pokud také požadujete, aby se filtrování IP adres použilo v centru událostí, kde jsou vaše zprávy uloženy, můžete tak učinit, můžete tak učinit, abyste přinesli vlastní prostředek centra událostí, kde můžete přímo nakonfigurovat požadovaná pravidla filtrování IP adres. Chcete-li tak učinit, musíte zřídit vlastní prostředek Centra událostí a nastavit [směrování zpráv](./iot-hub-devguide-messages-d2c.md) pro odesílání zpráv do tohoto prostředku namísto integrovaného centra událostí služby IoT Hub. Nakonec, jak je popsáno v tabulce výše, chcete-li povolit funkce směrování zpráv, musíte také povolit připojení z předpony IP adresy služby IoT Hub k prostředku zřízeného centra událostí.

* Při směrování na účet úložiště je povolení provozu z předpon IP adres služby IoT Hub možné pouze v případě, že je účet úložiště v jiné oblasti jako vaše služba IoT Hub.

## <a name="support-for-ipv6"></a>Podpora pro IPv6 

IPv6 není momentálně v centru IoT Hub podporován.
