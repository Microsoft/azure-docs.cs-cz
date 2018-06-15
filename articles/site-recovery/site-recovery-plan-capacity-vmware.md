---
title: Plánování kapacity a škálování replikace VMware do Azure s Azure Site Recovery | Microsoft Docs
description: Použijte tento článek k plánování kapacity a škálování při replikaci virtuálních počítačů VMware do Azure s Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/27/2018
ms.author: rayne
ms.openlocfilehash: dbaf1e29fbf4be8ef9432842b7ea4d6511b21cbb
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
ms.locfileid: "29692194"
---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>Plánování kapacity a škálování pro replikaci VMware s Azure Site Recovery

Pomocí tohoto článku a pokuste se zjistit plánování kapacity a škálování, při replikaci do Azure s místní virtuální počítače VMware a fyzické servery [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Jak spustit plánování kapacity?

Shromážděte informace o prostředí replikace spuštěním [Azure Site Recovery nasazení Planner](https://aka.ms/asr-deployment-planner-doc) pro replikaci VMware. [Další informace](site-recovery-deployment-planner.md) o tomto nástroji. Budete shromažďovat informace o kompatibilní a nekompatibilní virtuálních počítačů, disků na virtuální počítač, a změn dat na disk. Tento nástroj pokrývá i požadavky na šířku pásma sítě a infrastrukturu Azure potřebnou k úspěšné replikaci a testovacímu převzetí služeb při selhání.

## <a name="capacity-considerations"></a>Aspekty kapacity

**Komponenta** | **Podrobnosti** |
--- | --- | ---
**Replikace** | **Maximální denní míry změn:** chráněného počítače lze použít pouze jeden server proces a jeden procesový server může zpracovat každý den změnu míra až 2 TB. Proto je 2 TB maximální denních dat změnit rychlost, kterou je podporována pro chráněný počítač.<br/><br/> **Maximální propustnost:** replikovaného počítače můžou patřit do jeden účet úložiště v Azure. Standardní účet úložiště může zpracovat nesmí být delší než 20 000 požadavků za sekundu, a doporučujeme zachovat počet vstupně výstupních operací za sekundu (IOPS) napříč zdrojový počítač s 20 000. Například pokud je zdrojový počítač s 5 disků a každý disk generuje 120 IOPS (8 kb velikost) na zdrojovém počítači, pak bude v rámci Azure za maximální IOPS disku 500. (Počet účtů úložiště vyžaduje se rovná celkový zdrojového počítače IOPS, dělený 20 000.)
**Konfigurační server** | Konfigurační server by měla být schopna zpracovávat kapacitu denní míra změn mezi všechny úlohy spuštěné na chráněných počítačích a potřebuje dostatečnou šířku pásma pro nepřetržitě replikaci dat do úložiště Azure.<br/><br/> Jako osvědčený postup vyhledejte konfigurační server ve stejné síti a segment sítě LAN jako počítače, které chcete chránit. Může být umístěn v jiné síti, ale počítače, které chcete chránit, musí mít viditelnost vrstvy 3 sítě do ní.<br/><br/> V tabulce v následující části jsou shrnuté velikost doporučení pro konfigurační server.
**Procesový server** | První procesový server je nainstalována ve výchozím nastavení na konfiguračním serveru. Můžete nasadit servery další proces škálování prostředí. <br/><br/> Procesový server přijímá data replikace z chráněného počítače a optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování. Pak odešle data do Azure. Počítač serveru procesu by měl mít dostatek prostředků k provedení těchto úloh.<br/><br/> Procesový server používá diskové mezipaměti. Použijte samostatné mezipaměti disk 600 GB nebo více zpracovat změny dat, které jsou uložené v případě přetížení sítě nebo výpadek.

## <a name="size-recommendations-for-the-configuration-server"></a>Velikost doporučení pro konfigurační server

**CPU** | **Paměť** | **Velikost disku mezipaměti** | **Míry změny dat** | **Chráněné počítače**
--- | --- | --- | --- | ---
8 Vcpu (2 sockets * @ 2,5 GHz [GHz] 4 jádra) | 16 GB | 300 GB | 500 GB nebo méně | Replikovat počítače méně než 100.
12 Vcpu (2 sockets * @ 2,5 GHz 6 jader) | 18 GB | 600 GB | 500 GB až 1 TB | Replikovat mezi 100 150 počítačů.
16 Vcpu (2 sockets * @ 2,5 GHz 8 jader) | 32 GB | 1 TB | 1 TB 2 TB | Replikovat mezi 150 až 200 počítačů.
Nasazení jiný procesový server | | | > 2 TB | Nasaďte další proces servery, pokud replikujete více než 200 počítačů, nebo pokud se změní denních dat míra překročí 2 TB.

Kde:

* Každý zdrojový počítač je nakonfigurovaný s 3 disky 100 GB.
* Použili jsme testu typovou úlohou úložiště 8 disky SAS ot. / min, 10 kb s RAID 10 pro měření disku mezipaměti.

## <a name="size-recommendations-for-the-process-server"></a>Velikost doporučení u procesového serveru

Pokud je potřeba chránit více než 200 počítačů nebo denní míra změn je větší než 2 TB, můžete přidat servery proces pro zpracování zátěže replikace. Chcete-li škálovat, můžete:

* Zvyšte počet konfigurační servery. Například můžete chránit až 400 počítačů se dvěma servery konfigurace.
* Přidat další servery proces a použít pro zpracování provozu mezi místo (nebo kromě) konfigurační server.

Následující tabulka popisuje scénář, ve kterém:

* Nemáte v plánu používat konfigurační server jako procesní server.
* Nastavili jste si serveru další proces.
* Jste nakonfigurovali chráněné virtuální počítače používat další procesový server.
* Každý počítač chráněného zdroje je nakonfigurovaný s tři disky 100 GB.

**Konfigurační server** | **Další procesového serveru** | **Velikost disku mezipaměti** | **Míry změny dat** | **Chráněné počítače**
--- | --- | --- | --- | ---
8 Vcpu (2 sockets * 4 jádra @ 2,5 GHz), 16 GB paměti | 4 Vcpu (2 sockets * 2 jádra @ 2,5 GHz), 8 GB paměti | 300 GB | 250 GB nebo méně | Replikovat počítače 85 nebo méně.
8 Vcpu (2 sockets * 4 jádra @ 2,5 GHz), 16 GB paměti | 8 Vcpu (2 sockets * 4 jádra @ 2,5 GHz), 12 GB paměti | 600 GB | 250 GB až 1 TB | Replikovat mezi 85 150 počítačů.
12 Vcpu (2 sockets * @ 2,5 GHz 6 jader), 18 GB paměti | 12 Vcpu (2 sockets * @ 2,5 GHz 6 jader) 24 GB paměti | 1 TB | 1 TB 2 TB | Replikovat mezi 150 225 počítačů.

Způsob, ve kterém můžete škálovat vaše servery závisí na vaši volbu pro model škálování nebo Škálováním na více systémů.  Vertikální navýšení kapacity nasazením několik vyšší kategorie konfigurace a proces servery nebo vertikální navýšení kapacity provádíte nasazení více serverů s méně prostředků. Například pokud je třeba chránit 220 počítače, můžete to udělat některým z těchto způsobů:

* Nastavení konfigurace serveru s 12 virtuálních procesorů, 18 GB paměti a aplikace 12 virtuálních procesorů, 24 GB paměti na server další proces. Nakonfigurujte chráněné počítače používat pouze server další proces.
* Nastavte dva servery configuration (2 x 8 virtuálních procesorů, 16 GB paměti RAM) a dva další proces servery (1 × 8 virtuálních procesorů a 4 virtuální procesory x 1 k popisovači 135 + 85 [220] počítače). Nakonfigurujte chráněné počítače používat pouze servery další proces.


## <a name="control-network-bandwidth"></a>Řídí šířku pásma sítě

Poté, co jste použili [nástroj pro nasazení Planner](site-recovery-deployment-planner.md) můžete vypočítat šířku pásma potřebujete pro replikaci (počáteční replikace a pak rozdílové), můžete řídit šířku pásma používané pro replikaci pomocí několika možností:

* **Omezení šířky pásma**: přenosů VMware, která se replikují do Azure prochází konkrétní procesní server. Můžete omezit šířku pásma na počítačích běžících jako servery procesu.
* **Ovlivnění šířky pásma**: můžete ovlivnit šířku pásma používanou pro replikaci pomocí několika klíčů registru:
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** hodnotu registru určuje počet vláken, které se používají pro přenos dat (počáteční nebo rozdílové replikace) disku. Vyšší hodnota zvětšuje šířku pásma sítě využívané pro replikaci.
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** určuje počet vláken používaných pro přenos dat během navrácení služeb po obnovení.

### <a name="throttle-bandwidth"></a>Omezení šířky pásma

1. Otevřete modul snap-in Azure Backup konzoly MMC v počítači, který funguje jako procesový server. Ve výchozím zástupce pro zálohování je k dispozici na ploše nebo v následující složce: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. V modulu snap-in klikněte na **Změnit vlastnosti**.

    ![Snímek obrazovky nástroje Azure Backup konzoly MMC modul snap-in možnost změnit vlastnosti](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na **omezování** vyberte **povolit využití šířky pásma Internetu u operací zálohování omezení**. Nastavte limity pro pracovní a nepracovní dobu. Platné rozsahy jsou 512 kB/s až 1023 MB/s za sekundu.

    ![Dialogové okno snímek obrazovky Vlastnosti zálohování Azure](./media/site-recovery-vmware-to-azure/throttle2.png)

Pro nastavení omezování můžete také použít rutinu [Set OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx). Tady je ukázkový skript:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** označuje, že není požadováno žádné omezování.

### <a name="influence-network-bandwidth-for-a-vm"></a>Ovlivnění šířky pásma sítě pro virtuální počítač

1. V registru Virtuálního počítače, přejděte na **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * K ovlivnění šířky pásma provoz na replikaci disku, změňte hodnotu z **UploadThreadsPerVM**, nebo vytvořte klíč, pokud neexistuje.
   * K ovlivnění šířky pásma pro přenosy navrácení služeb po obnovení z Azure, upravte hodnotu **DownloadThreadsPerVM**.
2. Výchozí hodnota je 4. V síti s „nadměrným zřízením“ je třeba tyto klíče registru změnit z výchozích hodnot. Maximum je 32. Monitorováním provozu hodnotu optimalizujte.


## <a name="deploy-additional-process-servers"></a>Nasadit další proces servery

Pokud máte škálování mimo nasazení nad 200 zdrojového počítače, nebo máte celkem denně změn počet více než 2 TB, musíte serverů další proces pro zpracování provozu. Postupujte podle těchto pokynů můžete nastavit na procesní server. Po nastavení serveru, migrovat zdrojový počítač používat.

1. V **servery Site Recovery**, klikněte na server, konfigurace a pak klikněte na tlačítko **procesový Server**.

    ![Snímek obrazovky Site Recovery serverů možnost Přidat procesového serveru](./media/site-recovery-vmware-to-azure/migrate-ps1.png)
2. V **typ serveru**, klikněte na tlačítko **procesového serveru (místní)**.

    ![Dialogové okno snímek obrazovky procesového serveru](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
3. Stáhněte si soubor Unified instalace nástroje Site Recovery a spusťte jej nainstalovat na procesní server. To také zaregistruje ho v trezoru.
4. V části **Než začnete** vyberte **Přidat další procesové servery pro horizontální navýšení kapacity nasazení**.
5. Stejně jako jste to udělali při dokončení průvodce můžete [nastavit](#step-2-set-up-the-source-environment) konfigurační server.

    ![Snímek obrazovky z instalace Azure Site Recovery Unified Průvodce](./media/site-recovery-vmware-to-azure/add-ps1.png)
6. V **podrobnosti o konfiguraci serveru**, zadejte IP adresu konfiguračního serveru a heslo. Chcete-li získat přístupové heslo, spusťte **[SiteRecoveryInstallationFolder]\home\sysystems\bin\genpassphrase.exe – n** na konfiguračním serveru.

    ![Stránka Podrobnosti – snímek obrazovky nástroje Konfigurace serveru](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migraci počítačů používat nový procesový server
1. V **nastavení** > **servery Site Recovery**, klikněte na server, konfigurace a potom rozbalte **zpracovat servery**.

    ![Dialogové okno snímek obrazovky procesového serveru](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Klikněte pravým tlačítkem na procesový server aktuálně používán a klikněte na tlačítko **přepínač**.

    ![Dialogové okno snímek obrazovky nástroje Konfigurace serveru](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. V **vyberte cílový procesový server**, vyberte nový procesový server, kterou chcete použít a potom vyberte virtuální počítače, které server, bude zpracovávat. Klikněte na ikonu informace získat informace o serveru. Pomoc při provádění načíst rozhodnutí, se zobrazí průměrnou prostor, který je potřeba k replikaci každý vybraný virtuální počítač na nový procesový server. Kliknutím na značku zaškrtnutí zahájíte replikace pro nový procesový server.


## <a name="next-steps"></a>Další postup

Stažení a spuštění [Azure Site Recovery nasazení plánovače](https://aka.ms/asr-deployment-planner)
