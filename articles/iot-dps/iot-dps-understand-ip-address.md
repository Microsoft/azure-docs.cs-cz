---
title: Principy IP adresy instance služby DPS (Device Provisioning Service) služby IoT | Dokumenty společnosti Microsoft
description: Zjistěte, jak se dotazovat na adresu služby DPS (Device Provisioning Service) služby IoT a její vlastnosti. IP adresa instance DPS se může změnit během určitých scénářů, jako je zotavení po havárii nebo místní převzetí služeb při selhání.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284925"
---
# <a name="iot-hub-dps-ip-addresses"></a>IP adresy DPS centra IoT

Předpony IP adres pro veřejné koncové body služby DPS (Device Provisioning Service) služby IoT Hub se pravidelně publikují pod [značkou služby](../virtual-network/service-tags-overview.md) _AzureIoTHub_ . Tyto předpony IP adres můžete použít k řízení připojení mezi instancí IoT DPS a zařízeními nebo síťovými prostředky za účelem implementace různých cílů izolace sítě:

| Cíl | Přístup |
|------|----------|
| Zajistěte, aby vaše zařízení a služby komunikovaly jenom s koncovými body Služby IoT Hub DPS | Pomocí značky služby _AzureIoTHub_ zjišťujte instance DPS služby IoT Hub. Nakonfigurujte pravidla POVOLIT na nastavení brány firewall vašich zařízení a služeb pro tyto předpony IP adres odpovídajícím způsobem. Nakonfigurujte pravidla tak, aby byl provoz přetaponován na jiné cílové adresy IP, se kterými nechcete, aby zařízení nebo služby komunikovaly. |
| Zajistěte, aby koncový bod DPS služby IoT Hub přijíral připojení pouze z vašich zařízení a síťových prostředků. | Pomocí funkce filtru [IP](iot-dps-ip-filtering.md) Protokolu IoT DPS vytvořte pravidla filtru pro rozhraní API zařízení a služby DPS. Tato pravidla filtru lze použít k povolení připojení pouze z vašich zařízení a ip adres síťových prostředků [(viz](#limitations-and-workarounds) omezení). | 




## <a name="best-practices"></a>Osvědčené postupy

* Při přidávání pravidel ALLOW do konfigurace brány firewall zařízení je nejlepší poskytnout konkrétní [porty používané příslušnými protokoly](../iot-hub/iot-hub-devguide-protocols.md#port-numbers).

* Předpony IP adres instancí IoT DPS se mohou změnit. Tyto změny jsou pravidelně zveřejňovány prostřednictvím značek služby, než se projeví. Je proto důležité, abyste vyvinuli procesy pro pravidelné načítání a používání nejnovějších značek služeb. Tento proces lze automatizovat prostřednictvím [rozhraní API pro zjišťování značek služeb](../virtual-network/service-tags-overview.md#service-tags-on-premises). Rozhraní API pro zjišťování značek služby je stále ve verzi preview a v některých případech nemusí vytvořit úplný seznam značek a adres IP. Dokud nebude rozhraní API pro zjišťování obecně dostupné, zvažte použití [značek služeb ve formátu JSON ke stažení](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Použijte *AzureIoTHub.[ název oblasti]* k identifikaci předpon IP používaných koncovými body DPS v určité oblasti. Chcete-li účet pro zotavení po havárii datového centra nebo [regionální převzetí služeb při selhání](../iot-hub/iot-hub-ha-dr.md), zajistěte, aby bylo povoleno také připojení k předponám IP v oblasti geografického páru instance DPS.

* Nastavení pravidel brány firewall pro instanci DPS může blokovat připojení potřebné ke spuštění příkazů Azure CLI a PowerShell proti němu. Chcete-li se těmto problémům s připojením vyhnout, můžete přidat pravidla POVOLIT pro předpony IP adres vašich klientů a znovu povolit klientům cli nebo PowerShell ke komunikaci s instancí DPS.  


## <a name="limitations-and-workarounds"></a>Omezení a řešení

* Funkce filtru DPS IP má limit 100 pravidel. Tento limit a lze zvýšit prostřednictvím požadavků prostřednictvím zákaznické podpory Azure. 

* Nakonfigurovaná [pravidla filtrování IP](iot-dps-ip-filtering.md) se použijí jenom na koncových bodech DPS a ne na propojených koncových bodech centra IoT Hub. Filtrování IP adres pro propojené rozbočovače IoT huby musí být nakonfigurováno samostatně. Další informace naleznete v tématu [Pravidla filtrování IP v centru IoT](../iot-hub/iot-hub-ip-filtering.md).

## <a name="support-for-ipv6"></a>Podpora pro IPv6 

IPv6 není aktuálně podporovánv centru IoT Hub nebo DPS.

## <a name="next-steps"></a>Další kroky

Další informace o konfiguracích IP adres pomocí DPS najdete v tématu:

* [Konfigurace filtrování IP](iot-dps-ip-filtering.md)
