---
title: Architektura infrastruktury BareMetal pro Oracle
description: Seznamte se s architekturou několika konfigurací infrastruktury BareMetal pro Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 1bdc32c14cfc8986c52a4ea916a6130ee29e6028
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559055"
---
# <a name="architecture-of-baremetal-infrastructure-for-oracle"></a>Architektura infrastruktury BareMetal pro Oracle

V tomto článku se podíváme na možnosti architektury pro BareMetal infrastrukturu pro Oracle a jednotlivé funkce, které podporuje.

## <a name="single-instance"></a>Jedna instance

Tato topologie podporuje jednu instanci Oracle Database se službou Oracle data Guard pro migraci do infrastruktury BareMetal. Podporuje použití pohotovostního uzlu pro zajištění vysoké dostupnosti a údržby.

[![Diagram znázorňující architekturu jedné instance Oracle Database se službou Oracle data Guard.](media/oracle-baremetal-architecture/single-instance-architecture.png)](media/oracle-baremetal-architecture/single-instance-architecture.png#lightbox)

## <a name="oracle-real-application-clusters-rac-one-node"></a>Cluster reálné aplikace (RAC) Oracle – jeden uzel

Tato topologie podporuje konfiguraci řadiče RAC se sdíleným clusterem úložiště a mřížky. Instance databáze běží pouze na jednom uzlu (konfigurace aktivní – pasivní).

K funkcím patří:

- Aktivní – pasivní s jedním uzlem Oracle RAC

    - Automatické převzetí služeb při selhání

    - Rychlé restartování na druhém uzlu

- Převzetí služeb při selhání a škálovatelnost v reálném čase pomocí Oracle RAC

- Údržba při provozu s nulovou výpadky

[![Diagram znázorňující architekturu nastavení aktivní – pasivního uzlu Oracle RAC One.](media/oracle-baremetal-architecture/one-node-rac-architecture.png)](media/oracle-baremetal-architecture/one-node-rac-architecture.png#lightbox)

## <a name="rac"></a>ŘADIČE

Tato topologie podporuje konfiguraci Oracle RAC se sdíleným clusterem úložiště a mřížky, zatímco víc instancí na databázi běží současně (aktivní-aktivní konfigurace).

- Výkon přidaných serverů můžete snadno škálovat prostřednictvím online zřizování. 
-  Uživatelé jsou aktivní na všech serverech a všechny servery sdílejí přístup ke stejnému Oracle Database. 
-  Všechny typy údržby databáze lze provádět online nebo v případě minimálního nebo nulového výpadku. 
- Pohotovostní systémy Oracle Active Data Guard (ADG) můžou snadno sloužit jako zkušební systémy pro duální účely. 

Tato konfigurace umožňuje testovat všechny změny v přesné kopii provozní databáze předtím, než se použije v produkčním prostředí.

> [!NOTE]
> Pokud máte v úmyslu používat službu Active Data Guard Far Sync (synchronní režim), budete muset zvážit regionální zóny, kde je tato funkce podporovaná. Pro geografické distribuované oblasti doporučujeme použití ochrany dat s asynchronním režimem.

[![Diagram znázorňující architekturu konfigurace typu aktivní-aktivní pro certifikát Oracle RAC.](media/oracle-baremetal-architecture/rac-architecture.png)](media/oracle-baremetal-architecture/rac-architecture.png#lightbox)

## <a name="next-steps"></a>Další kroky

Přečtěte si o zřizování instancí BareMetal pro úlohy Oracle.

> [!div class="nextstepaction"]
> [Zřízení infrastruktury BareMetal pro Oracle](oracle-baremetal-provision.md)

