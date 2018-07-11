---
title: Plánování kapacity a škálování pro replikaci VMware do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Použijte tento článek k plánování kapacity a škálování při replikaci virtuálních počítačů VMware do Azure pomocí Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.date: 07/06/2018
ms.topic: conceptual
ms.author: rayne
ms.openlocfilehash: 905798acd5836c31953714d7984cfb19f16cecab
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920793"
---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>Plánování kapacity a škálování pro replikaci VMware pomocí Azure Site Recovery

Pomocí tohoto článku můžete zjistit, plánování kapacity a škálování, při replikaci místních virtuálních počítačů VMware a fyzických serverů do Azure s využitím [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Jak začít, plánování kapacity?

Shromážděte informace o prostředí replikace spuštěním [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) pro replikaci VMware. [Přečtěte si další informace](site-recovery-deployment-planner.md) o tomto nástroji. Budete shromažďovat informace o kompatibilních a nekompatibilních virtuálních počítačích, discích jednotlivých virtuálních počítačů, a četnost změn dat na disku. Tento nástroj pokrývá i požadavky na šířku pásma sítě a infrastrukturu Azure potřebnou k úspěšné replikaci a testovacímu převzetí služeb při selhání.

## <a name="capacity-considerations"></a>Důležité informace o kapacity

**Komponenta** | **Podrobnosti** |
--- | --- | ---
**Replikace** | **Maximální denní frekvenci změn:** chráněného počítače lze použít pouze jeden procesový server, a jeden procesový server dokáže zpracovat denní změnu přenosové rychlosti až do velikosti 2 TB. 2 TB tedy, že že maximální frekvence každodenní změny dat, která je podporována pro chráněný počítač.<br/><br/> **Maximální propustnost:** replikovaného počítače můžou patřit do jednoho účtu úložiště v Azure. Účet standard storage dokáže zpracovat až 20 000 požadavků za sekundu. proto doporučujeme ponechat počet vstupně výstupní operace za sekundu (IOPS) ve zdrojovém počítači na 20 000. Například pokud máte zdrojový počítač s 5 disků a každý disk generuje 120 vstupně-výstupních operací (velikosti 8 kB) na zdrojovém počítači, pak bude v rámci Azure za maximální IOPS na disku 500. (Počet účtů úložiště vyžaduje je rovna celkové zdrojový počítač vstupně-výstupních operací, dělený 20 000.)
**Konfigurační server** | Konfigurační server by měl být schopen zpracovat denní kapacitu změnit rychlost přes všechny úlohy spuštěné na chráněných počítačích a potřebuje dostatečnou šířku pásma, pokud chcete nepřetržitě replikovat data do služby Azure Storage.<br/><br/> Jako osvědčený postup vyhledejte konfigurační server na segment sítě LAN ve stejné síti jako počítače, které chcete chránit. Můžou být umístěné na jinou síť, ale počítače, které chcete chránit, by měly mít viditelnost vrstvy 3 sítě do ní.<br/><br/> V tabulce v následující části jsou shrnuté doporučené velikosti pro konfigurační server.
**Procesový server** | První procesový server je nainstalovaný ve výchozím nastavení na konfiguračním serveru. Můžete nasadit další Procesové servery pro horizontální vašeho prostředí. <br/><br/> Procesový server přijímá data replikace z chráněného počítače a optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování. Pak odešle data do Azure. Počítač serveru proces by měla mít dostatek prostředků k provedení těchto úloh.<br/><br/> Procesový server používá mezipaměť založené na disku. Použijte samostatný mezipaměti disku 600 GB nebo více ke zpracování změny dat uložených v případě kritický bod sítě nebo kvůli výpadku.

## <a name="size-recommendations-for-the-configuration-server"></a>Doporučené velikosti pro konfigurační server

**CPU** | **Paměť** | **Velikost mezipaměti disku** | **Frekvence změny dat** | **Chráněné počítače**
--- | --- | --- | --- | ---
8 virtuálních procesorů (2 sockets * @ 2,5 GHz [GHz] 4 jádra) | 16 GB | 300 GB | 500 GB nebo méně | Replikace méně než 100 počítačů.
12 virtuálních procesorů (2 sockets * @ 2,5 GHz 6 jader) | 18 GB | 600 GB | 500 GB až 1 TB | Replikace mezi 100 150 počítačů.
16 virtuálních procesorů (2 sockets * @ 2,5 GHz 8 jader) | 32 GB | 1 TB | 1 TB na 2 TB | Replikace mezi 150 až 200 počítačů.
Nasazení jiný procesový server | | | > 2 TB | Nasazení dalších procesových serverů, pokud replikujete více než 200 počítačů nebo pokud každodenní změny dat překračuje rychlost 2 TB.

Kde:

* Každý zdrojový počítač je nakonfigurován se 3 disky o 100 GB.
* Jsme použili srovnávací testy úložiště z 10 TIS ot. / min, 8 disků SAS pomocí diskového pole RAID 10 pro měření mezipaměti disku.

## <a name="size-recommendations-for-the-process-server"></a>Velikost doporučení k procesového serveru

Pokud potřebujete chránit více než 200 počítačů nebo denní frekvenci změn je větší než 2 TB, můžete přidat procesní servery pro zpracování zátěže replikace. Chcete-li horizontálně navýšit kapacitu, můžete:

* Zvýšíte počet konfiguračních serverů. Například můžete chránit až 400 počítačů s dva konfigurační servery.
* Přidat další procesových serverů a pomocí nich zpracovat provoz místo (nebo kromě) konfiguračního serveru.

Následující tabulka popisuje scénář, ve kterém:

* Nemáte v úmyslu použít konfigurační server jako procesový server.
* Nastavili jste si další procesový server.
* Nakonfigurujete chráněné virtuální počítače používat další procesový server.
* Každý počítač chráněného zdroje se nakonfigurují tři disky 100 GB.

**Konfigurační server** | **Další procesový server** | **Velikost mezipaměti disku** | **Frekvence změny dat** | **Chráněné počítače**
--- | --- | --- | --- | ---
8 virtuálních procesorů (2 sockets * @ 2,5 GHz 4 jádra), 16 GB paměti | 4 virtuální procesory (2 sockets * @ 2,5 GHz 2 jádra), 8 GB paměti | 300 GB | Aby se 250 GB nebo méně | Replikace počítačů 85 nebo méně.
8 virtuálních procesorů (2 sockets * @ 2,5 GHz 4 jádra), 16 GB paměti | 8 virtuálních procesorů (2 sockets * @ 2,5 GHz 4 jádra), 12 GB paměti | 600 GB | 250 GB až 1 TB | Replikace mezi 85 150 počítačů.
12 virtuálních procesorů (2 sockets * @ 2,5 GHz 6 jader), 18 GB paměti | 12 virtuálních procesorů (2 sockets * @ 2,5 GHz 6 jader) 24 GB paměti | 1 TB | 1 TB na 2 TB | Replikace mezi 150 225 počítačů.

Způsob, ve kterém škálování serverů, závisí na vaši volbu pro vertikální nebo horizontální navýšení kapacity modelu.  Vertikálně navýšit kapacitu nasazením špičkové konfigurace a procesových serverů, několik nebo vertikální navýšení kapacity provádíte nasazení více serverů s méně prostředků. Například pokud potřebujete k ochraně počítačů 220, proveďte z následujících akcí:

* Nastavení konfigurace serveru s 12 virtuálních procesorů, 18 GB paměti a další procesový server s 12 virtuálních procesorů, 24 GB paměti. Chráněné počítače používat jenom další procesový server nakonfigurujte.
* Nastavte dvě konfigurační servery (2 × 8 virtuálních procesorů, 16 GB paměti RAM) a dvou dalších procesových serverů (1 × 8 virtuálních procesorů a 4 virtuální procesory x 1 obsluhovat 135 + 85 [220] počítače). Nakonfigurujte chráněné počítače používat dalších procesových serverů.


## <a name="control-network-bandwidth"></a>Řídí šířku pásma sítě

Poté, co jste použili [nástroj Deployment Planner](site-recovery-deployment-planner.md) k výpočtu šířky pásma pro replikaci (počáteční replikace a pak rozdílovou) potřebujete, můžete řídit šířku pásma používanou k replikaci pomocí několika možností:

* **Omezení šířky pásma**: VMware provozu, která se replikují do Azure, prochází konkrétním procesový server. Můžete omezit šířku pásma počítače spuštěné jako procesových serverů.
* **Ovlivnění šířky pásma**: můžete ovlivnit pásma používanou k replikaci pomocí několika klíčů registru:
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** hodnotu registru určuje počet vláken, které se používají pro přenos dat (počáteční nebo rozdílové replikace) disku. Vyšší hodnota zvětšuje šířku pásma sítě využívané pro replikaci.
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** určuje počet vláken, použitá pro přenos dat během navrácení služeb po obnovení.

### <a name="throttle-bandwidth"></a>Omezení šířky pásma

1. Otevřete modul snap-in Azure Backup konzoly MMC v počítači, který funguje jako procesový server. Ve výchozím nastavení, zástupce pro zálohování je k dispozici na ploše nebo v následující složce: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. V modulu snap-in klikněte na **Změnit vlastnosti**.

    ![Snímek obrazovky z Azure Backup konzoly MMC modul snap-in možnost změnit vlastnosti](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na **omezování** kartu, vyberte možnost **Povolit omezování šířky pásma Internetu u operací zálohování**. Nastavte limity pro pracovní a nepracovní dobu. Platné rozsahy jsou 512 kB/s až 1023 MB/s za sekundu.

    ![Dialogové okno Vlastnosti snímku obrazovky z Azure Backup](./media/site-recovery-vmware-to-azure/throttle2.png)

Pro nastavení omezování můžete také použít rutinu [Set OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx). Tady je ukázkový skript:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** označuje, že není požadováno žádné omezování.

### <a name="influence-network-bandwidth-for-a-vm"></a>Ovlivnění šířky pásma sítě pro virtuální počítač

1. V registru Virtuálního počítače, přejděte na **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * K ovlivnění šířky pásma provoz na replikačního disku, upravte hodnotu **UploadThreadsPerVM**, nebo vytvořit klíč, pokud neexistuje.
   * K ovlivnění šířky pásma pro přenosy navrácení služeb po obnovení z Azure, upravte hodnotu **DownloadThreadsPerVM**.
2. Výchozí hodnota je 4. V síti s „nadměrným zřízením“ je třeba tyto klíče registru změnit z výchozích hodnot. Maximum je 32. Monitorováním provozu hodnotu optimalizujte.


## <a name="deploy-additional-process-servers"></a>Nasazení dalších procesových serverů

Pokud máte ke škálování nasazení nad 200 zdrojové počítače, nebo máte celkem denní četnosti změn počet větší než 2 TB, můžete potřebovat další procesní servery pro zpracování objem přenosů. Postupujte podle pokynů na [v tomto článku](vmware-azure-set-up-process-server-scale.md) nastavit procesový server. Po nastavení serveru, můžete provést migraci zdrojové počítače jeho použití.

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrace počítačů používat nový procesový server

1. V **nastavení** > **Site Recovery servery**, klikněte na konfiguračním serveru a potom rozbalte **zpracovat servery**.

    ![Dialogové okno snímek obrazovky procesový Server](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Klikněte pravým tlačítkem na procesový server aktuálně používán a klikněte na tlačítko **přepínač**.

    ![Dialogové okno snímek obrazovky nástroje Konfigurace serveru](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. V **vyberte cílový procesový server**, vyberte nový procesový server, který chcete použít a potom vyberte virtuální počítače, které budou zpracovávat na serveru. Klikněte na ikonu informace a získat informace o serveru. Pro vám pomůže zajistit načíst rozhodnutí, zobrazí se průměrný prostor, který je nezbytný k replikaci každý vybraný virtuální počítač pro nový procesový server. Kliknutím na značku zaškrtnutí zahájíte replikaci pro nový procesový server.

## <a name="deploy-additional-master-target-servers"></a>Nasadit další hlavní cílové servery

Budete potřebovat další hlavní cílový server během následující scénáře

1. Pokud se pokoušíte ochránit virtuální počítač s linuxem.
2. Pokud je k dispozici na konfiguračním serveru hlavní cílový server nemá přístup k úložišti dat virtuálního počítače.
3. Pokud celkový počet disků na hlavní cílový server (ne. místní disky na serveru) + disky, které se mají chránit překročí 60 disky.

Chcete-li přidat nový hlavní cílový server pro **virtuálního počítače založeného na Linuxu**, [kliknutím sem](vmware-azure-install-linux-master-target.md).

Pro **virtuálního počítače založeného na Windows**, postupujte podle níže uvedených pokynů.

1. Přejděte do **trezor služby Recovery Services** > **infrastruktura Site Recovery** > **konfigurační servery**.
2. Klikněte na požadované konfigurační server > **+ hlavní cílový Server**.![ Přidat master-target-server.png](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Stažení sjednocené instalace a spuštění ve virtuálním počítači vytvořit hlavní cílový server.
4. Zvolte **instalace hlavního cílového** > **Další**. ![Zvolte MT.PNG](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Zvolte výchozí umístění instalace > klikněte na tlačítko **nainstalovat**. ![Instalace mt.](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Klikněte na **pokračovat na konfiguraci** zaregistrovat hlavní cílový u konfiguračního serveru. ![MT pokračovat configuration.PNG](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Zadejte IP adresu konfiguračního serveru a heslo. [Kliknutím sem](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) se naučíte generovat přístupové heslo.![ cs-ip heslo](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Klikněte na tlačítko **zaregistrovat** a klikněte na možnost registrace příspěvek **Dokončit**.
9. Po úspěšné registraci tohoto serveru je uvedena na portálu v části **trezor služby Recovery Services** > **infrastruktura Site Recovery** > **konfigurace servery** > hlavní cílové servery relevantní konfiguračního serveru.

 >[!NOTE]
 >Můžete také stáhnout nejnovější verzi hlavní cílový server sjednocené instalace pro Windows [tady](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Další postup

Stáhněte a spusťte [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner)
