---
title: Migrace virtuálních počítačů Hyper-V do Azure pomocí migrace serveru Azure Migrate
description: Přečtěte si, jak migrovat místní virtuální počítače Hyper-V do Azure pomocí migrace Azure Migrate serveru.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 3b50c11f43d29de354f04e1a4296818c5bd8cbab
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773520"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrace virtuálních počítačů Hyper-V do Azure 

V tomto článku se dozvíte, jak migrovat místní virtuální počítače Hyper-V do Azure pomocí migrace bez agenta s nástrojem Azure Migrate: Server pro migraci.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a virtuálních a veřejných cloudových virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Tento kurz je třetí část série, která ukazuje, jak vyhodnotit a migrovat Hyper-V do Azure pomocí Azure Migrate serveru pro vyhodnocení a migraci serveru. V tomto kurzu:


> [!div class="checklist"]
> * Příprava Azure a místního prostředí technologie Hyper-V
> * Nastavte zdrojové prostředí.
> * Nastavte cílové prostředí.
> * Povolte replikaci.
> * Spusťte test migrace a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci do Azure.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/pricing/free-trial/), ještě než začnete.


## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Zkontrolujte](hyper-v-migration-architecture.md) architekturu migrace technologie Hyper-V.
2. [Kontrola](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Požadavky na hostitele Hyper-V a adresy URL Azure, ke kterým mají hostitelé Hyper-V přístup.
3. [Zkontrolujte](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) požadavky na virtuální počítače Hyper-V, které chcete migrovat. Virtuální počítače Hyper-V musí splňovat [požadavky na virtuální počítače Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).
2. Doporučujeme, abyste v této sérii dokončili předchozí kurzy. V [prvním kurzu](tutorial-prepare-hyper-v.md) se dozvíte, jak nastavit Azure a Hyper-V pro migraci. Druhý kurz ukazuje, jak [vyhodnotit virtuální počítače Hyper-V](tutorial-assess-hyper-v.md) před migrací pomocí Azure Migrate: posouzení serveru. 
    > [!NOTE]
    > I když doporučujeme, abyste si vyzkoušeli posouzení, nemusíte před migrací virtuálních počítačů spustit posouzení.
    > Pro migraci virtuálních počítačů Hyper-V Azure Migrate: Migrace serveru spouští agenty softwaru (Microsoft Azure poskytovatele Site Recovery a agenta Microsoft Azure Recovery Service) na hostitelích Hyper-V nebo na uzlech clusteru, aby bylo možné orchestrovat a replikovat data do Azure Migrate. [Zařízení Azure Migrate](migrate-appliance.md) se nepoužívá pro migraci technologie Hyper-V.

3. Ujistěte se, že váš účet Azure má přiřazenou roli Přispěvatel virtuálních počítačů, takže máte oprávnění k těmto akcím:

    - Vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Vytvoření virtuálního počítače ve vybrané virtuální síti
    - Zapište na spravovaný disk Azure.
4. [Nastavte síť Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Při migraci do Azure se vytvořené virtuální počítače Azure připojí k síti Azure, kterou určíte při nastavování migrace.

## <a name="add-the-azure-migrateserver-migration-tool"></a>Přidejte Azure Migrate: Nástroj pro migraci serveru

Přidejte nástroj Azure Migrate: Server pro migraci.

- Pokud jste postupovali podle druhého kurzu, abyste [vyhodnotili virtuální počítače Hyper-V](tutorial-assess-hyper-v.md), už jste nastavili Azure Migrate projekt a mohli byste tento nástroj teď přidat.
- Pokud jste nesledovali druhý kurz[, postupujte podle těchto pokynů](how-to-add-tool-first-time.md) a nastavte projekt Azure Migrate. Při vytváření projektu přidáte Azure Migrate: Nástroj pro migraci serveru.

Pokud máte projekt nastavený, přidejte nástroj následujícím způsobem:

1. V projektu Azure Migrate klikněte na **Přehled**. 
2. V nabídce **zjišťování, posouzení a migrace serverů**klikněte na možnost **zhodnotit a migrovat servery**.
3. V okně **Nástroje pro migraci**vyberte **kliknutím sem přidáte Nástroj pro migraci, který jste připraveni migrovat**.

    ![Vybrat nástroj](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. V seznamu Nástroje vyberte možnost **Azure Migrate:**  >  **Nástroj pro přidání** migrace serveru

    ![Nástroj pro migraci serverů](./media/tutorial-migrate-hyper-v/server-migration-tool.png)

## <a name="prepare-hyper-v-hosts"></a>Příprava hostitelů technologie Hyper-V


1. V Azure Migrate Project > **servery**v části **Azure Migrate: Migrace serveru**klikněte na **Vyhledat**.
2. V rozevíracích **seznamech počítačů**  >  , ve**kterých jsou počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**.
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


## <a name="replicate-hyper-v-vms"></a>Replikace virtuálních počítačů Hyper-V

Po dokončení zjišťování můžete zahájit replikaci virtuálních počítačů Hyper-V do Azure.

> [!NOTE]
> Můžete replikovat až 10 počítačů dohromady. Pokud potřebujete replikovat více, proveďte jejich replikaci současně v dávkách po 10.

1. V Azure Migrate Project > **servery** **Azure Migrate: Migrace serveru**klikněte na **replikovat**.
2. V případě **replikace**> **Nastavení zdroje**  >  **jsou vaše počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**. Pak klikněte na **Další: virtuální počítače**.
3. V části **Virtuální počítače** vyberte počítače, které chcete replikovat.
    - Pokud jste pro virtuální počítače spustili posouzení, můžete použít doporučenou velikost a typ disku (Premium nebo Standard) z výsledků posouzení. Pokud to chcete provést, v části **Importovat nastavení migrace z posouzení Azure Migrate?** vyberte možnost **Ano**.
    - Pokud jste neprovedli posouzení nebo pokud nechcete použít nastavení posouzení, vyberte možnost **Ne**.
    - Pokud jste se rozhodli použít posouzení, vyberte skupinu virtuálních počítačů a název posouzení.

        ![Výběr posouzení](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. V části **Virtuální počítače** vyhledejte požadované virtuální počítače a zkontrolujte všechny virtuální počítače, které chcete migrovat. Pak klikněte na **Další: nastavení cíle**.

    ![Vybrat virtuální počítače](./media/tutorial-migrate-hyper-v/select-vms.png)

5. V části **Nastavení cíle**vyberte cílovou oblast, do které budete migrovat, předplatné a skupinu prostředků, ve kterých se virtuální počítače Azure po migraci budou nacházet.
7. V části **účet úložiště replikace**vyberte účet Azure Storage, ve kterém budou replikovaná data uložená v Azure.
8. **Virtual Network**vyberte virtuální síť nebo podsíť Azure, ke které se po migraci připojí virtuální počítače Azure.
9. V části **Zvýhodněné hybridní využití Azure**:

    - Vyberte **Ne**, pokud nechcete využít Zvýhodněné hybridní využití Azure. Pak klikněte na tlačítko **Další**.
    - Vyberte **Ano**, pokud máte počítače s Windows Serverem s aktivním Software Assurance nebo předplatným Windows Serveru a u migrovaných počítačů chcete využít tuto výhodu. Pak klikněte na **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-hyper-v/target-settings.png)

10. V části **Výpočetní prostředky** zkontrolujte název, velikost, typ disku s operačním systémem a skupinu dostupnosti virtuálního počítače. Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro vyhodnocení, bude rozevírací seznam velikost virtuálního počítače obsahovat doporučenou velikost. Jinak Azure Migrate vybere velikost na základě nejbližší shody v předplatném Azure. Případně můžete velikost vybrat ručně v části **Velikost virtuálního počítače Azure**. 
    - **Disk s operačním systémem**: zadejte operační systém (spouštěcí) disk pro virtuální počítač. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč a instalační program operačního systému. 
    - **Skupina dostupnosti**: Pokud má být virtuální počítač v sadě dostupnosti Azure po migraci, zadejte sadu. Skupina musí být v cílové skupině prostředků, kterou pro migraci zadáte.

    ![Nastavení výpočetního virtuálního počítače](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. V části **Disky** zadejte, jestli se mají disky virtuálních počítačů replikovat do Azure, a vyberte typ disků (disky SSD nebo HDD úrovně Standard nebo spravované disky úrovně Premium) v Azure. Pak klikněte na **Další**.
    - Disky můžete z replikace vyloučit.
    - Pokud disky vyloučíte, po migraci nebudou na virtuálním počítači Azure. 

    ![Disky](./media/tutorial-migrate-hyper-v/disks.png)

10. V části **Kontrola a zahájení replikace** zkontrolujte nastavení a kliknutím na **Replikovat** spusťte počáteční replikaci serverů.

> [!NOTE]
> Nastavení replikace můžete aktualizovat kdykoli před spuštěním replikace, v nástroji **Spravovat**  >  **replikační počítače**. Po spuštění replikace není možné nastavení změnit.

## <a name="provisioning-for-the-first-time"></a>Zřizování pro první čas

Pokud se jedná o první virtuální počítač, který budete replikovat v projektu Azure Migrate, Azure Migrate: Migrace serveru automaticky zřídí tyto prostředky ve stejné skupině prostředků jako projekt.

- **Service Bus**: Azure Migrate: Migrace serveru používá Service Bus k posílání zpráv orchestrace replikace do zařízení.
- **Účet úložiště brány**: Azure Migrate: Migrace serveru používá účet úložiště brány k ukládání informací o stavu virtuálních počítačů, které se replikují.
- **Účet úložiště protokolu**: zařízení Azure Migrate nahrává protokoly replikace pro virtuální počítače do účtu úložiště protokolu. Azure Migrate použije informace o replikaci na disky spravované replikou.
- **Trezor klíčů**: zařízení Azure Migrate používá Trezor klíčů ke správě připojovacích řetězců pro Service Bus a přístup k klíčům pro účty úložiště používané v replikaci. Měli byste nastavit oprávnění, která musí Trezor klíčů mít přístup k účtu úložiště, když jste [připravili Azure](tutorial-prepare-hyper-v.md#prepare-azure) pro vyhodnocení a migraci virtuálních počítačů Hyper-V. 


## <a name="track-and-monitor"></a>Sledování a sledování


- Po kliknutí na **replikace** se spustí úloha spustit replikaci. 
- Po úspěšném dokončení úlohy spuštění replikace začnou počítače počáteční replikaci do Azure.
- Po dokončení počáteční replikace se spustí rozdílová replikace. Přírůstkové změny místních disků se pravidelně replikují do Azure.

Stav úlohy můžete sledovat v oznámeních na portálu.

Stav replikace můžete sledovat kliknutím na **servery replikace** v **Azure Migrate: Migrace serveru**.
![Monitorování replikace](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Spuštění testu migrace


Po zahájení rozdílové replikace můžete spustit testovací migraci pro virtuální počítače před spuštěním úplné migrace do Azure. Důrazně doporučujeme, abyste to pro každý počítač provedli aspoň jednou, a teprve potom ho migrujete.

- Při spuštění testu migrace proběhne kontrola, že migrace bude fungovat podle očekávání, aniž by to ovlivnilo místní počítače, které zůstávají v provozu, a pokračuje v replikaci. 
- Testovací migrace simuluje migraci tím, že vytvoří virtuální počítač Azure pomocí replikovaných dat (obvykle se migruje do neprodukční virtuální sítě Azure ve vašem předplatném Azure).
- Replikovaný virtuální počítač Azure můžete použít k ověření migrace, provádění testování aplikace a řešení všech problémů před úplnou migrací.

Proveďte migraci testu následujícím způsobem:


1. V Azure Migrate **cíle migrace**  >  na**servery**  >  **: Migrace serveru**klikněte na **test migrovaných serverů**.

     ![Test migrovaných serverů](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Klikněte pravým tlačítkem na virtuální počítač, který chcete otestovat, a klikněte na **Testovací migrace**.

    ![Testovací migrace](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. V části **test migrace**vyberte virtuální síť Azure, ve které bude virtuální počítač Azure umístěný po migraci. Doporučujeme použít virtuální síť, která není v provozu.
4. Spustí se úloha **Testovací migrace**. Tuto úlohu můžete monitorovat pomocí oznámení portálu.
5. Po dokončení migrace si můžete migrovaný virtuální počítač Azure prohlédnout na webu Azure Portal v části **Virtuální počítače**. Název počítače má příponu **-Test**.
6. Po dokončení testu v části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač Azure a klikněte na **Vyčistit testovací migraci**.

    ![Vyčištění migrace](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrace virtuálních počítačů

Po ověření, že migrace testu funguje podle očekávání, můžete migrovat místní počítače.

1. V Azure Migrate Project > **servery**  >  **Azure Migrate: Migrace serveru**klikněte na **replikace serverů**.

    ![Replikace serverů](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. V části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač a vyberte **Migrovat**.
3. V **Migrate**  >  **nástroji migrovat vypínání virtuálních počítačů a provádění plánované migrace bez ztráty dat**vyberte **Ano**  >  **OK**.
    - Azure Migrate ve výchozím nastavení vypne místní virtuální počítač a spustí replikaci na vyžádání, při které se synchronizují všechny změny virtuálního počítače, ke kterým došlo od poslední replikace. Tím se zajistí, že nedojde ke ztrátě dat.
    - Pokud virtuální počítač nechcete vypnout, vyberte **Ne**.
4. Pro virtuální počítač se spustí úloha migrace. Tuto úlohu můžete sledovat pomocí oznámení Azure.
5. Po dokončení úlohy můžete virtuální počítač zobrazit a spravovat na stránce **Virtuální počítače**.

## <a name="complete-the-migration"></a>Dokončete migraci

1. Po dokončení migrace klikněte pravým tlačítkem na virtuální počítač > **zastavit migraci**. Provede následující akce:
    - Zastaví replikaci místního počítače.
    - Odebere počítač z počtu **replikačních serverů** v Azure Migrate: Migrace serveru.
    - Vyčistí informace o stavu replikace pro virtuální počítač.
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
