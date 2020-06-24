---
title: Připojení služby partnerského vztahu Azure
description: Další informace o připojení služby Microsoft Azure peering Service
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: df337736a676c40f1345310afb9a2e685ddc1d5c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "84872019"
---
# <a name="peering-service-connection"></a>Připojení ke službě peering Service

Připojení obvykle odkazuje na logickou sadu informací, která identifikuje službu partnerského vztahu. Definuje se zadáním následujících atributů:

- Logický název
- Partner pro připojení
- Fyzické umístění zákazníka
- Předpony IP adres

Zákazník může na základě požadavku vytvořit jedno nebo více připojení. Připojení se používá také jako jednotka kolekce telemetrie. Například pro upozornění na výstrahy telemetrie musí zákazník definovat připojení, které se bude monitorovat.

> [!Note]
> Když se přihlásíte ke službě partnerského vztahu, analyzujeme vaše telemetrie Windows a Office 365, abychom vám poskytli měření latence pro vybrané předpony.
>Další informace o telemetrii připojení najdete v tématu [telemetrie připojení ke službě peering Service](connection-telemetry.md).
>

>## <a name="how-to-register-a-connection"></a>Jak zaregistrovat připojení?

**Scénář** – řekněme, že firemní pobočka je rozdělená do různých zeměpisných míst, jak je znázorněno na následujícím obrázku. Tady je nutné zadat logický název, název poskytovatele služeb (SP), fyzické umístění zákazníka a předpony IP adres (vlastněné zákazníkem nebo přidělené poskytovatelem služeb) přidruženého k jednomu připojení. Tento proces se musí opakovat pro registraci služby partnerského vztahu pro samostatná geograficky redundantní připojení.

![Geograficky redundantní připojení](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> Úroveň stavu – filtr je považován za fyzické umístění zákazníka, pokud je připojení geograficky umístěné v USA.
>

## <a name="next-steps"></a>Další kroky

Podrobné informace o tom, jak zaregistrovat připojení ke službě peering Service, najdete v tématu [Register peering Service pomocí Azure Portal](azure-portal.md).

Další informace o telemetrie připojení ke službě peering Service najdete v tématu [telemetrie připojení ke službě peering Service](connection-telemetry.md).

Měření telemetrie najdete v tématu [měření telemetrie připojení](measure-connection-telemetry.md).
