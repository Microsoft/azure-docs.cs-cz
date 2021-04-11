---
title: Monitorování Azure Site Recovery procesového serveru
description: Tento článek popisuje, jak monitorovat Azure Site Recovery procesový Server, který se používá pro zotavení po havárii virtuálního počítače VMware nebo fyzického serveru.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 0dcee641a2de73238557a6a29173f71ee1c4312a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580567"
---
# <a name="monitor-the-process-server"></a>Monitorování procesového serveru

Tento článek popisuje, jak monitorovat [Site Recovery](site-recovery-overview.md) procesový Server.

- Procesový Server se používá při nastavení zotavení po havárii místních virtuálních počítačů VMware a fyzických serverů do Azure.
- Ve výchozím nastavení běží procesový Server na konfiguračním serveru. Je nainstalována ve výchozím nastavení při nasazení konfiguračního serveru.
- Pokud budete chtít škálovat a zpracovávat větší počet replikovaných počítačů a vyšších objemů replikačních přenosů, můžete nasadit další procesové servery se škálováním na více instancí.

[Přečtěte si další informace](vmware-physical-azure-config-process-server-overview.md) o roli a nasazení procesových serverů.

## <a name="monitoring-overview"></a>Přehled monitorování

Vzhledem k tomu, že procesový Server má tolik rolí, zejména při ukládání replikovaných dat do mezipaměti, kompresi a přenos do Azure, je důležité monitorovat stav procesového serveru průběžně.

K dispozici je řada situací, které mají obvykle vliv na výkon procesového serveru. Problémy, které mají vliv na výkon, budou mít na stav virtuálních počítačů kaskádový efekt, takže se procesový Server i jeho replikované počítače dostanou do kritického stavu. Mezi situace patří:

- Vysoký počet virtuálních počítačů používá procesový Server, který se blíží nebo nepřekračuje doporučeným omezením.
- Virtuální počítače používající procesový Server mají vysokou frekvenci přenosů.
- Propustnost sítě mezi virtuálními počítači a procesovým serverem není dostatečná pro nahrání dat replikace na procesový Server.
- Propustnost sítě mezi procesovým serverem a Azure není dostatečná pro nahrání dat replikace z procesového serveru do Azure.

Všechny tyto problémy mohou ovlivnit cíl bodu obnovení (RPO) virtuálních počítačů. 

**Proč?** Vzhledem k tomu, že generování bodu obnovení pro virtuální počítač vyžaduje, aby všechny disky na virtuálním počítači měly společný bod. Pokud má jeden disk velkou četnost změn, je replikace pomalá nebo procesový Server není optimální, ovlivňuje způsob, jakým se vytvářejí efektivní body obnovení.

## <a name="monitor-proactively"></a>Proaktivní monitorování

Aby se zabránilo problémům s procesovým serverem, je důležité:

- Seznamte se s konkrétními požadavky na procesové servery s využitím [pokynů pro kapacitu a velikosti](site-recovery-plan-capacity-vmware.md#capacity-considerations)a zajistěte, aby byly procesní servery nasazené a spuštěné podle doporučení.
- Sledujte výstrahy a řešte problémy při jejich výskytu a udržujte tak efektivní procesy procesů.


## <a name="process-server-alerts"></a>Výstrahy procesového serveru

Procesový Server vygeneruje množství upozornění na stav, shrnuté v následující tabulce.

**Typ upozornění** | **Podrobnosti**
--- | ---
![V pořádku][green] | Procesový Server je připojený a v pořádku.
![Upozornění][yellow] | Využití CPU > 80% za posledních 15 minut
![Upozornění][yellow] | Využití paměti > 80% za posledních 15 minut.
![Upozornění][yellow] | Volné místo ve složce mezipaměti < 30% za posledních 15 minut
![Upozornění][yellow] | Site Recovery monitorovat nevyřízená nebo odchozí data každých pět minut a odhadne, že data v mezipaměti procesového serveru nejde do 30 minut nahrát do Azure.
![Upozornění][yellow] | Služby procesového serveru nejsou spuštěné za posledních 15 minut.
![Kritické][red] | Využití CPU > 95% za posledních 15 minut
![Kritické][red] | Využití paměti > 95% za posledních 15 minut.
![Kritické][red] | Volné místo ve složce mezipaměti < 25% za posledních 15 minut
![Kritické][red] | Site Recovery monitorovat nevyřízená nebo odchozí data každých pět minut a odhadne, že data v mezipaměti procesového serveru nejde do Azure nahrávat během 45 minut.
![Kritické][red] | Žádný prezenční signál od procesového serveru po dobu 15 minut.

![Klíč tabulky](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Celkový stav procesového serveru je založený na nejhorším vygenerovaném upozornění.



## <a name="monitor-process-server-health"></a>Monitorovat stav procesového serveru

Stav serverů procesů můžete monitorovat následujícím způsobem: 

1. Pokud chcete monitorovat stav replikace a stav replikovaného počítače a jeho procesový Server, v trezoru > **replikované položky** klikněte na počítač, který chcete monitorovat.
2. V části stav **replikace** můžete monitorovat stav virtuálního počítače. Kliknutím na stav přejdete k podrobnostem o podrobnostech o chybě.

    ![Stav procesového serveru na řídicím panelu virtuálních počítačů](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. V části stav **procesového** serveru můžete monitorovat stav procesového serveru. Podrobnosti najdete v podrobnostech.

    ![Podrobnosti procesového serveru na řídicím panelu virtuálních počítačů](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Stav lze také monitorovat pomocí grafické reprezentace na stránce virtuálního počítače.
    - Procesový Server se škálováním na více instancí bude zvýrazněný oranžová, pokud jsou k němu přidružená nějaká upozornění a červené, pokud má kritické problémy. 
    - Pokud je procesový Server spuštěný ve výchozím nasazení na konfiguračním serveru, pak bude konfigurační server zvýrazněný odpovídajícím způsobem.
    - Chcete-li přejít k podrobnostem, klikněte na konfigurační server nebo procesový Server. Poznamenejte si všechny problémy a veškerá doporučení k nápravě.

Můžete také monitorovat procesní servery v trezoru v části **Site Recovery infrastruktura**. V oblasti **Správa infrastruktury Site Recovery** klikněte na **konfigurační servery**. Vyberte konfigurační server přidružený k procesu serveru a přejděte k podrobnostem o procesovém serveru.


## <a name="next-steps"></a>Další kroky

- Pokud máte nějaké problémy s procesovým serverem, postupujte podle pokynů pro [řešení potíží](vmware-physical-azure-troubleshoot-process-server.md) .
- Pokud potřebujete další nápovědu, vystavte svůj dotaz na [stránce s dotazem Microsoft Q&Azure Site Recovery](/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
