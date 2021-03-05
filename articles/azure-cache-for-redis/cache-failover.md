---
title: Převzetí služeb při selhání a opravy – Azure Cache for Redis
description: Přečtěte si o převzetí služeb při selhání, opravování a procesu aktualizace pro Azure cache pro Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: cc7c70fa2e7131f09f621e992d537e0b120061ef
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210729"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Převzetí služeb při selhání a opravy pro Azure cache pro Redis

Aby bylo možné vytvářet odolné a úspěšné klientské aplikace, je důležité pochopit převzetí služeb při selhání v kontextu služby Azure cache pro Redis. Převzetí služeb při selhání může být součástí plánovaných operací správy nebo může být způsobeno neplánovaným hardwarem nebo selháním sítě. Běžné použití převzetí služeb při selhání mezipaměti přichází, když služba pro správu opraví mezipaměť Azure pro binární soubory Redis. Tento článek popisuje, co je převzetí služeb při selhání, jak probíhá při opravách a jak vytvořit odolnou klientskou aplikaci.

## <a name="what-is-a-failover"></a>Co je převzetí služeb při selhání?

Pojďme začít s přehledem převzetí služeb při selhání pro Azure cache pro Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Rychlý souhrn architektury mezipaměti

Mezipaměť je tvořena několika virtuálními počítači s oddělenými privátními IP adresami. Každý virtuální počítač, označovaný také jako uzel, je připojen ke sdílenému nástroji pro vyrovnávání zatížení s jednou virtuální IP adresou. Každý uzel spouští proces serveru Redis a je přístupný prostřednictvím názvu hostitele a portů Redis. Každý uzel je považován za primární nebo uzel repliky. Když se klientská aplikace připojí k mezipaměti, provoz projde tímto nástrojem pro vyrovnávání zatížení a automaticky se směruje na primární uzel.

V mezipaměti Basic je jediným uzlem vždy primární. V mezipaměti Standard nebo Premium existují dva uzly: jedna je zvolena jako primární a druhá je replika. Vzhledem k tomu, že mezipaměti úrovně Standard a Premium mají více uzlů, může být jeden uzel nedostupný, zatímco druhý nadále zpracovává požadavky. Clusterované mezipaměti se skládají z mnoha horizontálních oddílů, z nichž každá má odlišné primární uzly a uzly repliky. Jeden horizontálních oddílů může být mimo provoz, zatímco ostatní zůstávají k dispozici.

> [!NOTE]
> Základní mezipaměť nemá více uzlů a nenabízí smlouvu o úrovni služeb (SLA) pro její dostupnost. Základní mezipaměti jsou doporučeny pouze pro účely vývoje a testování. Pro zvýšení dostupnosti použijte mezipaměť Standard nebo Premium pro nasazení s více uzly.

### <a name="explanation-of-a-failover"></a>Vysvětlení převzetí služeb při selhání

K převzetí služeb při selhání dojde, když uzel repliky propaguje sám sebe, aby se stal primárním uzlem, a starý primární uzel ukončí existující připojení. Jakmile se primární uzel vrátí zpět, vystaví změnu v rolích a úroveň samotné se odvede na repliku. Pak se připojí k nové primární primární a synchronizuje data. Převzetí služeb při selhání může být plánované nebo neplánované.

*Plánované převzetí služeb při selhání* proběhne během aktualizací systému, jako jsou třeba opravy Redis nebo upgrady operačního systému a operace správy, jako je například škálování a restartování. Vzhledem k tomu, že uzly dostanou předběžné oznámení o aktualizaci, můžou snadno měnit role a rychle aktualizovat Nástroj pro vyrovnávání zatížení změny. Plánované převzetí služeb při selhání obvykle skončí za méně než 1 sekundu.

K *neplánovanému převzetí služeb při selhání* může dojít kvůli selhání hardwaru, selhání sítě nebo jiné neočekávané výpadky primárního uzlu. Uzel repliky propaguje sám sebe na primární, ale proces trvá déle. Uzel repliky musí nejprve zjistit, že primární uzel není k dispozici, než může zahájit proces převzetí služeb při selhání. Uzel repliky musí také ověřit, že tato neplánovaná chyba není přechodná nebo místní, aby nedocházelo k nepotřebnému převzetí služeb při selhání. Tato prodleva při detekci znamená, že neplánované převzetí služeb při selhání se obvykle dokončí do 10 až 15 sekund.

## <a name="how-does-patching-occur"></a>Jak dochází k opravám?

Služba Azure cache for Redis pravidelně aktualizuje mezipaměť o nejnovější funkce a opravy platformy. Chcete-li opravit mezipaměť, služba bude postupovat podle těchto kroků:

1. Služba správy vybere jeden uzel, který má být opraven.
1. Pokud je vybraný uzel primárním uzlem, odpovídající uzel repliky se družstvem samostatně propaguje. Tato propagační akce je považována za plánované převzetí služeb při selhání.
1. Vybraný uzel se restartuje, aby se nové změny projevily, a jako uzel repliky se vrátí.
1. Uzel replika se připojí k primárnímu uzlu a synchronizuje data.
1. Po dokončení synchronizace dat se proces opravy opakuje u zbývajících uzlů.

Vzhledem k tomu, že jsou opravy plánovaného převzetí služeb při selhání, uzel repliky rychle propaguje sám sebe, aby se stal primárním a počátečním žádostí o údržbu. Základní mezipaměti nemají uzel repliky a nejsou k dispozici, dokud se aktualizace nedokončí. Každý horizontálních oddílů clusterované mezipaměti se samostatně opraví a neukončí připojení k jinému horizontálních oddílů.

> [!IMPORTANT]
> Uzly jsou postupně opraveny, aby nedošlo ke ztrátě dat. U základních mezipamětí dojde ke ztrátě dat. Clusterované mezipaměti se v jednom okamžiku odhorizontálních oddílů.

Několik mezipamětí ve stejné skupině prostředků a oblasti se také postupně opraví.  Mezipaměti, které jsou v různých skupinách prostředků nebo různých oblastech, se můžou opravovat současně.

Vzhledem k tomu, že se Úplná synchronizace dat stane, než se proces opakuje, může dojít ke ztrátě dat, když použijete mezipaměť Standard nebo Premium. Můžete se chránit před ztrátou dat [exportem](cache-how-to-import-export-data.md#export) dat a povolením [trvalosti](cache-how-to-premium-persistence.md).

## <a name="additional-cache-load"></a>Další zatížení mezipaměti

Vždy, když dojde k převzetí služeb při selhání, musí mezipaměť Standard a Premium replikovat data z jednoho uzlu na druhý. Tato replikace způsobí zvýšení zátěže v paměti serveru i procesoru. Pokud je instance mezipaměti již silně načtena, může se u klientských aplikací vyskytnout větší latence. V extrémních případech můžou klientské aplikace obdržet výjimky časového limitu. Chcete-li snížit dopad tohoto dodatečného zatížení, [nakonfigurujte](cache-configure.md#memory-policies) `maxmemory-reserved` nastavení mezipaměti.

## <a name="how-does-a-failover-affect-my-client-application"></a>Vliv převzetí služeb při selhání na klientskou aplikaci

Počet chyb, které klientská aplikace zobrazuje, závisí na tom, kolik operací čekalo na tomto připojení v době převzetí služeb při selhání. Všechna připojení, která jsou směrována přes uzel, který zavřel připojení, uvidí chyby. Mnoho klientských knihoven může při přerušení připojení vyvolat různé typy chyb, včetně výjimek vypršení platnosti, výjimek připojení nebo výjimek soketu. Počet a typ výjimek závisí na tom, kde v cestě kódu je požadavek v případě, že mezipaměť ukončí své připojení. Například operace, která odesílá požadavek, ale neobdržela odpověď, když dojde k převzetí služeb při selhání, může obdržet výjimku časového limitu. Nové požadavky na uzavřený objekt připojení obdrží výjimky připojení, dokud se znovu neproběhne opětovné připojení.

Většina klientských knihoven se pokusí o opětovné připojení k mezipaměti, pokud jsou nakonfigurované. Avšak nepředvídatelné chyby mohou občas umístit objekty knihovny do neobnovitelné stavu. Pokud jsou chyby trvalé po dobu delší, než je předem nakonfigurované množství času, je nutné znovu vytvořit objekt připojení. V Microsoft.NET a dalších objektově orientovaných jazycích je možné znovu vytvořit připojení bez restartování aplikace, a to pomocí [opožděného \<T\> vzoru](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Návody nastavit moji aplikaci jako odolnou?

Vzhledem k tomu, že se nedaří převzetí služeb při selhání úplně zabránit, napište klientské aplikace tak, aby odolné proti chybám připojení a neúspěšné I když se většina klientských knihoven automaticky znovu připojí ke koncovému bodu mezipaměti, několik z nich se pokusí opakovat neúspěšné požadavky. V závislosti na scénáři aplikace může být vhodné použít logiku opakování v omezení rychlosti.

Chcete-li otestovat odolnost klientské aplikace, použijte [restart](cache-administration.md#reboot) jako ruční Trigger pro přerušení připojení. Kromě toho doporučujeme [naplánovat aktualizace](cache-administration.md#schedule-updates) v mezipaměti. Sdělte službě správy pokyn, aby během určených týdenních oken použili opravy modulu runtime Redis. Tato okna jsou obvykle období, kdy jsou přenosy klientských aplikací nízké, aby nedocházelo k potenciálním incidentům.

### <a name="can-i-be-notified-in-advance-of-a-planned-maintenance"></a>Můžu se mi předem informovat o plánované údržbě?

Azure cache for Redis nyní publikuje oznámení na kanálu pro publikování a odběr s názvem [AzureRedisEvents](https://github.com/Azure/AzureCacheForRedis/blob/main/AzureRedisEvents.md) přibližně 30 sekund před plánovanými aktualizacemi. Jedná se o běhová oznámení, která jsou vytvořená hlavně pro aplikace, které mohou používat nárazníky okruhů k obcházení příkazů mezipaměti nebo vyrovnávací paměti, například během plánovaných aktualizací. Nejedná se o mechanismus, který vám může předem sdělit dny nebo hodiny.

### <a name="client-network-configuration-changes"></a>Klientská síť – změny konfigurace

Určitá síť na straně klienta – změny konfigurace můžou aktivovat chyby "bez připojení k dispozici". Tyto změny mohou zahrnovat:

- Výměna virtuální IP adresy klientské aplikace mezi pracovními a provozními sloty
- Změna velikosti nebo počtu instancí aplikace

Tyto změny můžou způsobit problémy s připojením, které trvají méně než jednu minutu. Klientská aplikace bude pravděpodobně přijít o připojení k jiným externím síťovým prostředkům kromě služby Azure cache for Redis.

## <a name="next-steps"></a>Další kroky

- [Naplánujte aktualizace](cache-administration.md#schedule-updates) pro mezipaměť.
- Otestujte odolnost aplikace pomocí [restartování](cache-administration.md#reboot).
- [Konfigurace](cache-configure.md#memory-policies) rezervací paměti a zásad.
