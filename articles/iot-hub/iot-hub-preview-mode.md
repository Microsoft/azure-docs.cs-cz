---
title: Zapnout režim náhledu pro Azure IoT Hub
description: Naučte se zapnout režim náhledu pro IoT Hub, proč byste měli chtít a některá upozornění.
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 5299a049ab2cc2246a861dd3fb81f466b56afad3
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603299"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>Zapněte režim náhledu pro IoT Hub a zkuste vybrat nové funkce.

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

Nové funkce jsou ve verzi Public Preview pro IoT Hub, včetně těchto:

- MQTT 5
- Certifikát serveru ECC
- Vyjednávání délky fragmentu TLS
- Podpora řetězu x509 CA pro HTTPS/WebSocket

Tyto funkce jsou vylepšením IoT Hub protokolů a ověřovacích vrstev, takže jsou dostupné jenom pro **nové** centrum IoT pro teď. Ještě *nejsou* k dispozici pro existující centra IoT. Chcete-li zobrazit náhled těchto funkcí, je třeba vytvořit centrum IoT s zapnutým režimem náhledu.

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>Zapnout režim náhledu pro nové centrum IoT

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Na domovské stránce Azure vyberte tlačítko **+ vytvořit prostředek** a potom do pole **Hledat na Marketplace** zadejte *IoT Hub* .

1. Ve výsledcích hledání vyberte **IoT Hub** a pak vyberte **vytvořit**.

1. Na kartě **základy** vyplňte pole [běžným](iot-hub-create-through-portal.md) způsobem, s výjimkou **oblasti**. Vyberte jednu z těchto oblastí:
    
    - Střední USA
    - West Europe
    - Jihovýchodní Asie

1. Vyberte kartu **Správa** a potom rozbalte část **Upřesnit nastavení** .

1. Vedle pole **režim náhledu** vyberte **zapnuto**. Pozorně zkontrolujte text upozornění.

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="Obrázek znázorňující, kde vybrat možnost režimu náhledu při vytváření nového centra IoT Hub":::

1. Vyberte **Další: zkontrolovat + vytvořit** a pak **vytvořit**.

Po vytvoření se v režimu náhledu vždy zobrazí tato IoT Hub zpráva, takže budete mít jistotu, že chcete používat toto centrum IoT pro účely verze Preview: 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="Obrázek znázorňující hlavičku pro IoT Hub v režimu náhledu":::

## <a name="using-an-iot-hub-in-preview-mode"></a>Používání služby IoT Hub v režimu náhledu

Nepoužívejte *IoT* hub v režimu náhledu pro produkci. Režim náhledu je určený *jenom* pro náhled vybraných funkcí uvedených v horní části této stránky. Některá další omezení pro režim náhledu IoT Hub jsou

- Některé existující funkce IoT Hub, jako je například soukromý odkaz, spravovaná identita, datové proudy zařízení a převzetí služeb při selhání, můžou fungovat neočekávaně nebo vůbec.
- Centrum IoT v režimu náhledu nejde změnit ani upgradovat na normální centrum IoT.
- Nemůžeme zaručit normální [IoT Hub smlouvu SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) – nepoužívejte pro produkční prostředí.

> [!TIP]
> Pro [datové proudy zařízení](iot-hub-device-streams-overview.md) a [distribuované trasování](iot-hub-distributed-tracing.md)není režim náhledu vyžadován. Pokud chcete používat tyto starší funkce verze Preview, postupujte podle svých dokumentů jako normálně. 

## <a name="next-steps"></a>Další kroky

- Pokud si chcete zobrazit náhled podpory MQTT 5, přečtěte IoT Hub si téma [Přehled podpory MQTT 5 (Preview)](iot-hub-mqtt-5.md) .
- Pokud si chcete zobrazit náhled certifikátu serveru ECC, přečtěte si certifikát TLS (standarded [Curve Cryptography) serveru (Preview)](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview) .
- Pokud chcete zobrazit náhled vyjednávání velikosti fragmentu TLS, přečtěte si téma [Maximální délka fragmentu protokolu TLS (Preview)](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview) .