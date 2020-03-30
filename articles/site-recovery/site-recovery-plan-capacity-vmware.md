---
title: Plánování kapacity pro zotavení po havárii v systému VMware pomocí Azure Site Recovery
description: Tento článek vám pomůže naplánovat kapacitu a škálování při nastavování zotavení po havárii virtuálních počítačích VMware do Azure pomocí Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 467c70a722b8a243be6ac2826188a4ba3459aa06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257612"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Plánování kapacity a škálování pro zotavení po havárii v Systému VMware do Azure

Tento článek slouží k plánování kapacity a škálování při replikaci místních virtuálních počítačů VMware a fyzických serverů do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Jak zahájím plánování kapacity?

Pokud se chcete dozvědět o požadavcích na infrastrukturu azure site recovery, shromážděte informace o vašem replikačním prostředí spuštěním [Plánovače nasazení webu Azure](https://aka.ms/asr-deployment-planner-doc) pro replikaci VMware. Další informace najdete [v tématu O Plánovači nasazení obnovení webu pro VMware do Azure](site-recovery-deployment-planner.md). 

Plánovač nasazení obnovení webu poskytuje sestavu, která obsahuje úplné informace o kompatibilních a nekompatibilních virtuálních počítačích, discích na virtuální počítač a datových konvech na disk. Nástroj také shrnuje požadavky na šířku pásma sítě, aby splňoval y cílové rpo a infrastrukturu Azure, která je vyžadována pro úspěšnou replikaci a testování převzetí služeb při selhání.

## <a name="capacity-considerations"></a>Důležité informace o kapacitách

Komponenta | Podrobnosti
--- | ---
**Replikace** | **Maximální denní rychlost změny**: Chráněný počítač může používat pouze jeden procesní server. Jeden procesový server může zpracovat denní četnost změn až do 2 TB. Takže 2 TB je maximální denní rychlost změny dat, která je podporována pro chráněný počítač.<br /><br /> **Maximální propustnost**: Replikovaný počítač může patřit do jednoho účtu úložiště v Azure. Standardní účet Azure Storage může zpracovat maximálně 20 000 požadavků za sekundu. Doporučujeme omezit počet vstupních a výstupních operací za sekundu (IOPS) ve zdrojovém počítači na 20 000. Například pokud máte zdrojový počítač, který má pět disků a každý disk generuje 120 IOPS (8 k velikosti) ve zdrojovém počítači, zdrojový počítač je v rámci limitu Azure na disk IOPS 500. (Počet požadovaných účtů úložiště se rovná celkovému videa VOPS zdrojového počítače vydělený 20 000.)
**Konfigurační server** | Konfigurační server musí být schopen zpracovat denní kapacitu rychlosti změny ve všech úlohách spuštěných v chráněných počítačích. Konfigurační počítač musí mít dostatečnou šířku pásma, aby bylo nutné průběžně replikovat data do služby Azure Storage.<br /><br /> Osvědčeným postupem je umístit konfigurační server do stejného segmentu sítě a sítě LAN jako počítače, které chcete chránit. Konfigurační server můžete umístit do jiné sítě, ale počítače, které chcete chránit, by měly mít viditelnost sítě vrstvy 3.<br /><br /> Doporučení velikosti konfiguračního serveru jsou shrnuta v tabulce v následující části.
**Procesní server** | První procesní server je ve výchozím nastavení nainstalován na konfiguračním serveru. Můžete nasadit další procesní servery pro škálování vašeho prostředí. <br /><br /> Procesní server přijímá replikační data z chráněných počítačů. Procesní server optimalizuje data pomocí ukládání do mezipaměti, komprese a šifrování. Potom procesový server odešle data do Azure. Počítač procesního serveru musí mít k provedení těchto úloh dostatečné prostředky.<br /><br /> Procesní server používá mezipaměť založenou na disku. Ke zpracování změn dat, které jsou uloženy v případě, že dojde k potížím nebo výpadku sítě, použijte samostatný disk mezipaměti o velikosti 600 GB nebo více.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Doporučení pro velikost konfiguračního serveru a vestavěného procesního serveru

Konfigurační server, který používá vestavěný procesní server k ochraně úlohy, může zpracovat až 200 virtuálních počítačů na základě následujících konfigurací:

Procesor | Memory (Paměť) | Velikost disku mezipaměti | Rychlost změny dat | Chráněné počítače
--- | --- | --- | --- | ---
8 virtuálních procesorů (2 zásuvky \@ * 4 jádra 2,5 GHz) | 16 GB | 300 GB | 500 GB nebo méně | Slouží k replikaci méně než 100 počítačů.
12 virtuálních procesorů (2 zásuvky \@ * 6 jader 2,5 GHz) | 18 GB | 600 GB | 501 GB až 1 TB | Slouží k replikaci 100 až 150 počítačů.
16 virtuálních procesorů (2 zásuvky \@ * 8 jader 2,5 GHz) | 32 GB | 1 TB | >1 TB až 2 TB | Slouží k replikaci 151 až 200 počítačů.
Nasazení jiného konfiguračního serveru pomocí [šablony OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). | | | | Pokud replikujete více než 200 počítačů, nasaďte nový konfigurační server.
Nasazení jiného [procesního serveru](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Pokud je celková denní rychlost změny dat větší než 2 TB, nasaďte nový horizontální navýšení kapacity procesu.

V těchto konfiguracích:

* Každý zdrojový počítač má tři disky o velikosti 100 GB.
* Pro měření disků mezipaměti jsme použili srovnávací úložiště osmi sdílených přístupových podpisových jednotek 10 K RPM s RAID 10.

## <a name="size-recommendations-for-the-process-server"></a>Doporučení pro velikost procesního serveru

Procesní server je komponenta, která zpracovává replikaci dat v Azure Site Recovery. Pokud je denní rychlost změny větší než 2 TB, je nutné přidat horizontální navýšení kapacity procesních serverů pro zpracování zatížení replikace. Chcete-li horizontální navýšení kapacity, můžete:

* Zvyšte počet konfiguračních serverů nasazením pomocí [šablony OVF](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). Můžete například chránit až 400 počítačů pomocí dvou konfiguračních serverů.
* Přidejte [horizontální navýšení kapacity procesních serverů](vmware-azure-set-up-process-server-scale.md#download-installation-file). Pomocí horizontálních navýšení kapacity procesových serverů zpracování replikačních přenosů namísto (nebo kromě) konfiguračního serveru.

Následující tabulka popisuje tento scénář:

* Nastavíte horizontální navýšení kapacity procesu serveru.
* Nakonfigurovali jste chráněné virtuální počítače tak, aby používaly horizontální navýšení kapacity.
* Každý chráněný zdrojový počítač má tři disky o velikosti 100 GB.

Další procesní server | Velikost disku mezipaměti | Rychlost změny dat | Chráněné počítače
--- | --- | --- | ---
4 vCPU (2 sokety \@ * 2 jádra 2,5 GHz), 8 GB paměti | 300 GB | 250 GB nebo méně | Slouží k replikaci 85 nebo méně počítačů.
8 virtuálních procesorů (2 sokety * 4 jádra \@ 2,5 GHz), 12 GB paměti | 600 GB | 251 GB až 1 TB | Slouží k replikaci 86 až 150 počítačů.
12 virtuálních procesorů (2 sokety * 6 jader \@ 2,5 GHz) 24 GB paměti | 1 TB | >1 TB až 2 TB | Slouží k replikaci 151 až 225 počítačů.

Způsob škálování serverů závisí na vašich preferencích pro model škálování nebo škálování na více. Chcete-li vertikálně navýšit kapacitu, nasaďte několik špičkových konfiguračních serverů a procesních serverů. Chcete-li horizontální navýšení kapacity, nasaďte více serverů, které mají méně prostředků. Chcete-li například chránit 200 počítačů s celkovou denní mírou změny dat 1,5 TB, můžete provést jednu z následujících akcí:

* Nastavte jeden procesní server (16 virtuálních procesorů, 24 GB paměti RAM).
* Nastavte dva procesní servery (2 x 8 virtuálních procesorů, 2 * 12 GB paměti RAM).

## <a name="control-network-bandwidth"></a>Řízení šířky pásma sítě

Po použití [Plánovače nasazení obnovení lokality](site-recovery-deployment-planner.md) k výpočtu šířky pásma, kterou potřebujete pro replikaci (počáteční replikace a potom delta), máte několik možností pro řízení velikosti pásma, která se používá pro replikaci:

* **Šířka pásma omezení:** Provoz vmware, který se replikuje do Azure, prochází konkrétním procesním serverem. Můžete omezit šířku pásma na počítačích, které jsou spuštěny jako procesní servery.
* **Vliv šířky pásma**: Šířku pásma, která se používá pro replikaci, můžete ovlivnit pomocí několika klíčů registru:
  * Hodnota registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** určuje počet vláken, které se používají pro přenos dat (počáteční nebo delta replikace) disku. Vyšší hodnota zvyšuje šířku pásma sítě, která se používá pro replikaci.
  * Hodnota **registru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** určuje počet vláken, které se používají pro přenos dat během navrácení služeb po selhání.

### <a name="throttle-bandwidth"></a>Omezení šířky pásma

1. Otevřete modul snap-in Konzoly mmc zálohování Azure v počítači, který používáte jako procesní server. Ve výchozím nastavení je zástupce programu Backup k dispozici na ploše nebo v následující složce: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. V modulu snap-in vyberte **Změnit vlastnosti**.

    ![Snímek obrazovky s možností modulu snap-in Azure Backup MMC pro změnu vlastností](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Na kartě **Omezení** vyberte **Povolit omezení využití šířky pásma internetu pro operace zálohování**. Nastavte limity pro pracovní dobu a nepracovní dobu. Platné rozsahy jsou od 512 kb/s do 1 023 Mb/s.

    ![Snímek obrazovky s dialogovém oknem Vlastnosti zálohování Azure](./media/site-recovery-vmware-to-azure/throttle2.png)

Pro nastavení omezování můžete také použít rutinu [Set OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx). Tady je příklad:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** označuje, že není požadováno žádné omezování.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Změna šířky pásma sítě pro virtuální hosti

1. V registru virtuálního počítače přejděte na **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Zálohování Azure\Replikace**.
   * Chcete-li změnit provoz šířky pásma na replikujícím se disku, upravte hodnotu **UploadThreadsPerVM**. Vytvořte klíč, pokud neexistuje.
   * Chcete-li změnit šířku pásma pro přenosy navrácení služeb po lyže z Azure, upravte hodnotu **DownloadThreadsPerVM**.
2. Výchozí hodnota pro každý klíč je **4**. V síti s „nadměrným zřízením“ je třeba tyto klíče registru změnit z výchozích hodnot. Maximální hodnota, kterou můžete použít, je **32**. Monitorováním provozu hodnotu optimalizujte.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Nastavení infrastruktury Site Recovery pro ochranu více než 500 virtuálních min

Před nastavením infrastruktury site recovery získáte přístup k prostředí a změřte tak následující faktory: kompatibilní virtuální počítače, denní rychlost změny dat, požadovanou šířku pásma sítě pro rpo, které chcete dosáhnout, počet site recovery součásti, které jsou požadovány, a čas potřebný k dokončení počáteční replikace. Chcete-li získat požadované informace, proveďte následující kroky:

1. Chcete-li tyto parametry změřit, spusťte plánovač nasazení obnovení lokality ve vašem prostředí. Užitečné pokyny najdete v [tématu O Plánovači nasazení obnovení webu pro VMware do Azure](site-recovery-deployment-planner.md).
2. Nasaďte konfigurační server, který splňuje [doporučení pro velikost konfiguračního serveru](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Pokud vaše produkční úloha přesahuje 650 virtuálních počítačů, nasaďte jiný konfigurační server.
3. Na základě naměřené denní rychlosti změny dat nasazujte [horizontální navýšení kapacity procesních serverů](vmware-azure-set-up-process-server-scale.md#download-installation-file) pomocí pokynů pro [velikost](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Pokud očekáváte, že rychlost změny dat pro virtuální počítač disku překročí 2 MB/s, ujistěte se, že používáte disky spravované pro premium. Plánovač nasazení obnovení webu se spouští po určité časové období. Špičky v míře změny dat v jiných časech nemusí být zachyceny v sestavě.
5. [Nastavte šířku pásma sítě](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) na základě rpo, kterého chcete dosáhnout.
6. Když je infrastruktura nastavena, povolte zotavení po havárii pro vaše úlohy. Postup najdete v [tématu Nastavení zdrojového prostředí pro replikaci VMware do Azure](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Nasazení dalších procesních serverů

Pokud horizontální navýšení kapacity nasazení nad 200 zdrojových počítačů nebo pokud máte celkovou denní konve rychlost více než 2 TB, je nutné přidat procesní servery pro zpracování objemu provozu. Vylepšili jsme produkt ve verzi 9.24, abychom [poskytovali výstrahy procesního serveru](vmware-physical-azure-monitor-process-server.md#process-server-alerts) o tom, kdy nastavit horizontální navýšení kapacity procesního serveru. [Nastavte procesní server](vmware-azure-set-up-process-server-scale.md) tak, aby chránil nové zdrojové počítače nebo [vyvažuje zatížení](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrace počítačů za účelem použití nového procesního serveru

1. Vyberte **možnost Nastavení** > **serverů pro obnovení sítě**. Vyberte konfigurační server a rozbalte **položku Proces ovat servery**.

    ![Snímek obrazovky s dialogovým oknem Procesní server](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Klepněte pravým tlačítkem myši na aktuálně používáný procesní server a vyberte příkaz **Přepnout**.

    ![Snímek obrazovky s dialogovým oknem Konfigurační server](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. V **části Vybrat cílový procesní server**vyberte nový procesní server, který chcete použít. Potom vyberte virtuální počítače, které server bude zpracovávat. Chcete-li získat informace o serveru, vyberte ikonu informace. Abychom vám pomohli při rozhodování o načtení, zobrazí se průměrné místo potřebné k replikaci každého vybraného virtuálního počítače na nový procesní server. Zaškrtnutím políčka zahájíte replikaci na nový procesní server.

## <a name="deploy-additional-master-target-servers"></a>Nasazení dalších hlavních cílových serverů

V následujících scénářích je vyžadován více než jeden hlavní cílový server:

*   Chcete chránit virtuální počítač založený na Linuxu.
*   Hlavní cílový server, který je k dispozici na konfiguračním serveru, nemá přístup k úložišti dat virtuálního počítače.
*   Celkový počet disků na hlavním cílovém serveru (počet místních disků na serveru plus počet disků, které mají být chráněny) je větší než 60 disků.

Informace o tom, jak přidat hlavní cílový server pro virtuální počítač se systémem Linux, naleznete [v tématu Instalace hlavního cílového serveru Linuxu pro navrácení služeb po selhání](vmware-azure-install-linux-master-target.md).

Přidání hlavního cílového serveru pro virtuální počítač se systémem Windows:

1. Přejděte na**servery konfigurace****infrastruktury obnovy** > úložiště **služby** > Recovery Services pro obnovení .
2. Vyberte požadovaný konfigurační server a potom vyberte **hlavní cílový server**.

    ![Snímek obrazovky s tlačítkem Přidat hlavní cílový server](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Stáhněte si sjednocený instalační soubor a pak spusťte soubor na virtuálním počítači a nastavte hlavní cílový server.
4. Vyberte **možnost Instalovat hlavní cíl** > **Další**.

    ![Snímek obrazovky, který zobrazuje výběr možnosti Instalovat hlavní cíl](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Vyberte výchozí umístění instalace a pak vyberte **Instalovat**.

     ![Snímek obrazovky s výchozím umístěním instalace](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Chcete-li zaregistrovat hlavní cíl u konfiguračního serveru, vyberte **možnost Pokračovat v konfiguraci**.

    ![Snímek obrazovky s tlačítkem Přejít na konfiguraci](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Zadejte IP adresu konfiguračního serveru a zadejte přístupové heslo. Informace o tom, jak generovat přístupové heslo, naleznete v [tématu Generování přístupové fráze konfiguračního serveru](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Snímek obrazovky, který ukazuje, kam zadat IP adresu a přístupové heslo pro konfigurační server](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Vyberte **Zaregistrovat**. Po dokončení registrace vyberte **dokončit**.

Po úspěšném dokončení registrace je server uveden na portálu Azure na**konfiguračních serverech****infrastruktury** >  **obnovy** > úložiště úložiště obnovení služby Recovery Infrastructure , na hlavních cílových serverech konfiguračního serveru.

 > [!NOTE]
 > Stáhněte si nejnovější verzi [sjednoceného instalačního souboru hlavního cílového serveru pro systém Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Další kroky

Stáhněte a spusťte [plánovač nasazení site recovery](https://aka.ms/asr-deployment-planner).
