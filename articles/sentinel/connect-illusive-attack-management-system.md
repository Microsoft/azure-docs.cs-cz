---
title: Připojit systémová data pro správu útoků Illusive do Azure Sentinel | Microsoft Docs
description: Naučte se propojit systémová data správy útoku Illusive do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 7d2d3871dd9836e2c68155aa82ce01dced128bf3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531262"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Připojte svůj systém pro správu útoků Illusive k Azure Sentinel

Tento článek vysvětluje, jak připojit váš [systém pro správu útoků Illusive](https://www.illusivenetworks.com/technology/platform/attack-detection-system) k Azure Sentinel. Konektor dat systému správy útoků Illusive umožňuje sdílet data analýzy a protokoly incidentů Illusive pomocí služby Azure Sentinel a zobrazit tyto informace na vyhrazených řídicích panelech, které nabízí přehled o riziku napadení vaší organizace (řídicí panel ASM), a sledovat neoprávněný pohyb v síti vaší organizace (na řídicím panelu ADS).

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Dopředné systémové protokoly pro správu útoků přes Illusive do agenta syslog  

Nakonfigurujte systém pro správu útoků tak, aby předal zprávy syslog ve formátu CEF do pracovního prostoru Azure prostřednictvím agenta syslog.

1. Přihlaste se ke konzole Illusive a přejděte do části nastavení->vytváření sestav.

1. Najít Serversץ syslog

1. Zadejte následující informace:
   - Název hostitele: IP adresa agenta pro systém Linux nebo název hostitele plně kvalifikovaného názvu domény
   - Port: 514
   - Protokol. TCP
   - Zprávy o auditu: odesílat zprávy pro audit na server

1. Chcete-li přidat server syslog, klikněte na tlačítko Přidat.

1. Pokud chcete použít příslušné schéma v Log Analytics pro systém správy útoků Illusive, vyhledejte CommonSecurityLog.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit systém pro správu útoků Illusive ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
