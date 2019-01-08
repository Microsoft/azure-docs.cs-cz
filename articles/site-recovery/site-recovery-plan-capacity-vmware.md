---
title: Plánování kapacity a škálování pro zotavení po havárii VMware do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Použijte tento článek k plánování kapacity a škálování při nastavování zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: dc903fca206f5d40f631181b83252f505b9f57a2
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065201"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Plánování kapacity a škálování pro zotavení po havárii VMware do Azure

Pomocí tohoto článku můžete zjistit, plánování kapacity a škálování, při replikaci místních virtuálních počítačů VMware a fyzických serverů do Azure s využitím [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Jak začít, plánování kapacity?

Znát požadavky infrastruktura Azure Site Recovery, shromážděte informace o prostředí replikace spuštěním [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) pro replikaci VMware. [Přečtěte si další informace](site-recovery-deployment-planner.md) o tomto nástroji. Tento nástroj poskytuje sestavy s kompletní informace o kompatibilních a nekompatibilních virtuálních počítačů, discích jednotlivých virtuálních počítačů, a četnost změn dat na disku. Nástroj také obsahuje souhrn požadavků na šířku pásma sítě pro splnění cíle cíle bodu obnovení a infrastrukturu Azure potřebnou k úspěšné replikaci a testovací převzetí služeb při selhání.

## <a name="capacity-considerations"></a>Důležité informace o kapacity

**Komponenta** | **Podrobnosti** |
--- | --- | ---
**Replikace** | **Maximální denní frekvenci změn:** Chráněný počítač lze použít pouze jeden procesový server, a jeden procesový server dokáže zpracovat denní změnu přenosové rychlosti až do velikosti 2 TB. 2 TB tedy, že že maximální frekvence každodenní změny dat, která je podporována pro chráněný počítač.<br/><br/> **Maximální propustnost:** Replikované počítače můžou patřit do jednoho účtu úložiště v Azure. Účet standard storage dokáže zpracovat až 20 000 požadavků za sekundu. proto doporučujeme ponechat počet vstupně výstupní operace za sekundu (IOPS) ve zdrojovém počítači na 20 000. Například pokud máte zdrojový počítač s 5 disků a každý disk generuje 120 vstupně-výstupních operací (velikosti 8 kB) na zdrojovém počítači, pak bude v rámci Azure za maximální IOPS na disku 500. (Počet účtů úložiště vyžaduje je rovna celkové zdrojový počítač vstupně-výstupních operací, dělený 20 000.)
**Konfigurační server** | Konfigurační server by měl být schopen zpracovat denní kapacitu změnit rychlost přes všechny úlohy spuštěné na chráněných počítačích a potřebuje dostatečnou šířku pásma, pokud chcete nepřetržitě replikovat data do služby Azure Storage.<br/><br/> Jako osvědčený postup vyhledejte konfigurační server na segment sítě LAN ve stejné síti jako počítače, které chcete chránit. Můžou být umístěné na jinou síť, ale počítače, které chcete chránit, by měly mít viditelnost vrstvy 3 sítě do ní.<br/><br/> V tabulce v následující části jsou shrnuté doporučené velikosti pro konfigurační server.
**Procesový server** | První procesový server je nainstalovaný ve výchozím nastavení na konfiguračním serveru. Můžete nasadit další Procesové servery pro horizontální vašeho prostředí. <br/><br/> Procesový server přijímá data replikace z chráněného počítače a optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování. Pak odešle data do Azure. Počítač serveru proces by měla mít dostatek prostředků k provedení těchto úloh.<br/><br/> Procesový server používá mezipaměť založené na disku. Použijte samostatný mezipaměti disku 600 GB nebo více ke zpracování změny dat uložených v případě kritický bod sítě nebo kvůli výpadku.

## <a name="size-recommendations-for-the-configuration-server-along-with-in-built-process-server"></a>Doporučené velikosti pro konfigurační server (spolu s integrovaný procesový server)

Každý server konfigurace nasazené prostřednictvím [šablony OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) obsahuje integrované procesový server. Prostředky konfiguračního serveru, jako je procesor, paměť, volné místo přistupujících s rychlostí při integrované procesový server se používá k ochraně virtuálních počítačů. Proto požadavky lišit při využít integrované procesový server.
Konfigurační server, kde se používá integrované procesový server k ochraně úloh dokáže zpracovat až 200 virtuálních počítačů na základě následujících konfigurací

**CPU** | **Paměť** | **Velikost mezipaměti disku** | **Frekvence změny dat** | **Chráněné počítače**
--- | --- | --- | --- | ---
8 virtuálních procesorů (2 sockets * 4 jádra \@ 2,5 GHz [GHz]) | 16 GB | 300 GB | 500 GB nebo méně | Replikace méně než 100 počítačů.
12 virtuálních procesorů (2 sockets * 6 jader \@ 2,5 GHz) | 18 GB | 600 GB | 500 GB až 1 TB | Replikace mezi 100 150 počítačů.
16 virtuálních procesorů (2 sockets * 8 jader \@ 2,5 GHz) | 32 GB | 1 TB | 1 TB na 2 TB | Replikace mezi 150 až 200 počítačů.
Nasazení jiném konfiguračním serveru prostřednictvím [šablony OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) | | | | Pokud replikujete více než 200 počítačů, nasadíte nový konfigurační server.
Nasadit další [procesového serveru](vmware-azure-set-up-process-server-scale.md#download-installation-file) | | | &GT; 2 TB| Nasaďte nový horizontální navýšení kapacity procesový server, když celková denní četnost změn dat překračuje 2 TB.

Kde:

* Každý zdrojový počítač je nakonfigurován se 3 disky o 100 GB.
* Jsme použili srovnávací testy úložiště z 10 TIS ot. / min, 8 disků SAS pomocí diskového pole RAID 10 pro měření mezipaměti disku.

## <a name="size-recommendations-for-the-process-server"></a>Velikost doporučení k procesového serveru

Procesový server je komponenta, která zpracovává proces replikace dat ve službě Azure Site Recovery. Pokud denní frekvenci změn je větší než 2 TB, budete muset přidat horizontální navýšení kapacity procesních serverů pro zpracování zátěže replikace. Chcete-li horizontálně navýšit kapacitu, můžete:

* Zvýšit počet konfiguračních serverů a nasadit prostřednictvím [šablony OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). Například můžete chránit až 400 počítačů s dva konfigurační servery.
* Přidat [horizontální navýšení kapacity procesových serverů](vmware-azure-set-up-process-server-scale.md#download-installation-file)a pomocí nich zpracovat provoz replikace místo (nebo kromě) konfiguračního serveru.

Následující tabulka popisuje scénář, ve kterém:

* Nastavili jste si horizontální navýšení kapacity procesového serveru.
* Nakonfigurujete chráněné virtuální počítače používat horizontální navýšení kapacity procesového serveru.
* Každý počítač chráněného zdroje se nakonfigurují tři disky 100 GB.

**Další procesový server** | **Velikost mezipaměti disku** | **Frekvence změny dat** | **Chráněné počítače**
--- | --- | --- | ---
4 virtuální procesory (2 sockets * 2 jádra \@ 2,5 GHz), 8 GB paměti | 300 GB | Aby se 250 GB nebo méně | Replikace počítačů 85 nebo méně.
8 virtuálních procesorů (2 sockets * 4 jádra \@ 2,5 GHz), 12 GB paměti | 600 GB | 250 GB až 1 TB | Replikace mezi 85 150 počítačů.
12 virtuálních procesorů (2 sockets * 6 jader \@ 2,5 GHz) 24 GB paměti | 1 TB | 1 TB na 2 TB | Replikace mezi 150 225 počítačů.

Způsob, ve kterém škálování serverů, závisí na vaši volbu pro vertikální nebo horizontální navýšení kapacity modelu.  Vertikálně navýšit kapacitu nasazením špičkové konfigurace a procesových serverů, několik nebo vertikální navýšení kapacity provádíte nasazení více serverů s méně prostředků. Například pokud je potřeba chránit 200 počítačů s četnost změn dat celkové každý den v 1,5 TB, proveďte z následujících akcí:

* Nastavte jeden procesový server s 16 virtuálních procesorů, 24 GB paměti RAM.
* Nastavte dva procesových serverů, (2 × 8 virtuálních procesorů, 2 * 12 GB paměti RAM).

## <a name="control-network-bandwidth"></a>Řídí šířku pásma sítě

Poté, co jste použili [nástroj Deployment Planner](site-recovery-deployment-planner.md) k výpočtu šířky pásma pro replikaci (počáteční replikace a pak rozdílovou) potřebujete, můžete řídit šířku pásma používanou k replikaci pomocí několika možností:

* **Omezení šířky pásma**: Přenos VMware, která se replikují do Azure, prochází konkrétním procesový server. Můžete omezit šířku pásma počítače spuštěné jako procesových serverů.
* **Ovlivnění šířky pásma**: Můžete ovlivnit pásma používanou k replikaci pomocí několika klíčů registru:
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** hodnotu registru určuje počet vláken, které se používají pro přenos dat (počáteční nebo rozdílové replikace) disku. Vyšší hodnota zvětšuje šířku pásma sítě využívané pro replikaci.
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** určuje počet vláken, použitá pro přenos dat během navrácení služeb po obnovení.

### <a name="throttle-bandwidth"></a>Omezení šířky pásma

1. Otevřete modul snap-in Azure Backup konzoly MMC v počítači, který funguje jako procesový server. Ve výchozím nastavení zástupce pro zálohování je k dispozici na ploše nebo v následující složce: Agent\bin C:\Program Files\Microsoft Azure Recovery Services.
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

## <a name="setup-azure-site-recovery-infrastructure-to-protect-more-than-500-virtual-machines"></a>Nastavení infrastruktury Azure Site Recovery pro ochranu víc než 500 virtuálních počítačů

Před nastavením infrastruktury Azure Site Recovery, budete potřebovat pro přístup k prostředí k měření následující faktory: frekvence, požadovaná šířka pásma sítě pro požadovaný cíl bodu obnovení Azure site recovery počet kompatibilních virtuálních počítačů, dat o denním změny komponenty požadované, čas potřebný k dokončení počáteční replikace atd.,

1. K měření tyto parametry, nezapomeňte spuštění plánovače nasazení služby ve vašem prostředí pomocí pokynů sdílené [tady](site-recovery-deployment-planner.md).
2. Nasazení konfiguračního serveru pomocí výše uvedené požadavky. Pokud vaše produkční úlohy překročí celkový počet virtuálních 650 počítačů, nasazování jiném konfiguračním serveru.
3. Podle měřené denní frekvenci změn dat, nasaďte [horizontální navýšení kapacity procesových serverů](vmware-azure-set-up-process-server-scale.md#download-installation-file) díky velikost pokyny uvedenými [tady](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Pokud očekáváte, že frekvence změny dat pro disk virtuálního počítače by být delší než 2 MB/s, nezapomeňte [nastavení účtu služby premium storage](tutorial-prepare-azure.md#create-a-storage-account). Protože spuštění plánovače nasazení pro určité časové období špičky v datech frekvence změny během další čas období nemusí být zachyceny v sestavě.
5. Podle požadovaného cíle bodu obnovení [nastavit šířku pásma sítě](site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
6. Po nastavení infrastruktury, postupujte podle pokynů publikovaných pod [postupy části](vmware-azure-set-up-source.md) umožňující zotavení po havárii na vašich úloh.

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
