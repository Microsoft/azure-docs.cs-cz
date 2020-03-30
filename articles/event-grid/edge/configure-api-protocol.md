---
title: Konfigurace protokolů rozhraní API – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Konfigurace protokolů rozhraní API vystavených službou Event Grid na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841806"
---
# <a name="configure-event-grid-api-protocols"></a>Konfigurace protokolů rozhraní API sítě událostí

Tato příručka uvádí příklady možných konfigurací protokolu modulu Event Grid. Modul Event Grid zveřejňuje rozhraní API pro jeho správu a operace runtime. Následující tabulka zachycuje protokoly a porty.

| Protocol (Protokol) | Port | Popis |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Ve výchozím nastavení je vypnuto. Užitečné pouze během testování. Není vhodné pro produkční úlohy.
| HTTPS | 4438 | Výchozí

Všechny možné konfigurace naleznete v příručce [zabezpečení a ověřování.](security-authentication.md)

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Vystavit protokol HTTPS modulům IoT ve stejné hraniční síti

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Povolení protokolu HTTPS k dalším modulům IoT a úlohám mimo IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> **PortBindings** část umožňuje mapovat interní porty na porty hostitele kontejneru. Tato funkce umožňuje dosáhnout modulu Event Grid mimo síť kontejnerů IoT Edge, pokud je hraniční zařízení IoT dostupné veřejně.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Vystavit moduly HTTP a HTTPS modulům IoT ve stejné hraniční síti

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Povolení protokolů HTTP a HTTPS do jiných modulů IoT a úloh bez IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Ve výchozím nastavení je každý modul IoT součástí modulu IoT Edge vytvořeného sítí mostu. Umožňuje různé moduly IoT ve stejné síti komunikovat mezi sebou. **PortBindings** umožňuje mapovat vnitřní port kontejneru na hostitelský počítač a tím umožňuje komukoli v přístupu k portu modulu Event Grid zvenčí.

>[!IMPORTANT]
> Zatímco porty mohou být přístupné mimo síť IoT Edge, ověřování klienta vynucuje, kdo je ve skutečnosti povoleno volat do modulu.
