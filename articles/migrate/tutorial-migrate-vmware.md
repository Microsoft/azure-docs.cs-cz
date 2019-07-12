---
title: Migrace místních virtuálních počítačů VMware do Azure s využitím bez agentů Azure Migrate migrace serveru | Dokumentace Microsoftu
description: Popisuje, jak provádět a bez agentů migrace místních virtuálních počítačů VMware do Azure pomocí služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3510c0505a5a3c1353642baf5060a83d13fdd43a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809124"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrace virtuálních počítačů VMware do Azure (bez agentů)

Tento článek popisuje, jak migrovat místní virtuální počítače VMware do Azure, pomocí funkce agentless migrace pomocí nástroje Azure Migrate serveru migrace.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální rozbočovač pro sledování zjišťování, vyhodnocení a migraci místních aplikací a úloh a instancí virtuálního počítače AWS a GCP, do Azure. Centrum poskytuje Azure Migrate nástroje pro vyhodnocení a migrace, jakož i nabídky softwaru třetích stran výrobce (ISV).

Tento kurz je třetí webinář z řady, který ukazuje, jak vyhodnocovat a migrovat virtuální počítače VMware do Azure pomocí Azure Migrate serveru vyhodnocení a migraci. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Příprava virtuálních počítačů pro migraci.
> * Přidáte nástroj pro migraci serveru migrace Azure.
> * Zjištění virtuálních počítačů, které chcete migrovat.
> * Zahájení replikace virtuálních počítačů.
> * Spuštění testu migrace, abyste měli jistotu, že všechno funguje podle očekávání.
> * Spusťte plné migraci virtuálního počítače.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.

## <a name="migration-methods"></a>Metody migrace

Můžete migrovat virtuální počítače VMware do Azure pomocí nástroje Azure Migrate serveru migrace. Tento nástroj nabízí několik možností pro migraci virtuálních počítačů VMware:

- Migrace pomocí bez agentů replikace. Migrace virtuálních počítačů, aniž by bylo potřeba nic instalovat na ně.
- Migrace s vloženým agentem pro replikaci. Instalace agenta na virtuální počítač pro replikaci.

Rozhodování o tom, zda chcete použít migraci bez agentů nebo založené na agentovi, projděte si tyto články:

- [Zjistěte, jak](server-migrate-overview.md) funguje migrace bez agenta a [přečtěte si omezení](server-migrate-overview.md#agentless-migration-limitations).
- [V tomto článku](tutorial-migrate-vmware-agent.md) Pokud chcete používat metodu založenou na agenta.

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Vysvětlení](migrate-architecture.md) architekturu migrace VMware.
2. [Dokončení tohoto kurzu první](tutorial-prepare-vmware.md) v této sérii nastavení Azure a VMware pro migraci. Konkrétně v tomto kurzu potřebujete:
    - [Příprava Azure](tutorial-prepare-vmware.md#prepare-azure) pro migraci.
    - [Příprava místního prostředí](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) pro migraci.
    
3. Doporučujeme vyzkoušet posouzení virtuálních počítačů VMware pomocí Azure Migrate Server Assessment před migrací do Azure. Chcete-li nastavit hodnocení, [dokončení druhé části kurzu](tutorial-assess-vmware.md) této série. Pokud nechcete, aby na posouzení virtuálních počítačů v tomto kurzu můžete přeskočit. Přestože doporučujeme vyzkoušet posouzení, ale není nutné spustit posouzení, než se pokusíte migraci.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Přidat nástroj pro migraci serveru migrace Azure

Pokud sledujete nebyl druhé části kurzu posouzení virtuálních počítačů VMware, budete muset [postupujte podle těchto pokynů](how-to-add-tool-first-time.md) nastavit projekt Azure Migrate a vyberte nástroj pro migraci serveru migrace Azure. 

Pokud a potom druhé části kurzu a již mají projekt Azure Migrate nastavení, přidejte nástroj pro migraci serveru migrace Azure následujícím způsobem:

1. V projektu Azure Migrate klikněte na tlačítko **přehled**. 
2. V **zjišťování, vyhodnocení, určení a serverech migrace**, klikněte na tlačítko **posouzení a migraci serverů**.

     ![Vyhodnocení a migrace serverů](./media/tutorial-migrate-vmware/assess-migrate.png)

3. V **nástrojů pro migraci**vyberte **kliknutím sem přidáte nástroj pro migraci, až budete připravení migrovat**.

    ![Výběr nástroje](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. V seznamu nástrojů vyberte **Azure Migrate: Migrace serveru** > **přidat nástroj**

    ![Nástroj pro migraci serveru](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate

Migrace serveru do Azure migrovat spustí jednoduchý virtuální počítač VMware zařízení. Zařízení provádí zjišťování virtuálních počítačů a odesílá data metadata a výkonu virtuálních počítačů do Azure Migrate serveru migrace. Na stejném zařízení také používá Azure Migrate Server Assessment tool.

Pokud jste postupovali podle druhé části kurzu posouzení virtuálních počítačů VMware, jste už nastavili zařízení během tohoto kurzu. Pokud nebyl postupovat podle tohoto kurzu, musíte zařízení nastavit teď. Chcete-li to provést, můžete: 

- Stáhnout soubor OVA šablony a importovat ho do systému vCenter Server.
- Toto zařízení vytvoříte tak a zkontrolujte, že se může připojit k Azure Migrate Server Assessment. 
- Konfigurace zařízení poprvé a zaregistrujte je v projektu Azure Migrate.

Postupujte podle pokynů v [v tomto článku](how-to-set-up-appliance-vmware.md) nastavení zařízení.


## <a name="prepare-vms-for-migration"></a>Příprava virtuálních počítačů pro migraci

Azure Migrate vyžaduje některé změny virtuálního počítače ověřte, že virtuální počítače se dají migrovat do Azure.

- U některých [operačních systémů](server-migrate-overview.md#agentless-migration-limitations), Azure Migrate automaticky provede tyto změny.
- Pokud migrujete virtuální počítač, který nemá žádnou z těchto operačních systémů, postupujte podle pokynů k přípravě virtuálního počítače.
- Je důležité, aby tyto změny před zahájením migrace. Pokud provádíte migraci virtuální počítač, před provedením změny, virtuální počítač nemusí spustit v Azure.
- Změny konfigurace, které provedete na místní virtuální počítače se replikují do Azure po povolení replikace pro virtuální počítač. Pokud chcete mít jistotu, že změny se replikují, ujistěte se, že je bod obnovení, které můžete migrovat na pozdější než čas, ve kterém byly provedeny změny konfigurace místní.


### <a name="prepare-windows-server-vms"></a>Příprava virtuálních počítačů s Windows serverem

**Akce** | **Podrobnosti** | **Pokyny**
--- | --- | ---
Ujistěte se, že Windows svazky na virtuálním počítači Azure používat stejné přiřazení písmeno jednotky jako místní virtuální počítač. | Konfigurace zásad po síti SAN jako Online všechny. | 1. Přihlaste se k virtuálnímu počítači pomocí účtu správce a otevřete okno příkazového řádku.<br/> 2. Typ **diskpart** ke spuštění nástroje Diskpart.<br/> 3. Typ **ZÁSADA SAN = OnlineAll**<br/> 4. Zadejte příkaz Exit odejít Diskpart a zavřete příkazový řádek.
Povolit přístup ke službě Azure sériové konzoly pro virtuální počítač Azure | To pomáhá při řešení potíží. Není nutné restartovat virtuální počítač. Virtuální počítač Azure se spustí pomocí bitové kopie disku a jedná se o ekvivalent k restartování pro nový virtuální počítač. | Postupujte podle [tyto pokyny](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console#enable-serial-console-in-custom-or-older-images) povolit.
Instalace technologie Hyper-V Host Integration | Pokud migrujete počítače s Windows serverem 2003, nainstalujte integrační služby hosta technologie Hyper-V v operačním systému virtuálního počítače. | [Další informace](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Vzdálená plocha | Povolení vzdálené plochy na virtuálním počítači a zkontrolujte, že brána Windows Firewall neblokuje přístup ke vzdálené ploše na všechny profily sítě. | [Další informace](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Příprava virtuálních počítačů s Linuxem

**Akce** | **Podrobnosti** 
--- | --- | ---
Instalace technologie Hyper-V Linux Integration Services | Většina nových verzí Linuxových distribucí, máte zahrnuté ve výchozím nastavení.
Opětovné sestavení image Linuxu init obsahuje potřebné ovladače Hyper-V | Tím se zajistí, že virtuální počítač se spustí v Azure a je vyžadováno pouze u některých distribucích.
Povolení protokolování Azure konzoly sériového portu | To pomáhá při řešení potíží. Není nutné restartovat virtuální počítač. Virtuální počítač Azure se spustí pomocí bitové kopie disku a jedná se o ekvivalent k restartování pro nový virtuální počítač.<br/> Postupujte podle [tyto pokyny](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) povolit.
Aktualizovat soubor mapování zařízení | Aktualizovat soubor mapování zařízení, která má název zařízení na přidružení svazku, používat identifikátory trvalé zařízení
Aktualizace položky fstab | Aktualizace položky identifikátory trvalý svazek.
Odebrat pravidlo pro proces udev | Odeberte všechna udev pravidla, která rezervuje názvy rozhraní na základě adresy mac atd.
Aktualizace síťová rozhraní | Aktualizujte síťová rozhraní pro příjem DHCP podle IP adresy.
Povolit ssh | Zajištění ssh je povolená a sshd service je nastavená na automatické spuštění při restartování.<br/> Ujistěte se, že příchozí ssh připojení požadavky nejsou blokovány bránou firewall operačního systému nebo skriptovatelný pravidla.

[Postupujte podle tohoto článku](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) , který popisuje tyto kroky pro spuštění virtuálního počítače s Linuxem v Azure a pokyny pro některé z oblíbených Linuxových distribucí.  


## <a name="replicate-vms"></a>Replikace virtuálních počítačů

Pomocí zjišťování bylo dokončeno můžete začít s replikací virtuálních počítačů VMware do Azure.

1. V projektu Azure Migrate > **servery**, **Azure Migrate: Migrace serveru**, klikněte na tlačítko **replikovat**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-vmware/select-replicate.png)

2. V **replikovat**, > **nastavení zdroje** > **jsou vaše počítače virtualizované?** vyberte **Ano, s VMware vSphere**.
3. V **místní zařízení**, vyberte název zařízení, které jste nastavili Azure Migrate > **OK**. 

    ![Nastavení zdroje](./media/tutorial-migrate-vmware/source-settings.png)

    - Tento krok předpokládá, že jste již nastavili zařízení po dokončení tohoto kurzu.
    - Pokud jste nenastavili zařízení, postupujte podle pokynů v [v tomto článku](how-to-set-up-appliance-vmware.md).

4. V **virtuálních počítačů**, vyberte počítače, které chcete replikovat.
    - Pokud spouštíte posouzení virtuálních počítačů můžete použít velikosti virtuálních počítačů a doporučení ohledně typu (premium nebo standard) disku z výsledků posouzení. Chcete-li to provést, v **importovat nastavení migrace z posouzení služby Azure Migrate?** , vyberte **Ano** možnost.
    - Pokud jste nespustili posouzení, nebo nechcete používat nastavení posouzení, vyberte **ne** možnosti.
    - Pokud jste se rozhodli použít posouzení, vyberte skupiny virtuálních počítačů a název posouzení.

    ![Vyberte hodnocení](./media/tutorial-migrate-vmware/select-assessment.png)

5. V **virtuálních počítačů**, vyhledávání pro virtuální počítače podle potřeby a zkontrolujte jednotlivé virtuální počítače, které chcete migrovat. Pak klikněte na tlačítko **Další: Cílová nastavení**.

    ![Vyberte virtuální počítače](./media/tutorial-migrate-vmware/select-vms.png)

6. V **cílit na nastavení**, vyberte předplatné a cílit na oblast, do kterého budete migrovat a zadejte skupinu prostředků, ve kterém budou umístěné virtuální počítače Azure po migraci. V **virtuální sítě**, vyberte Azure virtuální sítě nebo podsítě do které budou připojeny virtuální počítače Azure po migraci.
7. V **zvýhodněné hybridní využití Azure**:

    - Vyberte **ne** Pokud nechcete použít zvýhodněné hybridní využití Azure. Pak klikněte na tlačítko **Další**.
    - Vyberte **Ano** Pokud máte počítače s Windows serverem, které se vztahuje aktivní Software Assurance nebo smlouvu Services systému Windows Server odběry a chcete použít tuto výhodu pro počítače, kterou migrujete. Pak klikněte na tlačítko **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-vmware/target-settings.png)

8. V **Compute**, zkontrolujte název virtuálního počítače, velikost, typ disku operačního systému a dostupnosti. Virtuální počítače musí splňovat [požadavky služby Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro interní hodnocení, rozevírací seznam velikostí virtuálních počítačů bude obsahovat doporučenou velikost. V opačném případě Azure Migrate použije velikost podle nejvíce odpovídá v rámci předplatného Azure. Můžete také vybrat ruční velikost v **velikost virtuálního počítače Azure**. 
    - **Disk s operačním systémem**: Zadejte disk s operačním systémem (spouštěcí) pro virtuální počítač. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč operačního systému a instalační služby. 
    - **Skupina dostupnosti**: Pokud virtuální počítač by měl být ve skupině dostupnosti Azure po migraci, určete sadu. Sada musí být v cílové skupině prostředků, kterou zadáte pro migraci.

    ![Nastavení výpočetního virtuálního počítače](./media/tutorial-migrate-vmware/compute-settings.png)

9. V **disky**, určete, jestli disky virtuálního počítače by měla být replikována do Azure a vyberte typ disku (SSD nebo HDD standard nebo premium managed disks) v Azure. Pak klikněte na tlačítko **Další**.
    - Vyloučení disků z replikace.
    - Pokud se můžete vyloučit disky, nemusí být na virtuálním počítači Azure po migraci. 

    ![Disky](./media/tutorial-migrate-vmware/disks.png)

10. V **zkontrolovat a zahájit replikaci**, zkontrolujte nastavení a klikněte na tlačítko **replikovat** má spustit počáteční replikace pro servery.

> [!NOTE]
> Můžete aktualizovat nastavení replikace kdykoli před zahájením replikace, **spravovat** > **replikaci počítačů**. Nastavení nelze změnit, jakmile replikace spustí.

### <a name="provisioning-for-the-first-time"></a>Zřizování poprvé

Pokud je prvním virtuálním počítači replikaci v projektu Azure Migrate, Azure Migrate migrace serveru automaticky zřídí těchto prostředků ve stejné skupině prostředků jako projekt.

- **Služba Service bus**: Migrace serveru do Azure migrovat používá service bus pro odesílání zpráv orchestraci replikace do zařízení.
- **Účet úložiště brány**: Migrace serveru používá účet úložiště brány k ukládání informací o stavu replikuje virtuální počítače.
- **Účtu úložiště protokolů**: Zařízení Azure Migrate nahraje protokoly replikace pro virtuální počítače do účtu úložiště protokolu. Azure Migrate použije replikace informace pro repliky spravovaných disků.
- **Trezor klíčů**: Zařízení Azure Migrate použije ke správě připojovací řetězce pro service bus a přístupových klíčů pro účty úložiště používané při replikaci služby key vault. Nastavíte by měl mít oprávnění, které jsou v trezoru klíčů přístup k účtu úložiště, když jste připravili. [Přečtěte si tato oprávnění](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Sledování a monitorování


- Po kliknutí na **replikovat** začne úloha spuštění replikace. 
- Když úloha spuštění replikace skončí úspěšně, počítačích začít jejich počáteční replikace do Azure.
- Během počáteční replikace se snímek virtuálního počítače vytvoří. Data na disku ze snímku se replikují do repliky spravovaných disků v Azure.
- Po dokončení počáteční replikace začne rozdílová replikace. Přírůstkové změny, které s místními disky se replikují pravidelně repliky disků v Azure.

Můžete sledovat stav úlohy v oznámeních portálu.

Stav replikace můžete sledovat kliknutím na **replikaci serverů** v **Azure Migrate: Migrace serveru**.
![Monitorování replikace](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Spuštění testu migrace


Po zahájení rozdílovou replikaci pro virtuální počítače, můžete spustit testovací migrace. před spuštěním úplné migrace do Azure. Důrazně doporučujeme to provést nejméně jednou pro každý počítač, před zahájením migrace.

- Spuštění testu migrace kontroly, které migrace budou fungovat podle očekávání, aniž by to ovlivnilo místních počítačů, které zůstat funkční a pokračovat v replikaci. 
- Migrace test simuluje migrace tím, že vytvoříte virtuální počítač Azure pomocí replikovaných dat (obvykle migrace na virtuální síť li se o neprodukční ve vašem předplatném Azure).
- Můžete ověřit migraci, proveďte testování aplikací pomocí replikovaných testovací virtuální počítač Azure a řešit případné problémy dříve než úplné migrace.

Testovací migrace postupujte následovně:


1. V **cílů migrace** > **servery** > **Azure Migrate: Migrace serveru**, klikněte na tlačítko **Test migrovat servery**.

     ![Testování migrované servery](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Klikněte pravým tlačítkem na virtuální počítač otestovat, a klikněte na **testovací migrace**.

    ![Testovací migrace.](./media/tutorial-migrate-vmware/test-migrate.png)

3. V **testovací migrace**, vyberte virtuální síť Azure, ve kterém virtuální počítač Azure se umístí po migraci. Doporučujeme, že používáte-li se o neprodukční virtuální sítě.
4. **Testovací migrace** úlohy spustí. Monitorování úlohy v oznámeních portálu.
5. Po dokončení migrace se zobrazit migrovaný virtuální počítač Azure v **virtuálních počítačů** na webu Azure Portal. Název počítače má příponu **-Test**.
6. Po dokončení testu, klikněte pravým tlačítkem na virtuální počítač Azure v **replikaci počítačů**a klikněte na tlačítko **vyčistěte testovací migrace**.

    ![Vyčištění migrace](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrace virtuálních počítačů

Po ověření, že testovací migrace funguje podle očekávání, můžete migrovat na místních počítačích.

1. V projektu Azure Migrate > **servery** > **Azure Migrate: Migrace serveru**, klikněte na tlačítko **replikaci serverů**.

    ![Replikaci serverů](./media/tutorial-migrate-vmware/replicate-servers.png)

2. V **replikaci počítačů**, klikněte pravým tlačítkem na virtuální počítač > **migrace**.
3. V **migrace** > **vypnout virtuální počítače a provádět při plánované migraci bez ztráty**vyberte **Ano** > **OK** .
    - Ve výchozím nastavení Azure Migrate místní virtuální počítač vypne a spustí replikaci na vyžádání synchronizovat změny virtuálního počítače, ke kterým došlo od poslední replikace. Tím se zajistí bez ztráty.
    - Pokud nechcete vypnout virtuální počítač, vyberte **ne**
4. Spustí úlohu migrace pro virtuální počítač. Sledování úlohy v Azure oznámení.
5. Po dokončení úlohy můžete zobrazit a spravovat virtuální počítač z **virtuálních počítačů** stránky.

## <a name="complete-the-migration"></a>Dokončení migrace

1. Poté, co migrace dokončí, klikněte pravým tlačítkem na virtuální počítač > **zastavit migraci**. Zastaví se replikace místních počítačů a vyčistí informace o stavu replikace pro virtuální počítač.
2. Nainstalujte virtuální počítač Azure [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) agenta na migrované počítače.
3. Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery.
4. U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
5. Vyjmete provozu na migrované instance virtuálního počítače Azure.
6. Odeberte místní virtuální počítače z místního inventáře virtuálních počítačů.
7. Odeberte místní virtuální počítače ze záloh.
8. Aktualizujte veškerou interní dokumentaci tak, aby obsahovala nová umístění a IP adresy virtuálních počítačů Azure. 

## <a name="post-migration-best-practices"></a>Osvědčené postupy po migraci

- Pro zvýšení odolnosti:
    - Zálohujte virtuální počítače Azure pomocí služby Azure Backup, abyste měli data zabezpečená. [Další informace](../backup/quick-backup-vm-portal.md).
    - Replikujte virtuální počítače Azure do sekundární oblasti pomocí služby Site Recovery, aby úlohy mohly neustále běžet a byly dostupné. [Další informace](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Pro zvýšení zabezpečení:
    - Zamknout a omezit příchozí provoz přístup s [Azure Security Center – Správa Just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Omezte síťový provoz na koncové body správy pomocí [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Nasaďte službu [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), která vám pomůže zabezpečit disky a zajistit bezpečnost dat před krádeží a neoprávněným přístupem.
    - Přečtěte si další informace o [zabezpečení prostředků IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) a navštivte [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Pro monitorování a správu:
-  Zvažte nasazení služby [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), která bude monitorovat využití prostředků a útratu.


## <a name="next-steps"></a>Další postup

Prozkoumat [cesty k migraci do cloudu](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) v rámci přechodu na Cloud Azure.
