---
title: Vysoká dostupnost pro Azure cache pro Redis
description: Informace o funkcích a možnostech vysoké dostupnosti služby Azure cache pro Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: yegu
ms.openlocfilehash: e44aed1415f85bf4ea597eac6720207301946b97
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076907"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Vysoká dostupnost pro Azure cache pro Redis

Mezipaměť Azure pro Redis má vestavěnou vysokou dostupnost. Cílem své architektury vysoké dostupnosti je zajistit funkčnost spravované instance Redis i v případě, že na její základní virtuální počítače má vliv plánovaná nebo neplánovaná výpadky. Nabízí mnohem větší procentní sazbu, než je dosažitelná hostováním Redis na jednom virtuálním počítači.

Azure cache pro Redis implementuje vysokou dostupnost pomocí několika virtuálních počítačů nazývaných *uzly* pro mezipaměť. Tyto uzly nakonfiguruje tak, aby replikace dat a převzetí služeb při selhání probíhalo v koordinovaných způsobech. Také orchestruje operace údržby, jako je Redis software patching. K dispozici jsou různé možnosti vysoké dostupnosti na úrovních Standard, Premium a Enterprise:

| Možnost | Popis | Dostupnost | Standard | Premium | Enterprise |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Standardní replikace](#standard-replication)| Konfigurace replikovaných dvou uzlů v jednom datovém centru nebo zóně dostupnosti (AZ) s automatickým převzetím služeb při selhání | 99,9 % |✔|✔|-|
| [Podnikový cluster](#enterprise-cluster) | Propojené instance mezipaměti ve dvou oblastech s automatickým převzetím služeb při selhání | 99,9 % |-|-|✔|
| [Zónová redundance](#zone-redundancy) | Konfigurace replikovaného více uzlů v rámci AZs s automatickým převzetím služeb při selhání | 99,95% (standardní replikace), 99,99% (podnikový cluster) |-|✔|✔|
| [Geografická replikace](#geo-replication) | Propojené instance mezipaměti ve dvou oblastech s převzetím služeb při selhání řízených uživatelem | 99,9% (pro jednu oblast) |-|✔|-|

## <a name="standard-replication"></a>Standardní replikace

Mezipaměť Azure pro Redis v úrovni Standard nebo Premium běží ve výchozím nastavení na páru Redis serverů. Tyto dva servery jsou hostovány na vyhrazených virtuálních počítačích. Open Source Redis umožňuje pouze jednomu serveru zpracovávat požadavky na zápis dat. Tento server je *primárním* uzlem, zatímco druhá *replika* . Až zřídí uzly serveru, Azure cache for Redis přiřadí role primární a repliky. Primární uzel obvykle zodpovídá za obsluhu zápisu i žádosti o čtení od klientů Redis. U operace zápisu se potvrdí nový klíč a aktualizuje se klíč do své interní paměti a okamžitě se odpoví na klienta. Přepošle operaci do repliky asynchronně.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Nastavení replikace dat":::
   
>[!NOTE]
>V normálním případě klient Redis komunikuje s primárním uzlem v mezipaměti Redis pro všechny požadavky na čtení a zápis. Některé klienty Redis je možné nakonfigurovat tak, aby je četli z uzlu repliky.
>
>

Pokud není primární uzel v mezipaměti Redis k dispozici, replika se bude povýšit na novou primární hodnotu automaticky. Tento proces se označuje jako *převzetí služeb při selhání* . Replika bude po celou dobu trvat, než se převezme v případě, že se primární uzel rychle obnoví. Když dojde k převzetí služeb při selhání, Azure cache for Redis zřídí nový virtuální počítač a připojí ho k mezipaměti jako uzel repliky. Replika provede úplnou synchronizaci dat s primárním serverem, aby měl další kopii dat mezipaměti.

Primární uzel může přijít o službu jako součást plánované aktivity údržby, jako je Redis software nebo aktualizace operačního systému. Může také přestat fungovat z důvodu neplánovaných událostí, jako jsou například chyby v podkladovém hardwaru, softwaru nebo síti. [Převzetí služeb při selhání a opravy pro Azure cache pro Redis](cache-failover.md) poskytuje podrobné vysvětlení typů převzetí služeb při selhání Redis. Mezipaměť Azure pro Redis projde během své životnosti během celé řady převzetí služeb při selhání. Architektura vysoké dostupnosti je navržena tak, aby byly tyto změny v mezipaměti transparentní pro její klienty, jak je to možné.

>[!NOTE]
>V rámci verze Preview jsou k dispozici následující.
>
>

Kromě toho Azure cache for Redis umožňuje další uzly repliky na úrovni Premium. [Mezipaměť s více replikami](cache-how-to-multi-replicas.md) se dá nakonfigurovat až se třemi uzly repliky. Další repliky obecně zvyšují odolnost proti chybám, protože další uzly zálohují primární. I s více replikami může být mezipaměť Azure pro instanci Redis vážně ovlivněná výpadkem datového centra nebo AZ-WAN. Můžete zvýšit dostupnost mezipaměti pomocí několika replik v kombinaci s [redundancí zóny](#zone-redundancy).

## <a name="enterprise-cluster"></a>Podnikový cluster

>[!NOTE]
>Tato verze je k dispozici ve verzi Preview.
>
>

Mezipaměť v podnikové vrstvě běží na clusteru Redis Enterprise. V každém okamžiku vyžaduje lichý počet uzlů serveru, aby bylo možné vytvořit kvorum. Ve výchozím nastavení se skládá ze tří uzlů, které jsou hostovány na vyhrazeném virtuálním počítači. Podniková mezipaměť má dva *datové uzly* stejné velikosti a jeden menší *uzel kvora* . Mezipaměť podnikového přehrávače má tři datové uzly stejné velikosti. Podnikový cluster rozdělí Redis data na oddíly interně. Každý oddíl má *primární* a alespoň jednu *repliku* . Každý datový uzel obsahuje jeden nebo více oddílů. Podnikový cluster zajišťuje, že primární a repliky libovolného oddílu nejsou nikdy společně umístěné na stejném datovém uzlu. Oddíly replikují data asynchronně z primárních dat na odpovídající repliky.

Když se datový uzel stane nedostupným nebo dojde k rozdělení sítě, dojde k převzetí služeb při selhání, které je popsané ve [standardní replikaci](#standard-replication) . Podnikový cluster používá model založený na kvoru k určení, které zbývající uzly budou zapojeny do nového kvora. V rámci těchto uzlů také propaguje oddíly repliky na primární, pokud je to potřeba.

## <a name="zone-redundancy"></a>Zónová redundance

>[!NOTE]
>Tato verze je k dispozici ve verzi Preview.
>
>

Mezipaměť Azure pro Redis podporuje redundantní konfigurace zóny na úrovni Premium a Enterprise. [Redundantní mezipaměť zóny](cache-how-to-zone-redundancy.md) může umístit své uzly napříč různými [zóny dostupnosti Azure](../availability-zones/az-overview.md) ve stejné oblasti. Eliminuje datové centrum nebo AZ výpadek jako jediný bod selhání a zvýší celkovou dostupnost mezipaměti.

Následující diagram znázorňuje redundantní konfiguraci zóny:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Nastavení replikace dat":::
   
Azure cache pro Redis distribuuje uzly v zóně redundantní mezipaměti v kruhovém dotazování na AZs, který jste vybrali. Také určuje, který uzel bude zpočátku sloužit jako primární.

Zóna redundantní mezipaměť zajišťuje automatické převzetí služeb při selhání. Pokud aktuální primární uzel není k dispozici, bude jedna z replik přebírat. V případě, že se nový primární uzel nachází v jiném AZ, může vaše aplikace vyskytnout vyšší dobu odezvy na mezipaměť. AZs jsou geograficky oddělené. Přepínání z jednoho AZ na jiný změní fyzickou vzdálenost mezi tím, kde je vaše aplikace a mezipaměť hostovány. Tato změna ovlivní latenci sítě s přenosovou dobou z vaší aplikace do mezipaměti. Největší latence by měla spadat do přijatelného rozsahu pro většinu aplikací. Doporučujeme, abyste aplikaci otestovali, abyste zajistili, že bude fungovat dobře s redundantní mezipamětí zóny.

## <a name="geo-replication"></a>Geografická replikace

Geografická replikace je navržena hlavně pro zotavení po havárii. Poskytuje možnost konfigurovat mezipaměť Azure pro instanci Redis v jiné oblasti Azure pro zálohování primární mezipaměti. [Nastavení geografické replikace pro Azure cache pro Redis](cache-how-to-geo-replication.md) poskytuje podrobné vysvětlení toho, jak geografická replikace funguje.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak nakonfigurovat Azure cache pro Redis možnosti vysoké dostupnosti.

* [Mezipaměť Azure pro úrovně služeb Redis Premium](cache-overview.md#service-tiers)
* [Přidání replik do Azure cache pro Redis](cache-how-to-multi-replicas.md)
* [Povolení redundance zóny pro Azure cache pro Redis](cache-how-to-zone-redundancy.md)
* [Nastavení geografické replikace pro Azure cache pro Redis](cache-how-to-geo-replication.md)