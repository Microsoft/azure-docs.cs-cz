---
title: Jak spravovat službu Azure cache pro Redis | Microsoft Docs
description: Naučte se provádět úlohy správy, jako je třeba restartování a naplánování aktualizací pro službu Azure cache pro Redis.
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
ms.openlocfilehash: afb48c18f9690e77a5478956e1147acf850290ab
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756611"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Jak spravovat službu Azure cache pro Redis
Toto téma popisuje, jak provádět úlohy správy, jako je třeba [restartování](#reboot) a [Plánování aktualizací](#schedule-updates) pro instance Redis v mezipaměti Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Restartování
Okno **restartování** vám umožní restartovat jeden nebo několik uzlů mezipaměti. Tato schopnost restartování umožňuje testovat aplikaci, aby byla odolná proti chybám, pokud dojde k selhání uzlu mezipaměti.

![Restartování](./media/cache-administration/redis-cache-administration-reboot.png)

Vyberte uzly, které chcete restartovat, a klikněte na **restartovat**.

![Restartování](./media/cache-administration/redis-cache-reboot.png)

Pokud máte mezipaměť Premium s povoleným clusteringem, můžete vybrat, které horizontálních oddílů mezipaměti se mají restartovat.

![Restartování](./media/cache-administration/redis-cache-reboot-cluster.png)

Chcete-li restartovat jeden nebo více uzlů mezipaměti, vyberte požadované uzly a klikněte na tlačítko **restartovat**. Pokud máte mezipaměť Premium s povoleným clusteringem, vyberte požadované horizontálních oddílů k restartování a pak klikněte na **restartovat**. Po několika minutách se vybrané uzly restartují a v průběhu několika minut budete zase online.

Dopad na klientské aplikace se liší v závislosti na tom, které uzly se restartovaly.

* **Hlavní** – když se hlavní uzel restartuje, Azure cache pro Redis převezme uzel repliky a propaguje ho do hlavní větve. Během tohoto převzetí služeb při selhání může dojít ke krátkému intervalu, během kterého může dojít k selhání připojení do mezipaměti.
* **Podřízený** – při restartu podřízeného uzlu není obvykle nijak ovlivněno ukládání klientů do mezipaměti.
* **Hlavní i podřízený** – při restartu obou uzlů mezipaměti dojde k výpadku všech dat v mezipaměti a připojení k mezipaměti selže, dokud se primární uzel nevrátí zpět do režimu online. Pokud jste nakonfigurovali [Trvalost dat](cache-how-to-premium-persistence.md), obnoví se poslední záloha, když se mezipaměť vrátí zpět do režimu online, ale všechny zápisy do mezipaměti, ke kterým došlo po poslední záloze, se ztratí.
* **Uzly mezipaměti Premium s povoleným clusteringem** – když restartujete jeden nebo víc uzlů mezipaměti Premium s povoleným clusteringem, chování pro vybrané uzly se shoduje s tím, jak restartujete odpovídající uzel nebo uzly, které nejsou v Clusterové mezipaměti.

## <a name="reboot-faq"></a>Nejčastější dotazy k restartování
* [Který uzel mám restartovat, aby se aplikace otestovala?](#which-node-should-i-reboot-to-test-my-application)
* [Můžu restartovat mezipaměť, aby se vymazala připojení klientů?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Ztratím během restartování data z mezipaměti?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Je možné restartovat tuto mezipaměť pomocí PowerShellu, CLI nebo jiných nástrojů pro správu?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Který uzel mám restartovat, aby se aplikace otestovala?
Chcete-li otestovat odolnost vaší aplikace proti selhání primárního uzlu vaší mezipaměti, restartujte **Hlavní** uzel. Chcete-li otestovat odolnost vaší aplikace proti selhání sekundárního uzlu, restartujte **podřízený** uzel. Chcete-li otestovat odolnost aplikace proti celkovému selhání mezipaměti, restartujte **oba** uzly.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Můžu restartovat mezipaměť, aby se vymazala připojení klientů?
Ano, Pokud restartujete mezipaměť, všechna připojení klientů budou vymazána. Restartování může být užitečné v případě, že se všechna připojení klientů využívala z důvodu chyby logiky nebo chyby v klientské aplikaci. Každá cenová úroveň má různá [omezení připojení klientů](cache-configure.md#default-redis-server-configuration) pro různé velikosti a po dosažení těchto limitů nejsou přijímána žádná další připojení klientů. Restartování mezipaměti poskytuje způsob, jak vymazat všechna připojení klientů.

> [!IMPORTANT]
> Pokud restartujete mezipaměť pro vymazání připojení klienta, StackExchange. Redis se automaticky znovu připojí, jakmile se uzel Redis vrátí zpět do online režimu. Pokud se základní problém nevyřeší, můžou se připojení klientů dál používat.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Ztratím během restartování data z mezipaměti?
Pokud restartujete **hlavní** i **podřízený** uzel, může dojít ke ztrátě všech dat v mezipaměti (nebo v tomto horizontálních oddílů, pokud používáte mezipaměť Premium s povoleným clusteringem), ale není zaručena žádná z nich. Pokud jste nakonfigurovali [Trvalost dat](cache-how-to-premium-persistence.md), obnoví se poslední záloha, jakmile se mezipaměť vrátí zpět do režimu online, ale všechny zápisy do mezipaměti, ke kterým došlo po provedení zálohování, budou ztraceny.

Pokud restartujete pouze jeden z uzlů, data se obvykle neztratí, ale přesto může být. Například pokud je hlavní uzel restartován a probíhá zápis do mezipaměti, dojde ke ztrátě dat z zápisu do mezipaměti. Dalším scénářem pro ztrátu dat by byl případ, že restartujete jeden uzel a druhý uzel přejde k výpadku z důvodu selhání ve stejnou dobu. Další informace o možných příčinách ztráty dat najdete v tématu [co se stalo s daty v Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Je možné restartovat tuto mezipaměť pomocí PowerShellu, CLI nebo jiných nástrojů pro správu?
Ano, pokyny k prostředí PowerShell najdete v tématu [restart mezipaměti Azure pro Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Aktualizace plánu
Okno **naplánovat aktualizace** umožňuje určit časové období údržby pro instanci mezipaměti. Po zadání časového období údržby se v průběhu tohoto okna provedou všechny aktualizace Redis serveru. 

> [!NOTE] 
> Časové období údržby se vztahuje jenom na aktualizace serveru Redis a ne na aktualizace nebo aktualizace Azure v operačním systému virtuálních počítačů, které hostují mezipaměť.
>

![Aktualizace plánu](./media/cache-administration/redis-schedule-updates.png)

Chcete-li určit časový interval pro správu a údržbu, zaškrtněte požadované dny a zadejte časový interval pro správu a údržbu pro každý den a klikněte na tlačítko **OK**. Všimněte si, že čas časového období údržby je UTC. 

Výchozí nastavení a minimální interval údržby pro aktualizace jsou pět hodin. Tato hodnota se nedá konfigurovat z Azure Portal, ale můžete ji nakonfigurovat v PowerShellu pomocí parametru `MaintenanceWindow` rutiny [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) . Další informace najdete v tématu Správa naplánovaných aktualizací pomocí PowerShellu, rozhraní příkazového řádku nebo jiných nástrojů pro správu.

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
* Prozkoumejte více funkcí [Azure cache pro Redis úrovně Premium](cache-premium-tier-intro.md) .

