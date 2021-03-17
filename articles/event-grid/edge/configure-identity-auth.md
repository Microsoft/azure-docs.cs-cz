---
title: Konfigurace identity-Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurace identity modulu Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2418c8518bbf298a102d4f29b4a973f6121de2eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171682"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Konfigurace identity pro modul Event Grid

Tento článek obsahuje informace o tom, jak nakonfigurovat identitu pro mřížku na hraničních zařízeních. Ve výchozím nastavení modul Event Grid prezentuje svůj certifikát identity, jak je nakonfigurovaný démon zabezpečení IoT. Event Grid na Edge prezentuje svůj certifikát identity s odchozími voláními při doručování událostí. Předplatitel pak může ověřit, že se jedná o modul Event Grid, který odeslal událost před přijetím.

V tématu Průvodce [zabezpečením a ověřováním](security-authentication.md) najdete všechny možné konfigurace.

## <a name="always-present-identity-certificate"></a>Vždy prezentovat certifikát identity
Tady je příklad konfigurace, která vždy prezentuje certifikát identity při odchozích voláních. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Neprezentovat certifikát identity
Tady je příklad konfigurace, která neprezentuje certifikát identity při odchozích voláních. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
