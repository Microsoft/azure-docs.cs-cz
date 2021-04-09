---
title: Agregace událostí (Preview)
description: Defender pro agenty zabezpečení IoT shromažďuje data a systémové události z místního zařízení a odesílá data do cloudu Azure pro zpracování a analýzu.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: c0280e97549009a1e4911c072a7a8ec052684b4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779320"
---
# <a name="event-aggregation-preview"></a>Agregace událostí (Preview)

Defender pro agenty zabezpečení IoT shromažďuje data a systémové události z místního zařízení a odesílá data do cloudu Azure pro zpracování a analýzu. Defender pro IoT Micro agent shromažďuje mnoho typů událostí zařízení, včetně nových procesů a všech nových událostí připojení. Nový proces a nové události připojení se můžou na zařízení nacházet často v rámci druhé. Tato možnost je důležitá pro komplexní zabezpečení, ale počet agentů zabezpečení, které odesílají, můžou rychle splnit nebo překročit vaši kvótu IoT Hub a limity nákladů. Tyto události však obsahují vysoce cenné informace o zabezpečení, které jsou zásadní pro ochranu zařízení. 

Aby se snížila další kvóta a náklady při zachování chráněných zařízení, Defender pro agenty IoT agreguje tyto typy událostí: 

- ProcessCreate (pouze Linux) 

- ConnectionCreate (jenom Azure RTO) 

## <a name="how-does-event-aggregation-work"></a>Jak funguje agregace událostí? 

Defender pro agenty IoT agreguje události pro období intervalu nebo časové okno. Po uplynutí časového intervalu pošle agent agregované události do cloudu Azure pro další analýzu. Agregované události jsou uloženy v paměti, dokud nejsou odesílány do cloudu Azure. 

Když Agent shromažďuje identickou událost, která je již v paměti, agent zvyšuje počet volání této konkrétní události, aby se snížilo nároky na paměť agenta. Po úspěšném časovém intervalu agregace pošle agentovi počet přístupů každého typu události, ke které došlo. Agregace událostí je jednoduše agregace počtu přístupů každého shromážděného typu události. 

## <a name="process-events"></a>Zpracování událostí 

Události procesu se momentálně podporují jenom v operačních systémech Linux. 

Události procesu jsou považovány za identické, když je *příkazový řádek* a  *ID uživatele*   identické. 

Výchozí vyrovnávací paměť pro události procesu je 32 procesů, po jejichž uplynutí se vyrovnávací paměť cyklicky a nejstarší události procesu budou zahozeny, aby se uvolnilo místo pro nové události procesu.  

## <a name="network-connection-events"></a>Události síťového připojení 

Události síťového připojení se aktuálně podporují jenom v Azure RTO. 

Události síťového připojení se považují za identické, když je *místní port*, *vzdálený port*, *Transportní protokol*, *místní adresa* a  *Vzdálená adresa* shodná. 

Výchozí vyrovnávací paměť pro události síťového připojení je 64. Do příštího cyklu shromažďování se do mezipaměti neuloží žádné nové události sítě. Dojde k zaznamenání upozornění na zvýšení velikosti mezipaměti.

## <a name="next-steps"></a>Další kroky

Podívejte se na [výstrahy zabezpečení v Defenderu pro IoT](concept-security-alerts.md).
