---
title: Architektura Service Fabric Azure
description: Tento článek popisuje architekturu Service Fabric, platformu distribuovaných systémů, která se používá k vytváření škálovatelných, spolehlivých a snadno spravovaných aplikací pro Cloud.
services: service-fabric
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 972700dded1841994de9252b4aa4bbc8eaefeaf8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76024707"
---
# <a name="service-fabric-architecture"></a>Architektura Service Fabric

Service Fabric je sestavený pomocí vrstvených subsystémů. Tyto subsystémy umožňují psát aplikace, které jsou:

* Vysoce dostupné
* Škálovatelné
* Spravovatelné
* Testovatelné

Následující diagram znázorňuje hlavní subsystémy Service Fabric.

![Diagram architektury Service Fabric](media/service-fabric-architecture/service-fabric-architecture.png)

V distribuovaném systému je možnost zabezpečená komunikace mezi uzly v clusteru zásadní. Základní část zásobníku je přenosový podsystém, který zajišťuje zabezpečenou komunikaci mezi uzly. Nad přenosový subsystém se nachází v rámci federačního subsystému, který clusteruje různé uzly do jedné entity (pojmenované clustery), aby Service Fabric mohl detekovat chyby, provádět volby vedoucího procesu a zajišťovat konzistentní směrování. Podsystém spolehlivosti, vrstvený nad federačním subsystému, zodpovídá za spolehlivost Service Fabricch služeb prostřednictvím mechanismů, jako je replikace, Správa prostředků a převzetí služeb při selhání. Subsystém federace také představuje hostitelský a aktivační subsystém, který spravuje životní cyklus aplikace na jednom uzlu. Subsystém správy spravuje životní cyklus aplikací a služeb. Podsystém testování pomáhá vývojářům aplikací testovat své služby prostřednictvím simulovaných chyb před a po nasazení aplikací a služeb do produkčního prostředí. Service Fabric poskytuje možnost přeložit umístění služby prostřednictvím svého komunikačního subsystému. Aplikační programovací modely vystavené vývojářům jsou vrstveny nad těmito subsystémy spolu s modelem aplikace, aby bylo možné nástroje povolit.

## <a name="transport-subsystem"></a>Subsystém přenosu

Transportní subsystém implementuje komunikační kanál datagramu typu Point-to-Point. Tento kanál se používá ke komunikaci v rámci clusterů Service Fabric a komunikaci mezi clusterem Service Fabric a klienty. Podporuje jednosměrné a komunikační modely požadavků a odpovědí, které poskytují základ pro implementaci všesměrového a vícesměrového vysílání ve federační vrstvě. Transportní subsystém zabezpečuje komunikaci pomocí certifikátů x509 nebo zabezpečení systému Windows. Tento podsystém používá interní Service Fabric a není přímo přístupný vývojářům při programování aplikací.

## <a name="federation-subsystem"></a>Subsystém federace

Vzhledem k tomu, že se jedná o sadu uzlů v distribuovaném systému, je nutné mít konzistentní pohled na systém. Subsystém federace používá komunikační primitivy poskytované transportním systémem a sestaví různé uzly do jediného sjednoceného clusteru, na který může mít důvod. Poskytuje základní systémy distribuovaných systémů, které vyžadují jiné subsystémy – detekce selhání, volby vedoucího procesu a konzistentní směrování. Subsystém federace je postaven nad distribuovanými zatřiďovacími tabulkami s 128m prostorem tokenu. Podsystém vytvoří v uzlech cyklickou topologii, přičemž každý uzel v okruhu přiděluje podmnožinu prostoru tokenu pro vlastnictví. V případě detekce selhání používá vrstva Leasingový mechanizmus na základě prezenčního oznámení a rozhodčího řízení. Subsystém federace také garantuje prostřednictvím komplikovaných protokolů připojení a odchodu, že v jednom okamžiku existuje pouze jeden vlastník tokenu. To poskytuje volbu vedoucího a konzistentní záruky směrování.

## <a name="reliability-subsystem"></a>Subsystém spolehlivosti

Subsystém pro spolehlivost poskytuje mechanismus pro zajištění vysoké dostupnosti stavu Service Fabric služby prostřednictvím použití *replikátoru*, *správce převzetí služeb při selhání*a *Nástroje pro vyrovnávání prostředků*.

* Replikátor zajistí, že se změny stavu v primární replice služby automaticky replikují do sekundárních replik, což zachovává konzistenci mezi primárními a sekundárními replikami v sadě replik služby. Replikátor zodpovídá za správu kvora mezi replikami v sadě replik. Komunikuje s jednotkou převzetí služeb při selhání, aby získala seznam operací, které se mají replikovat, a Agent rekonfigurace ho poskytne konfiguraci sady replik. Tato konfigurace indikuje, které repliky se operace musí replikovat. Service Fabric poskytuje výchozí Replikátor nazvaný Replikátor Fabric, který může používat rozhraní API pro programování modelů k zajištění vysoké dostupnosti a spolehlivého stavu služby.
* Správce převzetí služeb při selhání zajistí, že při přidání nebo odebrání uzlů z clusteru se zatížení automaticky rozšíří napříč dostupnými uzly. Pokud uzel v clusteru selže, cluster automaticky překonfiguruje repliky služby na zachování dostupnosti.
* Správce prostředků umístí repliky služby napříč doménami selhání v clusteru a zajistí fungování všech jednotek převzetí služeb při selhání. Správce prostředků taky vyrovnává prostředky služby v rámci nadřazeného sdíleného fondu uzlů clusteru, aby se dosáhlo optimálního rovnoměrného rozložení zátěže.

## <a name="management-subsystem"></a>Subsystém správy

Subsystém správy poskytuje ucelenou správu životního cyklu služeb a aplikací. Rutiny prostředí PowerShell a rozhraní API pro správu umožňují zřídit, nasadit, opravovat, upgradovat a zrušit zřízení aplikací bez ztráty dostupnosti. Subsystém pro správu provádí tyto služby prostřednictvím následujících služeb.

* **Správce clusterů**: Jedná se o primární službu, která spolupracuje se správce převzetí služeb při selháníou spolehlivostí pro umístění aplikací na uzlech na základě omezení umístění služby. Správce prostředků v subsystému převzetí služeb při selhání zajišťuje, že omezení nebudou nikdy přerušena. Správce clusteru spravuje životní cyklus aplikací od zřízení až po zrušení zřízení. Integruje se se správcem stavu, aby se zajistilo, že nedojde ke ztrátě dostupnosti aplikace z hlediska sémantického stavu během upgradů.
* **Správce stavu**: Tato služba umožňuje sledování stavu aplikací, služeb a entit clusteru. Entity clusteru (například uzly, oddíly služeb a repliky) mohou hlásit informace o stavu, které jsou poté agregovány do centralizovaného Health Store. Tyto informace o stavu poskytují Celkový časový snímek služeb a uzlů distribuovaných napříč několika uzly v clusteru, což vám umožní provádět potřebné opravné akce. Rozhraní API pro dotazování stavu vám umožní dotazovat se na události stavu hlášené do subsystému stavu. Rozhraní API pro dotazování na stav vrací nezpracovaná data o stavu uložená v Health Store nebo agregovaná a interpretovaná data o stavu konkrétní entity clusteru.
* **Image Store**: Tato služba poskytuje úložiště a distribuci binárních souborů aplikace. Tato služba poskytuje jednoduché distribuované úložiště souborů, ve kterém jsou aplikace nahrané a stažené z.

## <a name="hosting-subsystem"></a>Hostující podsystém

Správce clusterů informuje hostující subsystém (spuštěný v jednotlivých uzlech), které služby potřebuje ke správě pro určitý uzel. Hostující subsystém pak spravuje životní cyklus aplikace v tomto uzlu. Komunikuje se součástmi pro spolehlivost a stav, aby bylo zajištěno, že jsou repliky správně umístěny a jsou v pořádku.

## <a name="communication-subsystem"></a>Komunikační subsystém

Tento podsystém poskytuje spolehlivé zasílání zpráv v rámci clusteru a zjišťování služby prostřednictvím názvové služby. Služba pojmenování překládá názvy služeb na umístění v clusteru a umožňuje uživatelům spravovat názvy a vlastnosti služby. Pomocí názvové služby můžou klienti bezpečně komunikovat s jakýmkoli uzlem v clusteru, aby se přeložil název služby a načetla se metadata služby. Pomocí jednoduchého klientského rozhraní API pro pojmenování může uživatelé Service Fabric vyvíjet služby a klienty, kteří budou schopni vyřešit aktuální síťové umístění navzdory dynamismům uzlů nebo změně velikosti clusteru.

## <a name="testability-subsystem"></a>Podsystém testování

Testování je sada nástrojů speciálně navržených pro testování služeb postavených na Service Fabric. Nástroje umožňují vývojářům snadno vyvolat smysluplné chyby a spouštět testovací scénáře pro výkon a ověření řady stavů a přechodů, ke kterým bude mít služba po celou dobu životnosti, a to vše kontrolovaným a bezpečným způsobem. Možnost testování také poskytuje mechanismus pro spouštění delších testů, které mohou iterovat v různých možných selháních, aniž by došlo ke ztrátě dostupnosti. To vám poskytne prostředí pro testování v produkčním prostředí.
