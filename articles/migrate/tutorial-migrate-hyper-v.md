---
title: Migrace místních virtuálních počítačů Hyper-V do Azure pomocí Azure Migrate migrace serveru | Dokumentace Microsoftu
description: Tento článek popisuje, jak migrovat místní virtuální počítače Hyper-V do Azure pomocí služby Azure Migrate migrace serveru
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0d4ab3225501c2573b07f27e40a8f42508a4df88
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809537"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrace virtuálních počítačů s Hyper-V do Azure 

Tento článek popisuje, jak migrovat místní virtuální počítače Hyper-V do Azure, pomocí funkce agentless migrace pomocí nástroje Azure Migrate serveru migrace.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální rozbočovač pro sledování zjišťování, vyhodnocení a migraci místních aplikací a úloh a soukromého a veřejného cloudu virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro vyhodnocení a migrace, jakož i nabídky softwaru třetích stran výrobce (ISV).

Tento kurz je třetí webinář z řady, který ukazuje, jak vyhodnocovat a migrovat Hyper-V do Azure pomocí Azure Migrate serveru vyhodnocení a migraci. V tomto kurzu se naučíte:


> [!div class="checklist"]
> * Příprava Azure a Hyper-V v místním prostředí
> * Nastavení zdrojového prostředí a nasazením zařízení replikace.
> * Nastavení cílové environmen...
> * Povolení replikace.
> * Spuštění testu migrace, abyste měli jistotu, že všechno funguje podle očekávání.
> * Spustit plné migraci do Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Kontrola](migrate-architecture.md) architekturu migrace technologie Hyper-V.
2. [Dokončení tohoto kurzu první](tutorial-prepare-hyper-v.md) v této sérii nastavení Azure a Hyper-V pro migraci. V tomto prvním kurzu jste:
    - [Příprava Azure](tutorial-prepare-hyper-v.md#prepare-azure) pro migraci.
    - [Příprava místního prostředí](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) pro migraci.
3. Doporučujeme vyzkoušet posouzení virtuálních počítačů Hyper-V, pomocí Azure Migrate Server Assessment před migrací do Azure. K tomu [dokončení druhé části kurzu](tutorial-assess-hyper-v.md) této série. I když vám doporučujeme vyzkoušet posouzení, není nutné spustit posouzení, před migrací virtuálních počítačů.
4. Ujistěte se, že váš účet Azure je přiřazena role Přispěvatel virtuálních počítačů, tak, že máte oprávnění pro:

    - Vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Vytvoření virtuálního počítače ve vybrané virtuální síti
    - Zapsat do služby Azure spravovaného disku.   
5. [Nastavit síť Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Při migraci do Azure vytvořené virtuální počítače Azure jsou připojené k síti, kterou zadáte při nastavování migrace.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Přidat nástroj pro migraci serveru migrace Azure

Pokud sledujete nebyl druhé části kurzu k posouzení virtuálních počítačů Hyper-V, budete muset [postupujte podle těchto pokynů](how-to-add-tool-first-time.md) nastavit projekt Azure Migrate a přidejte do projektu Azure Migrate serveru migračního nástroje.

Pokud a potom druhé části kurzu a již mít nastavení projektu Azure Migrate, přidejte nástroj pro migraci serveru migrace Azure následujícím způsobem:

1. V projektu Azure Migrate klikněte na tlačítko **přehled**. 
2. V **zjišťování, vyhodnocení, určení a serverech migrace**, klikněte na tlačítko **posouzení a migraci serverů**.
3. V **nástrojů pro migraci**vyberte **kliknutím sem přidáte nástroj pro migraci, až budete připravení migrovat**.

    ![Výběr nástroje](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. V seznamu nástrojů vyberte **Azure Migrate: Migrace serveru** > **přidat nástroj**

    ![Nástroj pro migraci serveru](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate

Migrace serveru do Azure migrovat spustí jednoduchý virtuální počítač Hyper-V zařízení.

- Zařízení provádí zjišťování virtuálních počítačů a odesílá data metadata a výkonu virtuálních počítačů do Azure Migrate serveru migrace.
- Na stejném zařízení také používá Azure Migrate Server Assessment tool.

Nastavení zařízení:
- Pokud jste postupovali podle druhé části kurzu k posouzení virtuálních počítačů Hyper-V, jste už nastavili zařízení během tohoto kurzu.
- Pokud nebyl postupovat podle tohoto kurzu, musíte zařízení nastavit teď. Chcete-li to provést, můžete: 

    - Stáhněte si komprimovaný virtuálního pevného disku Hyper-V na webu Azure Portal.
    - Toto zařízení vytvoříte tak a zkontrolujte, že se může připojit k Azure Migrate Server Assessment. 
    - Konfigurace zařízení poprvé a zaregistrujte je v projektu Azure Migrate.

    Postupujte podle pokynů v [v tomto článku](how-to-set-up-appliance-hyper-v.md) nastavení zařízení.

## <a name="prepare-hyper-v-hosts"></a>Příprava hostitelů Hyper-V

1. V projektu Azure Migrate > **servery**v **Azure Migrate: Migrace serveru**, klikněte na tlačítko **Discover**.
2. V **zjistit počítače** > **jsou vaše počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**.
3. V **cílové oblasti**, vyberte oblast Azure, do kterého chcete migrovat počítače.
6. Vyberte **potvrdit, že cílová oblast pro migraci název oblasti**.
7. Klikněte na tlačítko **vytvářet prostředky**. Tím se vytvoří trezor služby Azure Site Recovery na pozadí.
    - Pokud jste již nastavili migrace se službou Azure Migrate serveru migrace, tato možnost se nezobrazí, protože prostředky byly dříve zřízeny.
    - Cílová oblast pro tento projekt nelze změnit po kliknutí na toto tlačítko.
    - Všechny následné migrace jsou do této oblasti.
    
8. V **hostitelských serverů Hyper-V přípravě**, stáhněte si zprostředkovatel replikace Hyper-V a soubor registračního klíče.
    - Registrační klíč je potřeba k registraci ve službě Azure Migrate migrace serveru hostitele technologie Hyper-V.
    - Klíč je platný pět dní od jeho vygenerování.

    ![Stáhněte si poskytovatelem a klíčem.](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Zkopírujte instalační soubor zprostředkovatele a soubor registračního klíče do každého hostitele Hyper-V (nebo uzel clusteru), spuštění virtuálních počítačů, které chcete replikovat.
5. Zprostředkovatel spusťte instalační soubor na každém hostiteli, jak je popsáno v následujícím postupu.
6. Po instalaci zprostředkovatele na hostitele, v **zjistit počítače**, klikněte na tlačítko **dokončení registrace**.

    ![Dokončení registrace](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Může trvat až 15 minut po dokončení registrace, dokud zjištěné virtuální počítače v Azure Migrate serveru migrace. Při zjištění virtuálních počítačů, **zjištěných serverů** počet složitost.

![Vyhledaných serverů](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Registrace hostitelů Hyper-V

Nainstalujte na stažený instalační soubor (AzureSiteRecoveryProvider.exe) na každém hostiteli relevantní technologie Hyper-V.

1. Spusťte instalační soubor na každého hostitele nebo uzel clusteru zprostředkovatele.
2. V Průvodci instalací zprostředkovatele > **Microsoft Update**, vyjádřit výslovný souhlas s použitím Microsoft Update ke kontrole aktualizací zprostředkovatele.
3. V **instalace**, přijměte výchozí umístění instalace zprostředkovatele a agenta a vyberte **nainstalovat**.
4. Po instalaci v Průvodci registrací > **nastavení trezoru**vyberte **Procházet**a v **soubor klíče**, vyberte soubor klíče trezoru, který jste stáhli.
5. V **nastavení proxy serveru**, určete, jak se zprostředkovatel, který běží na hostiteli připojí k Internetu.
    - Pokud se zařízení nachází za proxy serverem, budete muset zadat nastavení proxy serveru.
    - Zadejte název proxy serveru jako **http://ip-address** , nebo **http://FQDN** . HTTPS proxy servery nejsou podporovány.
   

6. Ujistěte se, že zprostředkovatel můžete oslovit [požadované adresy URL](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access).
7. V **registrace**, po registraci hostitele, klikněte na tlačítko **Dokončit**.

## <a name="replicate-hyper-v-vms"></a>Replikace virtuálních počítačů Hyper-V

Pomocí zjišťování bylo dokončeno můžete začít s replikací virtuálních počítačů Hyper-V do Azure.

1. V projektu Azure Migrate > **servery**, **Azure Migrate: Migrace serveru**, klikněte na tlačítko **replikovat**.
2. V **replikovat**, > **nastavení zdroje** > **jsou vaše počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**. Pak klikněte na tlačítko **Další: Virtuální počítače**.
3. V **virtuálních počítačů**, vyberte počítače, které chcete replikovat.
    - Pokud spouštíte posouzení virtuálních počítačů můžete použít velikosti virtuálních počítačů a doporučení ohledně typu (premium nebo standard) disku z výsledků posouzení. Chcete-li to provést, v **importovat nastavení migrace z posouzení služby Azure Migrate?** , vyberte **Ano** možnost.
    - Pokud jste nespustili posouzení, nebo nechcete používat nastavení posouzení, vyberte **ne** možnosti.
    - Pokud jste se rozhodli použít posouzení, vyberte skupiny virtuálních počítačů a název posouzení.

        ![Vyberte hodnocení](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. V **virtuálních počítačů**, vyhledávání pro virtuální počítače podle potřeby a zkontrolujte jednotlivé virtuální počítače, které chcete migrovat. Potom klikněte na **Další: Cílová nastavení**.

    ![Vyberte virtuální počítače](./media/tutorial-migrate-hyper-v/select-vms.png)

5. V **cílit na nastavení**, vyberte cílovou oblast, do kterého jste budete migrovat, předplatné a skupinu prostředků, ve kterém budou umístěné virtuální počítače Azure po migraci.
7. V **replikace účtu úložiště**, vyberte účet úložiště Azure, ve kterém replikovaná data se budou ukládat v Azure.
8. **Virtuální síť**, vyberte Azure virtuální sítě nebo podsítě do které budou připojeny virtuální počítače Azure po migraci.
9. V **zvýhodněné hybridní využití Azure**:

    - Vyberte **ne** Pokud nechcete použít zvýhodněné hybridní využití Azure. Pak klikněte na **Další**.
    - Vyberte **Ano** Pokud máte počítače s Windows serverem, které se vztahuje aktivní Software Assurance nebo smlouvu Services systému Windows Server odběry a chcete použít tuto výhodu pro počítače, kterou migrujete. Pak klikněte na tlačítko **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-hyper-v/target-settings.png)

10. V **Compute**, zkontrolujte název virtuálního počítače, velikost, typ disku operačního systému a dostupnosti. Virtuální počítače musí splňovat [požadavky služby Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro interní hodnocení, rozevírací seznam velikostí virtuálních počítačů bude obsahovat doporučenou velikost. V opačném případě Azure Migrate použije velikost podle nejvíce odpovídá v rámci předplatného Azure. Můžete také vybrat ruční velikost v **velikost virtuálního počítače Azure**. 
    - **Disk s operačním systémem**: Zadejte disk s operačním systémem (spouštěcí) pro virtuální počítač. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč operačního systému a instalační služby. 
    - **Skupina dostupnosti**: Pokud virtuální počítač by měl být ve skupině dostupnosti Azure po migraci, určete sadu. Sada musí být v cílové skupině prostředků, kterou zadáte pro migraci.

    ![Nastavení výpočetního virtuálního počítače](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. V **disky**, určete, jestli disky virtuálního počítače by měla být replikována do Azure a vyberte typ disku (SSD nebo HDD standard nebo premium managed disks) v Azure. Pak klikněte na tlačítko **Další**.
    - Vyloučení disků z replikace.
    - Pokud se můžete vyloučit disky, nemusí být na virtuálním počítači Azure po migraci. 

    ![Disky](./media/tutorial-migrate-hyper-v/disks.png)

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
- Po dokončení počáteční replikace začne rozdílová replikace. Přírůstkové změny, které s místními disky jsou pravidelně replikují do Azure.

Můžete sledovat stav úlohy v oznámeních portálu.

Stav replikace můžete sledovat kliknutím na **replikaci serverů** v **Azure Migrate: Migrace serveru**.
![Monitorování replikace](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Spuštění testu migrace


Po zahájení rozdílovou replikaci pro virtuální počítače, můžete spustit testovací migrace. před spuštěním úplné migrace do Azure. Důrazně doporučujeme to provést nejméně jednou pro každý počítač, před zahájením migrace.

- Spuštění testu migrace kontroly, které migrace budou fungovat podle očekávání, aniž by to ovlivnilo místních počítačů, které zůstat funkční a pokračovat v replikaci. 
- Migrace test simuluje migrace tím, že vytvoříte virtuální počítač Azure pomocí replikovaných dat (obvykle migrace na virtuální síť li se o neprodukční ve vašem předplatném Azure).
- Můžete ověřit migraci, proveďte testování aplikací pomocí replikovaných testovací virtuální počítač Azure a řešit případné problémy dříve než úplné migrace.

Testovací migrace postupujte následovně:


1. V **cílů migrace** > **servery** > **Azure Migrate: Migrace serveru**, klikněte na tlačítko **Test migrovat servery**.

     ![Testování migrované servery](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Klikněte pravým tlačítkem na virtuální počítač otestovat, a klikněte na **testovací migrace**.

    ![Testovací migrace.](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. V **testovací migrace**, vyberte virtuální síť Azure, ve kterém virtuální počítač Azure se umístí po migraci. Doporučujeme, že používáte-li se o neprodukční virtuální sítě.
4. **Testovací migrace** úlohy spustí. Monitorování úlohy v oznámeních portálu.
5. Po dokončení migrace se zobrazit migrovaný virtuální počítač Azure v **virtuálních počítačů** na webu Azure Portal. Název počítače má příponu **-Test**.
6. Po dokončení testu, klikněte pravým tlačítkem na virtuální počítač Azure v **replikaci počítačů**a klikněte na tlačítko **vyčistěte testovací migrace**.

    ![Vyčištění migrace](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrace virtuálních počítačů

Po ověření, že testovací migrace funguje podle očekávání, můžete migrovat na místních počítačích.

1. V projektu Azure Migrate > **servery** > **Azure Migrate: Migrace serveru**, klikněte na tlačítko **replikaci serverů**.

    ![Replikaci serverů](./media/tutorial-migrate-hyper-v/replicate-servers.png)

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
