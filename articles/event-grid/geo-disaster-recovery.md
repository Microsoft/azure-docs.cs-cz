---
title: Obnovení geografického zotavení po havárii v Azure Event Grid | Microsoft Docs
description: Popisuje, jak Azure Event Grid automaticky podporuje obnovení geografického zotavení po havárii (GeoDR).
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 10beaf0ae25f3ed9b7bcda5961a89494b18b84d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94980843"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Geografické zotavení po havárii na straně serveru v Azure Event Grid
Event Grid nyní obsahuje automatickou geografickou obnovu po havárii (GeoDR) meta-data nejenom pro nové, ale všechny existující domény, témata a odběry událostí. Pokud dojde k výpadku celé oblasti Azure, Event Grid už budou všechna metadata infrastruktury související s událostmi synchronizovaná do spárované oblasti. Vaše nové události začnou pokračovat bez zásahu. 

Zotavení po havárii se měří se dvěma metrikami:

- Cíl bodu obnovení (RPO): počet minut nebo hodin dat, která mohou být ztracena.
- Plánovaná doba obnovení (RTO): počet minut nebo hodin, po které může být služba vypnutá.

Automatické převzetí služeb při selhání v Event Grid má různé RPO a RTO pro vaše metadata (odběry událostí atd.) a data (události). Pokud potřebujete různé specifikace z následujících možností, můžete i nadále implementovat vlastní funkci [převzetí služeb při selhání na straně klienta pomocí rozhraní API pro stav tématu](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Cíl bodu obnovení (RPO)
- **RPO metadat**: 0 minut. Kdykoli je prostředek vytvořen v Event Grid, je okamžitě replikován do různých oblastí. Pokud dojde k převzetí služeb při selhání, nedojde ke ztrátě metadat.
- **RPO dat**: Pokud je váš systém v pořádku a v době regionálního převzetí služeb při selhání se zachytí na stávajícím provozu, bude RPO pro události asi 5 minut.

## <a name="recovery-time-objective-rto"></a>Plánovaná doba obnovení (RTO)
- **Metadata RTO**: i když obvykle dojde k mnohem rychlejšímu navýšení během 60 minut, Event Grid začne přijímat volání metody Create/Update/Delete pro témata a odběry.
- **Data RTO**: stejně jako metadata obvykle dochází k mnohem rychlejšímu, ale během 60 Event Grid minut začne přijímat nový provoz po rámci regionálního převzetí služeb při selhání.

> [!NOTE]
> Náklady na metadata GeoDR v Event Grid jsou: $0.


## <a name="next-steps"></a>Další kroky
Pokud chcete implementovat vlastní logiku převzetí služeb při selhání na straně klienta, přečtěte si téma [# sestavení vlastního zotavení po havárii pro vlastní témata v Event Grid](custom-disaster-recovery.md)
