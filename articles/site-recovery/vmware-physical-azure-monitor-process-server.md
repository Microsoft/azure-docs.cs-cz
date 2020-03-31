---
title: Monitorování serveru pro obnovení webu Azure
description: Tento článek popisuje, jak sledovat server procesu obnovení webu Azure používaný pro zotavení po havárii virtuálního počítače v systému VMware nebo fyzického serveru.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257066"
---
# <a name="monitor-the-process-server"></a>Sledování procesního serveru

Tento článek popisuje, jak sledovat server [procesu obnovení webu.](site-recovery-overview.md)

- Procesní server se používá při nastavování zotavení po havárii místních virtuálních počítačích VMware a fyzických serverů do Azure.
- Ve výchozím nastavení je procesní server spuštěn na konfiguračním serveru. Je nainstalován ve výchozím nastavení při nasazení konfiguračního serveru.
- Volitelně můžete nasadit další procesní servery s horizontálním navýšením kapacity pro škálování a zpracování většího počtu replikovaných počítačů a vyšších objemů provozu replikace.

[Přečtěte si další informace](vmware-physical-azure-config-process-server-overview.md) o roli a nasazení procesních serverů.

## <a name="monitoring-overview"></a>Přehled monitorování

Vzhledem k tomu, že procesový server má tolik rolí, zejména v replikovaných dat ukládání do mezipaměti, komprese a přenosu do Azure, je důležité sledovat stav serveru procesu průběžně.

Existuje řada situací, které běžně ovlivňují výkon procesu serveru. Problémy ovlivňující výkon budou mít kaskádový vliv na stav virtuálního počítače, nakonec tlačí procesový server a jeho replikované počítače do kritického stavu. Situace zahrnují:

- Vysoký počet virtuálních stránek používá procesní server, který se blíží nebo překračuje doporučená omezení.
- Virtuální servery používající procesní server mají vysokou míru změn.
- Propustnost sítě mezi virtuálními servery a procesním serverem nestačí k nahrání dat replikace na procesní server.
- Propustnost sítě mezi procesním serverem a Azure nestačí k nahrání dat replikace z procesního serveru do Azure.

Všechny tyto problémy mohou ovlivnit cíl bodu obnovení (RPO) virtuálních zařízení. 

**Proč?** Protože generování bodu obnovení pro virtuální počítač vyžaduje, aby všechny disky na virtuálním počítači měly společný bod. Pokud má jeden disk vysokou rychlost změn, replikace je pomalá nebo procesový server není optimální, má vliv na to, jak efektivně jsou vytvořeny body obnovení.

## <a name="monitor-proactively"></a>Proaktivní monitorování

Chcete-li se vyhnout problémům s procesním serverem, je důležité:

- Seznamte se s konkrétními požadavky na procesní servery, které používají [pokyny pro kapacitu a velikost](site-recovery-plan-capacity-vmware.md#capacity-considerations), a ujistěte se, že procesní servery jsou nasazeny a spuštěny podle doporučení.
- Monitorujte výstrahy a vyřešujte problémy, jakmile k nim dojde, aby procesové servery běžely efektivně.


## <a name="process-server-alerts"></a>Zpracovat výstrahy serveru

Procesový server generuje řadu výstrah stavu shrnutých v následující tabulce.

**Typ upozornění** | **Podrobnosti**
--- | ---
![V pořádku][green] | Procesní server je připojen a v pořádku.
![Upozornění][yellow] | Využití procesoru > 80 % za posledních 15 minut
![Upozornění][yellow] | Využití paměti > 80% za posledních 15 minut
![Upozornění][yellow] | Volné místo ve složce mezipaměti < 30 % za posledních 15 minut
![Upozornění][yellow] | Site Recovery monitoruje čekající/odchozí data každých pět minut a odhaduje, že data v mezipaměti procesního serveru nelze odeslat do Azure do 30 minut.
![Upozornění][yellow] | Služby procesního serveru nejsou spuštěny za posledních 15 minut
![Kritická][red] | Využití procesoru > 95 % za posledních 15 minut
![Kritická][red] | Využití paměti > 95% za posledních 15 minut
![Kritická][red] | Volné místo ve složce mezipaměti < 25 % za posledních 15 minut
![Kritická][red] | Site Recovery monitoruje čekající/odchozí data každých pět minut a odhaduje, že data v mezipaměti procesního serveru nelze odeslat do Azure do 45 minut.
![Kritická][red] | Žádný prezenční signál z procesního serveru po dobu 15 minut.

![Klíč tabulky](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Celkový stav procesového serveru je založen na nejhorší generované výstrahě.



## <a name="monitor-process-server-health"></a>Sledování stavu serveru procesu

Stav procesních serverů můžete sledovat následujícím způsobem: 

1. Chcete-li sledovat stav replikace a stav replikovaného počítače a jeho procesního serveru, klepněte v úschovně > **replikovaných položek**na počítač, který chcete monitorovat.
2. V **stavu replikace**můžete sledovat stav virtuálního aplikace. Kliknutím na stav přejdete k podrobnostem o chybě.

    ![Stav procesního serveru v řídicím panelu virtuálního aplikace](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. V **části Stav procesního serveru**můžete sledovat stav procesního serveru. Podrobnosti zobrazí tecí.

    ![Zpracování podrobností o serveru na řídicím panelu virtuálního aplikace](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Stav lze také sledovat pomocí grafické ho znázornění na stránce virtuálního počítači.
    - Horizontální navýšení kapacity procesu server bude zvýrazněn oranžově, pokud jsou k němu spojena upozornění, a červená, pokud má nějaké kritické problémy. 
    - Pokud je procesní server spuštěn ve výchozím nasazení na konfiguračním serveru, bude odpovídajícím způsobem zvýrazněn konfigurační server.
    - Chcete-li přejít k podrobnostem, klikněte na konfigurační server nebo procesní server. Poznamenejte si všechny problémy a doporučení pro nápravu.

Můžete také sledovat procesní servery v úschovně v části **Infrastruktura obnovení webu**. V **části Správa infrastruktury site recovery**klepněte na **položku Konfigurační servery**. Vyberte konfigurační server přidružený k procesnímu serveru a přejděte k podrobnostem procesního serveru.


## <a name="next-steps"></a>Další kroky

- Pokud máte nějaké problémy s procesními servery, postupujte podle našich [pokynů pro řešení problémů](vmware-physical-azure-troubleshoot-process-server.md)
- Pokud potřebujete další pomoc, zadejte svůj dotaz ve [fóru Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
