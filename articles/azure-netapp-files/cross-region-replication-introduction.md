---
title: Replikace Azure NetApp Filesch svazků mezi oblastmi | Microsoft Docs
description: Popisuje, co Azure NetApp Files replikace mezi oblastmi, podporované páry oblastí, cíle na úrovni služby, odolnost dat a nákladový model.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: b-juche
ms.openlocfilehash: 7c78d5fa89d6d4f3c73bdd1fe9b6d2fdae97c011
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937470"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Replikace Azure NetApp Files svazků mezi oblastmi

Funkce replikace Azure NetApp Files zajišťuje ochranu dat prostřednictvím replikace svazků mezi oblastmi. Data můžete asynchronně replikovat z Azure NetApp Filesho svazku (zdroje) v jedné oblasti do jiného Azure NetApp Filesho svazku (cíle) v jiné oblasti.  Tato funkce umožňuje převzetí služeb při selhání vaší důležité aplikace v případě výpadku nebo havárie v rámci oblasti.

> [!IMPORTANT]
> Funkce replikace mezi oblastmi je aktuálně ve verzi Public Preview. Pro přístup k této funkci musíte odeslat žádost pořadníku Azure NetApp Files prostřednictvím stránky pro [odeslání pořadníku replikace mezi oblastmi](https://aka.ms/anfcrrpreviewsignup). Než použijete funkci replikace mezi oblastmi, počkejte na oficiální e-mail s potvrzením od Azure NetApp Files týmu.

## <a name="supported-region-pairs"></a>Podporované páry oblastí

Replikace svazku Azure NetApp Files je aktuálně dostupná v následujících dvojicích pevné oblasti:  

* USA – západ a USA – východ
* USA – západ 2 a USA – východ 
* USA (střed) – jih a USA – střed 
* USA (střed) – jih a USA – východ
* USA (střed) – jih a USA – východ 2 
* USA – východ 2 a USA – střed 
* Austrálie – východ a Austrálie – jihovýchod
* Kanada – střed a Kanada – východ
* Střed Indie a Jižní Indie
* Německo – středozápad a Německo – sever
* Japonsko – východ a Japonsko – západ
* Severní Evropa a Západní Evropa
* Jihovýchodní Asie a Austrálie – východ
* Velká Británie – jih a Německo – středozápad
* Velká Británie – jih a Velká Británie – západ

## <a name="service-level-objectives"></a>Cíle na úrovni služby

Cíle bodu obnovení (RPO) nebo maximální povolená ztráta dat jsou definovány dvakrát jako plán replikace.  Skutečný zjištěný bod obnovení se může lišit v závislosti na faktorech, jako je celková velikost datové sady spolu s frekvencí změny, procentuální hodnota přepsání dat a šířka pásma replikace dostupná pro přenos.   

* Pro plán replikace 10 minut je maximální počet RPO 20 minut.  
* Pro hodinový plán replikace je maximální cíl bodu obnovení dvě hodiny.  
* U denního plánu replikace je maximální cíl bodu obnovení dva dny.  

Plánovaná doba obnovení (RTO) nebo maximální množství nepřípustných výpadků obchodních aplikací je určena faktory při zavedení aplikace a poskytnutí přístupu k datům v druhé lokalitě. Část úložiště RTO pro přerušení vztahu partnerských vztahů za účelem aktivace cílového svazku a poskytnutí přístupu k datům pro čtení a zápis v druhé lokalitě by se měla dokončit během jedné minuty.

## <a name="cost-model-for-cross-region-replication"></a>Nákladový model pro replikaci mezi oblastmi  

Při Azure NetApp Files replikace mezi oblastmi platíte jenom za množství dat, která se replikují. Neúčtují se žádné poplatky za nastavení ani minimální poplatky za použití. Cena za replikaci vychází z četnosti replikace a oblasti *cílového* svazku, kterou jste si zvolili při počáteční konfiguraci replikace. Další informace najdete na stránce s [cenami Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) .  

Poplatek za kapacitu úložiště pro běžný Azure NetApp Files se vztahuje na cílový svazek replikace (označovaný také jako svazek *ochrany dat* ). 

### <a name="pricing-examples"></a>Příklady cen

Částka replikace mezi oblastmi fakturovaná v měsíci vychází z množství dat replikovaných prostřednictvím funkce replikace mezi oblastmi během daného měsíce. Množství replikovaných dat se měří v GiB. Představuje součet dat replikovaných mezi dvěma oblastmi při všech pravidelných replikacích ze zdrojových svazků na cílové svazky a při všech resynchronizovaných replikaci z cílových svazků na zdrojové svazky.

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>Příklad 1: základní replikace a přírůstkové replikace v měsíci 1

Předpokládejme následující situace:

* *Zdrojový* svazek pochází z úrovně služby Azure NetApp Files *Premium* . Má velikost kvóty 1000 GiB a velikost spotřebovaného svazku je 500 GiB na začátku prvního dne v měsíci. Svazek je v oblasti *USA (střed) – jih* .
* *Cílový* svazek pochází z úrovně služby Azure NetApp Files *Standard* . Je v oblasti *USA – východ 2* .
* Nakonfigurovali jste *každou hodinovou* replikaci mezi dvěma svazky nad rámec. Proto je cena replikace $0,12 na GiB.
* V zájmu jednoduchosti předpokládáme, že váš zdrojový svazek má při každé hodinu konstantu 0,5-GiB dat, ale celková velikost spotřebovaného objemu neroste (zůstane v 500 GiB). 

Po počáteční instalaci proběhne replikace standardních hodnot okamžitě.  

* Počet replikovaných dat během replikace směrného plánu: `500 GiB`
* Poplatky za replikaci standardních hodnot: `500 GiB * $0.12 = $60`

Po základní replikaci se replikují pouze změněné bloky. Proto se za každou hodinu v následných přírůstkových replikacích replikuje jenom 0,5 GiB dat.

* Součet množství dat replikovaných v rámci přírůstkových replikací po dobu 30 dnů: `0.5 GiB * 24 hours * 30 days = 360 GiB`
* Poplatky za přírůstkovou replikaci: `360 GiB * $0.12 = $43.2`

Do konce měsíce 1 je celkový poplatek za replikaci mezi jednotlivými oblastmi následujícím způsobem:  

*  Celkový poplatek za replikaci mezi jednotlivými oblastmi z měsíce 1: `$60 + $43.2 = $103.2`

Pro cílový svazek se vztahuje standardní poplatek za kapacitu úložiště Azure NetApp Files. Cílový svazek ale může používat vrstvu úložiště, která se liší od zdrojové vrstvy (a je levnější než).

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>Příklad 2: přírůstkové replikace a resynchronizace replikace v měsíci 2  

Předpokládejme, že máte zdrojový svazek, cílový svazek a vztah replikace mezi dvěma nastaveními, jak je popsáno v příkladu 1. Po dobu 29 dnů druhého měsíce (30 dní měsíčně) se hodinová replikace objevila podle očekávání.

* Součet množství dat replikovaných v rámci přírůstkových replikací po dobu 29 dní: `0.5 GiB * 24 hours * 29 days = 348 GiB`

Předpokládat, že poslední den v měsíci došlo k neplánovanému výpadku ve zdrojové oblasti a převzali jste služby při selhání na cílový svazek. Po 2 hodinách se zdrojová oblast obnovila a provedli jste novou synchronizaci replikace z cílového svazku ke zdrojovému svazku. Během 2 hodin 0,8 GiB změny dat v cílovém svazku a je potřeba je znovu synchronizovat se zdrojem.

* Součet množství dat replikovaných v rámci pravidelných replikací po dobu 22 hodin za poslední den: `0.5 GiB * 22 hours = 11 GiB`
* Počet replikovaných dat při jedné nové synchronizaci replikace: `0.8 GiB`

Proto na konci měsíce 2 platí následující celkové poplatky za replikaci mezi oblastmi:  

* Celkový poplatek za replikaci mezi jednotlivými oblastmi z měsíce 2: `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

Na cílový svazek se vztahuje normální Azure NetApp Files poplatek za kapacitu úložiště pro měsíc 2.

## <a name="next-steps"></a>Další kroky
* [Požadavky a předpoklady pro použití replikace mezi oblastmi](cross-region-replication-requirements-considerations.md)
* [Vytvoření replikace svazku](cross-region-replication-create-peering.md)
* [Zobrazení stavu vztahu replikace](cross-region-replication-display-health-status.md)
* [Správa zotavení po havárii](cross-region-replication-manage-disaster-recovery.md)
* [Metriky replikace svazků](azure-netapp-files-metrics.md#replication)
* [Odstranění svazků nebo replikací svazků](cross-region-replication-delete.md)
* [Řešení potíží s replikací mezi oblastmi](troubleshoot-cross-region-replication.md)


