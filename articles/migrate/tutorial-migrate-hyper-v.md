---
title: Migrace místních virtuálních počítačů Hyper-V do Azure s Azure Migrate migrací serveru | Microsoft Docs
description: Tento článek popisuje, jak migrovat místní virtuální počítače Hyper-V do Azure pomocí migrace Azure Migrate serveru.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 755bb6d019418cf9dae22ebf7ee6a3c94af3c750
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309451"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrace virtuálních počítačů s Hyper-V do Azure 

V tomto článku se dozvíte, jak migrovat místní virtuální počítače Hyper-V do Azure pomocí migrace bez agentů pomocí nástroje pro migraci Azure Migrate serveru.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a virtuálních a veřejných cloudových virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Tento kurz je třetí součástí série, která ukazuje, jak vyhodnotit a migrovat Hyper-V do Azure s využitím posouzení Azure Migrate serveru a migrace. V tomto kurzu se naučíte:


> [!div class="checklist"]
> * Příprava Azure a místního prostředí technologie Hyper-V
> * Nastavte zdrojové prostředí a nasaďte zařízení replikace.
> * Nastavte cílovou environmen...
> * Povolte replikaci.
> * Spusťte test migrace a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci do Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Zkontrolujte](migrate-architecture.md) architekturu migrace technologie Hyper-V.
2. [Dokončením prvního kurzu](tutorial-prepare-hyper-v.md) v této sérii nastavte Azure a Hyper-V pro migraci. V prvním kurzu:
    - [Připravte Azure](tutorial-prepare-hyper-v.md#prepare-azure) na migraci.
    - [Připravte místní prostředí](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) pro migraci.
3. Před migrací do Azure doporučujeme vyzkoušet vyhodnocování virtuálních počítačů Hyper-V pomocí Azure Migrate posouzení serveru. Provedete to tak, že v této řadě [dokončíte druhý kurz](tutorial-assess-hyper-v.md) . I když doporučujeme, abyste si vyzkoušeli posouzení, nemusíte před migrací virtuálních počítačů spustit posouzení.
4. Ujistěte se, že váš účet Azure má přiřazenou roli Přispěvatel virtuálních počítačů, takže máte oprávnění k těmto akcím:

    - Vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Vytvoření virtuálního počítače ve vybrané virtuální síti
    - Zapište na spravovaný disk Azure.   
5. [Nastavte síť Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Při migraci do Azure se vytvořené virtuální počítače Azure připojí k síti Azure, kterou určíte při nastavování migrace.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Přidání nástroje pro migraci Azure Migrate serveru

Pokud jste nepoužili druhý kurz pro vyhodnocení virtuálních počítačů Hyper-V, je potřeba při nastavení Azure Migrate projektu použít následující [pokyny](how-to-add-tool-first-time.md) a přidat do projektu nástroj pro migraci Azure Migrate serveru.

Pokud jste postupovali podle druhého kurzu a již máte nastavení Azure Migrate projektu, přidejte Nástroj pro migraci Azure Migrate serveru následujícím způsobem:

1. V projektu Azure Migrate klikněte na **Přehled**. 
2. V nabídce **zjišťování, posouzení a migrace serverů**klikněte na možnost **zhodnotit a migrovat servery**.
3. V okně **Nástroje pro migraci**vyberte **kliknutím sem přidáte Nástroj pro migraci, který jste připraveni migrovat**.

    ![Výběr nástroje](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. V seznamu Nástroje vyberte **Azure Migrate: Nástroj pro** **Přidání** migrace serveru > 

    ![Nástroj pro migraci serveru](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate

Migrace serveru Azure Migrate spouští jednoduché zařízení virtuálního počítače Hyper-V.

- Zařízení provádí zjišťování virtuálních počítačů a odesílá data o výkonu a data virtuálních počítačů do Azure Migrate migrace serveru.
- Stejné zařízení používá také nástroj pro vyhodnocení Azure Migrate serveru.

Nastavení zařízení:
- Pokud jste postupovali podle druhého kurzu a vyhodnotili virtuální počítače Hyper-V, zařízení jste už v tomto kurzu nastavili.
- Pokud jste tento kurz neudělali, musíte zařízení nastavit nyní. Uděláte to takto: 

    - Z Azure Portal Stáhněte komprimovaný VHD Hyper-V.
    - Vytvořte zařízení a ověřte, že se může připojit k Azure Migrate posouzení serveru. 
    - Nakonfigurujete zařízení poprvé a zaregistrujete ho do projektu Azure Migrate.

    Pokud chcete zařízení nastavit, postupujte podle pokynů v [tomto článku](how-to-set-up-appliance-hyper-v.md) .

## <a name="prepare-hyper-v-hosts"></a>Příprava hostitelů technologie Hyper-V

1. V Azure Migrate Project > **servery**v **Azure Migrate: Migrace**serveru klikněte na **Vyhledat**.
2. V rozevíracích **seznamech počítačů** > , ve**kterých jsou počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**.
3. V části **cílová oblast**vyberte oblast Azure, do které chcete migrovat počítače.
6. Vyberte **potvrdit, že cílová oblast pro migraci je název regionu**.
7. Klikněte na **vytvořit prostředky**. Tím dojde k vytvoření trezoru Azure Site Recovery na pozadí.
    - Pokud jste už nastavili migraci pomocí migrace serveru Azure Migrate, tato možnost se nezobrazí, protože se předtím nastavily prostředky.
    - Po kliknutí na toto tlačítko nemůžete změnit cílovou oblast tohoto projektu.
    - Všechny následné migrace jsou v této oblasti.
    
8. V části **Příprava hostitelských serverů Hyper-v**Stáhněte zprostředkovatele replikace technologie Hyper-v a soubor registračního klíče.
    - Registrační klíč je nutný k registraci hostitele Hyper-V s migrací serveru Azure Migrate.
    - Klíč je platný pět dní od jeho vygenerování.

    ![Stažení poskytovatele a klíče](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Zkopírujte instalační soubor zprostředkovatele a soubor registračního klíče na každého hostitele Hyper-V (nebo uzlu clusteru) se spuštěnými virtuálními počítači, které chcete replikovat.
5. Spusťte instalační soubor zprostředkovatele na každém hostiteli, jak je popsáno v následujícím postupu.
6. Po instalaci poskytovatele na hostitele v části **zjistit počítače**klikněte na **Dokončit registraci**.

    ![Dokončit registraci](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Po dokončení registrace může trvat až 15 minut, než se zjištěné virtuální počítače objeví v Azure Migrate migraci serveru. Po zjištění virtuálních počítačů se počet **zjištěných serverů** zvyšuje.

![Zjištěné servery](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Registrovat hostitele Hyper-V

Nainstalujte stažený instalační soubor (AzureSiteRecoveryProvider. exe) na všechny relevantní hostitele Hyper-V.

1. Spusťte instalační soubor zprostředkovatele na každém hostiteli nebo uzlu clusteru.
2. V Průvodci instalací poskytovatele > **Microsoft Update**Přihlaste se ke kontrole aktualizací poskytovatele pomocí Microsoft Update.
3. V části **instalace**přijměte výchozí umístění instalace pro poskytovatele a agenta a vyberte **nainstalovat**.
4. Po instalaci v Průvodci registrací > **Nastavení trezoru**vyberte **Procházet**a v **souboru klíče**vyberte soubor klíče trezoru, který jste stáhli.
5. V části **nastavení proxy**určete, jak se zprostředkovatel, který běží na hostiteli, připojí k Internetu.
    - Pokud je zařízení umístěné za proxy server, je nutné zadat nastavení proxy serveru.
    - Zadejte název proxy jako **http://ip-address** nebo. **http://FQDN** Proxy servery HTTPS nejsou podporované.
   

6. Ujistěte se, že zprostředkovatel má přístup k [požadovaným adresám URL](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access).
7. V části **registrace**po registraci hostitele klikněte na **Dokončit**.

## <a name="replicate-hyper-v-vms"></a>Replikace virtuálních počítačů Hyper-V

Po dokončení zjišťování můžete zahájit replikaci virtuálních počítačů Hyper-V do Azure.

> [!NOTE]
> Můžete replikovat až 10 počítačů dohromady. Pokud potřebujete replikovat více, proveďte jejich replikaci současně v dávkách po 10.

1. V projektu Azure Migrate v části **Servery** > **Azure Migrate: Migrace serverů** klikněte na **Replikovat**.
2. V případě **replikace**> **Nastavení** > zdroje**jsou vaše počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**. Pak klikněte na **Další: Virtuální počítače**.
3. V části **Virtuální počítače** vyberte počítače, které chcete replikovat.
    - Pokud jste pro virtuální počítače spustili posouzení, můžete použít doporučenou velikost a typ disku (Premium nebo Standard) z výsledků posouzení. Pokud to chcete provést, v části **Importovat nastavení migrace z posouzení Azure Migrate?** vyberte možnost **Ano**.
    - Pokud jste neprovedli posouzení nebo pokud nechcete použít nastavení posouzení, vyberte možnost **Ne**.
    - Pokud jste se rozhodli použít posouzení, vyberte skupinu virtuálních počítačů a název posouzení.

        ![Výběr posouzení](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. V části **Virtuální počítače** vyhledejte požadované virtuální počítače a zkontrolujte všechny virtuální počítače, které chcete migrovat. Pak klikněte na **další: Nastavení cíle**.

    ![Vybrat virtuální počítače](./media/tutorial-migrate-hyper-v/select-vms.png)

5. V části **Nastavení cíle**vyberte cílovou oblast, do které budete migrovat, předplatné a skupinu prostředků, ve kterých se virtuální počítače Azure po migraci budou nacházet.
7. V části **účet úložiště replikace**vyberte účet úložiště Azure, ve kterém budou replikovaná data uložená v Azure.
8. **Virtual Network**vyberte virtuální síť nebo podsíť Azure, ke které se po migraci připojí virtuální počítače Azure.
9. V části **Zvýhodněné hybridní využití Azure**:

    - Vyberte **Ne**, pokud nechcete využít Zvýhodněné hybridní využití Azure. Pak klikněte na **Další**.
    - Vyberte **Ano**, pokud máte počítače s Windows Serverem s aktivním Software Assurance nebo předplatným Windows Serveru a u migrovaných počítačů chcete využít tuto výhodu. Pak klikněte na tlačítko **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-hyper-v/target-settings.png)

10. V části **Výpočetní prostředky** zkontrolujte název, velikost, typ disku s operačním systémem a skupinu dostupnosti virtuálního počítače. Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Velikost virtuálního počítače:** Pokud používáte doporučení posouzení, v rozevíracím seznamu velikostí virtuálního počítače se zobrazí doporučená velikost. Jinak Azure Migrate vybere velikost na základě nejbližší shody v předplatném Azure. Případně můžete velikost vybrat ručně v části **Velikost virtuálního počítače Azure**. 
    - **Disk s operačním systémem:** Zadejte pro virtuální počítač disk s operačním systémem (spouštěcí disk). Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč a instalační program operačního systému. 
    - **Skupina dostupnosti:** Pokud má být virtuální počítač po migraci ve skupině dostupnosti Azure, zadejte příslušnou skupinu. Skupina musí být v cílové skupině prostředků, kterou pro migraci zadáte.

    ![Nastavení výpočetního virtuálního počítače](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. V části **Disky** zadejte, jestli se mají disky virtuálních počítačů replikovat do Azure, a vyberte typ disků (disky SSD nebo HDD úrovně Standard nebo spravované disky úrovně Premium) v Azure. Pak klikněte na tlačítko **Další**.
    - Disky můžete z replikace vyloučit.
    - Pokud disky vyloučíte, po migraci nebudou na virtuálním počítači Azure. 

    ![Disky](./media/tutorial-migrate-hyper-v/disks.png)

10. V části **Kontrola a zahájení replikace** zkontrolujte nastavení a kliknutím na **Replikovat** spusťte počáteční replikaci serverů.

> [!NOTE]
> Před zahájením replikace můžete nastavení replikace kdykoli aktualizovat v části **Správa** > **Replikace počítačů**. Po spuštění replikace není možné nastavení změnit.

### <a name="provisioning-for-the-first-time"></a>Zřizování pro první čas

Pokud se jedná o první virtuální počítač, který se replikuje v projektu Azure Migrate, Azure Migrate migrace serveru automaticky zřídí tyto prostředky ve stejné skupině prostředků jako projekt.

- **Služba Service Bus**: Migrace Azure Migrate serveru používá Service Bus k posílání zpráv orchestrace replikace do zařízení.
- **Účet úložiště brány**: Migrace serveru používá účet úložiště brány k ukládání informací o stavu virtuálních počítačů, které se replikují.
- **Účet úložiště protokolu**: Zařízení Azure Migrate nahrává protokoly replikace pro virtuální počítače do účtu úložiště protokolu. Azure Migrate použije informace o replikaci na spravované disky repliky.
- **Trezor klíčů**: Zařízení Azure Migrate používá Trezor klíčů ke správě připojovacích řetězců pro Service Bus a přístup k klíčům pro účty úložiště používané v replikaci. Měli byste nastavit oprávnění, která Trezor klíčů potřebuje k přístupu k účtu úložiště, když jste připravili. [Zkontrolujte tato oprávnění](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Sledování a sledování


- Po kliknutí na **replikace** se spustí úloha spustit replikaci. 
- Po úspěšném dokončení úlohy spuštění replikace začnou počítače počáteční replikaci do Azure.
- Po dokončení počáteční replikace se spustí rozdílová replikace. Přírůstkové změny místních disků se pravidelně replikují do Azure.

Stav úlohy můžete sledovat v oznámeních na portálu.

Kliknutím na **replikace serverů** v **Azure Migrate můžete monitorovat stav replikace: Migrace**serveru.
![Monitorování replikace](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Spuštění testu migrace


Po zahájení rozdílové replikace můžete spustit testovací migraci pro virtuální počítače před spuštěním úplné migrace do Azure. Důrazně doporučujeme, abyste to pro každý počítač provedli aspoň jednou, a teprve potom ho migrujete.

- Při spuštění testu migrace proběhne kontrola, že migrace bude fungovat podle očekávání, aniž by to ovlivnilo místní počítače, které zůstávají v provozu, a pokračuje v replikaci. 
- Testovací migrace simuluje migraci tím, že vytvoří virtuální počítač Azure pomocí replikovaných dat (obvykle se migruje na virtuální síť, která není v produkčním prostředí, ve vašem předplatném Azure).
- Replikovaný virtuální počítač Azure můžete použít k ověření migrace, provádění testování aplikace a řešení všech problémů před úplnou migrací.

Proveďte migraci testu následujícím způsobem:


1. V části **Cíle migrace** > **Servery** > **Azure Migrate: Migrace serverů** klikněte na **Otestovat migrované servery**.

     ![Test migrovaných serverů](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Klikněte pravým tlačítkem na virtuální počítač, který chcete otestovat, a klikněte na **Testovací migrace**.

    ![Testovací migrace](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. V části **Testovací migrace** vyberte virtuální síť Azure, ve které se po migraci bude nacházet virtuální počítač Azure. Doporučujeme použít jinou než produkční virtuální síť.
4. Spustí se úloha **Testovací migrace**. Tuto úlohu můžete monitorovat pomocí oznámení portálu.
5. Po dokončení migrace si můžete migrovaný virtuální počítač Azure prohlédnout na webu Azure Portal v části **Virtuální počítače**. Název počítače má příponu **-Test**.
6. Po dokončení testu v části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač Azure a klikněte na **Vyčistit testovací migraci**.

    ![Vyčištění migrace](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrace virtuálních počítačů

Po ověření, že migrace testu funguje podle očekávání, můžete migrovat místní počítače.

1. V projektu Azure Migrate v části **Servery** > **Azure Migrate: Migrace serverů** klikněte na **Replikace serverů**.

    ![Replikace serverů](./media/tutorial-migrate-hyper-v/replicate-servers.png)

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


## <a name="next-steps"></a>Další postup

Prozkoumejte [cestu k migraci do cloudu](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) v rozhraní Azure cloudu pro přijetí.
