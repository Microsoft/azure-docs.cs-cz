---
title: Migrace hypervirtuálních virtuálních počítačů do Azure pomocí migrace serveru Azure
description: Zjistěte, jak migrovat místní virtuální počítače Hyper-V do Azure pomocí migrace serveru Azure
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 6b9732aab9e3fe0d26b4c572efe87c3a9d3e29f6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535345"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrace virtuálních počítačů Hyper-V do Azure 

Tento článek ukazuje, jak migrovat místní virtuální počítače Hyper-V do Azure pomocí migrace bez agenta pomocí nástroje Migrace: Migrace serveru Azure.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální rozbočovač pro sledování zjišťování, hodnocení a migrace místních aplikací a úloh a virtuálních počítačích privátního/veřejného cloudu do Azure. Centrum poskytuje nástroje pro migraci Azure pro hodnocení a migraci, stejně jako nabídky nezávislého dodavatele softwaru (ISV) třetích stran.

Tento kurz je třetí v řadě, která ukazuje, jak posoudit a migrovat Hyper-V do Azure pomocí Azure Migrate Server Assessment a migrace serveru. V tomto kurzu se naučíte:


> [!div class="checklist"]
> * Příprava Azure a místního prostředí Hyper-V
> * Nastavte zdrojové prostředí.
> * Nastavte cílové prostředí.
> * Povolte replikaci.
> * Spusťte testovací migraci a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci do Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete.


## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Projděte si](hyper-v-migration-architecture.md) architekturu migrace Hyper-V.
2. [Recenze](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Požadavky na hostitele Hyper-V a adresy URL Azure, ke kterým potřebují hostitelé Technologie Hyper-V přístup.
3. [Zkontrolujte](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) požadavky na virtuální aplikace Hyper-V, které chcete migrovat. Virtuální počítače Hyper-V musí splňovat [požadavky virtuálních zařízení Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).
2. Doporučujeme dokončit předchozí kurzy v této sérii. [První kurz](tutorial-prepare-hyper-v.md) ukazuje, jak nastavit Azure a Hyper-V pro migraci. Druhý kurz ukazuje, jak [posoudit hyper-V virtuální počítače](tutorial-assess-hyper-v.md před migrací pomocí Azure Migrate:Server Assessment. 
    > [!NOTE]
    > I když doporučujeme vyzkoušet hodnocení, není třeba spustit hodnocení před migrací virtuálních mích.
    > Pro migraci virtuálních počítačů Hyper-V spouští migrace:serveru migrace softwaru (Microsoft Azure Site Recovery provider a Microsoft Azure Recovery Service) na hostitelích hyperv nebo uzlech clusteru, aby bylo možné řídit a replikovat data do Migrace Azure. [Zařízení Migrace Azure](migrate-appliance.md) se nepoužívá pro migraci Hyper-V.

3. Ujistěte se, že je vašemu účtu Azure přiřazena role Přispěvatel virtuálního počítače, abyste měli oprávnění k:

    - Vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Vytvoření virtuálního počítače ve vybrané virtuální síti
    - Zapisovat na spravovaný disk Azure.
4. [Nastavte síť Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Když migrujete do Azure, vytvořené virtuální počítače Azure se připojí k síti Azure, kterou zadáte při nastavování migrace.

## <a name="add-the-azure-migrateserver-migration-tool"></a>Přidání nástroje Migrace:migrace serveru

Přidejte nástroj Migrace:Migrace serveru Azure.

- Pokud jste postupovali podle druhého kurzu k [posouzení virtuálních počítačích VMware](/tutorial-assess-hyper-v.md), už jste nastavili projekt Migrace Azure a můžete pokračovat a přidat nástroj nyní.
- Pokud jste nepostupovali podle druhého kurzu[, postupujte podle těchto pokynů](how-to-add-tool-first-time.md) k nastavení projektu Migrace Azure. Nástroj Migrace:migrace serveru Azure při vytváření projektu přidáte.

Pokud máte nastavený projekt, přidejte nástroj takto:

1. V projektu Migrace Azure klikněte na **Přehled**. 
2. V **polezit, posoudit a migrace serverů**klepněte na tlačítko Posoudit a **migrovat servery**.
3. V **nástrojích migrace**vyberte **Klepnutím sem přidejte nástroj pro migraci, až budete připraveni k migraci**.

    ![Výběr nástroje](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. V seznamu nástrojů vyberte **Nástroj pro migraci: Přidat migraci** > **serveru.**

    ![Nástroj pro migraci serverů](./media/tutorial-migrate-hyper-v/server-migration-tool.png)

## <a name="prepare-hyper-v-hosts"></a>Příprava hostitelů Hyper-V


1. V projektu Azure Migrate > **servery**klikněte v **části Migrace: Migrace serveru**na **zjistit**.
2. V **discover strojích** > **Jsou vaše počítače virtualizované?**, vyberte **Ano, s Technologiemi Hyper-V**.
3. V **oblasti Cíl**vyberte oblast Azure, do které chcete počítače migrovat.
6. Vyberte **Možnost Potvrdit, že cílová oblast pro migraci je název oblasti**.
7. Klepněte na **tlačítko Vytvořit zdroje**. Tím se vytvoří trezor azure site recovery na pozadí.
    - Pokud jste už nastavili migraci pomocí migrace serveru Azure, tato možnost se nezobrazí, protože prostředky byly nastaveny dříve.
    - Cílovou oblast pro tento projekt nelze změnit po klepnutí na toto tlačítko.
    - Všechny následné migrace jsou do této oblasti.
    
8. V **části Příprava hostitelských serverů Technologie Hyper-V**stáhněte zprostředkovatele služby Hyper-V Replication a soubor registračního klíče.
    - Registrační klíč je potřeba zaregistrovat hostitele Hyper-V s migrací serveru Azure.
    - Klíč je platný pět dní od jeho vygenerování.

    ![Stáhnout zprostředkovatele a klíč](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Zkopírujte instalační soubor zprostředkovatele a soubor registračního klíče do každého hostitele Hyper-V (nebo uzlu clusteru) se spuštěným virtuálními počítači, které chcete replikovat.
5. Spusťte instalační soubor zprostředkovatele na každém hostiteli, jak je popsáno v dalším postupu.
6. Po instalaci zprostředkovatele na hostitelích klikněte v **počítačích Discover**na **dokončit registraci**.

    ![Dokončení registrace](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Může trvat až 15 minut po dokončení registrace, dokud se zjištěné virtuální počítače nezobrazí v migraci serveru Azure. Jak jsou zjištěny virtuální chodů, počet **zjištěných serverů** stoupá.

![Zjištěné servery](./media/tutorial-migrate-hyper-v/discovered-servers.png)


## <a name="replicate-hyper-v-vms"></a>Replikovat virtuální aplikace Hyper-V

Po dokončení zjišťování můžete zahájit replikaci virtuálních počítačů Hyper-V do Azure.

> [!NOTE]
> Můžete replikovat až 10 počítačů dohromady. Pokud potřebujete replikovat více, replikujte je současně v dávkách po 10.

1. V projektu Azure Migrate > **servery** **, Azure Migrate: Migrace serveru**, klikněte na **Replikovat**.
2. V **části Replikovat**> **nastavení** > zdroje**Jsou vaše počítače virtualizovány?**, vyberte možnost **Ano, pomocí technologie Hyper-V**. Pak klikněte na **Další: Virtuální počítače**.
3. V části **Virtuální počítače** vyberte počítače, které chcete replikovat.
    - Pokud jste pro virtuální počítače spustili posouzení, můžete použít doporučenou velikost a typ disku (Premium nebo Standard) z výsledků posouzení. Pokud to chcete provést, v části **Importovat nastavení migrace z posouzení Azure Migrate?** vyberte možnost **Ano**.
    - Pokud jste neprovedli posouzení nebo pokud nechcete použít nastavení posouzení, vyberte možnost **Ne**.
    - Pokud jste se rozhodli použít posouzení, vyberte skupinu virtuálních počítačů a název posouzení.

        ![Výběr posouzení](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. V části **Virtuální počítače** vyhledejte požadované virtuální počítače a zkontrolujte všechny virtuální počítače, které chcete migrovat. Potom klepněte na tlačítko **Další: Nastavení cíle**.

    ![Výběr virtuálních disponecí](./media/tutorial-migrate-hyper-v/select-vms.png)

5. V **nastavení cíle**vyberte cílovou oblast, do které budete migrovat, předplatné a skupinu prostředků, ve které se virtuální počítače Azure budou po migraci navíjet.
7. V **účtu úložiště replikace**vyberte účet úložiště Azure, ve kterém se budou replikovaná data ukládat v Azure.
8. **Virtuální síť**, vyberte síť Azure Virtuální síť/podsíť, ke kterému se po migraci připojí virtuální počítače Azure.
9. V části **Zvýhodněné hybridní využití Azure**:

    - Vyberte **Ne**, pokud nechcete využít Zvýhodněné hybridní využití Azure. Potom klepněte na tlačítko **Další**.
    - Vyberte **Ano**, pokud máte počítače s Windows Serverem s aktivním Software Assurance nebo předplatným Windows Serveru a u migrovaných počítačů chcete využít tuto výhodu. Pak klikněte na **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-hyper-v/target-settings.png)

10. V části **Výpočetní prostředky** zkontrolujte název, velikost, typ disku s operačním systémem a skupinu dostupnosti virtuálního počítače. Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro vyhodnocení, rozbalovací seznam velikosti virtuálního počítače bude obsahovat doporučenou velikost. Jinak Azure Migrate vybere velikost na základě nejbližší shody v předplatném Azure. Případně můžete velikost vybrat ručně v části **Velikost virtuálního počítače Azure**. 
    - **Disk operačního systému**: Zadejte disk operačního systému (boot) pro virtuální počítače. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč a instalační program operačního systému. 
    - **Dostupnost set**: Pokud virtuální počítač by měl být v sadě dostupnosti Azure po migraci, zadejte sadu. Skupina musí být v cílové skupině prostředků, kterou pro migraci zadáte.

    ![Výpočetní nastavení virtuálního počítače](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. V části **Disky** zadejte, jestli se mají disky virtuálních počítačů replikovat do Azure, a vyberte typ disků (disky SSD nebo HDD úrovně Standard nebo spravované disky úrovně Premium) v Azure. Pak klikněte na **Další**.
    - Disky můžete z replikace vyloučit.
    - Pokud disky vyloučíte, po migraci nebudou na virtuálním počítači Azure. 

    ![Disky](./media/tutorial-migrate-hyper-v/disks.png)

10. V části **Kontrola a zahájení replikace** zkontrolujte nastavení a kliknutím na **Replikovat** spusťte počáteční replikaci serverů.

> [!NOTE]
> Nastavení replikace můžete aktualizovat kdykoli před zahájením replikace v části **Správa** > **replikačních počítačů**. Po spuštění replikace není možné nastavení změnit.

## <a name="provisioning-for-the-first-time"></a>Zřizování poprvé

Pokud se jedná o první virtuální počítač, který replikujete v projektu Migrace Azure, migrace Azure: Migrace serveru automaticky zřídí tyto prostředky ve stejné skupině prostředků jako projekt.

- **Service Bus**: Migrace Azure: Migrace serveru používá Service Bus k odesílání zpráv orchestrace replikace do zařízení.
- **Účet úložiště brány**: Migrace Azure: Migrace serveru používá účet úložiště brány k ukládání informací o stavu replikovaných virtuálních počítačích.
- **Účet úložiště protokolu**: Zařízení Migrace Azure nahraje protokoly replikace pro virtuální počítače do účtu úložiště protokolu. Azure Migrate použije informace o replikaci na disky spravované replikami.
- **Trezor klíčů**: Zařízení Migrace Azure používá trezor klíčů ke správě připojovacích řetězců pro sběrnici a přístupové klíče pro účty úložiště používané při replikaci. Měli byste mít nastavena oprávnění, která trezor klíčů potřebuje pro přístup k účtu úložiště při [přípravě](tutorial-prepare-hyper-v.md#prepare-azure) azure pro hodnocení virtuálních počítačů Hyper-V a migrace. 


## <a name="track-and-monitor"></a>Sledování a sledování


- Po klepnutí na tlačítko **Replikovat** úlohu Zahájit replikaci se spustí. 
- Po úspěšném dokončení úlohy Spustit replikaci začnou počítače zahájit počáteční replikaci do Azure.
- Po dokončení počáteční replikace začíná rozdílová replikace. Přírůstkové změny na místních discích se pravidelně replikují do Azure.

Stav úlohy můžete sledovat v oznámeních portálu.

Stav replikace můžete sledovat kliknutím na **Replikace serverů** v **Azure Migrate: Server Migration**.
![Monitorování replikace](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Spuštění testu migrace


Když začíná rozdílová replikace, můžete spustit testovací migraci pro virtuální počítače, před spuštěním úplné migrace do Azure. Důrazně doporučujeme provést alespoň jednou pro každý počítač, před migrací.

- Spuštění testovací migrace kontroluje, že migrace bude fungovat podle očekávání, aniž by to mělo vliv na místní počítače, které zůstávají funkční a pokračují v replikaci. 
- Migrace testů simuluje migraci vytvořením virtuálního počítače Azure pomocí replikovaných dat (obvykle migrace na neprodukční virtuální síť Azure v předplatném Azure).
- Replikovaný testovací virtuální počítač Azure můžete použít k ověření migrace, provedení testování aplikací a řešení všech problémů před úplnou migrací.

Proveďte testovací migraci následujícím způsobem:


1. V **oblasti cílů** > migrace**Servery, které** > **Azure migruje: Migrace serveru**, klikněte na testovat **migrované servery**.

     ![Test migrovaných serverů](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Klikněte pravým tlačítkem na virtuální počítač, který chcete otestovat, a klikněte na **Testovací migrace**.

    ![Testovací migrace](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. V **části Test Migration**vyberte virtuální síť Azure, ve které bude virtuální počítač Azure po migraci umístěn. Doporučujeme použít neprodukční virtuální síť.
4. Spustí se úloha **Testovací migrace**. Tuto úlohu můžete monitorovat pomocí oznámení portálu.
5. Po dokončení migrace si můžete migrovaný virtuální počítač Azure prohlédnout na webu Azure Portal v části **Virtuální počítače**. Název počítače má příponu **-Test**.
6. Po dokončení testu v části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač Azure a klikněte na **Vyčistit testovací migraci**.

    ![Vyčištění migrace](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrace virtuálních počítačů

Po ověření, že migrace testu funguje podle očekávání, můžete migrovat místní počítače.

1. V projektu Azure Migrate > **servery, které** > **Azure migruje: Migrace serveru**klikněte na **Replikace serverů**.

    ![Replikace serverů](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. V části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač a vyberte **Migrovat**.
3. V **migrovat** > **Vypnout virtuální počítače a provést plánovanou migraci bez ztráty dat**vyberte **Ano** > **OK**.
    - Azure Migrate ve výchozím nastavení vypne místní virtuální počítač a spustí replikaci na vyžádání, při které se synchronizují všechny změny virtuálního počítače, ke kterým došlo od poslední replikace. Tím se zajistí, že nedojde ke ztrátě dat.
    - Pokud virtuální počítač nechcete vypnout, vyberte **Ne**.
4. Pro virtuální počítač se spustí úloha migrace. Tuto úlohu můžete sledovat pomocí oznámení Azure.
5. Po dokončení úlohy můžete virtuální počítač zobrazit a spravovat na stránce **Virtuální počítače**.

## <a name="complete-the-migration"></a>Dokončení migrace

1. Po dokončení migrace klikněte pravým tlačítkem myši na **> zastavit migraci**virtuálního . Tím postupujte takto:
    - Zastaví replikaci pro místní počítač.
    - Odebere počítač z počtu **replikačních serverů** v Azure Migrate: Migrace serveru.
    - Vyčistí informace o stavu replikace pro virtuální hosti.
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
