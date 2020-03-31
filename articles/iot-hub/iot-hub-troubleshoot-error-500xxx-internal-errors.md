---
title: Řešení potíží s interními chybami služby Azure IoT Hub 500xxx
description: Pochopit, jak opravit 500xxx Vnitřní chyby
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7f3f5177e084693c45bed1088a4e1d091be100ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271041"
---
# <a name="500xxx-internal-errors"></a>500xxx Vnitřní chyby

Tento článek popisuje příčiny a řešení pro **vnitřní chyby 500xxx**.

## <a name="symptoms"></a>Příznaky

Váš požadavek na Službu IoT Hub se nezdaří s chybou, která začíná 500 a/nebo nějakou "chybou serveru". Některé možnosti jsou:

* **500001 ServerChyba**: Služba IoT Hub narazila na problém na straně serveru.

* **500008 GenericTimeout**: Služba IoT Hub nemohla dokončit požadavek na připojení před vypršením časového limitu.

* **ServiceUnavailable (bez kódu chyby)**: Služba IoT Hub zjistila vnitřní chybu.

* **InternalServerError (bez kódu chyby)**: Služba IoT Hub zjistila vnitřní chybu.

## <a name="cause"></a>Příčina

Může existovat řada příčin pro odpověď na chybu 500xxx. Ve všech případech je problém s největší pravděpodobností přechodné. Zatímco tým ioT Hub tvrdě pracuje na [údržbě sla](https://azure.microsoft.com/support/legal/sla/iot-hub/), malé podmnožiny uzlů IoT Hub může občas dojít k přechodné chyby. Když se zařízení pokusí připojit k uzlu, který má problémy, zobrazí se tato chyba.

## <a name="solution"></a>Řešení

Chcete-li zmírnit chyby 500xxx, vystavte opakování ze zařízení. Chcete-li [automaticky spravovat opakování](./iot-hub-reliability-features-in-sdks.md#connection-and-retry), ujistěte se, že používáte nejnovější verzi sad Azure [IoT SDK](./iot-hub-devguide-sdks.md). Osvědčený postup při zpracování přechodných chyb a opakováních naleznete [v tématu Přechodné zpracování chyb](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults).  Pokud problém přetrvává, zkontrolujte [stav prostředků](./iot-hub-monitor-resource-health.md#use-azure-resource-health) a [stav Azure](https://status.azure.com/) a zjistěte, jestli má služba IoT Hub známý problém. Můžete také použít [funkci ručního převzetí služeb při selhání](./tutorial-manual-failover.md). Pokud nejsou známy žádné problémy a problém pokračuje, [obraťte se](https://azure.microsoft.com/support/options/) na podporu pro další šetření.
