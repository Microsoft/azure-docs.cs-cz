---
title: Plánování kapacity pro zotavení po havárii VMware pomocí Azure Site Recovery
description: Tento článek vám může pomoci při plánování kapacity a škálování při nastavování zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 4b86d0c189bcf0687a703f2338188df2090feaf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368022"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Plánování kapacity a škálování pro zotavení po havárii VMware do Azure

Tento článek slouží k plánování kapacity a škálování při replikaci místních virtuálních počítačů VMware a fyzických serverů do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Návody začít plánovat kapacitu?

Pokud se chcete dozvědět víc o Azure Site Recovery požadavcích na infrastrukturu, shromážděte informace o prostředí replikace spuštěním [Plánovač nasazení služby Azure Site Recovery](./site-recovery-deployment-planner.md) pro replikaci VMware. Další informace najdete v tématu [o Plánovač nasazení Site Recovery pro VMware do Azure](site-recovery-deployment-planner.md). 

Site Recovery Plánovač nasazení poskytuje sestavu s úplnými informacemi o kompatibilních a nekompatibilních virtuálních počítačích, discích na virtuálním počítači a četnosti změn dat na disk. Nástroj také shrnuje požadavky na šířku pásma sítě tak, aby splňovaly cílovou RPO a infrastrukturu Azure, která je potřebná pro úspěšnou replikaci a testovací převzetí služeb při selhání.

## <a name="capacity-considerations"></a>Důležité informace o kapacitách

Součást | Podrobnosti
--- | ---
**Replikace** | **Maximální denní četnost změn**: chráněný počítač může používat jenom jeden procesový Server. Jeden procesový server může zpracovat denní četnost změn až do 2 TB. Proto je 2 TB maximální denní četnost změn dat, která je podporovaná pro chráněný počítač.<br /><br /> **Maximální propustnost**: replikovaný počítač může patřit k jednomu účtu úložiště v Azure. Účet standardní Azure Storage může zpracovávat maximálně 20 000 požadavků za sekundu. Doporučujeme, abyste omezili počet vstupně-výstupních operací za sekundu (IOPS) v rámci zdrojového počítače na 20 000. Pokud máte například zdrojový počítač, který má pět disků a každý disk generuje na zdrojovém počítači 120 IOPS (8 KB), bude zdrojový počítač v rámci limitu IOPS v Azure na disk, který je 500. (Počet požadovaných účtů úložiště je stejný jako celkový počet vstupně-výstupních operací na zdrojovém počítači dělený 20 000.)
**Konfigurační server** | Konfigurační server musí být schopný zvládnout kapacitu denních změn napříč všemi úlohami spuštěnými v chráněných počítačích. Aby bylo možné průběžně replikovat data do Azure Storage, musí mít konfigurační počítač dostatečnou šířku pásma.<br /><br /> Osvědčeným postupem je umístit konfigurační server do sítě a segmentu LAN jako počítače, které chcete chránit. Konfigurační server můžete umístit na jinou síť, ale počítače, které chcete chránit, by měly mít viditelnost sítě 3.<br /><br /> Doporučení velikosti pro konfigurační server jsou shrnutá v tabulce v následující části.
**Procesový Server** | První procesový Server je ve výchozím nastavení nainstalován na konfiguračním serveru. Můžete nasadit další procesní servery pro škálování prostředí. <br /><br /> Procesový server přijímá data replikace z chráněných počítačů. Procesový Server optimalizuje data pomocí ukládání do mezipaměti, komprese a šifrování. Procesový Server pak odešle data do Azure. Aby bylo možné provádět tyto úlohy, musí mít počítač procesového serveru dostatek prostředků.<br /><br /> Procesový Server používá mezipaměť založenou na disku. Pro zpracování změn dat uložených v případě, že dojde k kritickým místu nebo výpadku sítě, použijte samostatný disk mezipaměti o velikosti 600 GB nebo více.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Doporučení pro velikost konfiguračního serveru a sestaveného procesového serveru

Konfigurační server, který používá sestavený procesový Server k ochraně zatížení, může zpracovat až 200 virtuálních počítačů na základě následujících konfigurací:

Procesor | Memory (Paměť) | Velikost disku mezipaměti | Frekvence změny dat | Chráněné počítače
--- | --- | --- | --- | ---
8 vCPU (2 sokety × 4 jádra \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB nebo méně | Použijte k replikaci méně než 100 počítačů.
12 vCPU (2 sokety × 6 jader \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB až 1 TB | Použijte k replikaci počítačů s 100 až 150.
16 vCPU (2 sokety × 8 jader \@ 2,5 GHz) | 32 GB | 1 TB | >1 TB až 2 TB | Použijte k replikaci počítačů s 151 až 200.
Nasazení jiného konfiguračního serveru pomocí [šablony OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) | | | | Pokud provádíte replikaci více než 200 počítačů, nasaďte nový konfigurační server.
Nasaďte jiný [procesový Server](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Nasazení nového procesového serveru se škálováním na více instancí, pokud je celková rychlost denních změn dat větší než 2 TB.

V těchto konfiguracích:

* Každý zdrojový počítač má tři disky o velikosti 100 GB.
* Pro měření disku mezipaměti jsme použili srovnávací úložiště osmi jednotek sdíleného přístupového podpisu z 10 K ot./min. s RAID 10.

## <a name="size-recommendations-for-the-process-server"></a>Doporučení pro velikost procesového serveru

Procesový Server je komponenta, která zpracovává replikaci dat v Azure Site Recovery. Pokud je rychlost denních změn větší než 2 TB, je nutné přidat procesové servery se škálováním na více instancí pro zpracování zatížení replikace. K horizontálnímu navýšení kapacity můžete:

* Zvyšte počet konfiguračních serverů nasazením pomocí [šablony OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). Můžete například chránit až 400 počítačů pomocí dvou konfiguračních serverů.
* Přidejte [procesové servery se škálováním na více](vmware-azure-set-up-process-server-scale.md#download-installation-file)instancí. Pro zpracování provozu replikace místo (nebo kromě) konfiguračního serveru použijte procesní servery se škálováním na více instancí.

Tento scénář je popsán v následující tabulce:

* Nastavíte procesový Server se škálováním na více instancí.
* Nakonfigurovali jste chráněné virtuální počítače na používání procesového serveru se škálováním na více instancí.
* Každý chráněný zdrojový počítač má tři disky o velikosti 100 GB.

Další procesový Server | Velikost disku mezipaměti | Frekvence změny dat | Chráněné počítače
--- | --- | --- | ---
4 vCPU (2 sokety × 2 jádra \@ 2,5 GHz), 8 GB paměti | 300 GB | 250 GB nebo méně | Použijte k replikaci 85 nebo méně počítačů.
8 vCPU (2 sokety × 4 jádra \@ 2,5 GHz), 12 GB paměti | 600 GB | 251 GB až 1 TB | Použijte k replikaci počítačů s 86 až 150.
12 vCPU (2 sokety × 6 jader \@ 2,5 GHz) 24 GB paměti | 1 TB | >1 TB až 2 TB | Použijte k replikaci počítačů s 151 až 225.

Způsob škálování serverů závisí na vašich preferencích pro model škálování na více instancí nebo pro škálování na více instancí. Pro horizontální navýšení kapacity nasaďte několik serverů a procesových serverů s vysokou úrovní konfigurace. Pro horizontální navýšení kapacity nasaďte další servery, které mají méně prostředků. Pokud například chcete chránit 200 počítačů s celkovou denní rychlostí změny dat o 1,5 TB, můžete provést jednu z následujících akcí:

* Nastavte jeden procesový Server (16 vCPU, 24 GB paměti RAM).
* Nastavte dva procesní servery (2 x 8 vCPU, 2 × 12 GB paměti RAM).

## <a name="control-network-bandwidth"></a>Řízení šířky pásma sítě

Po použití [Site Recovery Plánovač nasazení](site-recovery-deployment-planner.md) k výpočtu šířky pásma, kterou potřebujete pro replikaci (počáteční replikace a rozdílové rozdíly), máte několik možností, jak řídit šířku pásma, která se používá pro replikaci:

* **Omezení šířky pásma**: provoz VMware, který se replikuje do Azure, prochází přes konkrétní procesový Server. Šířku pásma můžete omezit na počítačích, které jsou spuštěny jako procesové servery.
* **Vliv na šířku pásma**: šířku pásma, která se používá pro replikaci, můžete ovlivnit pomocí několika klíčů registru:
  * Hodnota registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** určuje počet vláken, která se používají pro přenos dat (počáteční nebo rozdílovou replikaci) disku. Vyšší hodnota zvyšuje šířku pásma sítě, která se používá pro replikaci.
  * Hodnota registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** určuje počet vláken, která se použijí pro přenos dat během navrácení služeb po obnovení.

### <a name="throttle-bandwidth"></a>Omezení šířky pásma

1. Na počítači, který používáte jako procesový Server, otevřete modul snap-in Azure Backup MMC. Ve výchozím nastavení je zástupce pro zálohování k dispozici na ploše nebo v následující složce: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. V modulu snap-in vyberte **změnit vlastnosti**.

    ![Snímek obrazovky s možností modulu snap-in Azure Backup MMC ke změně vlastností](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na kartě **omezování** vyberte **Povolit omezování šířky pásma internetu u operací zálohování**. Nastavte limity pro pracovní a nepracovní dobu. Platné rozsahy jsou od 512 do 1 023 MB/s.

    ![Snímek obrazovky dialogového okna vlastností Azure Backup](./media/site-recovery-vmware-to-azure/throttle2.png)

Pro nastavení omezování můžete také použít rutinu [Set OBMachineSetting](/previous-versions/windows/powershell-scripting/hh770409(v=wps.640)). Tady je příklad:

```azurepowershell-interactive
$mon = [System.DayOfWeek]::Monday
$tue = [System.DayOfWeek]::Tuesday
Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)
```

**Set-OBMachineSetting -NoThrottle** označuje, že není požadováno žádné omezování.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Změna šířky pásma sítě pro virtuální počítač

1. V registru virtuálního počítače přejít na **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Pokud chcete změnit provoz šířky pásma na replikačním disku, upravte hodnotu **UploadThreadsPerVM**. Pokud klíč neexistuje, vytvořte ho.
   * Pokud chcete změnit šířku pásma pro překlopení provozu z Azure, upravte hodnotu **DownloadThreadsPerVM**.
2. Výchozí hodnota pro každý klíč je **4**. V síti s „nadměrným zřízením“ je třeba tyto klíče registru změnit z výchozích hodnot. Maximální hodnota, kterou můžete použít, je **32**. Monitorováním provozu hodnotu optimalizujte.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Nastavení infrastruktury Site Recovery pro ochranu více než 500 virtuálních počítačů

Než nastavíte infrastrukturu Site Recovery, získáte přístup k prostředí a změříte následující faktory: kompatibilní virtuální počítače, denní frekvence změny dat, požadovaná šířka pásma sítě pro RPO, který chcete dosáhnout, počet požadovaných Site Recovery součástí a čas potřebný k dokončení počáteční replikace. Pro shromáždění požadovaných informací proveďte následující kroky:

1. Pro měření těchto parametrů spusťte Site Recovery Plánovač nasazení ve vašem prostředí. Užitečné pokyny najdete v tématu [informace o Site Recovery Plánovač nasazení pro VMware do Azure](site-recovery-deployment-planner.md).
2. Nasaďte konfigurační server, který splňuje [doporučení pro velikost konfiguračního serveru](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Pokud vaše produkční úlohy překročí 650 virtuálních počítačů, nasaďte další konfigurační server.
3. Na základě měřené míry denní změny dat nasaďte [procesové servery se škálováním na více](vmware-azure-set-up-process-server-scale.md#download-installation-file) instancí s [pokyny pro velikost](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Pokud očekáváte, že rychlost změny dat pro virtuální počítač disku překročí 2 MB/s, ujistěte se, že používáte spravované disky úrovně Premium. Site Recovery Plánovač nasazení běžet po určitou dobu. Špičky v rychlosti změny dat v jiné době nemusí být zachyceny v sestavě.
5. [Nastavte šířku pásma sítě](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) na základě bodu obnovení, který chcete dosáhnout.
6. Po nastavení infrastruktury povolte zotavení po havárii pro vaše úlohy. Další informace najdete v tématu [nastavení zdrojového prostředí pro replikaci z VMware do Azure](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Nasazení dalších procesových serverů

Pokud nasáhnete horizontální navýšení kapacity nasazení nad rámec 200 zdrojových počítačů nebo pokud máte celkovou denní četnost změn více než 2 TB, je nutné přidat procesové servery pro zpracování objemu přenosů. Vylepšili jsme produkt ve verzi 9,24, aby poskytoval [výstrahy procesového serveru](vmware-physical-azure-monitor-process-server.md#process-server-alerts) při nastavení procesu serveru se škálováním na více instancí. [Nastavte procesový Server](vmware-azure-set-up-process-server-scale.md) pro ochranu nových zdrojových počítačů nebo [vyvážení zátěže](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrace počítačů na použití nového procesového serveru

1. Vyberte **Nastavení**  >  **Site Recovery servery**. Vyberte konfigurační server a pak rozbalte **procesové servery**.

    ![Snímek obrazovky s dialogovým oknem procesového serveru](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Klikněte pravým tlačítkem na procesový Server, který se právě používá, a pak vyberte **přepínač**.

    ![Snímek obrazovky s dialogovým oknem konfigurační server](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. V části **Vybrat cílový procesový Server** vyberte nový procesový Server, který chcete použít. Pak vyberte virtuální počítače, které bude server zpracovávat. Chcete-li získat informace o serveru, vyberte ikonu informace. V rámci rozhodování o zatížení se zobrazí průměrné místo, které je nutné pro replikaci jednotlivých vybraných virtuálních počítačů na nový procesový Server. Zaškrtnutím tohoto políčka zahájíte replikaci na nový procesový Server.

## <a name="deploy-additional-master-target-servers"></a>Nasaďte další hlavní cílové servery.

V následujících scénářích je vyžadován více než jeden hlavní cílový server:

*   Chcete chránit virtuální počítač se systémem Linux.
*   Hlavní cílový server dostupný na konfiguračním serveru nemá přístup k úložišti dat virtuálního počítače.
*   Celkový počet disků na hlavním cílovém serveru (počet místních disků na serveru a počet chráněných disků) je větší než 60 disků.

Další informace o tom, jak přidat hlavní cílový server pro virtuální počítač se systémem Linux, najdete v tématu [instalace hlavního cílového serveru Linux pro navrácení služeb po obnovení](vmware-azure-install-linux-master-target.md).

Postup přidání hlavního cílového serveru pro virtuální počítač se systémem Windows:

1. Přejít na **Recovery Services trezor**  >  **Site Recovery**  >  **konfiguračního serveru** infrastruktury.
2. Vyberte požadovaný konfigurační server a pak vyberte **hlavní cílový server**.

    ![Snímek obrazovky, na kterém se zobrazuje tlačítko Přidat hlavní cílový server](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Stáhněte si soubor sjednocené instalace a pak na virtuálním počítači spusťte soubor a nastavte hlavní cílový server.
4. Jako další vyberte **nainstalovat hlavní cíl instalace**  >  .

    ![Snímek obrazovky, který zobrazuje výběr možnosti instalovat hlavní cíl](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Vyberte výchozí umístění instalace a pak vyberte **nainstalovat**.

     ![Snímek obrazovky, který zobrazuje výchozí umístění instalace](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Pokud chcete zaregistrovat hlavní cíl s konfiguračním serverem, vyberte **pokračovat ke konfiguraci**.

    ![Snímek obrazovky, který zobrazuje tlačítko Přejít na konfiguraci](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Zadejte IP adresu konfiguračního serveru a pak zadejte heslo. Informace o tom, jak vygenerovat heslo, najdete v tématu [generování hesla konfiguračního serveru](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Snímek obrazovky, který ukazuje, kde zadat IP adresu a přístupové heslo pro konfigurační server](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Vyberte **Zaregistrovat**. Po dokončení registrace vyberte **Dokončit**.

Po úspěšném dokončení registrace se server zobrazí v Azure Portal v **Recovery Services trezoru**  >  **Site Recovery**  >  **konfigurační servery** infrastruktury, a to na hlavních cílových serverech konfiguračního serveru.

 > [!NOTE]
 > Stáhněte si nejnovější verzi [souboru sjednocené instalace hlavního cílového serveru pro Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Další kroky

Stáhněte a spusťte [Site Recovery Plánovač nasazení](https://aka.ms/asr-deployment-planner).