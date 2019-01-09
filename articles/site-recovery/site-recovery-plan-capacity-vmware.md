---
title: Plánování kapacity a škálování pro zotavení po havárii VMware do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek vám může pomoci plánování kapacity a škálování při nastavování zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 29e01177d4b096449cd906a22b47223078c6493e
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107816"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Plánování kapacity a škálování pro zotavení po havárii VMware do Azure

Tento článek slouží k plánování kapacity a škálování při replikaci místních virtuálních počítačů VMware a fyzických serverů do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Jak začít, plánování kapacity?

Další informace o požadavky na infrastrukturu Azure Site Recovery, shromážděte informace o prostředí replikace spuštěním [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) pro replikaci VMware. Další informace najdete v tématu [o Site Recovery Deployment Planner pro replikaci z VMware do Azure](site-recovery-deployment-planner.md). 

Plánovač nasazení služby Site Recovery poskytuje sestavu, která obsahuje kompletní informace o kompatibilních a nekompatibilních virtuálních počítačích, discích jednotlivých virtuálních počítačů, a četnost změn dat na disku. Nástroj také obsahuje souhrn požadavků na šířku pásma sítě pro splnění cíle cíle bodu obnovení a infrastrukturu Azure, které je nutné pro úspěšné replikaci a testovací převzetí služeb při selhání.

## <a name="capacity-considerations"></a>Důležité informace o kapacity

Komponenta | Podrobnosti
--- | ---
**Replikace** | **Maximální denní frekvenci změn**: Chráněný počítač lze použít pouze jeden procesový server. Jeden procesový server dokáže zpracovat denní změnu přenosové rychlosti až do velikosti 2 TB. Ano 2 TB se, že že maximální frekvence každodenní změny dat, která je podporována pro chráněný počítač.<br /><br /> **Maximální propustnost**: Replikované počítače můžou patřit do jednoho účtu úložiště v Azure. Standardní účet úložiště Azure dokáže zpracovat až 20 000 požadavků za sekundu. Doporučujeme omezit počet vstupně výstupní operace za sekundu (IOPS) ve zdrojovém počítači, na 20 000. Například pokud máte zdrojový počítač, který má pět disků a každý disk generuje 120 IOPS (velikosti 8 kB) na zdrojovém počítači, zdrojový počítač je v rámci limit 500 vstupně-výstupních operací Azure na disku. (Počet účtů úložiště vyžaduje je rovna celkové zdrojový počítač dělený 20 000 IOPS.)
**Konfigurační server** | Konfigurační server musí být schopna zpracovávat denní kapacitu změnit rychlost přes všechny úlohy spuštěné na chráněných počítačích. Konfigurace počítače musí mít dostatečnou šířku pásma, pokud chcete nepřetržitě replikovat data do služby Azure Storage.<br /><br /> Osvědčeným postupem je umístit konfigurační server na segment sítě LAN ve stejné síti jako počítače, které chcete chránit. Konfigurační server můžete umístit na jinou síť, ale počítače, které chcete chránit by měly mít viditelnost vrstvy 3 sítě.<br /><br /> V tabulce v následující části jsou shrnuté doporučené velikosti pro konfigurační server.
**Procesový server** | První procesový server je nainstalovaný ve výchozím nastavení na konfiguračním serveru. Můžete nasadit další Procesové servery pro horizontální vašeho prostředí. <br /><br /> Procesový server přijímá data replikace z chráněného počítače. Procesový server optimalizuje data pomocí ukládání do mezipaměti, komprese a šifrování. Procesový server poté odešle data do Azure. Počítač serveru proces musí mít dostatek prostředků k provedení těchto úloh.<br /><br /> Procesový server používá mezipaměť založené na disku. Použijte samostatný mezipaměti disku 600 GB nebo více ke zpracování změny dat, které jsou uloženy v případě kritický bod sítě nebo kvůli výpadku.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Doporučené velikosti pro konfigurační server a integrované procesový server

Konfigurační server, který chrání úlohy pomocí integrované procesový server dokáže zpracovat až 200 virtuálních počítačů na základě následujících konfigurací:

Procesor | Memory (Paměť) | Velikost mezipaměti disku | Frekvence změny dat | Chráněné počítače
--- | --- | --- | --- | ---
8 virtuálních procesorů (2 sockets * 4 jádra \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB nebo méně | Můžete replikovat počítače méně než 100.
12 virtuálních procesorů (2 sockets * 6 jader \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB až 1 TB | Můžete replikovat počítače 100 až 150.
16 virtuálních procesorů (2 sockets * 8 jader \@ 2,5 GHz) | 32 GB | 1 TB | > 1 TB na 2 TB | Můžete replikovat počítače 151 až 200.
Nasadit jiném konfiguračním serveru pomocí [šablony OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). | | | | Pokud replikujete více než 200 počítačů, nasazování nového serveru konfigurace.
Nasadit další [procesový server](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | &GT; 2 TB| Nasaďte nový horizontální navýšení kapacity procesový server, když celková denní četnost změn dat je větší než 2 TB.

V těchto konfigurací:

* Každý zdrojový počítač má tři disky 100 GB.
* Srovnávací testy úložiště osm jednotek sdíleného přístupového podpisu 10 TIS ot. / min jsme použili s RAID 10 pro měření mezipaměti disku.

## <a name="size-recommendations-for-the-process-server"></a>Velikost doporučení k procesového serveru

Procesový server je komponenta, která zpracovává replikační data ve službě Azure Site Recovery. Pokud denní frekvenci změn je větší než 2 TB, je nutné přidat horizontální navýšení kapacity procesových serverů pro zpracování zátěže replikace. Chcete-li horizontálně navýšit kapacitu, můžete:

* Zvýšit počet konfiguračních serverů a nasadit pomocí [šablony OVF](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). Například můžete chránit až 400 počítačů s použitím dva konfigurační servery.
* Přidat [horizontální navýšení kapacity procesových serverů](vmware-azure-set-up-process-server-scale.md#download-installation-file). Horizontální navýšení kapacity procesových serverů můžete zpracovat provoz replikace místo (nebo kromě) konfiguračního serveru.

Následující tabulka popisuje tento scénář:

* Nastavit horizontální navýšení kapacity procesového serveru.
* Nakonfigurujete chráněné virtuální počítače používat horizontální navýšení kapacity procesového serveru.
* Každý chráněný zdrojový počítač má tři disky 100 GB.

Další procesový server | Velikost mezipaměti disku | Frekvence změny dat | Chráněné počítače
--- | --- | --- | ---
4 virtuální procesory (2 sockets * 2 jádra \@ 2,5 GHz), 8 GB paměti | 300 GB | Aby se 250 GB nebo méně | Můžete replikovat počítače 85 nebo méně.
8 virtuálních procesorů (2 sockets * 4 jádra \@ 2,5 GHz), 12 GB paměti | 600 GB | 251 GB až 1 TB | Můžete replikovat počítače 86 až 150.
12 virtuálních procesorů (2 sockets * 6 jader \@ 2,5 GHz) 24 GB paměti | 1 TB | > 1 TB na 2 TB | Můžete replikovat počítače 151 k 225.

Jak škálovat vaše servery závisí na vaši volbu pro vertikální nebo horizontální navýšení kapacity modelu. Vertikální navýšení kapacity, nasazení několika serverů špičkové konfigurace a zpracování servery. Pro horizontální navýšení kapacity, nasaďte další servery, které obsahují méně materiálů. Pokud chcete chránit 200 počítačů s tak celkovou frekvence každodenní změny dat 1,5 TB, může trvat jednu z následujících akcí:

* Nastavení jednoho procesu serveru (16 virtuálních procesorů, 24 GB paměti RAM).
* Nastavte dva procesových serverů, (2 × 8 virtuálních procesorů, 2 * 12 GB paměti RAM).

## <a name="control-network-bandwidth"></a>Řídí šířku pásma sítě

Když použijete [Site Recovery Deployment Planner](site-recovery-deployment-planner.md) k výpočtu šířky pásma, které potřebujete pro replikaci (počáteční replikace a potom tato rozdílová), máte několik možností pro řízení šířky pásma, který se používá pro replikace:

* **Omezení šířky pásma**: Přenos VMware, která se replikují do Azure, prochází konkrétním procesový server. Můžete omezit šířku pásma na počítačích, na kterých běží jako procesových serverů.
* **Ovlivnění šířky pásma**: Můžete ovlivnit šířku pásma, která se bude používat pro replikaci pomocí několika klíčů registru:
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** hodnotu registru určuje počet vláken, které se používají pro přenos dat (počáteční nebo rozdílové replikace) disku. Vyšší hodnota zvětšuje šířku pásma sítě, který se používá pro replikaci.
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** hodnotu registru určuje počet vláken, které se používají pro přenos dat při navrácení služeb po obnovení.

### <a name="throttle-bandwidth"></a>Omezení šířky pásma

1. Otevřete modul snap-in Azure Backup konzoly MMC v počítači, který používáte jako procesový server. Ve výchozím nastavení zástupce pro zálohování je k dispozici na ploše nebo v následující složce: Agent\bin C:\Program Files\Microsoft Azure Recovery Services.
2. V modulu snap-in, vyberte **změnit vlastnosti**.

    ![Snímek obrazovky s Azure Backup konzoly MMC modul snap-in umožňuje změnit vlastnosti](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na **omezování** kartu, vyberte možnost **Povolit omezování šířky pásma Internetu u operací zálohování**. Nastavte limity pro pracovní a nepracovní dobu. Platné rozsahy jsou 512 kB/s na 1,023 MB/s.

    ![Snímek obrazovky dialogového okna Vlastnosti zálohování Azure](./media/site-recovery-vmware-to-azure/throttle2.png)

Pro nastavení omezování můžete také použít rutinu [Set OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx). Tady je příklad:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** označuje, že není požadováno žádné omezování.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Příkaz ALTER šířky pásma sítě pro virtuální počítač

1. V registru Virtuálního počítače, přejděte na **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Ke změně šířky pásma provoz na replikačního disku, upravte hodnotu **UploadThreadsPerVM**. Vytvořte klíč, pokud neexistuje.
   * Ke změně šířky pásma pro přenosy navrácení služeb po obnovení z Azure, upravte hodnotu **DownloadThreadsPerVM**.
2. Výchozí hodnota pro každý klíč je **4**. V síti s „nadměrným zřízením“ je třeba tyto klíče registru změnit z výchozích hodnot. Maximální hodnota, můžete je **32**. Monitorováním provozu hodnotu optimalizujte.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Nastavení infrastruktury Site Recovery pro ochranu víc než 500 virtuálních počítačů

Před nastavením infrastruktura Site Recovery, přístup k prostředí k měření následující faktory: kompatibilních virtuálních počítačů, frekvence každodenní změny dat, požadovaná šířka pásma sítě pro cíl bodu obnovení, které chcete dosáhnout, počet Site Recovery součásti, které jsou požadované a čas potřebný k dokončení počáteční replikace. Proveďte následující kroky k získání požadovaných informací:

1. K měření tyto parametry, spusťte Plánovač nasazení služby Site Recovery ve vašem prostředí. Užitečné pokyny najdete v části [o Site Recovery Deployment Planner pro replikaci z VMware do Azure](site-recovery-deployment-planner.md).
2. Nasazení konfiguračního serveru, který splňuje [doporučené velikosti pro konfigurační server](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Pokud vaše produkční úlohy překročí celkový počet virtuálních 650 počítačů, nasazování jiném konfiguračním serveru.
3. Podle měřené denní frekvenci změn dat, nasaďte [horizontální navýšení kapacity procesových serverů](vmware-azure-set-up-process-server-scale.md#download-installation-file) díky [velikost pokyny](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Pokud očekáváte, že rychlost disku virtuálního počítače 2 MB/s, překročí změn dat [nastavení účtu služby Premium storage](tutorial-prepare-azure.md#create-a-storage-account). Site Recovery Deployment Planner se spouští v konkrétním časovém období. Špičky v četnost změn dat v jinou dobu nemusí být zachyceny v sestavě.
5. [Nastavení šířky pásma sítě](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) podle k dosažení cíle bodu obnovení.
6. Při nastavení infrastruktury, povolte zotavení po havárii pro vaše úlohy. Další informace o postupu [nastavení zdrojového prostředí pro VMware pro replikaci Azure](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Nasazení dalších procesových serverů

Pokud horizontální navýšení kapacity nasazení nad 200 zdrojové počítače nebo pokud máte celkem denní četnost více než 2 TB změn dat, je nutné přidat procesní servery pro zpracování objem přenosů. Zjistěte, jak nastavit službu na procesovém serveru, najdete v článku [škálování pro navrácení služeb po obnovení s použitím dalších procesových serverů](vmware-azure-set-up-process-server-scale.md). Po nastavení procesový server, můžete provést migraci zdrojové počítače jeho použití.

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrace počítačů používat nový procesový server

1. Vyberte **nastavení** > **Site Recovery servery**. Vyberte konfigurační server a potom rozbalte **zpracovat servery**.

    ![Snímek obrazovky dialogového okna procesový Server](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Klikněte pravým tlačítkem na procesový server aktuálně používán a pak vyberte **přepínač**.

    ![Snímek obrazovky dialogového okna konfigurace serveru](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. V **vyberte cílový procesový server**, vyberte nový procesový server, který chcete použít. Vyberte virtuální počítače, které budou zpracovávat na serveru. Pokud chcete získat informace o serveru, vyberte ikonu informace. Pro vám pomůže zajistit načíst rozhodnutí, se zobrazí průměrnou prostor, který je potřeba replikovat každý vybraný virtuální počítač pro nový procesový server. Výběrem symbolu zaškrtnutí zahájíte replikaci pro nový procesový server.

## <a name="deploy-additional-master-target-servers"></a>Nasadit další hlavní cílové servery

V následující scénáře se vyžaduje více než jeden hlavní cílový server:

*   Chcete chránit virtuální počítač s linuxem.
*   Hlavní cílový server k dispozici na konfiguračním serveru nemá přístup k úložišti dat virtuálního počítače.
*   Celkový počet disků na hlavním cílovém serveru (počet místní disky na serveru) a počtu disků, které se mají chránit, je větší než 60 disky.

Zjistěte, jak přidat hlavní cílový server pro virtuální počítač s linuxem, najdete v článku [instalace hlavního cílového serveru s Linuxem pro navrácení služeb po obnovení](vmware-azure-install-linux-master-target.md).

Chcete-li přidat hlavní cílový server pro virtuální počítač na základě Windows:

1. Přejděte na **trezor služby Recovery Services** > **infrastruktura Site Recovery** > **konfigurační servery**.
2. Vyberte požadované konfigurační server a pak vyberte **hlavního cílového serveru**.

    ![Snímek obrazovky zobrazující tlačítko Přidat hlavního cílového serveru](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Stáhněte si soubor sjednocené instalace a potom spusťte soubor na virtuálním počítači vytvořit hlavní cílový server.
4. Vyberte **instalace hlavního cílového** > **Další**.

    ![Snímek obrazovky s výběrem možnosti instalace hlavního cíle](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Vyberte výchozí umístění instalace a pak vyberte **nainstalovat**.

     ![Snímek obrazovky zobrazující výchozí umístění instalace](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. K registraci hlavního cíle s konfiguračním serverem, vyberte **pokračovat na konfiguraci**.

    ![Snímek obrazovky zobrazující pokračovat na konfiguraci tlačítko](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Zadejte IP adresu konfiguračního serveru a pak zadejte přístupové heslo. Zjistěte, jak generovat přístupové heslo, najdete v článku [vygenerovat heslo konfiguračního serveru](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Snímek obrazovky, který ukazuje, kam je třeba zadat IP adresu a heslo pro konfigurační server](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Vyberte **Zaregistrovat**. Po dokončení registrace vyberte **Dokončit**.

Po úspěšném dokončení registrace je server uveden na webu Azure Portal v **trezor služby Recovery Services** > **infrastruktura Site Recovery**  >   **Konfigurace serverů**, na hlavním cílovém serveru konfiguračního serveru.

 > [!NOTE]
 > Stáhněte si nejnovější verzi [hlavního cílového serveru jednotný instalační soubor Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Další postup

Stažení a spuštění [Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).
