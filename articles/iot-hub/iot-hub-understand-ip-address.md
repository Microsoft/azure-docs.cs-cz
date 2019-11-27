---
title: Princip IP adresy vašeho centra IoT Hub | Microsoft Docs
description: Zjistěte, jak zadat dotaz na IP adresu služby IoT Hub a její vlastnosti. IP adresa vašeho centra IoT se může změnit během určitých scénářů, jako je například zotavení po havárii nebo regionální převzetí služeb při selhání.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 5c77fb30ef60c1ad82d0a87442bc8af186c54321
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383904"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub IP adresy

Předpony IP adres centra IoT Hub se pravidelně publikují v rámci [značky služby](../virtual-network/service-tags-overview.md)AzureIoTHub. Aby bylo zajištěno správné fungování zařízení IoT, musí mít odchozí připojení k předponám adres uvedeným v části *AzureIoTHub* Service Tag. Vaše aplikační služby IoT musí mít navíc odchozí připojení k předponám adres uvedeným v části značka služby *EventHub* .


## <a name="best-practices"></a>Osvědčené postupy

* Předpony IP adres centra IoT Hub se mohou měnit. Tyto změny jsou pravidelně publikovány prostřednictvím značek služeb, než začne platit. Je proto důležité, abyste vytvořili procesy pro pravidelné načítání a používání nejnovějších značek služeb. Tento proces může být automatizovaný prostřednictvím [rozhraní API pro zjišťování značek služeb](../virtual-network/service-tags-overview.md#service-tags-in-on-premises).
* Použijte *AzureIoTHub. [ název oblasti]* : Značka pro identifikaci předpon IP adres používaných koncovými body centra IoT Hub v konkrétní oblasti. Aby se mohlo přihlédnout k zotavení po havárii datacentra, nebo k [místnímu převzetí služeb při selhání](iot-hub-ha-dr.md) , je povolené taky připojení k PŘEDPONám IP v oblasti geografického páru IoT Hub.


## <a name="support-for-ipv6"></a>Podpora protokolu IPv6 

Protokol IPv6 se v této IoT Hub v tuto chvíli nepodporuje.