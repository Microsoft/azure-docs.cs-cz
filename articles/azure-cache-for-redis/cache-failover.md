---
title: Převzetí služeb při selhání a opravy – Azure Cache for Redis
description: Přečtěte si o převzetí služeb při selhání, oprava a proces aktualizace pro Azure Cache pro Redis.
author: asasine
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 6ff33bd594181aabc4fd7d55ce33f780a0d06086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74122186"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Převzetí služeb při selhání a opravy pro Azure Cache pro Redis

Chcete-li vytvářet odolné a úspěšné klientské aplikace, je důležité pochopit převzetí služeb při selhání v kontextu služby Azure Cache for Redis. Převzetí služeb při selhání může být součástí plánovaných operací správy nebo může být způsobeno neplánovanými selháními hardwaru nebo sítě. Běžné použití převzetí služeb při selhání mezipaměti nastane, když služba pro správu opravuje binární soubory Azure Cache for Redis. Tento článek popisuje, co je převzetí služeb při selhání, jak k němu dochází během opravy a jak vytvořit odolné klientské aplikace.

## <a name="what-is-a-failover"></a>Co je převzetí služeb při selhání?

Začněme s přehledem převzetí služeb při selhání pro Azure Cache pro Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Stručný přehled architektury mezipaměti

Mezipaměť je vytvořena z více virtuálních počítačů se samostatnými privátními IP adresami. Každý virtuální počítač, označovaný také jako uzel, je připojený ke sdílenému nástrojovi pro vyrovnávání zatížení s jedinou virtuální IP adresou. Každý uzel spouští proces serveru Redis a je přístupný pomocí názvu hostitele a portů Redis. Každý uzel je považován za hlavní nebo replika uzlu. Když se klientská aplikace připojí ke mezipaměti, její provoz prochází tímto vyvyčovávatelem zatížení a je automaticky směrován do hlavního uzlu.

V mezipaměti Basic je jeden uzel vždy předloha. V mezipaměti Standard nebo Premium existují dva uzly: jeden je vybrán jako hlavní a druhý je replika. Vzhledem k tomu, že mezipaměti Standard a Premium mají více uzlů, může být jeden uzel nedostupný, zatímco druhý pokračuje ve zpracování požadavků. Clusterované mezipaměti jsou tvořeny mnoha úlomky, každý s odlišnými uzly předlohy a repliky. Jeden úlomek může být dole, zatímco ostatní zůstávají k dispozici.

> [!NOTE]
> Základní mezipaměť nemá více uzlů a nenabízí smlouvu o úrovni služeb (SLA) pro její dostupnost. Základní mezipaměti se doporučují pouze pro účely vývoje a testování. Chcete-li zvýšit dostupnost, použijte mezipaměť Standard nebo Premium pro nasazení s více uzny.

### <a name="explanation-of-a-failover"></a>Vysvětlení převzetí služeb při selhání

Převzetí služeb při selhání nastane, když uzel repliky povýhá sám sebe, aby se stal hlavním uzlem a starý hlavní uzel zavře existující připojení. Po hlavní uzel vrátí nahoru, si všimne změny v rolích a sníží úrovně sebe, aby se stal replikou. Poté se připojí k nové předloze a synchronizuje data. Převzetí služeb při selhání může být plánované nebo neplánované.

*Plánované převzetí služeb při selhání* probíhá během aktualizací systému, jako jsou opravy Redis nebo upgrady operačního systému a operace správy, jako je například škálování a restartování. Vzhledem k tomu, že uzly obdrží předem oznámení o aktualizaci, mohou kooperativně zaměnit role a rychle aktualizovat vyrovnávání zatížení změny. Plánované převzetí služeb při selhání obvykle skončí za méně než 1 sekundu.

*K neplánovanému převzetí služeb při selhání* může dojít z důvodu selhání hardwaru, selhání sítě nebo jiných neočekávaných výpadků hlavního uzlu. Uzel repliky se povyšuje na hlavní server, ale proces trvá déle. Uzel repliky musí nejprve zjistit, že jeho hlavní uzel není k dispozici, než může zahájit proces převzetí služeb při selhání. Uzel repliky musí také ověřit, že tato neplánovaná chyba není přechodná nebo místní, aby se zabránilo zbytečnému převzetí služeb při selhání. Toto zpoždění při zjišťování znamená, že neplánované převzetí služeb při selhání obvykle skončí během 10 až 15 sekund.

## <a name="how-does-patching-occur"></a>Jak dochází k záplatování?

Služba Azure Cache for Redis pravidelně aktualizuje vaši mezipaměť pomocí nejnovějších funkcí a oprav platformy. Chcete-li opravit mezipaměť, služba provede následující kroky:

1. Služba pro správu vybere jeden uzel, který má být opraven.
1. Pokud je vybraný uzel hlavní uzel, odpovídající uzel repliky se kooperativně povyšuje. Tato propagační akce se považuje za plánované převzetí služeb při selhání.
1. Vybraný uzel se restartuje, aby převzal nové změny a vrátí se zpět jako uzel repliky.
1. Uzel repliky se připojí k hlavnímu uzlu a synchronizuje data.
1. Po dokončení synchronizace dat se proces opravy opakuje pro zbývající uzly.

Vzhledem k tomu, že oprava je plánované převzetí služeb při selhání, uzel repliky se rychle povyšuje, aby se stal hlavním serverem, a zahájí obsluhu požadavků a nových připojení. Základní mezipaměti nemají uzel repliky a nejsou k dispozici, dokud nebude aktualizace dokončena. Každý oddíl clusterované mezipaměti je opraven samostatně a nezavře připojení k jinému oddílu.

> [!IMPORTANT]
> Uzly jsou opraveny jeden po druhém, aby se zabránilo ztrátě dat. Základní mezipaměti budou mít ztrátu dat. Clusterované mezipaměti jsou opraveny jeden úlomek najednou.

Více mezipamětí ve stejné skupině prostředků a oblasti jsou také oprava jeden po druhém.  Mezipaměti, které jsou v různých skupinách prostředků nebo v různých oblastech, mohou být současně opraveny.

Vzhledem k tomu, že úplné synchronizace dat probíhá před opakováním procesu, je nepravděpodobné, že dojde ke ztrátě dat při použití standardní nebo premium mezipaměti. Před ztrátou dat můžete dále chránit [exportem](cache-how-to-import-export-data.md#export) dat a povolením [trvalosti](cache-how-to-premium-persistence.md).

## <a name="additional-cache-load"></a>Další zatížení mezipaměti

Kdykoli dojde k převzetí služeb při selhání, standardní a premium mezipaměti je třeba replikovat data z jednoho uzlu do druhého. Tato replikace způsobí určité zvýšení zatížení v paměti serveru i procesoru. Pokud je instance mezipaměti již silně načtena, klientské aplikace mohou zaznamenat zvýšenou latenci. V extrémních případech klientské aplikace mohou obdržet výjimky časového výpadku. Chcete-li zmírnit dopad tohoto dodatečného zatížení, [nakonfigurujte](cache-configure.md#memory-policies) `maxmemory-reserved` nastavení mezipaměti.

## <a name="how-does-a-failover-affect-my-client-application"></a>Jak převzetí služeb při selhání ovlivní klientskou aplikaci?

Počet chyb zaznamenaných klientskou aplikací závisí na tom, kolik operací bylo čekající na toto připojení v době převzetí služeb při selhání. Jakékoli připojení, které je směrováno přes uzel, který uzavřel jeho připojení se zobrazí chyby. Mnoho klientských knihoven může vyvolat různé typy chyb při přerušení připojení, včetně výjimek časového limitu, výjimek připojení nebo výjimek soketu. Počet a typ výjimek závisí na tom, kde v cestě kódu je požadavek, když mezipaměť zavře svá připojení. Například operace, která odešle požadavek, ale neobdržela odpověď, když dojde k převzetí služeb při selhání, může získat výjimku časového odčasového času. Nové požadavky na objekt uzavřeného připojení přijímat výjimky připojení, dokud dojde úspěšně k opětovnému připojení.

Většina klientských knihoven se pokusí znovu připojit ke mezipaměti, pokud jsou k tomu nakonfigurovány. Nepředvídané chyby však mohou příležitostně umístit objekty knihovny do neopravitelného stavu. Pokud chyby přetrvávají déle než předem nakonfigurované množství času, objekt připojení by měl být znovu vytvořen. V Microsoft.NET a dalších objektově orientovaných jazycích lze znovu vytvořit připojení bez restartování aplikace pomocí [vzoru Lazy\<T\> ](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Jak lze učinit aplikaci odolnou?

Vzhledem k tomu, že se nemůžete zcela vyhnout převzetí služeb při selhání, napište klientské aplikace pro odolnost proti chybám k přerušení připojení a neúspěšným požadavkům. Přestože se většina klientských knihoven automaticky znovu připojí ke koncovému bodu mezipaměti, několik z nich se pokusí opakovat neúspěšné požadavky. V závislosti na scénáři aplikace může mít smysl použít logiku opakování s backoff.

Chcete-li otestovat odolnost klientské aplikace, použijte [restartování](cache-administration.md#reboot) jako ruční aktivační událost pro přerušení připojení. Dále doporučujeme [naplánovat aktualizace](cache-administration.md#schedule-updates) mezipaměti. Informujte službu správy, aby použila opravy runtime Redis během zadaných týdenních oken. Tato okna jsou obvykle období, kdy je nízký provoz klientských aplikací, aby se zabránilo potenciálním incidentům.

### <a name="client-network-configuration-changes"></a>Změny konfigurace klientské sítě

Některé změny konfigurace sítě na straně klienta mohou vyvolat chyby "Není k dispozici žádné připojení". Tyto změny mohou zahrnovat:

- Výměna virtuální IP adresy klientské aplikace mezi pracovní a produkční sloty.
- Změna velikosti nebo počtu instancí aplikace.

Tyto změny mohou způsobit problém s připojením, který trvá méně než jednu minutu. Vaše klientská aplikace pravděpodobně ztratí připojení k jiným externím síťovým prostředkům kromě služby Azure Cache for Redis.

## <a name="next-steps"></a>Další kroky

- [Naplánujte aktualizace](cache-administration.md#schedule-updates) mezipaměti.
- Otestujte odolnost aplikace pomocí [restartování](cache-administration.md#reboot).
- [Nakonfigurujte](cache-configure.md#memory-policies) rezervace paměti a zásady.
