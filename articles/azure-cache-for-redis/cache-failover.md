---
title: Převzetí služeb při selhání a opravy – mezipaměť Azure pro Redis | Microsoft Docs
description: Přečtěte si o převzetí služeb při selhání, opravování a procesu aktualizace pro Azure cache pro Redis.
services: cache
author: asasine
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 305511efe86d2b241ef5014d9c3f0501cfd3fbdc
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675899"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Převzetí služeb při selhání a opravy pro Azure cache pro Redis

Vysvětlení toho, co je převzetí služeb při selhání v kontextu služby Azure cache for Redis, je důležité pro vytváření odolných a úspěšných klientských aplikací. Běžnou příčinou převzetí služeb při selhání mezipaměti pochází ze služby správy, která obsahuje opravy binárních souborů Redis. Tento článek popisuje, co je převzetí služeb při selhání, jak k nim dochází při opravách a jak vytvořit odolnou klientskou aplikaci.

## <a name="what-is-a-failover"></a>Co je převzetí služeb při selhání?

### <a name="a-quick-summary-of-our-architecture"></a>Rychlý souhrn naší architektury

Mezipaměť je tvořena několika virtuálními počítači s oddělenými privátními IP adresami. Každý virtuální počítač, označovaný také jako uzel, je připojen ke sdílenému nástroji pro vyrovnávání zatížení s jednou virtuální IP adresou. Každý uzel spouští proces serveru Redis a je přístupný prostřednictvím názvu hostitele a portů Redis. Každý uzel je buď považován za hlavní uzel, nebo jako uzel repliky. Když se klientská aplikace připojí k mezipaměti, provoz projde tímto nástrojem pro vyrovnávání zatížení a automaticky se směruje do hlavního uzlu.

V mezipaměti Basic je jeden uzel vždy hlavní. V mezipaměti Standard nebo Premium existují dva uzly, kde jedna z nich je zvolena jako hlavní a druhá je replika. Vzhledem k tomu, že mezipaměti úrovně Standard a Premium mají více uzlů, může být jeden uzel nedostupný, zatímco druhý nadále zpracovává požadavky. Clusterované mezipaměti se skládají z mnoha horizontálních oddílů, z nichž každá má odlišné hlavní uzly a uzly repliky. Jeden horizontálních oddílů může být mimo provoz, zatímco ostatní zůstávají k dispozici.

> [!NOTE]
> Základní mezipaměť nemá více uzlů a nenabízí smlouvu SLA k dostupnosti. Základní mezipaměti se doporučují jenom pro účely vývoje a testování. Pro zvýšení dostupnosti použijte mezipaměť Standard nebo Premium pro nasazení na více uzlů.

### <a name="a-failover-explained"></a>Vysvětlení převzetí služeb při selhání

K převzetí služeb při selhání dojde, když uzel repliky propaguje sám sebe, aby se stal hlavním uzlem, a starý hlavní uzel ukončí existující připojení. Poté, co se hlavní uzel vrátí zpět, si všimněte změny v rolích a snížit úroveň samotného, aby se staly replikou. Pak se připojí k novému hlavnímu serveru a synchronizuje data. Převzetí služeb při selhání může být plánované nebo neplánované.

Plánované převzetí služeb při selhání proběhne během aktualizací systému, jako jsou třeba opravy Redis nebo upgrady operačního systému a operace správy, jako je například škálování a restartování. Vzhledem k tomu, že uzly mají Pokročilá oznámení o aktualizaci, můžou družstva snadno swap rolí a rychle aktualizovat Nástroj pro vyrovnávání zatížení. Plánované převzetí služeb při selhání by mělo být dokončeno za méně než 1 sekundu.

K neplánovanému převzetí služeb při selhání může dojít z důvodu selhání hardwaru, selhání sítě nebo jiného neočekávaného výpadku pro hlavní uzel. Uzel repliky se povýší do hlavní větve, ale proces trvá déle. Uzel repliky musí nejprve zjistit, že hlavní uzel není k dispozici, než může zahájit proces převzetí služeb při selhání. Uzel repliky musí také ověřit, že neplánovaná chyba není přechodná nebo místní, aby nedošlo k převzetí služeb při selhání overeager. Toto zpoždění při detekci znamená, že neplánované převzetí služeb při selhání se obvykle dokončuje do 10 až 15 sekund

## <a name="how-does-patching-occur"></a>Jak dochází k opravám?

Služba Azure cache for Redis pravidelně provádí údržbu k aktualizaci mezipaměti s využitím nejnovějších funkcí a oprav platformy. Chcete-li opravit mezipaměť, služba bude postupovat podle následujících kroků:

1. Služba správy vybere jeden uzel, který má být opraven.
1. Pokud je vybraný uzel hlavním uzlem, jeho uzel repliky spolupracuje sama o sobě. Tato propagační akce je považována za plánované převzetí služeb při selhání.
1. Vybraný uzel se restartuje, aby se nové změny projevily, a jako uzel repliky se vrátí. Uzly repliky se připojují k hlavnímu uzlu a synchronizují data.
1. Po dokončení synchronizace dat se proces opravy opakuje u zbývajících uzlů.

Vzhledem k tomu, že se jedná o plánované převzetí služeb při selhání, uzel repliky rychle propaguje sám sebe, aby se stal hlavním serverem, a zahájí požadavky na údržbu Základní mezipaměti nemají uzel repliky a nejsou k dispozici, dokud se aktualizace nedokončí. Každý horizontálních oddílů clusterované mezipaměti se samostatně opraví a neukončí připojení k jinému horizontálních oddílů.

> [!IMPORTANT]
> Uzly jsou postupně opraveny, aby nedošlo ke ztrátě dat. U základních mezipamětí dojde ke ztrátě dat. Clusterované mezipaměti se v jednom okamžiku odhorizontálních oddílů.

Několik mezipamětí ve stejné skupině prostředků a oblasti se také postupně opraví.  Mezipaměti, které jsou v různých skupinách prostředků nebo různých oblastech, se dají opravovat současně.

Vzhledem k tomu, že se Úplná synchronizace dat stane, než se proces opakuje, může dojít ke ztrátě dat při použití mezipaměti Standard nebo Premium. Můžete se chránit před ztrátou dat pomocí [exportu](cache-how-to-import-export-data.md#export) dat a povolením [trvalosti](cache-how-to-premium-persistence.md).

### <a name="additional-cache-load"></a>Další zatížení mezipaměti

Vždy, když dojde k převzetí služeb při selhání, musí mezipaměť Standard a Premium replikovat data z jednoho uzlu na druhý. Tato replikace způsobí zvýšení zátěže v paměti serveru i procesoru. Pokud je instance mezipaměti již silně načtena, může se u klientských aplikací vyskytnout vyšší latence. V extrémních případech můžou klientské aplikace obdržet výjimky časového limitu. [Nakonfigurujte](cache-configure.md#memory-policies) nastavení `maxmemory-reserved` mezipaměti, aby se zabránilo zmírnění dopadu tohoto dodatečného zatížení.

## <a name="how-does-a-failover-impact-my-client-application"></a>Jaký vliv má převzetí služeb při selhání klientskou aplikací?

Počet chyb, které klientská aplikace uvidí, bude záviset na tom, kolik operací na tomto připojení v době převzetí služeb při selhání čekalo. Všechna připojení, která jsou směrována přes uzel, který uzavřel připojení, uvidí chyby. Mnoho klientských knihoven může vyvolat různé typy chyb, včetně výjimek časového limitu, výjimek připojení nebo výjimek soketu při přerušení připojení. Počet a typ výjimek závisí na tom, kde v cestě kódu je požadavek v případě, že mezipaměť ukončí své připojení. Například operace, která odesílá požadavek, ale neobdržela odpověď, když dojde k převzetí služeb při selhání, může obdržet výjimku časového limitu. Nové žádosti na zavřeném objektu připojení obdrží výjimky připojení, dokud nedojde k úspěšnému opětovnému připojení.

Většina klientských knihoven se pokusí znovu připojit k mezipaměti, je-li nakonfigurována tak, ale nepředvídatelné chyby mohou občas umístit objekty knihovny do neobnovitelné stavu. Pokud chyby trvají déle než předem nakonfigurovanou dobu, měl by být objekt připojení znovu vytvořen. V rozhraní .NET a dalších objektově orientovaných jazycích je možné znovu vytvořit připojení bez restartování aplikace pomocí [\>ho vzoru opožděného \<T](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="what-should-i-do-in-my-application"></a>Co mám dělat v mé aplikaci?

Vzhledem k tomu, že se převzetí služeb při selhání nedá zcela vyhnout, klientské aplikace by se měly zapisovat z důvodu odolnosti proti přerušení připojení a chybám. I když se většina klientských knihoven automaticky znovu připojí ke koncovému bodu mezipaměti, několik klientských knihoven se pokusí opakovat neúspěšné požadavky. V závislosti na scénáři použití může být logika opakování s Back-off vhodná.

Chcete-li otestovat odolnost klientské aplikace, použijte [restart](cache-administration.md#reboot) jako ruční Trigger pro přerušení připojení. Kromě toho doporučujeme [naplánovat aktualizace](cache-administration.md#schedule-updates) v mezipaměti, aby služba pro správu informovala, že se v určených týdenních oknech použijí opravy modulu runtime Redis. Tato okna se většinou volí do období, kdy jsou přenosy klientských aplikací nižší, aby nedocházelo k potenciálním incidentům.

### <a name="client-network-configuration-changes"></a>Změny konfigurace sítě klienta

Určité změny konfigurace sítě na straně klienta můžou aktivovat chyby "bez připojení k dispozici".  Výměna virtuální IP adresy klientské aplikace mezi přípravnými a produkčními sloty nebo změna velikosti/počtu instancí aplikace může způsobit potíže s připojením, které byly poslední méně než jedna minuta. Klientská aplikace bude nejspíš přijít o připojení k jiným externím síťovým prostředkům kromě Redis.

## <a name="next-steps"></a>Další kroky

- [Naplánujte aktualizace](cache-administration.md#schedule-updates) pro mezipaměť.
- Otestujte odolnost aplikace pomocí [restartování](cache-administration.md#reboot).
- [Konfigurace](cache-configure.md#memory-policies) rezervací paměti a zásad.
