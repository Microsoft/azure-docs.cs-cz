---
title: Konfigurace protokolů rozhraní API – Azure Event Grid IoT Edge | Microsoft Docs
description: Přečtěte si o možných konfiguracích protokolu modulu Event Grid.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: a9cf6088201ffeaed76d99a9b211e5bcd1ea139a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91322576"
---
# <a name="configure-event-grid-api-protocols"></a>Konfigurace Event Gridch protokolů rozhraní API

Tato příručka obsahuje příklady možných konfigurací protokolu Event Grid modulu. Modul Event Grid zpřístupňuje rozhraní API pro své operace správy a běhu. Následující tabulka zachycuje protokoly a porty.

| Protokol | Port | Description |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Ve výchozím nastavení vypnuté. Užitečné jenom při testování. Není vhodné pro produkční úlohy.
| HTTPS | 4438 | Výchozí

V tématu Průvodce [zabezpečením a ověřováním](security-authentication.md) najdete všechny možné konfigurace.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Zveřejnění protokolu HTTPS v modulech IoT ve stejné hraniční síti

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Povolení HTTPS pro jiné moduly IoT a úlohy mimo IoT

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
> Část **PortBindings** umožňuje mapovat interní porty na porty hostitele kontejneru. Tato funkce umožňuje kontaktovat modul Event Grid mimo IoT Edgeou síť kontejneru, pokud je zařízení IoT Edge veřejně dosažitelné.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Vystavení HTTP a HTTPS pro moduly IoT ve stejné hraniční síti

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Povolení protokolu HTTP a HTTPS jiným modulům IoT a úlohám mimo IoT

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
> Ve výchozím nastavení je každý modul IoT součástí modulu runtime IoT Edge vytvořeného přes síť mostu. Umožňuje různým modulům IoT ve stejné síti komunikovat mezi sebou. **PortBindings** vám umožňuje namapovat interní port kontejneru na hostitelský počítač a umožnit tak komukoli přístup k portu Event Grid modulu z vnějšku.

>[!IMPORTANT]
> I když lze porty zpřístupnit mimo IoT Edge síť, ověřování klienta vynutilo, kdo je ve skutečnosti povolen volání do modulu.
