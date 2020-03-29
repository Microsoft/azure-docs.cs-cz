---
title: Geografické zotavení po havárii ve službě Azure Event Grid | Dokumenty společnosti Microsoft
description: Popisuje, jak Azure Event Grid podporuje geografické zotavení po havárii (GeoDR) automaticky.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66307314"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Geografické zotavení po havárii na straně serveru v Azure Event Grid
Event Grid má nyní automatické geografické zotavení po havárii (GeoDR) metadat nejen pro nové, ale všechny existující domény, témata a odběry událostí. Pokud se zřekne celá oblast Azure, event grid už bude mít všechna metadata infrastruktury související s událostmi synchronizovaná do spárované oblasti. Vaše nové události začnou opět proudit bez vašeho zásahu. 

Zotavení po havárii se měří se dvěma metrikami:

- [Cíl bodu obnovení (RPO):](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)minuty nebo hodiny dat, která mohou být ztracena.
- [Cíl doby zotavení (RTO):](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective)minuty hodin, kdy může být služba mimo provoz.

Automatické převzetí služeb při selhání event gridu má různé RPO a RTO pro vaše metadata (odběry událostí atd.) a data (události). Pokud potřebujete jinou specifikaci než následující, můžete stále implementovat vlastní [převzetí služeb při selhání na straně klienta pomocí apis stavu tématu](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Cíl bodu obnovení (RPO)
- **RPO metadat**: nula minut. Kdykoli je prostředek vytvořen v Event Grid, je okamžitě replikován napříč oblastmi. Dojde-li k převzetí služeb při selhání, dojde ke ztrátě metadat.
- **RPO dat**: Pokud je váš systém v pořádku a zachycený na existující provoz v době převzetí služeb při selhání místní, RPO pro události je asi 5 minut.

## <a name="recovery-time-objective-rto"></a>Plánovaná doba obnovení (RTO)
- **Metadata RTO**: I když obecně se to stane mnohem rychleji, během 60 minut, Event Grid začne přijímat volání vytvořit / aktualizovat / odstranit pro témata a odběry.
- **Data RTO**: Stejně jako metadata se obvykle děje mnohem rychleji, ale během 60 minut začne Event Grid přijímat nový provoz po regionálním převzetí služeb při selhání.

> [!NOTE]
> Náklady na metadata GeoDR na Event Grid je: $0.


## <a name="next-steps"></a>Další kroky
Pokud chcete implementovat vlastní logiku převzetí služeb při selhání na straně klienta, přečtěte si téma [# Vytvoření vlastního zotavení po havárii pro vlastní témata v programu Event Grid](custom-disaster-recovery.md)
