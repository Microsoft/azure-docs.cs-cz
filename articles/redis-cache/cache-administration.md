---
title: Postupy při správě Azure Redis Cache | Microsoft Docs
description: Zjistěte, jak provádět úlohy správy, jako je například restartování a plán aktualizace pro Azure Redis Cache
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 07/05/2017
ms.author: wesmc
ms.openlocfilehash: 3b62b41fb7b9d7ff6f40191c48d00c1f0a941e48
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639448"
---
# <a name="how-to-administer-azure-redis-cache"></a>Postupy při správě Azure Redis Cache
Toto téma popisuje, jak provádět úlohy správy, například [restartování](#reboot) a [plánování aktualizace](#schedule-updates) pro vaše instance služby Azure Redis Cache.

## <a name="reboot"></a>Restartování
**Restartovat** okno umožňuje restartovat jeden nebo více uzlů svojí mezipaměti. Tato možnost restartování umožňuje otestovat aplikaci pro odolnost proti chybám, pokud dojde k selhání uzlu mezipaměti.

![Restartování](./media/cache-administration/redis-cache-administration-reboot.png)

Vyberte uzly restartovat a klikněte na tlačítko **restartovat**.

![Restartování](./media/cache-administration/redis-cache-reboot.png)

Pokud máte s povoleným clusteringem cache ve verzi premium, můžete vybrat které horizontálních oddílů mezipaměti restartovat.

![Restartování](./media/cache-administration/redis-cache-reboot-cluster.png)

Restartovat jeden nebo více uzlů svojí mezipaměti, vyberte požadovaný uzel a klikněte na tlačítko **restartovat**. Pokud máte cache ve verzi premium s povoleným clusteringem, vyberte požadované horizontálních oddílů restartovat a pak klikněte na tlačítko **restartovat**. Po několika minutách restartování vybrané uzly a jsou zpět do režimu online několik minut později.

Dopad na klientské aplikace se liší v závislosti na tom, které uzly restartovat.

* **Hlavní** – Pokud hlavní uzel je restartovaný, Azure Redis Cache selže na uzlu repliky a podporuje hlavní server. Během této převzetí služeb při selhání může být krátký interval, ve kterém může připojení selhat do mezipaměti.
* **Podřízený** – Pokud je podřízený uzel restartován, je obvykle mít žádný vliv klientů mezipaměti.
* **Hlavní a podřízený** – když mezipaměti uzly se restartují, v mezipaměti ke ztrátě všech dat i neúspěšné připojení k mezipaměti, dokud nebude primární uzel přejde do režimu online. Pokud jste nakonfigurovali [trvalosti dat](cache-how-to-premium-persistence.md), nejvíce poslední zálohy se obnoví, když mezipaměť přejde do režimu online, ale dojde ke ztrátě všech mezipaměti zápisu, které nastaly po poslední záloze.
* **Uzly prémiový mezipaměti s povoleným clusteringem** – Když restartujete jeden nebo více uzlů cache ve verzi premium s clusteringem povoleno, chování pro vybrané uzly je stejný jako při restartování odpovídající uzel nebo uzly mezipaměti vypojené z clusteru.

> [!IMPORTANT]
> Restart je nyní k dispozici pro všechny cenové úrovně.
> 
> 

## <a name="reboot-faq"></a>Nejčastější dotazy k restartování počítače
* [Uzel restartovat k testování Moje aplikace?](#which-node-should-i-reboot-to-test-my-application)
* [Můžete restartovat mezipaměti zrušte připojení klienta?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Dojde I ke ztrátě dat z mé mezipaměti restartu dělat?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Můžete restartovat Moje mezipaměti pomocí prostředí PowerShell, rozhraní příkazového řádku nebo jiné nástroje pro správu?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Jaké cenové úrovně můžete použít funkci restartování počítače?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Uzel restartovat k testování Moje aplikace?
K testování odolnost vaší aplikace proti selhání primárního uzlu mezipaměti, restartovat **hlavní** uzlu. K testování aplikace proti selhání sekundárního uzlu odolnost, restartovat **podřízený** uzlu. K testování odolnost aplikace proti celkový počet selhání mezipaměti, restartovat **i** uzlů.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Můžete restartovat mezipaměti zrušte připojení klienta?
Ano, pokud restartování mezipaměti jsou vymazány všechna připojení klientů. Restartování může být užitečné v případě, kdy se používá všechna připojení klientů z důvodu logická chyba nebo chyb v aplikaci klienta. Každá cenová úroveň má jiný [omezení připojení klienta](cache-configure.md#default-redis-server-configuration) pro různé velikosti a jednou těchto omezení se dosáhne, jsou podmínky přijaty žádné další připojení klientů. Restartování mezipaměti poskytuje způsob, jak vymazat všechna připojení klientů.

> [!IMPORTANT]
> Pokud restartujete mezipaměti zrušte připojení klientů, StackExchange.Redis automaticky znovu připojí po Redis uzel je zpět do režimu online. Pokud není základní problém vyřešen, může nadále používat připojení klienta.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Dojde I ke ztrátě dat z mé mezipaměti restartu dělat?
Pokud restartujete i **hlavní** a **podřízený** uzly, dojde ke ztrátě všech dat v mezipaměti (nebo horizontálního oddílu Pokud používáte cache ve verzi premium s povoleným clusteringem). Pokud jste nakonfigurovali [trvalosti dat](cache-how-to-premium-persistence.md), nejvíce poslední zálohy se obnoví, když mezipaměť přejde do režimu online, ale všech mezipaměti zápisu, které nastaly po zálohy jsou ztraceny.

Pokud restartujete právě jeden z uzlů, data nejsou obvykle ztraceny, ale stále může být. Například pokud je hlavní uzel restartován a zápisu mezipaměti probíhá, data z mezipaměti zápisu je ztraceny. Jiné scénáře ztrátě dat bude-li restartovat jeden uzel a jiného uzlu se stane přejděte z důvodu chyby ve stejnou dobu. Další informace o možných příčinách ztráty dat najdete v tématu [co se stalo s mým datům v Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Můžete restartovat Moje mezipaměti pomocí prostředí PowerShell, rozhraní příkazového řádku nebo jiné nástroje pro správu?
Ano, pro prostředí PowerShell pokyny naleznete v části [restartování mezipaměti Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Jaké cenové úrovně můžete použít funkci restartování počítače?
Restart je k dispozici pro všechny cenové úrovně.

## <a name="schedule-updates"></a>Plán aktualizací
**Naplánovat aktualizace** okno umožňuje určit údržby pro mezipaměť úrovně Premium. Pokud je zadána pro správu a údržbu, jsou všechny aktualizace serveru Redis provedené během této doby. 

> [!NOTE] 
> Pro správu a údržbu platí jenom pro Redis serveru aktualizací a tak, aby všechny Azure aktualizace nebo aktualizace operačního systému virtuálních počítačů, které jsou hostiteli mezipaměti.
> 
> 

![Plán aktualizací](./media/cache-administration/redis-schedule-updates.png)

Zadejte časové období údržby, zkontrolujte požadované dny a zadejte hodina spouštění údržby okna pro každý den a klikněte na tlačítko **OK**. Všimněte si, že časového období údržby se ve standardu UTC. 

Okno údržby, default a minimální, aktualizací je pět hodin. Tato hodnota se nedá konfigurovat na portálu Azure, ale můžete ji nakonfigurovat v prostředí PowerShell pomocí `MaintenanceWindow` parametr [New-AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry) rutiny. Další informace najdete v tématu [můžete spravovat plánovaných aktualizací pomocí prostředí PowerShell, rozhraní příkazového řádku nebo jiné nástroje pro správu?](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)


## <a name="schedule-updates-faq"></a>Plán aktualizace – nejčastější dotazy
* [Pokud aktualizace dojít, pokud nepoužívám funkci plán aktualizace?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Jaký typ aktualizací probíhají během plánované údržby?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Můžete spravovat plánovaných aktualizací pomocí prostředí PowerShell, rozhraní příkazového řádku nebo jiné nástroje pro správu?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Jaké cenové úrovně můžete použít funkci plán aktualizace?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Pokud aktualizace dojít, pokud nepoužívám funkci plán aktualizace?
Pokud nezadáte údržby, můžete kdykoli provedeny aktualizace.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Jaký typ aktualizací probíhají během plánované údržby?
Pouze Redis serveru, které jsou provedeny aktualizace během plánované údržby. Pro správu a údržbu se nevztahuje na Azure aktualizace nebo aktualizace pro operační systém virtuálního počítače.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Můžete spravované plánovaných aktualizací pomocí prostředí PowerShell, rozhraní příkazového řádku nebo jiné nástroje pro správu?
Ano, můžete spravovat vaše naplánované aktualizace pomocí následující rutiny prostředí PowerShell:

* [Get-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/get-azurermrediscachepatchschedule)
* [New-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/new-azurermrediscachepatchschedule)
* [New-AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry)
* [Remove-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/remove-azurermrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Jaké cenové úrovně můžete použít funkci plán aktualizace?
**Naplánovat aktualizace** funkce je k dispozici v cenová úroveň premium.

## <a name="next-steps"></a>Další postup
* Prozkoumat více [Azure Redis Cache premium vrstvy](cache-premium-tier-intro.md) funkce.

