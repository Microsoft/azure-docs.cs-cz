---
title: Konfigurace protokolů rozhraní API – Azure Event Grid IoT Edge | Microsoft Docs
description: Nakonfigurujte protokoly rozhraní API, které jsou vystavené Event Grid v IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: f39968476f2fecf655e6c69bea2ff19304d2b465
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992519"
---
# <a name="configure-event-grid-api-protocols"></a>Konfigurace Event Gridch protokolů rozhraní API

Tato příručka obsahuje příklady možných konfigurací protokolu Event Grid modulu. Modul Event Grid zpřístupňuje rozhraní API pro své operace správy a běhu. Následující tabulka zachycuje protokoly a porty.

| Protocol (Protokol) | Port | Popis |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Ve výchozím nastavení vypnuté. Užitečné jenom při testování. Není vhodné pro produkční úlohy.
| HTTPS | 4438 | Výchozí

V tématu Průvodce [zabezpečením a ověřováním](security-authentication.md) najdete všechny možné konfigurace.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Zveřejnění protokolu HTTPS v modulech IoT ve stejné hraniční síti

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Povolení HTTPS pro jiné moduly IoT a úlohy mimo IoT

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge"
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
    "inbound:serverAuth:tlsPolicy=enabled",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Povolení protokolu HTTP a HTTPS jiným modulům IoT a úlohám mimo IoT

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=enabled",
    "inbound:serverAuth:serverCert:source=IoTEdge"
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
