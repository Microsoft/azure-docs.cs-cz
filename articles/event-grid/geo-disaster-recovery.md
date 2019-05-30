---
title: Geografické zotavení po havárii ve službě Azure Event Grid | Dokumentace Microsoftu
description: Popisuje, jak Azure podporuje Služba Event Grid geografického zotavení po havárii (GeoDR) automaticky.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307314"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Na straně serveru geografického zotavení po havárii ve službě Azure Event Grid
Event Grid má teď automatického geografického zotavení po havárii (GeoDR) meta-data nejen na nový, ale všech stávajících domén, témata a odběry událostí. Pokud přestane fungovat celá oblast Azure, služby Event Grid už máte všechny vaše metadata související události infrastruktury se synchronizují se spárované oblasti. Nové události se začnou tok znovu bez nutnosti zásahu sami. 

Zotavení po havárii se měří s dvě metriky:

- [Cíl bodu obnovení (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective): minuty nebo hodiny dat, které mohou být ztraceny.
- [Cíl času obnovení (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective): dobu, po které může být služba hodin dolů.

Automatické převzetí služeb při selhání služby Event Grid má jiný rpo a RTO pro metadata (odběry událostí atd.) a data (události). Pokud potřebujete jinou specifikací z následující dotazy, můžete stále implementovat vlastní [na straně klienta, převzetí služeb při selhání pomocí tématu rozhraní API stavu](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Cíl bodu obnovení (RPO)
- **Cíl bodu obnovení metadat**: nula minut. Kdykoli se prostředek vytvoří ve službě Event Grid, se okamžitě replikuje napříč oblastmi. Pokud dojde k selhání dojde ke ztrátě žádná metadata.
- **Data cíle bodu obnovení**: Pokud váš systém je v pořádku a zachycena na existující provoz v době regionální převzetí služeb při selhání, cíle bodu obnovení pro události je asi 5 minut.

## <a name="recovery-time-objective-rto"></a>Plánovaná doba obnovení (RTO)
- **Metadata RTO**: Ale obecně to se stane mnohem rychleji, do 60 minut, začne přijímat volání vytvoření/aktualizace/odstranění pro témata a odběry služby Event Grid.
- **Data RTO**: Například metadata obecně Odehrává se mnohem rychleji, ale do 60 minut služby Event Grid se začne přijímat nové přenosy dat po regionální převzetí služeb při selhání.

> [!NOTE]
> Náklady na metadata GeoDR na služby Event Grid jsou: 0 USD.


## <a name="next-steps"></a>Další postup
Pokud chcete implementovat logiku vlastní převzetí služeb při selhání na straně klienta, přečtěte si téma [# sestavit vlastní zotavení po havárii pro vlastní témata ve službě Event Grid](custom-disaster-recovery.md)
