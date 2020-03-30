---
title: Architektura Azure Service Fabric
description: Tento článek vysvětluje architekturu Service Fabric, platformy distribuovaných systémů, která se používá k vytváření škálovatelných, spolehlivých a snadno spravovaných aplikací pro cloud.
services: service-fabric
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 972700dded1841994de9252b4aa4bbc8eaefeaf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024707"
---
# <a name="service-fabric-architecture"></a>Architektura Service Fabric

Service Fabric je postaven s vrstvenými subsystémy. Tyto subsystémy umožňují psát aplikace, které jsou:

* Vysoce dostupné
* Škálovatelné
* Zvládnutelné
* Testovatelné

Následující diagram znázorňuje hlavní subsystémy Service Fabric.

![Diagram architektury Service Fabric](media/service-fabric-architecture/service-fabric-architecture.png)

V distribuovaném systému je schopnost bezpečné komunikace mezi uzly v clusteru zásadní. Na základně zásobníku je dopravní subsystém, který poskytuje zabezpečenou komunikaci mezi uzly. Nad subsystémem přenosu leží federační podsystém, který clusteruje různé uzly do jedné entity (pojmenované clustery), aby service fabric mohl detekovat chyby, provádět volby vedoucího a poskytovat konzistentní směrování. Subsystém spolehlivosti, vrstvený nad federační subsystém, je zodpovědný za spolehlivost služeb Service Fabric prostřednictvím mechanismů, jako je replikace, správa prostředků a převzetí služeb při selhání. Federační subsystém je také základem podsystému hostování a aktivace, který spravuje životní cyklus aplikace na jednom uzlu. Subsystém managementu spravuje životní cyklus aplikací a služeb. Subsystém testovatelnosti pomáhá vývojářům aplikací testovat jejich služby prostřednictvím simulovaných chyb před a po nasazení aplikací a služeb do produkčního prostředí. Service Fabric poskytuje možnost řešit umístění služeb prostřednictvím svého komunikačního subsystému. Programovací modely aplikací vystavené vývojářům jsou vrstvené nad těmito subsystémy spolu s aplikačním modelem, který umožňuje nástroje.

## <a name="transport-subsystem"></a>Dopravní subsystém

Dopravní subsystém implementuje komunikační kanál datagramu z bodu do bodu. Tento kanál se používá pro komunikaci v clusterech prostředků infrastruktury služeb a komunikaci mezi clusterem prostředků infrastruktury služeb a klienty. Podporuje jednosměrné a požadavek odpověď komunikační vzory, který poskytuje základ pro implementaci vysílání a vícesměrového vysílání ve vrstvě federace. Dopravní subsystém zabezpečuje komunikaci pomocí certifikátů X509 nebo zabezpečení systému Windows. Tento subsystém je používán interně Service Fabric a není přímo přístupný vývojářům pro programování aplikací.

## <a name="federation-subsystem"></a>Federační podsystém

Chcete-li uvažovat o sadě uzlů v distribuovaném systému, musíte mít konzistentní zobrazení systému. Federační podsystém používá komunikační primitiva poskytovaná subsystémem přenosu a sešívá různé uzly do jednoho sjednoceného clusteru, o kterých může uvažovat. Poskytuje distribuované systémy primitiva potřebné pro ostatní subsystémy - detekce selhání, vůdce volby, a konzistentní směrování. Federační podsystém je postaven na distribuovaných hash tabulkách s 128bitovým tokenovým prostorem. Subsystém vytvoří prstencovou topologii nad uzly, přičemž každému uzlu v kroužku je přidělena podmnožina prostoru tokenu pro vlastnictví. Pro detekci selhání, vrstva používá leasingový mechanismus založený na tlukot srdce a arbitráže. Federační subsystém také zaručuje prostřednictvím složitých protokolů spojení a odjezdu, že kdykoli existuje pouze jeden vlastník tokenu. To poskytuje volby vedoucího a konzistentní záruky směrování.

## <a name="reliability-subsystem"></a>Subsystém spolehlivosti

Subsystém spolehlivosti poskytuje mechanismus, který umožňuje vysoce zpřístupnit stav služby Service Fabric pomocí *nástroje Replikátor*, *Správce převzetí služeb při selhání*a nástroje *Balancer prostředků*.

* Replikátor zajišťuje, že změny stavu v replice primární služby budou automaticky replikovány do sekundárních replik a zachová konzistenci mezi primární a sekundární replikou v sadě replik služby. Replikátor je zodpovědný za správu kvora mezi replikami v sadě replik. Spolupracuje s jednotkou převzetí služeb při selhání, aby získal seznam operací, které má být replikován, a agent pro změnu konfigurace mu poskytuje konfiguraci sady replik. Tato konfigurace označuje, které repliky operace je třeba replikovat. Service Fabric poskytuje výchozí replikátor s názvem Fabric Replicator, který může být použit rozhraní API programovacímodel, aby stav služby vysoce dostupné a spolehlivé.
* Správce převzetí služeb při selhání zajišťuje, že při přidání uzlů do clusteru nebo z něj se zatížení automaticky přerozdělí mezi dostupné uzly. Pokud uzel v clusteru selže, cluster automaticky překonfiguruje repliky služby tak, aby byla zachována dostupnost.
* Správce prostředků umístí repliky služeb napříč doménami selhání v clusteru a zajistí, že všechny jednotky s podporou převzetí služeb při selhání jsou funkční. Správce prostředků také vyvažuje prostředky služeb v rámci základního sdíleného fondu uzlů clusteru, aby bylo dosaženo optimálního rovnoměrného rozložení zatížení.

## <a name="management-subsystem"></a>Subsystém řízení

Subsystém správy poskytuje komplexní správu služeb a životního cyklu aplikací. Rutiny prostředí PowerShell a api pro správu umožňují zřídit, nasadit, opravit, upgradovat a odpojit aplikace bez ztráty dostupnosti. Subsystém správy to provádí prostřednictvím následujících služeb.

* **Správce clusteru**: Toto je primární služba, která spolupracuje se správcem převzetí služeb při selhání ze spolehlivosti a umístí aplikace na uzly na základě omezení umístění služby. Správce prostředků v subsystému převzetí služeb při selhání zajišťuje, že omezení nejsou nikdy přerušena. Správce clusteru spravuje životní cyklus aplikací od zřízení až po zrušení zřizování. Integruje se správcem stavu a zajišťuje, že dostupnost aplikací není ztracena z hlediska sémantického stavu během upgradů.
* **Správce stavu**: Tato služba umožňuje sledování stavu aplikací, služeb a entit clusteru. Entity clusteru (například uzly, oddíly služby a repliky) mohou vykazovat informace o stavu, které se pak agregují do centralizovaného úložiště stavu. Tyto informace o stavu poskytuje celkový snímek stavu bodu v čase služeb a uzlů distribuovaných mezi více uzly v clusteru, což vám umožní provést všechny potřebné nápravné akce. Pole API dotazu stavu umožňují dotaz na události stavu hlášené subsystému stavu. Pole API dotazu stavu vrátí nezpracovaná data o stavu uložená v úložišti stavu nebo agregovaná interpretovaná data stavu pro určitou entitu clusteru.
* **Úložiště obrázků**: Tato služba poskytuje ukládání a distribuci binárních souborů aplikace. Tato služba poskytuje jednoduché distribuované úložiště souborů, kde jsou aplikace odeslány a staženy z.

## <a name="hosting-subsystem"></a>Hostingový subsystém

Správce clusteru informuje hostitelský podsystém (spuštěný na každém uzlu), které služby potřebuje spravovat pro konkrétní uzel. Hostitelský podsystém pak spravuje životní cyklus aplikace v tomto uzlu. Spolupracuje s komponentami spolehlivosti a stavu, aby bylo zajištěno, že repliky jsou správně umístěny a jsou v pořádku.

## <a name="communication-subsystem"></a>Komunikační subsystém

Tento podsystém poskytuje spolehlivé zasílání zpráv v rámci clusteru a zjišťování služby prostřednictvím služby Pojmenování. Služba Pojmenování překládá názvy služeb do umístění v clusteru a umožňuje uživatelům spravovat názvy a vlastnosti služeb. Pomocí služby Naming mohou klienti bezpečně komunikovat s libovolným uzlem v clusteru, aby vyřešili název služby a načetli metadata služby. Pomocí jednoduchého rozhraní API pro pojmenování klienta mohou uživatelé service fabric vyvíjet služby a klienty schopné vyřešit aktuální umístění v síti navzdory dynamismu uzlu nebo změna velikosti clusteru.

## <a name="testability-subsystem"></a>Podsystém testovatelnosti

Testovatelnost je sada nástrojů speciálně navržených pro testování služeb postavených na service fabric. Nástroje umožňují vývojáři snadno vyvolat smysluplné chyby a spustit testovací scénáře pro cvičení a ověření mnoha stavů a přechodů, které služba zažije po celou dobu své životnosti, a to vše řízeným a bezpečným způsobem. Testovatelnost také poskytuje mechanismus pro spuštění delší testy, které lze iterate prostřednictvím různých možných selhání bez ztráty dostupnosti. To poskytuje testovací prostředí v produkčním prostředí.
