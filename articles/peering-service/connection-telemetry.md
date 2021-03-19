---
title: Telemetrie připojení ke službě Azure peering Service
description: Další informace o telemetrii Microsoft Azure pro připojení ke službě peering Service
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84872005"
---
# <a name="peering-service-connection-telemetry"></a>Telemetrie připojení ke službě peering Service

Telemetrie připojení poskytuje přehledy shromažďované pro připojení mezi umístěním zákazníka a sítí Microsoftu. Zákazníci mohou získat telemetrii k připojení služby Azure peering Service tím, že se do Azure Portal zaregistrují připojení. Tato funkce poskytuje zabezpečení prefixu a přehledy o výkonu sítě.


Telemetrie připojení se skládá z těchto rozsahů:

### <a name="latency-measurement"></a>Měření latence

 Od klienta se měří latence pro registrované předpony v rámci služby partnerského vztahu.

### <a name="route-prefix-monitoring-and-protection"></a>Sledování a ochrana předpony trasy

Cesty směrování se monitorují pro všechny podezřelé aktivity, které jsou pak zachyceny v protokolech událostí. Například protokoly událostí jsou vytvořeny pro některé z těchto faktorů:

- Napadení předpony
- Odnětí předpony
- Nevracení trasy

## <a name="next-steps"></a>Další kroky

- Další informace o připojení ke službě peering Service najdete v tématu [připojení ke službě peering Service](connection.md).
- Pokud chcete připojit připojení ke službě peering Service, přečtěte si téma připojování [modelu služby peering Service](onboarding-model.md).
- Měření telemetrie najdete v tématu [měření telemetrie připojení](measure-connection-telemetry.md).
