---
title: Jak spravovat službu Azure cache pro Redis
description: Naučte se provádět úlohy správy, jako je třeba restartování a naplánování aktualizací pro službu Azure cache pro Redis.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 0a79b0b5b5f21d1c75fec6b062f1ca91cfe9dd1f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219195"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Jak spravovat službu Azure cache pro Redis
Toto téma popisuje, jak provádět úlohy správy, jako je třeba [restartování](#reboot) a [Plánování aktualizací](#schedule-updates) pro instance Redis v mezipaměti Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Restartování
Okno **restartování** vám umožní restartovat jeden nebo několik uzlů mezipaměti. Tato schopnost restartování umožňuje testovat aplikaci, aby byla odolná proti chybám, pokud dojde k selhání uzlu mezipaměti.

![Snímek obrazovky, který zvýrazní možnost nabídky restartovat.](./media/cache-administration/redis-cache-administration-reboot.png)

Vyberte uzly, které chcete restartovat, a klikněte na **restartovat**.

![Snímek obrazovky, který zobrazuje uzly, které můžete restartovat.](./media/cache-administration/redis-cache-reboot.png)

Pokud máte mezipaměť Premium s povoleným clusteringem, můžete vybrat, které horizontálních oddílů mezipaměti se mají restartovat.

![Restartování](./media/cache-administration/redis-cache-reboot-cluster.png)

Chcete-li restartovat jeden nebo více uzlů mezipaměti, vyberte požadované uzly a klikněte na tlačítko **restartovat**. Pokud máte mezipaměť Premium s povoleným clusteringem, vyberte požadované horizontálních oddílů k restartování a pak klikněte na **restartovat**. Po několika minutách se vybrané uzly restartují a v průběhu několika minut budete zase online.

Dopad na klientské aplikace se liší v závislosti na tom, které uzly se restartovaly.

* **Hlavní** – když se restartuje primární uzel, Azure cache pro Redis převezme uzel repliky a propaguje ho na primární. Během tohoto převzetí služeb při selhání může dojít ke krátkému intervalu, během kterého může dojít k selhání připojení do mezipaměti.
* **Replika** – při restartu uzlu repliky není obvykle nijak ovlivněno ukládání klientů do mezipaměti.
* **Primární i replika** – při restartu obou uzlů mezipaměti se v mezipaměti ztratí všechna data a připojení k mezipaměti selže, dokud se primární uzel nevrátí zpět do režimu online. Pokud jste nakonfigurovali [Trvalost dat](cache-how-to-premium-persistence.md), obnoví se poslední záloha, když se mezipaměť vrátí zpět do režimu online, ale všechny zápisy do mezipaměti, ke kterým došlo po poslední záloze, se ztratí.
* **Uzly mezipaměti Premium s povoleným clusteringem** – když restartujete jeden nebo víc uzlů mezipaměti Premium s povoleným clusteringem, chování pro vybrané uzly se shoduje s tím, jak restartujete odpovídající uzel nebo uzly, které nejsou v Clusterové mezipaměti.

## <a name="reboot-faq"></a>Nejčastější dotazy k restartování
* [Který uzel mám restartovat, aby se aplikace otestovala?](#which-node-should-i-reboot-to-test-my-application)
* [Můžu restartovat mezipaměť, aby se vymazala připojení klientů?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Ztratím během restartování data z mezipaměti?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Je možné restartovat tuto mezipaměť pomocí PowerShellu, CLI nebo jiných nástrojů pro správu?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Který uzel mám restartovat, aby se aplikace otestovala?
Chcete-li otestovat odolnost vaší aplikace proti selhání primárního uzlu vaší mezipaměti, restartujte **Hlavní** uzel. Chcete-li otestovat odolnost aplikace proti selhání uzlu repliky, restartujte uzel **repliky** . Chcete-li otestovat odolnost aplikace proti celkovému selhání mezipaměti, restartujte **oba** uzly.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Můžu restartovat mezipaměť, aby se vymazala připojení klientů?
Ano, Pokud restartujete mezipaměť, všechna připojení klientů budou vymazána. Restartování může být užitečné v případě, že se všechna připojení klientů využívala z důvodu chyby logiky nebo chyby v klientské aplikaci. Každá cenová úroveň má různá [omezení připojení klientů](cache-configure.md#default-redis-server-configuration) pro různé velikosti a po dosažení těchto limitů nejsou přijímána žádná další připojení klientů. Restartování mezipaměti poskytuje způsob, jak vymazat všechna připojení klientů.

> [!IMPORTANT]
> Pokud restartujete mezipaměť pro vymazání připojení klienta, StackExchange. Redis se automaticky znovu připojí, jakmile se uzel Redis vrátí zpět do online režimu. Pokud se základní problém nevyřeší, můžou se připojení klientů dál používat.
> 
> 



### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Ztratím během restartování data z mezipaměti?
Pokud restartujete **Hlavní** uzel i uzel **repliky** , může dojít ke ztrátě všech dat v mezipaměti (nebo v tomto horizontálních oddílů, pokud používáte mezipaměť Premium s povoleným clusteringem), ale není zaručena žádná z nich. Pokud jste nakonfigurovali [Trvalost dat](cache-how-to-premium-persistence.md), obnoví se poslední záloha, jakmile se mezipaměť vrátí zpět do režimu online, ale všechny zápisy do mezipaměti, ke kterým došlo po provedení zálohování, budou ztraceny.

Pokud restartujete pouze jeden z uzlů, data se obvykle neztratí, ale přesto může být. Například pokud je primární uzel restartován a probíhá zápis do mezipaměti, dojde ke ztrátě dat z zápisu do mezipaměti. Dalším scénářem pro ztrátu dat by byl případ, že restartujete jeden uzel a druhý uzel přejde k výpadku z důvodu selhání ve stejnou dobu. Další informace o možných příčinách ztráty dat najdete v tématu [co se stalo s daty v Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Je možné restartovat tuto mezipaměť pomocí PowerShellu, CLI nebo jiných nástrojů pro správu?
Ano, pokyny k prostředí PowerShell najdete v tématu [restart mezipaměti Azure pro Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Plán aktualizací
Okno **naplánovat aktualizace** umožňuje určit časové období údržby pro instanci mezipaměti. Časové období údržby vám umožňuje řídit dny a dobu v týdnu, během kterých lze virtuální počítače hostující vaši mezipaměť aktualizovat. Azure cache pro Redis vám umožní začít a dokončit aktualizaci softwaru Redis serveru v zadaném časovém intervalu, který definujete.

> [!NOTE] 
> Časové období údržby se vztahuje na aktualizace serveru Redis a aktualizace operačního systému virtuálních počítačů, které hostují mezipaměť. Časové období údržby se nevztahuje na aktualizace operačního systému hostitele na hostitele hostujících virtuální počítače mezipaměti nebo jiné síťové součásti Azure. Ve výjimečných případech, kde jsou mezipaměti hostované na starších modelech (můžete zjistit, jestli je vaše mezipaměť na starším modelu, pokud se název DNS mezipaměti překládá na příponu "cloudapp.net", "chinacloudapp.cn", "usgovcloudapi.net" nebo "cloudapi.de"), časové období údržby se neuplatní ani na aktualizace operačního systému hosta.
>


![Plán aktualizací](./media/cache-administration/redis-schedule-updates.png)

Chcete-li určit časový interval pro správu a údržbu, zaškrtněte požadované dny a zadejte časový interval pro správu a údržbu pro každý den a klikněte na tlačítko **OK**. Všimněte si, že čas časového období údržby je UTC. 

Výchozí nastavení a minimální interval údržby pro aktualizace jsou pět hodin. Tato hodnota se nedá konfigurovat z Azure Portal, ale můžete ji nakonfigurovat v PowerShellu pomocí `MaintenanceWindow` parametru rutiny [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) . Další informace najdete v tématu Správa naplánovaných aktualizací pomocí PowerShellu, rozhraní příkazového řádku nebo jiných nástrojů pro správu.

## <a name="schedule-updates-faq"></a>Nejčastější dotazy k plánu aktualizací
* [Kdy k aktualizacím dochází, když nepoužívám funkci plán Updates?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Jaký typ aktualizací se provede během naplánovaného časového období údržby?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Můžu spravovat plánované aktualizace pomocí PowerShellu, rozhraní příkazového řádku nebo jiných nástrojů pro správu?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Kdy k aktualizacím dochází, když nepoužívám funkci plán Updates?
Pokud nezadáte časový interval pro správu a údržbu, můžete kdykoli provést aktualizace.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Jaký typ aktualizací se provede během naplánovaného časového období údržby?
Během naplánovaných časových období údržby se provádí jenom aktualizace serveru Redis. Časové období údržby se nevztahuje na aktualizace nebo aktualizace Azure v operačním systému virtuálního počítače.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Můžu spravovat plánované aktualizace pomocí PowerShellu, rozhraní příkazového řádku nebo jiných nástrojů pro správu?
Ano, naplánované aktualizace můžete spravovat pomocí následujících rutin PowerShellu:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o funkcích Azure cache pro Redis.

* [Mezipaměť Azure pro úrovně služeb Redis](cache-overview.md#service-tiers)

