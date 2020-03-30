---
title: Jak spravovat Azure Cache pro Redis
description: Zjistěte, jak provádět úlohy správy, jako je restartování a plánování aktualizací pro Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 69686cad20bc4ce70bff2a92a216c9430522c301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278841"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Jak spravovat Azure Cache pro Redis
Toto téma popisuje, jak provádět úlohy správy, jako je [restartování](#reboot) a [plánování aktualizací](#schedule-updates) pro instance Azure Cache for Redis.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Restartování
Okno **Reboot** umožňuje restartovat jeden nebo více uzlů mezipaměti. Tato možnost restartování umožňuje otestovat odolnost aplikace, pokud dojde k selhání uzlu mezipaměti.

![Restartování](./media/cache-administration/redis-cache-administration-reboot.png)

Vyberte uzly, které chcete restartovat, a klepněte na tlačítko **Restartovat**.

![Restartování](./media/cache-administration/redis-cache-reboot.png)

Pokud máte prémiovou mezipaměť s povoleným clusteringem, můžete vybrat, které úlomky mezipaměti se mají restartovat.

![Restartování](./media/cache-administration/redis-cache-reboot-cluster.png)

Chcete-li restartovat jeden nebo více uzlů mezipaměti, vyberte požadované uzly a klepněte na tlačítko **Restartovat**. Pokud máte prémiovou mezipaměť s povoleným clusterováním, vyberte požadované úlomky, které chcete restartovat, a klepněte na tlačítko **Restartovat**. Po několika minutách se vybrané uzly restartují a o několik minut později jsou zpět online.

Dopad na klientské aplikace se liší v závislosti na uzlech, které restartujete.

* **Hlavní** server – při restartování hlavního uzlu azure cache pro Redis převzetí služby při selhání do uzlu repliky a povýší ji do hlavního serveru. Během tohoto převzetí služeb při selhání může být krátký interval, ve kterém připojení může selhat do mezipaměti.
* **Slave** - Při restartování slave uzlu, je obvykle žádný vliv na cache klientů.
* **Hlavní i slave** – při restartování obou uzlů mezipaměti dojde ke ztrátě všech dat v mezipaměti a připojení ke mezipaměti se nezdaří, dokud primární uzel nepřejde zpět do režimu online. Pokud jste nakonfigurovali [trvalost dat](cache-how-to-premium-persistence.md), poslední záloha je obnovena, když se mezipaměť vrátí do režimu online, ale dojde ke ztrátě všech zápisů do mezipaměti, ke kterým došlo po poslední záloze.
* **Uzly prémiové mezipaměti s povoleným clusterováním** – při restartování jednoho nebo více uzlů prémiové mezipaměti s povoleným clusterováním je chování vybraných uzlů stejné jako při restartování odpovídajícího uzlu nebo uzlů neclusterované mezipaměti.

## <a name="reboot-faq"></a>Nejčastější dotazy k restartování
* [Který uzel mám restartovat, abych otestoval svou aplikaci?](#which-node-should-i-reboot-to-test-my-application)
* [Mohu restartovat mezipaměť, aby se vymaže připojení klientů?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Ztratím data z mezipaměti, pokud restartuji počítač?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Můžu restartovat mezipaměť pomocí PowerShellu, CLI nebo jiných nástrojů pro správu?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Který uzel mám restartovat, abych otestoval svou aplikaci?
Chcete-li otestovat odolnost aplikace proti selhání primárního uzlu mezipaměti, restartujte **hlavní** uzel. Chcete-li otestovat odolnost aplikace proti selhání sekundárního uzlu, restartujte uzel **Slave.** Chcete-li otestovat odolnost aplikace proti úplnému selhání mezipaměti, restartujte **oba** uzly.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Mohu restartovat mezipaměť, aby se vymaže připojení klientů?
Ano, pokud restartujete mezipaměť, všechna klientská připojení jsou vymazána. Restartování může být užitečné v případě, kdy jsou všechna připojení klientů vymáhána z důvodu logické chyby nebo chyby v klientské aplikaci. Každá cenová úroveň má [různá omezení připojení klienta](cache-configure.md#default-redis-server-configuration) pro různé velikosti a po dosažení těchto limitů nejsou přijata žádná další připojení klientů. Restartování mezipaměti poskytuje způsob, jak vymazat všechna připojení klientů.

> [!IMPORTANT]
> Pokud restartujete mezipaměť vymazat připojení klientů, StackExchange.Redis automaticky znovu připojí, jakmile uzel Redis je zpět online. Pokud základní problém není vyřešen, připojení klienta může být i nadále používat.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Ztratím data z mezipaměti, pokud restartuji počítač?
Pokud restartujete **hlavní** i **slave** uzly, všechna data v mezipaměti (nebo v tomto úlomku, pokud používáte prémiovou mezipaměť s povoleným clusteringem) mohou být ztracena, ale to také není zaručeno. Pokud jste nakonfigurovali [trvalost dat](cache-how-to-premium-persistence.md), bude nejnovější záloha obnovena, jakmile se mezipaměť vrátí do režimu online, ale všechny zápisy do mezipaměti, ke kterým došlo po vytvoření zálohy, budou ztraceny.

Pokud restartujete pouze jeden z uzlů, data nejsou obvykle ztracena, ale stále může být. Například pokud je hlavní uzel restartován a probíhá zápis do mezipaměti, dojde ke ztrátě dat z zápisu do mezipaměti. Dalším scénářem pro ztrátu dat by bylo, pokud restartujete jeden uzel a druhý uzel se stane přejít dolů z důvodu selhání ve stejnou dobu. Další informace o možných příčinách ztráty dat najdete v tématu [Co se stalo s mými daty v redisu?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Můžu restartovat mezipaměť pomocí PowerShellu, CLI nebo jiných nástrojů pro správu?
Ano, pokyny k Prostředí PowerShell uvidíte [restartování mezipaměti Azure pro Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Plán aktualizací
Okno **Aktualizace plánu** umožňuje určit okno údržby pro instanci mezipaměti. Po zadání okna údržby jsou během tohoto okna provedeny všechny aktualizace serveru Redis. 

> [!NOTE] 
> Okno údržby se vztahuje jenom na aktualizace serveru Redis a nikoli na žádné aktualizace Azure nebo aktualizace operačního systému virtuálních počítače, které hostují mezipaměť.
>

![Plán aktualizací](./media/cache-administration/redis-schedule-updates.png)

Chcete-li určit okno údržby, zkontrolujte požadované dny a zadejte počáteční hodinu okna údržby pro každý den a klepněte na tlačítko **OK**. Všimněte si, že doba časového intervalu údržby je v utc. 

Výchozí a minimální okno údržby aktualizací je pět hodin. Tato hodnota není konfigurovatelná z portálu Azure, ale můžete `MaintenanceWindow` ji nakonfigurovat v prostředí PowerShell pomocí parametru rutiny [New-AzRedisCacheScheduleEntry.](/powershell/module/az.rediscache/new-azrediscachescheduleentry) Další informace najdete v tématu Můžu spravovat naplánované aktualizace pomocí PowerShellu, CLI nebo jiných nástrojů pro správu?

## <a name="schedule-updates-faq"></a>Nejčastější dotazy k plánování aktualizací
* [Kdy dojde k aktualizacím, když nepoužívám funkci aktualizace plánu?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Jaký typ aktualizací se provádí během okna plánované údržby?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Můžu spravovat naplánované aktualizace pomocí PowerShellu, CLI nebo jiných nástrojů pro správu?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Kdy dojde k aktualizacím, když nepoužívám funkci aktualizace plánu?
Pokud nezadáte okno údržby, aktualizace lze provést kdykoli.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Jaký typ aktualizací se provádí během okna plánované údržby?
Během okna plánované údržby jsou prováděny pouze aktualizace serveru Redis. Okno údržby se nevztahuje na aktualizace Azure nebo aktualizace operačního systému virtuálních aplikací.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Můžu spravovat naplánované aktualizace pomocí PowerShellu, CLI nebo jiných nástrojů pro správu?
Ano, naplánované aktualizace můžete spravovat pomocí následujících rutin prostředí PowerShell:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [Nový-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [Nová-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Odebrat-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Další kroky
* Prozkoumejte další funkce [azure cache pro úroveň úrovně Redis.](cache-premium-tier-intro.md)

