---
title: Model připojování služby Azure peering Service
description: Informace o tom, jak se připojit ke službě Azure peering
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84872075"
---
# <a name="onboarding-peering-service-model"></a>Připojování modelu služby peering Service

Proces připojování služby peering Service se skládá ze dvou modelů, jak je uvedeno níže:

 - Připojování připojení ke službě peering Service

 - Zaregistrování telemetrie připojení ke službě peering Service

Plány akcí pro výše uvedené modely jsou popsány níže:

| **Krok** | **Akce**| **Co získáte**|
|-----------|---------|---------|---------|
|1|Zákazník, který zřídí připojení od partnera pro připojení (bez interakce s Microsoftem). |Poskytovatel internetových služeb, který je dobře připojený k Microsoftu a splňuje technické požadavky na výkonné a spolehlivé připojení k Microsoftu.  |
|2 (volitelné)|Zákazník zaregistruje umístění do Azure Portal. Umístění je definováno: poskytovatel internetových služeb/název IXP, fyzické umístění zákazníka (stavové úrovně), předponu IP danou umístění poskytovatelem služeb nebo podnikem.  |Telemetrie: monitorování tras Internetu, stanovení priorit provozu od Microsoftu až po umístění bodu POP nejbližšího okraje uživatele. |



## <a name="onboarding-peering-service-connection"></a>Připojování připojení ke službě peering Service

Připojení ke službě peering Service provedete následovně:

Spolupracovat s partnerem poskytovatele internetových služeb (ISP) nebo Internet Exchange (IX) a získat službu partnerského vztahu pro připojení vaší sítě k síti Microsoftu.

Zajistěte, aby [poskytovatelé připojení](location-partners.md) byli u služby partnerského vztahu spolupracuje s Microsoftem. 

## <a name="onboarding-peering-service-connection-telemetry"></a>Zaregistrování telemetrie připojení ke službě peering Service

Zákazníci se můžou rozhodnout pro svou telemetrii, jako je analýza tras protokolu BGP, aby se při přístupu k síti Microsoftu sledovala latence a výkon sítě. Toho lze dosáhnout registrací připojení do Azure Portal.

Aby bylo možné začlenit telemetrii připojení ke službě peering Service, musí zákazník zaregistrovat připojení ke službě peering Service do Azure Portal. Postup najdete v tématu věnovaném [registraci služby peering Service – Azure Portal](azure-portal.md) .

V takovém případě můžete telemetrie změřit tím, že se [sem](measure-connection-telemetry.md)odkazují.

## <a name="next-steps"></a>Další kroky

Podrobné informace o tom, jak zaregistrovat připojení ke službě peering Service, najdete v tématu [Registrace služby peering Service – Azure Portal](azure-portal.md).

Další informace o měření telemetrie připojení najdete v tématu [měření telemetrie připojení](measure-connection-telemetry.md).
