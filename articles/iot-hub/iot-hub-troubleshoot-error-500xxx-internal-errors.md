---
title: Řešení potíží s interními chybami služby Azure IoT Hub 500xxx
description: Vysvětlení, jak opravit interní chyby 500xxx
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f83ec4d09389d91c2dd427c9840885c0b829eafb
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150311"
---
# <a name="500xxx-internal-errors"></a>500xxx Vnitřní chyby

Tento článek popisuje příčiny a řešení pro **vnitřní chyby 500xxx**.

## <a name="symptoms"></a>Příznaky

Vaše žádost o IoT Hub se nezdařila s chybou začínající 500 a/nebo určitým řazením "Chyba serveru". Některé možnosti jsou:

* **500001 ServerError**: IoT Hub došlo k problému na straně serveru.

* **500008 GenericTimeout**: IoT Hub nemohl dokončit požadavek na připojení před vypršením časového limitu.

* **ServiceUnavailable (žádný kód chyby)**: došlo k vnitřní chybě IoT Hub.

* **Nenalezeno (žádný kód chyby)**: došlo k vnitřní chybě IoT Hub.

## <a name="cause"></a>Příčina

Může to být počet příčin odezvy 500xxx chyby. Ve všech případech je problém pravděpodobně přechodný. I když tým IoT Hub zachovává [smlouvu SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/), malé podmnožiny IoT Hub uzlů můžou občas docházet k přechodným chybám. Když se vaše zařízení pokusí připojit k uzlu, kde dochází k problémům, zobrazí se tato chyba.

## <a name="solution"></a>Řešení

Pokud chcete zmírnit 500xxx chyby, vydejte na zařízení Opakovaný pokus. Pokud chcete [automaticky spravovat opakované pokusy](./iot-hub-reliability-features-in-sdks.md#connection-and-retry), ujistěte se, že používáte nejnovější verzi [sad SDK služby Azure IoT](./iot-hub-devguide-sdks.md). Osvědčené postupy při zpracování přechodných chyb a opakování najdete v tématu [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).  Pokud se problém opakuje, zkontrolujte [Resource Health](./iot-hub-monitor-resource-health.md#use-azure-resource-health) a [Azure status](https://status.azure.com/) a zjistěte, jestli IoT Hub má známý problém. Můžete také použít [funkci ručního převzetí služeb při selhání](./tutorial-manual-failover.md). Pokud neexistují žádné známé problémy a problém přetrvává, obraťte se na [podporu a požádejte](https://azure.microsoft.com/support/options/) o další šetření.