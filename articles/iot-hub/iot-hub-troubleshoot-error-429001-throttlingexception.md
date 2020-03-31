---
title: Poradce při potížích s chybou služby Azure IoT Hub 429001 OmezeníVýjimka
description: Pochopit, jak opravit chybu 429001 OmezeníException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960695"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

Tento článek popisuje příčiny a řešení pro **chyby 429001 Omezení Výjimka.**

## <a name="symptoms"></a>Příznaky

Vaše požadavky na službu IoT Hub se nezdaří s chybou **429001 OmezeníException**.

## <a name="cause"></a>Příčina

Pro požadovanou operaci byly překročeny [limity omezení centra](./iot-hub-devguide-quotas-throttling.md) IoT.

## <a name="solution"></a>Řešení

Zkontrolujte, zda jste dosáhli omezení limit porovnáním *vaše zpráva telemetrie odeslat metriku pokusů s* limity uvedenými výše. Můžete také zkontrolovat *metriku Počet chyb omezení.* Další informace o těchto a dalších metrikách dostupných pro Službu IoT Hub najdete v [tématu metriky služby IoT Hub a způsob jejich použití](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them).

IoT Hub vrátí 429 OmezeníException pouze po omezení byla porušena příliš dlouho. To se provádí tak, aby vaše zprávy nejsou vynechány, pokud vaše centrum IoT dostane burst provozu. IoT Hub mezitím zpracovává zprávy rychlostí odpovídající omezení šířky pásma, která může být nízká, pokud je v backlogu příliš velký provoz. Další informace najdete v tématu věnovaném [formování provozu služby IoT Hub](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Další kroky

Zvažte [horizontální navýšení kapacity vašeho ioT hubu,](./iot-hub-scaling.md) pokud běžíte do kvóty nebo omezení omezení.