---
title: Migrace místních virtuálních počítačů VMware do Azure s migrací Azure Migrate serveru bez agenta | Microsoft Docs
description: Popisuje, jak provést migraci místních virtuálních počítačů VMware do Azure bez agenta pomocí Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7fba9cbbaa15359e7e4dd95e66645dd5f4022431
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640779"
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

- [Přečtěte si, jak](server-migrate-overview.md) funguje migrace bez agentů, a porovnejte [metody migrace](server-migrate-overview.md#compare-migration-methods).
- Pokud chcete použít metodu založenou na agentech, [Přečtěte si tento článek](tutorial-migrate-vmware-agent.md) .

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Pochopení](migrate-architecture.md) architektury migrace VMware
2. [Provedením prvního kurzu](tutorial-prepare-vmware.md) v této sérii nastavíte Azure a VMware pro migraci. Konkrétně v tomto kurzu potřebujete:
    - [Připravte Azure](tutorial-prepare-vmware.md#prepare-azure) na migraci.
    - [Připravte místní prostředí](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) pro migraci.
    
3. Před migrací do Azure doporučujeme vyzkoušet vyhodnocování virtuálních počítačů VMware pomocí Azure Migrate posouzení serveru. Pokud chcete nastavit posouzení, [dokončete druhý kurz](tutorial-assess-vmware.md) v této sérii. Pokud nechcete vyhodnotit virtuální počítače, můžete tento kurz vynechat. I když doporučujeme, abyste si vyzkoušeli posouzení, ale před migrací nemusíte spustit posouzení.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Přidání nástroje pro migraci Azure Migrate serveru

Pokud jste nepoužili druhý kurz pro vyhodnocení virtuálních počítačů VMware, musíte [postupovat podle těchto pokynů](how-to-add-tool-first-time.md) pro nastavení Azure Migrate projektu a vybrat nástroj pro migraci Azure Migrate serveru. 

Pokud jste postupovali s druhým kurzem a již máte nastaven Azure Migrate projekt, přidejte Nástroj pro migraci Azure Migrate serveru následujícím způsobem:

1. V projektu Azure Migrate klikněte na **Přehled**. 
2. V nabídce **zjišťování, posouzení a migrace serverů**klikněte na možnost **zhodnotit a migrovat servery**.

     ![Posouzení a migrace serverů](./media/tutorial-migrate-vmware/assess-migrate.png)

3. V okně **Nástroje pro migraci**vyberte **kliknutím sem přidáte Nástroj pro migraci, který jste připraveni migrovat**.

    ![Výběr nástroje](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. V seznamu Nástroje vyberte **Azure Migrate: Nástroj pro** **Přidání** migrace serveru > 

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

- Pro některé operační systémy Azure Migrate provede tyto změny automaticky. [Víc se uč](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)
- Pokud migrujete virtuální počítač, který nemá některý z těchto operačních systémů, připravte si virtuální počítač podle pokynů.
- Před zahájením migrace je důležité tyto změny provést. Pokud před provedením změny migrujete virtuální počítač, nemusí se virtuální počítač spustit v Azure.
- Změny konfigurace, které provedete na místních virtuálních počítačích, se po povolení replikace pro virtuální počítač replikují do Azure. Aby se zajistilo, že se změny replikují, ujistěte se, že bod obnovení, na který migrujete, je pozdější než čas, kdy se změny konfigurace provedly v místním prostředí.


### <a name="prepare-windows-server-vms"></a>Příprava virtuálních počítačů s Windows serverem

**Akce** | **Podrobnosti** | **Pokynů**
--- | --- | ---
Zajistěte, aby svazky Windows na virtuálním počítači Azure používaly stejná přiřazení písmen jednotek jako místní virtuální počítač. | Nakonfigurujte zásady sítě SAN jako online vše. | 1. Přihlaste se k virtuálnímu počítači pomocí účtu správce a otevřete příkazové okno.<br/> 2. Zadáním příkazu **DiskPart** spustíte nástroj Diskpart.<br/> 3. Zadejte **zásady sítě San = OnlineAll**<br/> 4. Zadejte exit pro opuštění programu DiskPart a zavřete příkazový řádek.
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

1. V Azure Migrate projektu > **servery** **Azure Migrate: Migrace**serveru klikněte na **replikovat**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-vmware/select-replicate.png)

2. V případě **replikace**> **Nastavení** > zdroje**jsou vaše počítače virtualizované?** vyberte **Ano s VMware vSphere**.
3. V části místní **zařízení**vyberte název zařízení Azure Migrate, které jste nastavili > **OK**. 

    ![Nastavení zdroje](./media/tutorial-migrate-vmware/source-settings.png)

    - V tomto kroku se předpokládá, že jste už po dokončení kurzu nastavili zařízení.
    - Pokud jste zařízení nevytvořili, postupujte podle pokynů v [tomto článku](how-to-set-up-appliance-vmware.md).

4. V části **virtuální počítače**vyberte počítače, které chcete replikovat.
    - Pokud jste spustili posouzení pro virtuální počítače, můžete použít doporučení pro velikost virtuálního počítače a typ disku (Premium/Standard) z výsledků hodnocení. Pokud to chcete provést, vyberte v části **Import nastavení migrace z Azure Migrate posouzení?** možnost **Ano** .
    - Pokud jste nespustili posouzení nebo nechcete použít nastavení posouzení, vyberte možnost **žádné** možnosti.
    - Pokud jste vybrali použití posouzení, vyberte skupinu virtuálních počítačů a název posouzení.

    ![Vyberte posouzení.](./media/tutorial-migrate-vmware/select-assessment.png)

5. V rámci **virtuálních počítačů**podle potřeby vyhledejte virtuální počítače a ověřte všechny virtuální počítače, které chcete migrovat. Pak klikněte **na další: Nastavení**cíle

    ![Vybrat virtuální počítače](./media/tutorial-migrate-vmware/select-vms.png)

6. V **Nastavení cíl**vyberte předplatné a cílovou oblast, do které migrujete, a určete skupinu prostředků, ve které se virtuální počítače Azure po migraci budou nacházet. V **Virtual Network**vyberte virtuální síť nebo podsíť Azure, ke které se po migraci připojí virtuální počítače Azure.
7. V **zvýhodněné hybridní využití Azure**:

    - Vyberte možnost **ne** , pokud nechcete použít zvýhodněné hybridní využití Azure. Pak klikněte na tlačítko **Další**.
    - Vyberte **Ano** , pokud máte počítače s Windows serverem, které jsou zahrnuté v aktivním programu Software Assurance nebo předplatných Windows serveru, a chcete tuto výhodu využít pro počítače, které migrujete. Pak klikněte na tlačítko **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-vmware/target-settings.png)

8. V části **COMPUTE**Zkontrolujte název virtuálního počítače, velikost, typ disku s operačním systémem a skupinu dostupnosti. Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro vyhodnocení, bude rozevírací seznam velikost virtuálního počítače obsahovat doporučenou velikost. V opačném případě Azure Migrate vybere velikost na základě nejbližší shody v rámci předplatného Azure. Případně můžete vybrat ruční velikost ve **velikosti virtuálního počítače Azure**. 
    - **Disk s operačním systémem**: Zadejte operační systém (spouštěcí) disk pro virtuální počítač. Disk s operačním systémem je disk, který má zaváděcí program pro spouštění a instalaci operačního systému. 
    - **Skupina dostupnosti**: Pokud má být virtuální počítač v sadě dostupnosti Azure po migraci, zadejte sadu. Sada musí být v cílové skupině prostředků, kterou zadáte pro migraci.

    ![Nastavení výpočetního virtuálního počítače](./media/tutorial-migrate-vmware/compute-settings.png)

9. V části **disky**určete, jestli se mají disky virtuálních počítačů replikovat do Azure, a v Azure vyberte typ disku (standardní disky SSD/HDD nebo Premium). Pak klikněte na tlačítko **Další**.
    - Z replikace můžete vyloučit disky.
    - Pokud disky vyloučíte, nebudou po migraci na virtuálním počítači Azure. 

    ![Disky](./media/tutorial-migrate-vmware/disks.png)

10. V části **zkontrolovat a spustit replikaci**zkontrolujte nastavení a kliknutím na **replika** spusťte počáteční replikaci pro servery.

> [!NOTE]
> Nastavení replikace můžete aktualizovat kdykoli před spuštěním replikace, v nástroji **Spravovat** > **replikační počítače**. Po spuštění replikace nelze změnit nastavení.

### <a name="provisioning-for-the-first-time"></a>Zřizování pro první čas

Pokud se jedná o první virtuální počítač, který se replikuje v projektu Azure Migrate, Azure Migrate migrace serveru automaticky zřídí tyto prostředky ve stejné skupině prostředků jako projekt.

- **Služba Service Bus**: Migrace Azure Migrate serveru používá Service Bus k posílání zpráv orchestrace replikace do zařízení.
- **Účet úložiště brány**: Migrace serveru používá účet úložiště brány k ukládání informací o stavu virtuálních počítačů, které se replikují.
- **Účet úložiště protokolu**: Zařízení Azure Migrate nahrává protokoly replikace pro virtuální počítače do účtu úložiště protokolu. Azure Migrate použije informace o replikaci na spravované disky repliky.
- **Trezor klíčů**: Zařízení Azure Migrate používá Trezor klíčů ke správě připojovacích řetězců pro Service Bus a přístup k klíčům pro účty úložiště používané v replikaci. Měli byste nastavit oprávnění, která Trezor klíčů potřebuje k přístupu k účtu úložiště, když jste připravili. [Zkontrolujte tato oprávnění](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Sledování a sledování


- Po kliknutí na **replikace** se spustí úloha spustit replikaci. 
- Po úspěšném dokončení úlohy spuštění replikace začnou počítače počáteční replikaci do Azure.
- Během počáteční replikace se vytvoří snímek virtuálního počítače. Data na disku ze snímku se replikují na spravované disky repliky v Azure.
- Po dokončení počáteční replikace se spustí rozdílová replikace. Přírůstkové změny na místních discích se pravidelně replikují na disky replik v Azure.

Stav úlohy můžete sledovat v oznámeních na portálu.

Kliknutím na **replikace serverů** v **Azure Migrate můžete monitorovat stav replikace: Migrace**serveru.
![Monitorování replikace](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Spuštění testu migrace


Po zahájení rozdílové replikace můžete spustit testovací migraci pro virtuální počítače před spuštěním úplné migrace do Azure. Důrazně doporučujeme, abyste to pro každý počítač provedli aspoň jednou, a teprve potom ho migrujete.

- Při spuštění testu migrace proběhne kontrola, že migrace bude fungovat podle očekávání, aniž by to ovlivnilo místní počítače, které zůstávají v provozu, a pokračuje v replikaci. 
- Testovací migrace simuluje migraci tím, že vytvoří virtuální počítač Azure pomocí replikovaných dat (obvykle se migruje na virtuální síť, která není v produkčním prostředí, ve vašem předplatném Azure).
- Replikovaný virtuální počítač Azure můžete použít k ověření migrace, provádění testování aplikace a řešení všech problémů před úplnou migrací.

Proveďte migraci testu následujícím způsobem:


1. V Azure Migrate **cíle** > migrace na**servery** > : **Migrace**serveru klikněte na **test migrovaných serverů**.

     ![Servery, pro které proběhla testovací migrace](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Klikněte pravým tlačítkem na virtuální počítač, který chcete otestovat, a klikněte na **test migrovat**.

    ![Otestovat migraci](./media/tutorial-migrate-vmware/test-migrate.png)

3. V části **test migrace**vyberte virtuální síť Azure, ve které bude virtuální počítač Azure umístěný po migraci. Doporučujeme použít virtuální síť, která není v produkčním prostředí.
4. Spustí se úloha **testování migrace** . Sledujte úlohu v oznámeních na portálu.
5. Po dokončení migrace si prohlédněte migrovaný virtuální počítač Azure v **Virtual Machines** Azure Portal. Název počítače má příponu **-test**.
6. Po dokončení testu klikněte pravým tlačítkem myši na virtuální počítač Azure v části **replikace**a klikněte na **vyčistit test migrace**.

    ![Vyčištění migrace](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrace virtuálních počítačů

Po ověření, že migrace testu funguje podle očekávání, můžete migrovat místní počítače.

1. V Azure Migrate >ch **serverech** > **projektu Azure Migrate: Migrace**serveru klikněte na **replikace serverů**.

    ![Replikují se servery.](./media/tutorial-migrate-vmware/replicate-servers.png)

2. V části **replikační počítače**klikněte pravým tlačítkem na virtuální počítač > **migrovat**.
3. V nástroji **migrovat** > vypínání**virtuálních počítačů a provádění plánované migrace bez ztráty dat**vyberte **Ano** > . ****
    - Ve výchozím nastavení Azure Migrate vypne místní virtuální počítač a spustí replikaci na vyžádání a provede synchronizaci všech změn virtuálních počítačů, ke kterým došlo od poslední replikace. To zajistí, že nedojde ke ztrátě dat.
    - Pokud nechcete virtuální počítač vypnout, vyberte **ne** .
4. Spustí se úloha migrace pro virtuální počítač. Sledujte úlohu v oznámeních Azure.
5. Po dokončení úlohy můžete virtuální počítač zobrazit a spravovat na stránce **Virtual Machines** .

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


## <a name="next-steps"></a>Další postup

Prozkoumejte [cestu k migraci](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) do cloudu v rozhraní Azure cloudu pro přijetí.
