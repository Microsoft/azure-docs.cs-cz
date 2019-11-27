---
title: Migrace Azure Migrate migrace serveru bez agentů pro virtuální počítače VMware
description: Naučte se spouštět migraci virtuálních počítačů VMware bez agenta pomocí Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2b4aad83abc92170df5a7e7cfa7f7751b49b3424
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196404"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrace virtuálních počítačů VMware do Azure (bez agenta)

V tomto článku se dozvíte, jak migrovat místní virtuální počítače VMware do Azure pomocí migrace bez agenta pomocí nástroje pro migraci Azure Migrate serveru.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a instancí virtuálních počítačů AWS/GCP do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Tento kurz je třetí součástí série, která ukazuje, jak vyhodnocovat a migrovat virtuální počítače VMware do Azure pomocí Azure Migrateho posuzování a migrace serveru. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte virtuální počítače pro migraci.
> * Přidejte Nástroj pro migraci serveru Azure Migration Server.
> * Vyhledejte virtuální počítače, které chcete migrovat.
> * Zahajte replikaci virtuálních počítačů.
> * Spusťte test migrace a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci virtuálního počítače.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.

## <a name="migration-methods"></a>Metody migrace

Virtuální počítače VMware můžete migrovat do Azure pomocí nástroje pro migraci Azure Migrate serveru. Tento nástroj nabízí několik možností migrace virtuálních počítačů VMware:

- Migrace pomocí replikace bez agentů. Migrujte virtuální počítače, aniž byste museli instalovat cokoli.
- Migrace s agentem pro replikaci. Nainstalujte na virtuální počítač agenta pro replikaci.

Pokud chcete rozhodnout, jestli chcete použít migraci bez agenta nebo agenta, přečtěte si tyto články:

- [Přečtěte si, jak](server-migrate-overview.md) funguje migrace bez agentů, a [Porovnejte metody migrace](server-migrate-overview.md#compare-migration-methods).
- Pokud chcete použít metodu založenou na agentech, [Přečtěte si tento článek](tutorial-migrate-vmware-agent.md) .

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Provedením prvního kurzu](tutorial-prepare-vmware.md) v této sérii nastavíte Azure a VMware pro migraci. Konkrétně v tomto kurzu potřebujete:
    - [Připravte Azure](tutorial-prepare-vmware.md#prepare-azure) na migraci.
    - [Připravte místní prostředí](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) pro migraci.
    
2. Před migrací do Azure doporučujeme vyzkoušet vyhodnocování virtuálních počítačů VMware pomocí Azure Migrate posouzení serveru. Pokud chcete nastavit posouzení, [dokončete druhý kurz](tutorial-assess-vmware.md) v této sérii. Pokud nechcete vyhodnotit virtuální počítače, můžete tento kurz vynechat. I když doporučujeme, abyste si vyzkoušeli posouzení, ale před migrací nemusíte spustit posouzení.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Přidání nástroje pro migraci Azure Migrate serveru

Pokud jste nepoužili druhý kurz pro vyhodnocení virtuálních počítačů VMware, musíte [postupovat podle těchto pokynů](how-to-add-tool-first-time.md) pro nastavení Azure Migrate projektu a vybrat nástroj pro migraci Azure Migrate serveru. 

Pokud jste postupovali s druhým kurzem a již máte nastaven Azure Migrate projekt, přidejte Nástroj pro migraci Azure Migrate serveru následujícím způsobem:

1. V projektu Azure Migrate klikněte na **Přehled**. 
2. V nabídce **zjišťování, posouzení a migrace serverů**klikněte na možnost **zhodnotit a migrovat servery**.

     ![Posouzení a migrace serverů](./media/tutorial-migrate-vmware/assess-migrate.png)

3. V okně **Nástroje pro migraci**vyberte **kliknutím sem přidáte Nástroj pro migraci, který jste připraveni migrovat**.

    ![Výběr nástroje](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. V seznamu Nástroje vyberte **Azure Migrate: Serverová migrace** > **Přidat nástroj** .

    ![Nástroj pro migraci serveru](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate

Migrace serveru Azure Migrate spouští odlehčené zařízení VMware VM. Zařízení provádí zjišťování virtuálních počítačů a odesílá data o výkonu a data virtuálních počítačů do Azure Migrate migrace serveru. Stejné zařízení používá také nástroj pro vyhodnocení Azure Migrate serveru.

Pokud jste postupovali podle druhého kurzu a vyhodnotili virtuální počítače VMware, zařízení jste už v tomto kurzu nastavili. Pokud jste tento kurz neudělali, musíte zařízení nastavit nyní. Uděláte to takto: 

- Stáhněte soubor šablony vajíček a naimportujte ho do vCenter Server.
- Vytvořte zařízení a ověřte, že se může připojit k Azure Migrate posouzení serveru. 
- Nakonfigurujete zařízení poprvé a zaregistrujete ho do projektu Azure Migrate.

Pokud chcete zařízení nastavit, postupujte podle pokynů v [tomto článku](how-to-set-up-appliance-vmware.md) .


## <a name="prepare-vms-for-migration"></a>Příprava virtuálních počítačů na migraci

Azure Migrate vyžaduje změny virtuálních počítačů, aby bylo zajištěno, že virtuální počítače lze migrovat do Azure.

- Pro některé operační systémy Azure Migrate provede tyto změny automaticky. [Další informace](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)
- Pokud migrujete virtuální počítač, který nemá některý z těchto operačních systémů, připravte si virtuální počítač podle pokynů.
- Před zahájením migrace je důležité tyto změny provést. Pokud před provedením změny migrujete virtuální počítač, nemusí se virtuální počítač spustit v Azure.
- Změny konfigurace, které provedete na místních virtuálních počítačích, se po povolení replikace pro virtuální počítač replikují do Azure. Aby se zajistilo, že se změny replikují, ujistěte se, že bod obnovení, na který migrujete, je pozdější než čas, kdy se změny konfigurace provedly v místním prostředí.


### <a name="prepare-windows-server-vms"></a>Příprava virtuálních počítačů s Windows serverem

**Akce** | **Podrobnosti** | **Pokynů**
--- | --- | ---
Zajistěte, aby svazky Windows na virtuálním počítači Azure používaly stejná přiřazení písmen jednotek jako místní virtuální počítač. | Nakonfigurujte zásady sítě SAN jako online vše. | 1. Přihlaste se k virtuálnímu počítači pomocí účtu správce a otevřete příkazové okno.<br/> 2. zadáním příkazu **DiskPart** spustíte nástroj Diskpart.<br/> 3. zadejte **zásady sítě San = OnlineAll**<br/> 4. Zadejte příkaz exit pro opuštění programu DiskPart a zavřete příkazový řádek.
Povolení konzole sériového přístupu Azure pro virtuální počítač Azure | To pomáhá při řešení potíží. Nemusíte restartovat virtuální počítač. Virtuální počítač Azure se spustí s použitím bitové kopie disku a je stejný jako restart nového virtuálního počítače. | Pokud chcete povolit, postupujte podle [těchto pokynů](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) .
Nainstalovat integraci hosta technologie Hyper-V | Pokud migrujete počítače se systémem Windows Server 2003, nainstalujte integrační služby technologie Hyper-V hosta do operačního systému virtuálního počítače. | [Další informace](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Vzdálená plocha | Povolte na VIRTUÁLNÍm počítači vzdálenou plochu a ověřte, že brána Windows firewall neblokuje přístup ke vzdálené ploše v žádných síťových profilech. | [Další informace](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Příprava virtuálních počítačů se systémem Linux

**Akce** | **Podrobnosti** 
--- | --- | ---
Nainstalovat integrační služby Hyper-V Linux | Ve výchozím nastavení je k dispozici většina nových verzí distribucí systému Linux.
Znovu sestavte inicializační image Linux tak, aby obsahovala potřebné ovladače Hyper-V. | Tím se zajistí, že se virtuální počítač spustí v Azure a vyžaduje se jenom u některých distribucí.
Povolit protokolování na sériové konzole Azure | To pomáhá při řešení potíží. Nemusíte restartovat virtuální počítač. Virtuální počítač Azure se spustí s použitím bitové kopie disku a je stejný jako restart nového virtuálního počítače.<br/> Pokud chcete povolit, postupujte podle [těchto pokynů](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) .
Aktualizovat soubor mapování zařízení | Aktualizujte soubor mapování zařízení s názvem zařízení na přidružení svazků, aby se používaly trvalé identifikátory zařízení.
Aktualizovat položky fstab | Aktualizujte položky tak, aby používaly trvalé identifikátory svazků.
Odebrat pravidlo udev | Odeberte všechna udev pravidla, která vyhradí názvy rozhraní na základě adresy Mac atd.
Aktualizace síťových rozhraní | Aktualizujte síťová rozhraní tak, aby přijímala IP adresu založenou na protokolu DHCP.
Povolit SSH | Ujistěte se, že je povolený protokol SSH a služba sshd je nastavená tak, aby se automaticky spustila při restartování.<br/> Zajistěte, aby příchozí požadavky na připojení SSH nebyly blokované pomocí brány firewall operačního systému nebo pravidel pro skriptování.

[Postupujte podle tohoto článku](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) , který popisuje tyto kroky pro spuštění virtuálního počítače se systémem Linux v Azure a obsahuje pokyny pro některé z oblíbených distribucí systému Linux.  


## <a name="replicate-vms"></a>Replikace virtuálních počítačů

Po dokončení zjišťování můžete zahájit replikaci virtuálních počítačů VMware do Azure. 

> [!NOTE]
> Můžete replikovat až 10 počítačů dohromady. Pokud potřebujete replikovat více, proveďte jejich replikaci současně v dávkách po 10. U migrace bez agenta můžete spustit až 100 simultánních replikací.

1. V Azure Migrate Project > **servery** **Azure Migrate: Migrace serveru**klikněte na **replikovat**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-vmware/select-replicate.png)

2. V části **Replikovat** > **Nastavení zdroje** > **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere**.
3. V části **Místní zařízení** vyberte název zařízení Azure Migrate, které jste nastavili, a klikněte na **OK**. 

    ![Nastavení zdroje](./media/tutorial-migrate-vmware/source-settings.png)

    - V tomto kroku se předpokládá, že jste už po dokončení kurzu nastavili zařízení.
    - Pokud jste zařízení nevytvořili, postupujte podle pokynů v [tomto článku](how-to-set-up-appliance-vmware.md).

4. V části **Virtuální počítače** vyberte počítače, které chcete replikovat.
    - Pokud jste pro virtuální počítače spustili posouzení, můžete použít doporučenou velikost a typ disku (Premium nebo Standard) z výsledků posouzení. Pokud to chcete provést, v části **Importovat nastavení migrace z posouzení Azure Migrate?** vyberte možnost **Ano**.
    - Pokud jste neprovedli posouzení nebo pokud nechcete použít nastavení posouzení, vyberte možnost **Ne**.
    - Pokud jste se rozhodli použít posouzení, vyberte skupinu virtuálních počítačů a název posouzení.

    ![Výběr posouzení](./media/tutorial-migrate-vmware/select-assessment.png)

5. V části **Virtuální počítače** vyhledejte požadované virtuální počítače a zkontrolujte všechny virtuální počítače, které chcete migrovat. Pak klikněte na **Další: nastavení cíle**.

    ![Vybrat virtuální počítače](./media/tutorial-migrate-vmware/select-vms.png)

6. V části **Nastavení cíle** vyberte předplatné a cílovou oblast migrace a zadejte skupinu prostředků, ve které se po migraci budou nacházet virtuální počítače Azure. V části **Virtuální síť** vyberte virtuální síť a podsíť Azure, ke kterým se po migraci připojí virtuální počítače Azure.
7. V části **Zvýhodněné hybridní využití Azure**:

    - Vyberte **Ne**, pokud nechcete využít Zvýhodněné hybridní využití Azure. Pak klikněte na tlačítko **Další**.
    - Vyberte **Ano**, pokud máte počítače s Windows Serverem s aktivním Software Assurance nebo předplatným Windows Serveru a u migrovaných počítačů chcete využít tuto výhodu. Pak klikněte na tlačítko **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-vmware/target-settings.png)

8. V části **Výpočetní prostředky** zkontrolujte název, velikost, typ disku s operačním systémem a skupinu dostupnosti virtuálního počítače. Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro vyhodnocení, bude rozevírací seznam velikost virtuálního počítače obsahovat doporučenou velikost. Jinak Azure Migrate vybere velikost na základě nejbližší shody v předplatném Azure. Případně můžete velikost vybrat ručně v části **Velikost virtuálního počítače Azure**. 
    - **Disk s operačním systémem**: zadejte operační systém (spouštěcí) disk pro virtuální počítač. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč a instalační program operačního systému. 
    - **Skupina dostupnosti**: Pokud má být virtuální počítač v sadě dostupnosti Azure po migraci, zadejte sadu. Skupina musí být v cílové skupině prostředků, kterou pro migraci zadáte.

    ![Nastavení výpočetního virtuálního počítače](./media/tutorial-migrate-vmware/compute-settings.png)

9. V části **Disky** zadejte, jestli se mají disky virtuálních počítačů replikovat do Azure, a vyberte typ disků (disky SSD nebo HDD úrovně Standard nebo spravované disky úrovně Premium) v Azure. Pak klikněte na tlačítko **Další**.
    - Disky můžete z replikace vyloučit.
    - Pokud disky vyloučíte, po migraci nebudou na virtuálním počítači Azure. 

    ![Disky](./media/tutorial-migrate-vmware/disks.png)

10. V části **Kontrola a zahájení replikace** zkontrolujte nastavení a kliknutím na **Replikovat** spusťte počáteční replikaci serverů.

> [!NOTE]
> Před zahájením replikace můžete nastavení replikace kdykoli aktualizovat v části **Správa** > **Replikace počítačů**. Po spuštění replikace není možné nastavení změnit.

### <a name="provisioning-for-the-first-time"></a>Zřizování pro první čas

Pokud se jedná o první virtuální počítač, který se replikuje v projektu Azure Migrate, Azure Migrate migrace serveru automaticky zřídí tyto prostředky ve stejné skupině prostředků jako projekt.

- **Service Bus**: migrace Azure Migrate serveru používá Service Bus k posílání zpráv orchestrace replikace do zařízení.
- **Účet úložiště brány**: Migrace serveru používá účet úložiště brány k ukládání informací o stavu virtuálních počítačů, které se replikují.
- **Účet úložiště protokolu**: zařízení Azure Migrate nahrává protokoly replikace pro virtuální počítače do účtu úložiště protokolu. Azure Migrate použije informace o replikaci na spravované disky repliky.
- **Trezor klíčů**: zařízení Azure Migrate používá Trezor klíčů ke správě připojovacích řetězců pro Service Bus a přístup k klíčům pro účty úložiště používané v replikaci. Měli byste nastavit oprávnění, která Trezor klíčů potřebuje k přístupu k účtu úložiště, když jste připravili. [Zkontrolujte tato oprávnění](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Sledování a sledování


- Po kliknutí na **replikace** se spustí úloha spustit replikaci. 
- Po úspěšném dokončení úlohy spuštění replikace začnou počítače počáteční replikaci do Azure.
- Během počáteční replikace se vytvoří snímek virtuálního počítače. Data na disku ze snímku se replikují na spravované disky repliky v Azure.
- Po dokončení počáteční replikace se spustí rozdílová replikace. Přírůstkové změny na místních discích se pravidelně replikují na disky replik v Azure.

Stav úlohy můžete sledovat v oznámeních na portálu.

Stav replikace můžete sledovat kliknutím na **servery replikace** v **Azure Migrate: Migrace serveru**.
![monitorování replikace](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Spuštění testu migrace


Po zahájení rozdílové replikace můžete spustit testovací migraci pro virtuální počítače před spuštěním úplné migrace do Azure. Důrazně doporučujeme, abyste to pro každý počítač provedli aspoň jednou, a teprve potom ho migrujete.

- Při spuštění testu migrace proběhne kontrola, že migrace bude fungovat podle očekávání, aniž by to ovlivnilo místní počítače, které zůstávají v provozu, a pokračuje v replikaci. 
- Testovací migrace simuluje migraci tím, že vytvoří virtuální počítač Azure pomocí replikovaných dat (obvykle se migruje na virtuální síť, která není v produkčním prostředí, ve vašem předplatném Azure).
- Replikovaný virtuální počítač Azure můžete použít k ověření migrace, provádění testování aplikace a řešení všech problémů před úplnou migrací.

Proveďte migraci testu následujícím způsobem:


1. V ** > ** **cíle migrace** > **Azure Migrate: Migrace serveru**klikněte na **test migrovaných serverů**.

     ![Test migrovaných serverů](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Klikněte pravým tlačítkem na virtuální počítač, který chcete otestovat, a klikněte na **Testovací migrace**.

    ![Testovací migrace](./media/tutorial-migrate-vmware/test-migrate.png)

3. V části **Testovací migrace** vyberte virtuální síť Azure, ve které se po migraci bude nacházet virtuální počítač Azure. Doporučujeme použít jinou než produkční virtuální síť.
4. Spustí se úloha **Testovací migrace**. Tuto úlohu můžete monitorovat pomocí oznámení portálu.
5. Po dokončení migrace si můžete migrovaný virtuální počítač Azure prohlédnout na webu Azure Portal v části **Virtuální počítače**. Název počítače má příponu **-Test**.
6. Po dokončení testu v části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač Azure a klikněte na **Vyčistit testovací migraci**.

    ![Vyčištění migrace](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrace virtuálních počítačů

Po ověření, že migrace testu funguje podle očekávání, můžete migrovat místní počítače.

1. V Azure Migrate Project > **servery** > **Azure Migrate: Migrace serveru**klikněte na **replikovat servery**.

    ![Replikace serverů](./media/tutorial-migrate-vmware/replicate-servers.png)

2. V části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač a vyberte **Migrovat**.
3. V části **Migrovat** > **Vypnout virtuální počítače a provést naplánovanou migraci bez ztráty dat** vyberte **Ano** > **OK**.
    - Azure Migrate ve výchozím nastavení vypne místní virtuální počítač a spustí replikaci na vyžádání, při které se synchronizují všechny změny virtuálního počítače, ke kterým došlo od poslední replikace. Tím se zajistí, že nedojde ke ztrátě dat.
    - Pokud virtuální počítač nechcete vypnout, vyberte **Ne**.
4. Pro virtuální počítač se spustí úloha migrace. Tuto úlohu můžete sledovat pomocí oznámení Azure.
5. Po dokončení úlohy můžete virtuální počítač zobrazit a spravovat na stránce **Virtuální počítače**.

## <a name="complete-the-migration"></a>Dokončení migrace

1. Po dokončení migrace klikněte pravým tlačítkem na virtuální počítač > **zastavit migraci**. Tím se zastaví replikace místního počítače a vyčistí se informace o stavu replikace pro virtuální počítač.
2. Na migrované počítače nainstalujte agenta Azure VM pro [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) .
3. Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery.
4. U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
5. Vyjmutí provozu do migrované instance virtuálního počítače Azure
6. Odeberte místní virtuální počítače z místního inventáře virtuálních počítačů.
7. Odeberte místní virtuální počítače ze záloh.
8. Aktualizujte veškerou interní dokumentaci tak, aby obsahovala nová umístění a IP adresy virtuálních počítačů Azure. 

## <a name="post-migration-best-practices"></a>Osvědčené postupy po migraci

- Pro zvýšení odolnosti:
    - Zálohujte virtuální počítače Azure pomocí služby Azure Backup, abyste měli data zabezpečená. [Další informace](../backup/quick-backup-vm-portal.md).
    - Replikujte virtuální počítače Azure do sekundární oblasti pomocí služby Site Recovery, aby úlohy mohly neustále běžet a byly dostupné. [Další informace](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Pro zvýšení zabezpečení:
    - Odblokujte a omezte přístup k příchozímu provozu pomocí [správy v čase Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Omezte síťový provoz na koncové body správy pomocí [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Nasaďte službu [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), která vám pomůže zabezpečit disky a zajistit bezpečnost dat před krádeží a neoprávněným přístupem.
    - Přečtěte si další informace o [zabezpečení prostředků IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) a navštivte [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Pro monitorování a správu:
-  Zvažte nasazení služby [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), která bude monitorovat využití prostředků a útratu.


## <a name="next-steps"></a>Další kroky

Prozkoumejte [cestu k migraci do cloudu](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) v rozhraní Azure cloudu pro přijetí.
