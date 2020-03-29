---
title: Konfigurace identity – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Konfigurace identity modulu Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841761"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Konfigurace identity pro modul Mřížka událostí

Tento článek ukazuje, jak nakonfigurovat identitu pro Grid on Edge. Ve výchozím nastavení modul Event Grid prezentuje svůj certifikát identity nakonfigurovaný daemonem zabezpečení IoT. Event Grid na okraji představuje svůj certifikát identity s odchozí volání při doručování událostí. Odběratel pak může ověřit, že je to modul Event Grid, který odeslal událost před přijetím.

Všechny možné konfigurace naleznete v příručce [zabezpečení a ověřování.](security-authentication.md)

## <a name="always-present-identity-certificate"></a>Vždy prezentovat certifikát identity
Tady je příklad konfigurace pro vždy prezentaci certifikátu identity při odchozích hovorech. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Neprezentovat certifikát identity
Tady je příklad konfigurace pro nepředložení certifikátu identity při odchozích hovorech. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
