---
title: Migrace migrace virtuálních počítačů VMware bez agentů migrace serveru Azure
description: Zjistěte, jak spustit migraci virtuálních počítačů VMware bez agenta pomocí migrace Azure.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 825d6ff16a1f51fa476541ee10fea5f8a1c2972e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78304204"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrace virtuálních počítačů VMware do Azure (bez agentů)

Tento článek ukazuje, jak migrovat místní virtuální počítače VMware do Azure pomocí migrace bez agenta pomocí nástroje migrace serveru Azure.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální rozbočovač pro sledování zjišťování, hodnocení a migrace místních aplikací a úloh a instancí virtuálních zařízení AWS/GCP do Azure. Centrum poskytuje nástroje pro migraci Azure pro hodnocení a migraci, stejně jako nabídky nezávislého dodavatele softwaru (ISV) třetích stran.

Tento kurz je třetí v řadě, která ukazuje, jak posoudit a migrovat virtuální počítače VMware do Azure pomocí Azure Migrate Server Assessment and Migration. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte virtuální počítačna migraci.
> * Přidejte nástroj migrace serveru Azure.
> * Objevte virtuální chod, které chcete migrovat.
> * Začněte replikovat virtuální počítače.
> * Spusťte testovací migraci a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci virtuálních můek.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete.

## <a name="migration-methods"></a>Metody migrace

Virtuální počítače VMware můžete migrovat do Azure pomocí nástroje Migrace serveru Azure. Tento nástroj nabízí několik možností pro migraci virtuálních zařízení VMware:

- Migrace pomocí replikace bez agenta. Migrujte virtuální počítače, aniž byste na ně museli nic instalovat.
- Migrace s agentem pro replikaci. Nainstalujte agenta na virtuální počítač pro replikaci.

Pokud se chcete rozhodnout, jestli chcete použít migraci bez agenta nebo na základě agenta, přečtěte si tyto články:

- [Zjistěte, jak](server-migrate-overview.md) migrace bez agenta funguje, a [porovnejte metody migrace](server-migrate-overview.md#compare-migration-methods).
- [Přečtěte si tento článek,](tutorial-migrate-vmware-agent.md) pokud chcete použít metodu založenou na agentovi.

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Dokončete první kurz](tutorial-prepare-vmware.md) v této sérii a nastavte Azure a VMware pro migraci. Konkrétně v tomto tutoriálu je třeba:
    - [Připravte Azure](tutorial-prepare-vmware.md#prepare-azure) na migraci.
    - [Připravte místní prostředí](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) pro migraci.
    
2. Doporučujeme, abyste se pokusili posoudit virtuální počítače VMware pomocí Azure Migrate Server Assessment před migrací do Azure. Chcete-li nastavit hodnocení, [dokončete druhý kurz](tutorial-assess-vmware.md) v této sérii. Pokud nechcete hodnotit virtuální chod, můžete tento kurz přeskočit. I když doporučujeme vyzkoušet hodnocení, ale není třeba spustit hodnocení před pokusem o migraci.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Přidání nástroje migrace serveru Azure

Pokud jste nepostupovali podle druhého kurzu k posouzení virtuálních počítačích VMware, musíte [podle těchto pokynů](how-to-add-tool-first-time.md) nastavit projekt Migrace Azure a vyberte nástroj migrace serveru Azure. 

Pokud jste postupovali podle druhého kurzu a už máte nastavenprojekt Migrace Azure, přidejte nástroj migrace serveru Azure takto:

1. V projektu Migrace Azure klikněte na **Přehled**. 
2. V **polezit, posoudit a migrace serverů**klepněte na tlačítko Posoudit a **migrovat servery**.

     ![Posouzení a migrace serverů](./media/tutorial-migrate-vmware/assess-migrate.png)

3. V **nástrojích migrace**vyberte **Klepnutím sem přidejte nástroj pro migraci, až budete připraveni k migraci**.

    ![Výběr nástroje](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. V seznamu nástrojů vyberte **Nástroj pro migraci: Přidat migraci** > **serveru.**

    ![Nástroj pro migraci serverů](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate

Migrace serveru Azure migrace běží zjednodušené zařízení Virtuální počítač VMware. Zařízení provádí zjišťování virtuálních počítačích a odesílá metadata virtuálního počítače a data o výkonu migraci serveru Azure. Stejné zařízení používá také nástroj Azure Migrate Server Assessment.

Pokud jste postupovali podle druhého kurzu k posouzení virtuálních měn VMware, již jste nastavili zařízení během tohoto kurzu. Pokud jste nepostupovali podle tohoto kurzu, musíte nyní nastavit zařízení. Chcete-li to provést, můžete: 

- Stáhněte soubor šablony OVA a importujte jej do serveru vCenter.
- Vytvořte zařízení a zkontrolujte, zda se může připojit k Azure Migrate Server Assessment. 
- Nakonfigurujte zařízení poprvé a zaregistrujte ho pomocí projektu Azure Migrate.

Přístroj nastavíte podle pokynů v [tomto článku.](how-to-set-up-appliance-vmware.md)


## <a name="prepare-vms-for-migration"></a>Příprava virtuálních počítačů na migraci

Azure Migrate vyžaduje některé změny virtuálních počítačích, aby bylo zajištěno, že virtuální počítače se můžou migrovat do Azure.

- U některých operačních systémů Azure Migrate provede tyto změny automaticky. [Další informace](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)
- Pokud migrujete virtuální počítač, který nemá jeden z těchto operačních systémů, postupujte podle pokynů k přípravě virtuálního počítače.
- Před zahájením migrace je důležité provést tyto změny. Pokud migrujete virtuální počítač před změnou, nemusí se virtuální počítač spustit v Azure.
- Změny konfigurace, které provedete na místních virtuálních počítačích, se replikují do Azure po povolení replikace pro virtuální počítač. Chcete-li zajistit replikaci změn, ujistěte se, že bod obnovení, do kterého migrujete, je pozdější než čas, kdy byly změny konfigurace provedeny v místním prostředí.


### <a name="prepare-windows-server-vms"></a>Příprava virtuálních počítačů se systémem Windows Server

**Akce** | **Podrobnosti** | **Pokyny**
--- | --- | ---
Ujistěte se, že svazky Windows ve virtuálním počítači Azure používají stejná přiřazení písmen e-jednotky jako místní virtuální počítač. | Nakonfigurujte zásady sítě SAN jako vše online. | 1. Přihlaste se k virtuálnímu virtuálnímu účtu pomocí účtu správce a otevřete příkazové okno.<br/> 2. Chcete-li spustit nástroj Diskpart, zadejte **diskpart.**<br/> 3. Zadejte **SAN POLICY=OnlineAll**<br/> 4. Chcete-li opustit program Diskpart, zadejte příkaz Exit a zavřete příkazový řádek.
Povolení konzoly pro sériový přístup Azure pro virtuální počítač Azure | To pomáhá při řešení potíží. Není nutné restartovat virtuální počítač. Virtuální počítač Azure se spustí pomocí bitové kopie disku a to je ekvivalentní restartování pro nový virtuální počítač. | Postupujte [podle těchto pokynů](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) pro povolení.
Instalace integrace hostů Hyper-V | Pokud migrujete počítače se systémem Windows Server 2003, nainstalujte služby integrace hosta Hyper-V do operačního systému virtuálního počítače. | [Další informace](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Vzdálená plocha | Povolte vzdálenou plochu na virtuálním počítači a zkontrolujte, zda brána Windows Firewall neblokuje přístup ke vzdálené ploše na všech síťových profilech. | [Další informace](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Příprava virtuálních počítačů s Linuxem

**Akce** | **Podrobnosti** 
--- | --- | ---
Instalace integračních služeb Hyper-V Linuxu | Většina nových verzí linuxových distribucí to má ve výchozím nastavení.
Znovu sestavte bitovou kopii linuxového initu tak, aby obsahovala potřebné ovladače Hyper-V | Tím zajistíte, že virtuální počítač se spustí v Azure a je vyžadována jenom v některých distribucích.
Povolení protokolování sériové konzoly Azure | To pomáhá při řešení potíží. Není nutné restartovat virtuální počítač. Virtuální počítač Azure se spustí pomocí bitové kopie disku a to je ekvivalentní restartování pro nový virtuální počítač.<br/> Postupujte [podle těchto pokynů](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) pro povolení.
Aktualizovat soubor mapy zařízení | Aktualizace souboru mapy zařízení, který má název zařízení, na přidružení svazků, chcete-li použít trvalé identifikátory zařízení
Aktualizovat položky fstab | Aktualizujte položky tak, aby používaly trvalé identifikátory svazků.
Odebrat pravidlo udev | Odstraňte všechna pravidla udev, která si rezervují názvy rozhraní na základě adresy mac atd.
Aktualizace síťových rozhraní | Aktualizujte síťová rozhraní tak, aby přijímali adresu IP na základě služby DHCP.
Povolit ssh | Ujistěte se, že je ssh povolen a služba sshd je nastavena na automatické spuštění při restartu.<br/> Ujistěte se, že příchozí požadavky na připojení ssh nejsou blokovány bránou firewall operačního systému nebo skriptovatelnými pravidly.

[Postupujte podle tohoto článku,](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) který popisuje tyto kroky pro spuštění virtuálního počítače s Linuxem v Azure a zahrnout pokyny pro některé populární distribuce Linuxu.  


## <a name="replicate-vms"></a>Replikace virtuálních počítačů

Po dokončení zjišťování můžete zahájit replikaci virtuálních počítačů VMware do Azure. 

> [!NOTE]
> Můžete replikovat až 10 počítačů dohromady. Pokud potřebujete replikovat více, replikujte je současně v dávkách po 10. Pro migraci bez agenta můžete spustit až 100 souběžných replikací.

1. V projektu Azure Migrate > **servery** **, Azure Migrate: Migrace serveru**, klikněte na **Replikovat**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-vmware/select-replicate.png)

2. V části **Replikovat** > **Nastavení zdroje** > **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere**.
3. V části **Místní zařízení** vyberte název zařízení Azure Migrate, které jste nastavili, a klikněte na **OK**. 

    ![Nastavení zdroje](./media/tutorial-migrate-vmware/source-settings.png)

    - Tento krok předpokládá, že jste již nastavili zařízení po dokončení kurzu.
    - Pokud jste zařízení nenastavili, postupujte podle pokynů v [tomto článku](how-to-set-up-appliance-vmware.md).

4. V části **Virtuální počítače** vyberte počítače, které chcete replikovat.
    - Pokud jste pro virtuální počítače spustili posouzení, můžete použít doporučenou velikost a typ disku (Premium nebo Standard) z výsledků posouzení. Pokud to chcete provést, v části **Importovat nastavení migrace z posouzení Azure Migrate?** vyberte možnost **Ano**.
    - Pokud jste neprovedli posouzení nebo pokud nechcete použít nastavení posouzení, vyberte možnost **Ne**.
    - Pokud jste se rozhodli použít posouzení, vyberte skupinu virtuálních počítačů a název posouzení.

    ![Výběr posouzení](./media/tutorial-migrate-vmware/select-assessment.png)

5. V části **Virtuální počítače** vyhledejte požadované virtuální počítače a zkontrolujte všechny virtuální počítače, které chcete migrovat. Pak klepněte na tlačítko **Další: Nastavení cíle**.

    ![Výběr virtuálních disponecí](./media/tutorial-migrate-vmware/select-vms.png)

6. V části **Nastavení cíle** vyberte předplatné a cílovou oblast migrace a zadejte skupinu prostředků, ve které se po migraci budou nacházet virtuální počítače Azure. V části **Virtuální síť** vyberte virtuální síť a podsíť Azure, ke kterým se po migraci připojí virtuální počítače Azure.
7. V části **Zvýhodněné hybridní využití Azure**:

    - Vyberte **Ne**, pokud nechcete využít Zvýhodněné hybridní využití Azure. Pak klikněte na **Další**.
    - Vyberte **Ano**, pokud máte počítače s Windows Serverem s aktivním Software Assurance nebo předplatným Windows Serveru a u migrovaných počítačů chcete využít tuto výhodu. Pak klikněte na **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-vmware/target-settings.png)

8. V části **Výpočetní prostředky** zkontrolujte název, velikost, typ disku s operačním systémem a skupinu dostupnosti virtuálního počítače. Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro vyhodnocení, rozbalovací seznam velikosti virtuálního počítače bude obsahovat doporučenou velikost. Jinak Azure Migrate vybere velikost na základě nejbližší shody v předplatném Azure. Případně můžete velikost vybrat ručně v části **Velikost virtuálního počítače Azure**. 
    - **Disk operačního systému**: Zadejte disk operačního systému (boot) pro virtuální počítače. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč a instalační program operačního systému. 
    - **Dostupnost set**: Pokud virtuální počítač by měl být v sadě dostupnosti Azure po migraci, zadejte sadu. Skupina musí být v cílové skupině prostředků, kterou pro migraci zadáte.

    ![Výpočetní nastavení virtuálního počítače](./media/tutorial-migrate-vmware/compute-settings.png)

9. V části **Disky** zadejte, jestli se mají disky virtuálních počítačů replikovat do Azure, a vyberte typ disků (disky SSD nebo HDD úrovně Standard nebo spravované disky úrovně Premium) v Azure. Pak klikněte na **Další**.
    - Disky můžete z replikace vyloučit.
    - Pokud disky vyloučíte, po migraci nebudou na virtuálním počítači Azure. 

    ![Disky](./media/tutorial-migrate-vmware/disks.png)

10. V části **Kontrola a zahájení replikace** zkontrolujte nastavení a kliknutím na **Replikovat** spusťte počáteční replikaci serverů.

> [!NOTE]
> Nastavení replikace můžete aktualizovat kdykoli před zahájením replikace v části **Správa** > **replikačních počítačů**. Po spuštění replikace není možné nastavení změnit.

### <a name="provisioning-for-the-first-time"></a>Zřizování poprvé

Pokud se jedná o první virtuální počítač, který replikujete v projektu Migrace Azure, migrace serveru Azure migrace automaticky zřídí tyto prostředky ve stejné skupině prostředků jako projekt.

- **Service bus**: Migrace serveru Azure používá sběrnici k odesílání zpráv orchestrace replikace do zařízení.
- **Účet úložiště brány**: Migrace serveru používá účet úložiště brány k ukládání informací o stavu replikovaných virtuálních počítačích.
- **Účet úložiště protokolu**: Zařízení Migrace Azure nahraje protokoly replikace pro virtuální počítače do účtu úložiště protokolu. Azure Migrate použije informace o replikaci na spravované disky repliky.
- **Trezor klíčů**: Zařízení Migrace Azure používá trezor klíčů ke správě připojovacích řetězců pro sběrnici a přístupové klíče pro účty úložiště používané při replikaci. Měli byste nastavit oprávnění, která trezor klíčů potřebuje pro přístup k účtu úložiště při přípravě. [Zkontrolujte tato oprávnění](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault).   


## <a name="track-and-monitor"></a>Sledování a sledování


- Po klepnutí na tlačítko **Replikovat** úlohu Zahájit replikaci se spustí. 
- Po úspěšném dokončení úlohy Spustit replikaci začnou počítače zahájit počáteční replikaci do Azure.
- Během počáteční replikace se vytvoří snímek virtuálního počítače. Data disku ze snímku se replikují na spravované disky repliky v Azure.
- Po dokončení počáteční replikace začíná rozdílová replikace. Přírůstkové změny na místních discích se pravidelně replikují na repliky disků v Azure.

Stav úlohy můžete sledovat v oznámeních portálu.

Stav replikace můžete sledovat kliknutím na **Replikace serverů** v **Azure Migrate: Server Migration**.
![Monitorování replikace](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Spuštění testu migrace


Když začíná rozdílová replikace, můžete spustit testovací migraci pro virtuální počítače, před spuštěním úplné migrace do Azure. Důrazně doporučujeme provést alespoň jednou pro každý počítač, před migrací.

- Spuštění testovací migrace kontroluje, že migrace bude fungovat podle očekávání, aniž by to mělo vliv na místní počítače, které zůstávají funkční a pokračují v replikaci. 
- Migrace testu simuluje migraci vytvořením virtuálního počítače Azure pomocí replikovaných dat (obvykle migrace na neprodukční virtuální síť v předplatném Azure).
- Replikovaný testovací virtuální počítač Azure můžete použít k ověření migrace, provedení testování aplikací a řešení všech problémů před úplnou migrací.

Proveďte testovací migraci následujícím způsobem:


1. V **oblasti cílů** > migrace**Servery, které** > **Azure migruje: Migrace serveru**, klikněte na testovat **migrované servery**.

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

1. V projektu Azure Migrate > **servery, které** > **Azure migruje: Migrace serveru**klikněte na **Replikace serverů**.

    ![Replikace serverů](./media/tutorial-migrate-vmware/replicate-servers.png)

2. V části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač a vyberte **Migrovat**.
3. V **migrovat** > **Vypnout virtuální počítače a provést plánovanou migraci bez ztráty dat**vyberte **Ano** > **OK**.
    - Azure Migrate ve výchozím nastavení vypne místní virtuální počítač a spustí replikaci na vyžádání, při které se synchronizují všechny změny virtuálního počítače, ke kterým došlo od poslední replikace. Tím se zajistí, že nedojde ke ztrátě dat.
    - Pokud virtuální počítač nechcete vypnout, vyberte **Ne**.
4. Pro virtuální počítač se spustí úloha migrace. Tuto úlohu můžete sledovat pomocí oznámení Azure.
5. Po dokončení úlohy můžete virtuální počítač zobrazit a spravovat na stránce **Virtuální počítače**.

## <a name="complete-the-migration"></a>Dokončení migrace

1. Po dokončení migrace klikněte pravým tlačítkem myši na virtuální mon > **zastavit replikaci**. Tím se zastaví replikace pro místní počítač a vyčistí informace o stavu replikace pro virtuální počítač.
2. Nainstalujte agenta Azure VM [Pro Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) do migrovaných počítačů.
3. Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery.
4. U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
5. Přeškrtněte provoz na migrovna v instanci virtuálního počítače Azure.
6. Odeberte místní virtuální počítače z místního inventáře virtuálních počítačů.
7. Odeberte místní virtuální počítače ze záloh.
8. Aktualizujte veškerou interní dokumentaci tak, aby obsahovala nová umístění a IP adresy virtuálních počítačů Azure. 

## <a name="post-migration-best-practices"></a>Osvědčené postupy po migraci

- Pro zvýšení odolnosti:
    - Zálohujte virtuální počítače Azure pomocí služby Azure Backup, abyste měli data zabezpečená. [Další informace](../backup/quick-backup-vm-portal.md).
    - Replikujte virtuální počítače Azure do sekundární oblasti pomocí služby Site Recovery, aby úlohy mohly neustále běžet a byly dostupné. [Další informace](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Pro zvýšení zabezpečení:
    - Uzamkněte a omezte přístup k příchozímu provozu pomocí [Azure Security Center – správa právě v čase](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Omezte síťový provoz na koncové body správy pomocí [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Nasaďte službu [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), která vám pomůže zabezpečit disky a zajistit bezpečnost dat před krádeží a neoprávněným přístupem.
    - Přečtěte si další informace o [zabezpečení prostředků IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) a navštivte [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Pro monitorování a správu:
-  Zvažte nasazení služby [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), která bude monitorovat využití prostředků a útratu.


## <a name="next-steps"></a>Další kroky

Prozkoumejte [cestu migrace do cloudu](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) v rámci azure cloudpřijetí.
