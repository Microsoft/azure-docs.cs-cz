---
title: Architektura služby Azure Service Fabric | Dokumentace Microsoftu
description: Service Fabric je platforma distribuovaných systémů sloužící k sestavení škálovatelné, spolehlivé a snadno spravovatelných aplikací pro cloud. Tento článek popisuje architekturu Service Fabric.
services: service-fabric
documentationcenter: .net
author: rishirsinha
manager: chackdan
editor: rishirsinha
ms.assetid: 6b554243-70cb-4c22-9b28-1a8b4703f45e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/12/2017
ms.author: rsinha
ms.openlocfilehash: a1e68e2e39ea6f1c8cf8669e2e02d8dacaf0f284
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097840"
---
# <a name="service-fabric-architecture"></a>Architektura Service Fabric
Service Fabric je vytvořené s vrstvami subsystémů. Tyto subsystémy umožňují také napsat aplikace, které jsou:

* S vysokou dostupností
* Škálovatelné
* Spravovat
* Možností intenzivního testování

Následující diagram znázorňuje hlavní subsystémy Service Fabric.

![Diagram architektury Service Fabric](media/service-fabric-architecture/service-fabric-architecture.png)

V distribuovaném systému možnost zabezpečeně komunikovat mezi uzly v clusteru je zásadní. Základní zásobníku je subsystém přenosu, který poskytuje zabezpečenou komunikaci mezi uzly. Nad přenos je subsystém subsystému federace, který clusterů různé uzly do jedné entity (s názvem clustery) tak, aby Service Fabric rozpoznávají poruchy, volba lídra nebo provádět a poskytují konzistentní směrování. Podsystém spolehlivosti jako vrstva nad podsystém federace je zodpovědná za spolehlivosti služeb Service Fabric prostřednictvím mechanismy, jako je replikace, Správa prostředků a převzetí služeb při selhání. Subsystém federation také základem hostování a aktivace subsystém, který spravuje životní cyklus aplikace v jednom uzlu. Subsystém správy slouží ke správě životního cyklu aplikací a služeb. Subsystém testovatelnosti pomáhá vývojářům aplikaci otestovat své služby simulované chyby před a po nasazení aplikace a služby do produkčního prostředí. Service Fabric nabízí možnost přeložit umístění služby prostřednictvím jeho komunikačním subsystému. Aplikačních programovacích modelů, které jsou vystaveny pro vývojáře pro jsou rozloženy do vrstev nad rámec těchto subsystémů spolu s modelem aplikace umožňující nástroje.

## <a name="transport-subsystem"></a>Subsystém přenosu
Subsystém přenosu implementuje kanálu datagramu komunikace typu point-to-point. Tento kanál se používá pro komunikaci v rámci clustery service fabric a komunikace mezi klienty a service fabric cluster. Podporuje jednosměrná a komunikačních schémat požadavek odpověď, která poskytuje základ pro implementaci vysílání a vícesměrové vysílání ve vrstvě federace. Subsystém přenos zabezpečuje komunikaci pomocí X509 certifikáty nebo zabezpečení Windows. Tato subsystému používá se interně pomocí Service Fabric a není přímo přístupný pro vývojáře pro programování aplikací.

## <a name="federation-subsystem"></a>Subsystém federace
Aby bylo možné odůvodnitelný sada uzlů v distribuovaném systému, musíte mít konzistentní zobrazení systému. Podsystém federace používá komunikaci primitivy poskytované subsystému přenosu a spojí různé uzly do jednoho clusteru unified, který můžete odůvodnitelný. Poskytuje primitivy distribuovaných systémů vyžadované ostatních subsystémů – detekce selhání, volba lídra nebo a konzistentní směrování. Subsystém federace je nástavbou distribuované zatřiďovacích tabulek s mezerou token 128 bitů. Subsystém vytvoří kruhová topologie více uzlů s každý uzel v okruhu přidělenou podmnožinu token místa pro vlastnictví. Pro zjištění selhání vrstvy používá mechanismus "pronájmu", na základě srdce signálu a ustanovení o rozhodčím řízení. Subsystém federace zaručuje také prostřednictvím komplikované spojení a odeslání protokolů, které existuje jenom jeden vlastník tokenu v každém okamžiku. Tímto způsobem, volba lídra nebo a konzistentní směrování záruky.

## <a name="reliability-subsystem"></a>Subsystém spolehlivosti
Subsystém spolehlivost poskytuje mechanismus pro zajištění vysoké dostupnosti prostřednictvím stav služby Service Fabric *Replikátor*, *Správce převzetí služeb při selhání*, a *prostředků Nástroje pro vyrovnávání*.

* Replikátor zajistí, že změny stavu v v primární replice služby budou automaticky replikovat do sekundární repliky, zachovat konzistenci mezi primární a sekundární repliky sady replik služby. Replikátor zodpovídá za správu kvora mezi repliky sady replik. Komunikuje se službou se jednotka převzetí služeb při selhání získat seznam operací replikace a agent Rekonfigurace poskytne mu konfiguraci sady replik. Tato konfigurace označuje replik, které operace disky se musí replikovat. Service Fabric poskytuje výchozí Replikátor volá Replikátor prostředků infrastruktury, kterým chcete-li služba stavu, vysoce dostupných a spolehlivých lze pomocí rozhraní API pro programovací model.
* Správce převzetí služeb při selhání zajistí, že při uzly jsou přidán či odebrán z clusteru, zatížení automaticky rozloží mezi uzly k dispozici. Pokud selže jeden uzel v clusteru, cluster automaticky překonfiguruje repliky služby dostupnost.
* Resource Manager umístí služby replik napříč domény selhání v clusteru a zajišťuje, že jsou funkční všechny jednotky převzetí služeb při selhání. Prostředky služeb Resource Manageru také vyrovnává napříč základní sdílený fond uzlů clusteru, abyste dosáhli optimálního jednotné zátěže mezi nimi.

## <a name="management-subsystem"></a>Subsystém správy
Subsystém správu poskytuje služby začátku do konce a správa životního cyklu aplikací. Rutiny prostředí PowerShell a rozhraní API pro správu umožňují zřizovat, nasazovat, opravit, upgradovat ani zrušit zřízení aplikace bez ztráty dostupnosti. Subsystém správu to provádí prostřednictvím následujících služeb.

* **Správce clusteru**: Toto je primární služba, která komunikuje pomocí převzetí služeb při selhání správce z spolehlivost k umístění aplikace na uzly podle omezení umístění služby. Správce prostředků v subsystému převzetí služeb při selhání zajišťuje omezení nejsou nikdy nefunkční. Správce clusteru slouží ke správě životního cyklu aplikací od zřízení ke zrušení zřízení. Se integruje s nástroje health manager k zajištění, že dostupnost aplikace není během upgradu ke ztrátě z hlediska sémantické stavu.
* **Health Manager**: Tato služba umožňuje monitorování stavu aplikací, služeb a entity clusteru. Informace o stavu, který je pak agregují do centralizované health store může hlásit clusteru entity (například uzly, služba oddíly a repliky). Informace o tomto stavu poskytuje snímku celkové v daném okamžiku stavu služeb a uzlů distribuované napříč několika uzly v clusteru, můžete o všechny potřebné opravné akce. Stav dotazu, že rozhraní API umožňují dotazování událostí stavu hlášené stavu subsystému. Stav dotaz, který rozhraní API vrátí nezpracované stavu data uložená v úložišti health nebo agregovaná, interpretovat data o stavu pro konkrétní entitu.
* **Image Store**: Tato služba poskytuje úložiště a distribuci binární soubory aplikace. Tato služba poskytuje jednoduché distribuované úložiště souborů ve kterém se aplikace odesílat a stahovat z.

## <a name="hosting-subsystem"></a>Hostování v subsystému
Správce clusteru informuje hostování subsystém (spuštění na každém uzlu) služby, které potřebuje ke správě pro jednotlivé uzly. Hostování subsystému pak slouží ke správě životního cyklu aplikací v tomto uzlu. Komunikuje se službou spolehlivost a stavu součásti, které chcete zajistit, aby byly správně umístěny repliky a jsou v pořádku.

## <a name="communication-subsystem"></a>Komunikační subsystém
Tato subsystému poskytuje spolehlivé zasílání zpráv v rámci clusteru a služby zjišťování ve službě pojmenování. Služba pojmenování překládá názvy služeb do umístění v clusteru a umožňuje uživatelům spravovat názvy služeb a vlastnosti. Použijte službu pojmenování, klienti mohou bezpečně komunikovat s libovolný uzel v clusteru k přeložení názvu služby a načíst metadata služby. Pomocí jednoduchých rozhraní API klienta pojmenování, uživatelé Service Fabric můžete vyvíjet, služeb a klientů podporujících řešení aktuální umístění v síti bez ohledu na uzlu dynamiky nebo znovu velikosti clusteru.

## <a name="testability-subsystem"></a>Subsystém testovatelnosti
Testovatelnost je sada nástrojů, které jsou vytvořené speciálně pro testování služby postavené na platformě Service Fabric. Nástroje umožňují vývojář jednoduše vyvolat smysluplné chyby a spusťte scénáře testování výkonu a ověření řadu stavy a přechody, která službu budou moct používat v průběhu svého životního cyklu, všechny řízeným a bezpečným způsobem. Testovatelnost také poskytuje mechanismus pro delší zkoušky, které může iterovat v projít různé možné chyby bez ztráty dostupnosti. To vám poskytne testů v produkčním prostředí.

