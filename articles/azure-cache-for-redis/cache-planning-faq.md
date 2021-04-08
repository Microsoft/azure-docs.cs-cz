---
title: Nejčastější dotazy k plánování služby Azure cache pro Redis
description: Přečtěte si odpovědi na nejčastější dotazy, které vám pomůžou naplánovat Azure cache pro Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398b567e173691068e6dd932e075d6598dcfe601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92537401"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Nejčastější dotazy k plánování služby Azure cache pro Redis

Tento článek obsahuje odpovědi na běžné dotazy týkající se plánování mezipaměti Azure pro Redis.

## <a name="common-questions-and-answers"></a>Běžné otázky a odpovědi
Tato část obsahuje následující Nejčastější dotazy:

* [Azure cache pro výkon Redis](#azure-cache-for-redis-performance)
* [V jaké oblasti mám najít mezipaměť?](#in-what-region-should-i-locate-my-cache)
* [Kde se nacházejí moje data z mezipaměti?](#where-do-my-cached-data-reside)
* [Jak se fakturuje Azure cache pro Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Můžu použít mezipaměť Azure pro Redis s cloudem Azure Government, Azure Čína 21Vianet Cloud nebo Microsoft Azure (Německo)?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

### <a name="azure-cache-for-redis-performance"></a>Azure cache pro výkon Redis
V následující tabulce jsou uvedeny maximální hodnoty šířky pásma zjištěné při testování různých velikostí mezipamětí Standard a Premium pomocí `redis-benchmark.exe` z virtuálního počítače s IaaS pro koncový bod Redis v mezipaměti Azure. V případě propustnosti TLS se Redis-test používá s stunnelu pro připojení ke koncovému bodu Azure cache pro Redis.

>[!NOTE] 
>Tyto hodnoty nejsou zaručené a pro tato čísla nejsou k dispozici žádná smlouva SLA, ale měla by být typická. Pro určení správné velikosti mezipaměti pro aplikaci byste měli načíst vlastní aplikaci.
>Tato čísla se můžou při pravidelném nastavování novějších výsledků změnit.
>

Z této tabulky můžeme vykreslit následující závěry:

* Propustnost pro mezipaměti, které mají stejnou velikost, je vyšší než úroveň Premium ve srovnání s úrovní Standard. Například s pamětí 6 GB je propustnost P1 180 000 požadavků za sekundu (RPS) v porovnání s 100 000 RPS pro C3.
* Díky clusteringu Redis se propustnost při zvýšení počtu horizontálních oddílů (uzlů) v clusteru lineárně zvyšuje. Pokud třeba vytvoříte cluster P4 o 10 horizontálních oddílů, pak je dostupná propustnost 400 000 × 10 = 4 000 000 RPS.
* Propustnost pro větší velikosti klíčů je vyšší než úroveň Premium ve srovnání s úrovní Standard.

| Cenová úroveň | Velikost | Procesorová jádra | Dostupná šířka pásma | velikost hodnoty 1 – KB | velikost hodnoty 1 – KB |
| --- | --- | --- | --- | --- | --- |
| **Standardní velikosti mezipaměti** | | |**Megabitů za sekundu (MB/s)/megabajtů za sekundu (MB/s)** |**Počet požadavků za sekundu (RPS) bez SSL** |**SSL požadavků za sekundu (RPS)** |
| C0 | 250 MB | Shared | 100/12,5  |  15 000 |   7 500 |
| C1 |   1 GB | 1      | 500/62,5  |  38 000 |  20 720 |
| C2 | 2,5 GB | 2      | 500/62,5  |  41 000 |  37 000 |
| C3 |   6 GB | 4      | 1000/125  | 100 000 |  90,000 |
| C4 |  13 GB | 2      | 500/62,5  |  60 000 |  55 000 |
| C5 |  26 GB | 4      | 1 000/125 | 102 000 |  93 000 |
| C6 |  53 GB | 8      | 2 000/250 | 126 000 | 120 000 |
| **Velikosti mezipaměti úrovně Premium** | |**PROCESORová jádra na horizontálních oddílů** | **Megabitů za sekundu (MB/s)/megabajtů za sekundu (MB/s)** |**Počet požadavků za sekundu (RPS) jiný než SSL na horizontálních oddílů** |**Počet požadavků SSL za sekundu (RPS), za horizontálních oddílů** |
| P1 |   6 GB |  2 | 1 500/187,5 | 180 000 | 172 000 |
| P2 |  13 GB |  4 | 3 000/375   | 350 000 | 341 000 |
| P3 |  26 GB |  4 | 3 000/375   | 350 000 | 341 000 |
| P4 |  53 GB |  8 | 6 000/750   | 400 000 | 373 000 |
| P5 | 120 GB | 20 | 6 000/750   | 400 000 | 373 000 |

Pokyny k nastavení stunnelu nebo stažení nástrojů Redis `redis-benchmark.exe` , jako je, najdete v tématu [Jak můžu spustit příkazy Redis?](cache-development-faq.md#how-can-i-run-redis-commands).

### <a name="in-what-region-should-i-locate-my-cache"></a>V jaké oblasti mám najít mezipaměť?
Pro nejlepší výkon a nejnižší latenci vyhledejte mezipaměť Azure pro Redis ve stejné oblasti, jako je klientská aplikace pro mezipaměť.

### <a name="where-do-my-cached-data-reside"></a>Kde se nacházejí moje data z mezipaměti?
Azure cache pro Redis ukládá data vaší aplikace do paměti RAM virtuálního počítače nebo virtuálních počítačů v závislosti na vrstvě, která hostuje vaši mezipaměť. Vaše data se budou nacházet výhradně v oblasti Azure, kterou jste vybrali ve výchozím nastavení. Existují dva případy, kdy vaše data mohou opustit oblast:
* Když v mezipaměti povolíte trvalost, mezipaměť Azure pro Redis bude zálohovat vaše data na účet Azure Storage, který vlastníte. Pokud se účet úložiště, který zadáte, nachází v jiné oblasti, ukončí se kopie vašich dat.
* Pokud nastavíte geografickou replikaci a sekundární mezipaměť je v jiné oblasti, což by mohlo být v normálním případě, vaše data budou replikována do této oblasti.

Aby bylo možné používat tyto funkce, budete muset explicitně nakonfigurovat Azure cache pro Redis. Máte také úplnou kontrolu nad oblastí, ve které se nachází účet úložiště nebo sekundární mezipaměť.

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Jak se fakturuje Azure cache pro Redis?
Ceny za Azure cache for Redis [najdete tady](https://azure.microsoft.com/pricing/details/cache/). Na stránce s cenami se zobrazují ceny jako hodiny a měsíční sazba. Mezipaměti se účtují po minutách od doby, kdy se mezipaměť vytvoří, až do doby, kdy se mezipaměť odstraní. Neexistuje možnost pro zastavení nebo pozastavení fakturace v mezipaměti.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>Můžu použít mezipaměť Azure pro Redis s cloudem Azure Government, Azure Čína 21Vianet Cloud nebo Microsoft Azure (Německo)?
Ano, mezipaměť Azure pro Redis je dostupná v cloudu Azure Government, Azure Čína 21Vianet a Microsoft Azure (Německo). Adresy URL pro přístup a správu Azure cache pro Redis se v porovnání s veřejným cloudem Azure liší.

| Cloud   | Přípona DNS pro Redis            |
|---------|---------------------------------|
| Veřejná  | *. redis.cache.windows.net       |
| US Gov  | *. redis.cache.usgovcloudapi.net |
| Německo | *. redis.cache.cloudapi.de       |
| Čína   | *. redis.cache.chinacloudapi.cn  |

Další informace o tom, co je třeba zvážit při použití mezipaměti Azure pro Redis s jinými cloudy, najdete na následujících odkazech.

- [Databáze Azure Government – mezipaměť Azure pro Redis](../azure-government/compare-azure-government-global-azure.md)
- [Cloud Azure Čína 21Vianet – Azure cache pro Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)

Informace o používání služby Azure cache pro Redis s prostředím PowerShell v Azure Government cloudu, Azure Čína 21Vianet Cloud a Microsoft Azure (Německo) najdete v tématu [jak se připojit k dalším cloudům – Azure cache pro Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

## <a name="next-steps"></a>Další kroky

Přečtěte si o dalších [nejčastějších dotazech k mezipaměti Azure pro Redis](cache-faq.md).