---
title: Konfigurace identity-Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurace identity modulu Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992441"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Konfigurace identity pro modul Event Grid

Tento článek obsahuje příklady možných konfigurací identity pro modul Event Grid. Ve výchozím nastavení bude modul Event Grid prezentovat svůj certifikát identity, jak je nakonfigurovaný démon zabezpečení IoT. Certifikát identity je prezentován modulem Event Grid na odchozích voláních, která jsou při doručování událostí. Odběratel události Event Grid se pak může rozhodnout ověřit, že se jedná o modul Event Grid, který před přijetím události odeslal událost.

V tématu Průvodce [zabezpečením a ověřováním](security-authentication.md) najdete všechny možné konfigurace.

## <a name="always-present-identity-certificate"></a>Vždy prezentovat certifikát identity
Tady je příklad konfigurace, která vždy prezentuje certifikát identity při odchozích voláních. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Neprezentovat certifikát identity
Tady je příklad konfigurace, která neprezentuje certifikát identity při odchozích voláních. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```
