---
title: Jak spravovat Azure mezipaměti Redis | Dokumentace Microsoftu
description: Zjistěte, jak provádět úkoly správy, jako je restartování a naplánovat aktualizace pro Azure Cache pro Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: b75a2faa2030fc343cbabb17eb37b63c9ea34f70
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232377"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Správa mezipaměti Azure pro Redis
Toto téma popisuje, jak provádět úlohy správy, například [restartování](#reboot) a [plánování aktualizace](#schedule-updates) pro mezipaměť Azure pro instance Redis.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Restartování
**Restartování** okno umožňuje restartovat jeden nebo více uzly mezipaměti. Tato možnost restartování umožňuje testovat aplikaci pro odolnost proti chybám, pokud dojde k selhání uzlu mezipaměti.

![Restartování](./media/cache-administration/redis-cache-administration-reboot.png)

Vyberte uzly, restartujte počítač a klikněte na tlačítko **restartování**.

![Restartování](./media/cache-administration/redis-cache-reboot.png)

Pokud máte cache ve verzi premium s aktivovaným clusteringem, můžete vybrat které horizontální oddíl mezipaměti až po restartování.

![Restartování](./media/cache-administration/redis-cache-reboot-cluster.png)

Chcete restartovat jeden nebo více uzly mezipaměti, vyberte požadované uzly a klikněte na tlačítko **restartování**. Pokud máte cache ve verzi premium s aktivovaným clusteringem, vyberte požadovanou částmi a restartujte počítač a potom klikněte na tlačítko **restartování**. Po několika minutách se restartování vybrané uzly a jsou zpět do režimu online později za několik minut.

Dopad na klientské aplikace se liší v závislosti na uzly, na kterých se restartování.

* **Hlavní** – po restartování hlavní uzel převezme služby při selhání uzlu repliky mezipaměti Azure Redis a podporuje do hlavní větve. Během tohoto převzetí služeb při selhání může být krátký interval, ve kterém se nemusí připojení podařit mezipaměti.
* **Podřízený server** – Pokud je podřízený uzel restartován, obvykle není žádný vliv na klientů mezipaměti.
* **Nadřízeného a podřízeného** – když mezipaměti uzly se restartují, se ztratí všechna data v mezipaměti i neúspěšné připojení k mezipaměti, dokud nebude primární uzel přejde do režimu online. Pokud jste nakonfigurovali [trvalost dat](cache-how-to-premium-persistence.md), nejvíce obnovení poslední zálohy při mezipaměti přejde do režimu online, ale budou ztraceny všechny zápisy mezipaměti, ke kterým došlo po poslední záloze.
* **Ukládat do mezipaměti uzly na úrovni premium s aktivovaným clusteringem** - li restartovat jeden nebo více uzlů cache ve verzi premium s clusteringem povolena, chování pro vybrané uzly je stejný jako při restartování odpovídající uzel nebo uzly mezipaměti bez clusterů.

> [!IMPORTANT]
> Restartování je nyní k dispozici pro všechny cenové úrovně.
> 
> 

## <a name="reboot-faq"></a>Restartovat – nejčastější dotazy
* [Který uzel restartovat k otestování Moje aplikace?](#which-node-should-i-reboot-to-test-my-application)
* [Můžete mezipaměť Vymazat připojení klienta restartovat?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Budou mi přijít o data z mezipaměť když restartování?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Můžete restartovat své mezipaměti pomocí Powershellu, rozhraní příkazového řádku nebo jiné nástroje pro správu?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Cenových úrovní může použít funkci restartování?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Který uzel restartovat k otestování Moje aplikace?
Otestujte odolnost vaší aplikace proti selhání primárního uzlu mezipaměti, restartuje **hlavní** uzlu. Otestujte odolnost vaší aplikace proti selhání sekundárního uzlu, restartuje **podřízený server** uzlu. Otestujte odolnost vaší aplikace proti celkový počet selhání mezipaměti, restartovat **obě** uzly.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Můžete mezipaměť Vymazat připojení klienta restartovat?
Ano, pokud restartování mezipaměti se vymaže všechna připojení klientů. Restartování může být užitečné v případě, kde jsou využilo všechna připojení klientů z důvodu chyby logiky nebo chybu v klientské aplikaci. Každá cenová úroveň má jiné [omezení počtu připojení klienta](cache-configure.md#default-redis-server-configuration) pro různé velikosti a jednou tato omezení se dosáhne, jsou přijaty žádné další připojení klientů. Restartování mezipaměti poskytuje způsob, jak vymazat všechna připojení klientů.

> [!IMPORTANT]
> Pokud restartování mezipaměti Vymazat připojení klienta StackExchange.Redis automaticky znovu připojí po uzlu Redis se zpátky do online režimu. Pokud daný problém nevyřeší, může nadále používat připojení klientů.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Budou mi přijít o data z mezipaměť když restartování?
Pokud restartujete i **hlavní** a **podřízený server** uzly, dojde ke ztrátě všech dat v mezipaměti (nebo v daném horizontálním oddílu, pokud používáte cache ve verzi premium s aktivovaným clusteringem). Pokud jste nakonfigurovali [trvalost dat](cache-how-to-premium-persistence.md), nejvíce poslední zálohu. až se obnoví do mezipaměti přejde do režimu online, ale všechny zápisy mezipaměti, ke kterým došlo po zálohování se ztratí.

Pokud restartujete právě jeden z uzlů, nedojde obvykle ke ztrátě dat, ale stále může být. Například pokud hlavní uzel se restartuje a zápis do mezipaměti je v průběhu, data z mezipaměti zápisu je ztraceny. Jiný scénář ztráty dat bude-li restartovat jeden uzel a druhý uzel se stane se přestanou fungovat, protože došlo k chybě ve stejnou dobu. Další informace o možných příčin ztráty dat, naleznete v tématu [co se stalo s Redis má data?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Můžete restartovat své mezipaměti pomocí Powershellu, rozhraní příkazového řádku nebo jiné nástroje pro správu?
Ano, prostředí PowerShell pokyny naleznete v tématu [až po restartování počítače mezipaměti Azure Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Cenových úrovní může použít funkci restartování?
Restart je k dispozici pro všechny cenové úrovně.

## <a name="schedule-updates"></a>Plán aktualizací
**Naplánovat aktualizace** okno umožňuje určit časové období údržby pro mezipaměť úrovně Premium. Pokud je zadána časové období údržby, všechny aktualizace serveru Redis probíhají během tohoto časového období. 

> [!NOTE] 
> Časové období údržby platí jenom pro Redis serveru aktualizace a ne Azure aktualizací nebo aktualizace operačního systému virtuálních počítačů, které jsou hostiteli mezipaměti.
> 
> 

![Plán aktualizací](./media/cache-administration/redis-schedule-updates.png)

Zadejte časové období údržby, zkontrolujte požadované dnů a zadat počáteční hodina časového období údržby pro každý den a klikněte na tlačítko **OK**. Všimněte si, že časového období údržby se ve standardu UTC. 

Výchozí a minimální období údržby pro aktualizace je pět hodin. Tato hodnota není konfigurovatelné z webu Azure portal, můžete ji ale konfigurovat pomocí prostředí PowerShell `MaintenanceWindow` parametr [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) rutiny. Další informace najdete v tématu [můžete spravovat plánované aktualizace pomocí prostředí PowerShell, CLI nebo jiné nástroje pro správu?](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)


## <a name="schedule-updates-faq"></a>Naplánovat aktualizace – nejčastější dotazy
* [Kdy aktualizace dojít, pokud nepoužívám funkce aktualizace plánu?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Jaký typ aktualizací jsou prováděny během plánované údržby?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Můžete spravovat plánované aktualizace pomocí prostředí PowerShell, CLI nebo jiné nástroje pro správu?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Cenových úrovní pomocí funkce aktualizace plánu?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Kdy aktualizace dojít, pokud nepoužívám funkce aktualizace plánu?
Pokud nechcete zadat časové období údržby, kdykoli provádět aktualizace.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Jaký typ aktualizací jsou prováděny během plánované údržby?
Pouze Redis server provedeny aktualizace během plánované časové období údržby. Časové období údržby se nevztahuje na Azure aktualizace nebo aktualizace operačního systému virtuálního počítače.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Je možné spravované plánované aktualizace pomocí prostředí PowerShell, CLI nebo jiné nástroje pro správu?
Ano, můžete spravovat vaše naplánované aktualizace pomocí následujících rutin Powershellu:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Cenových úrovní pomocí funkce aktualizace plánu?
**Naplánovat aktualizace** funkce dostupná jenom v cenové úrovně premium.

## <a name="next-steps"></a>Další postup
* Prozkoumat další [mezipaměti Azure Redis na úrovni premium](cache-premium-tier-intro.md) funkce.

