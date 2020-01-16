---
title: Migrace virtuálních počítačů Hyper-V do Azure pomocí migrace serveru Azure Migrate
description: Přečtěte si, jak migrovat místní virtuální počítače Hyper-V do Azure pomocí migrace Azure Migrate serveru.
ms.topic: tutorial
ms.date: 11/18/2019
ms.custom: MVC
ms.openlocfilehash: b9c0de866a61ee2646d987c4fb98cb24a218417b
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028970"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrace virtuálních počítačů s Hyper-V do Azure 

V tomto článku se dozvíte, jak migrovat místní virtuální počítače Hyper-V do Azure pomocí migrace bez agenta s nástrojem Azure Migrate: Server pro migraci.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a virtuálních a veřejných cloudových virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

Tento kurz je třetí součástí série, která ukazuje, jak vyhodnotit a migrovat Hyper-V do Azure s využitím posouzení Azure Migrate serveru a migrace. V tomto kurzu se naučíte:


> [!div class="checklist"]
> * Příprava Azure a místního prostředí technologie Hyper-V
> * Nastavte zdrojové prostředí a nasaďte zařízení replikace.
> * Nastavte cílové prostředí.
> * Povolte replikaci.
> * Spusťte test migrace a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci do Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Zkontrolujte](hyper-v-migration-architecture.md) architekturu migrace technologie Hyper-V.
2. [Dokončením prvního kurzu](tutorial-prepare-hyper-v.md) v této sérii nastavte Azure a Hyper-V pro migraci. V prvním kurzu:
    - [Připravte Azure](tutorial-prepare-hyper-v.md#prepare-azure) na migraci.
    - [Připravte místní prostředí](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) pro migraci.
3. Doporučujeme, abyste si vyzkoušeli vyhodnocování virtuálních počítačů Hyper-V pomocí Azure Migrate: posouzení serveru, než je migrujete do Azure. Provedete to tak, že v této řadě [dokončíte druhý kurz](tutorial-assess-hyper-v.md) . I když doporučujeme, abyste si vyzkoušeli posouzení, nemusíte před migrací virtuálních počítačů spustit posouzení.
4. Ujistěte se, že váš účet Azure má přiřazenou roli Přispěvatel virtuálních počítačů, takže máte oprávnění k těmto akcím:

    - Vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Vytvoření virtuálního počítače ve vybrané virtuální síti
    - Zapište na spravovaný disk Azure.
5. [Nastavte síť Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Při migraci do Azure se vytvořené virtuální počítače Azure připojí k síti Azure, kterou určíte při nastavování migrace.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Přidání nástroje pro migraci Azure Migrate serveru

Pokud jste nepoužili druhý kurz pro vyhodnocení virtuálních počítačů Hyper-V, je potřeba při nastavení Azure Migrate projektu použít následující [pokyny](how-to-add-tool-first-time.md) a přidat do projektu nástroj pro migraci Azure Migrate serveru.

Pokud jste postupovali s druhým kurzem a již máte projekt Azure Migrate, přidejte Azure Migrate: Nástroj pro migraci serveru následujícím způsobem:

1. V projektu Azure Migrate klikněte na **Přehled**. 
2. V nabídce **zjišťování, posouzení a migrace serverů**klikněte na možnost **zhodnotit a migrovat servery**.
3. V okně **Nástroje pro migraci**vyberte **kliknutím sem přidáte Nástroj pro migraci, který jste připraveni migrovat**.

    ![Výběr nástroje](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. V seznamu Nástroje vyberte **Azure Migrate: Serverová migrace** > **Přidat nástroj** .

    ![Nástroj pro migraci serveru](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate

Migrace serveru Azure Migrate spouští jednoduché zařízení virtuálního počítače Hyper-V.

- Zařízení provádí zjišťování virtuálních počítačů a odesílá data o výkonu a data virtuálních počítačů do Azure Migrate migrace serveru.
- Pro migraci virtuálních počítačů Hyper-V do Azure používá zařízení taky Azure Migrate: Nástroj pro vyhodnocení serveru.

Nastavení zařízení:
- Pokud jste postupovali podle druhého kurzu pro vyhodnocení virtuálních počítačů Hyper-V, už jste v tomto kurzu nastavili zařízení a nemusíte to dělat znovu.
- Pokud jste tento kurz neudělali, musíte zařízení nastavit nyní. Uděláte to takto: 

    - Z Azure Portal Stáhněte komprimovaný VHD Hyper-V.
    - Vytvořte zařízení a ověřte, že se může připojit k Azure Migrate posouzení serveru. 
    - Nakonfigurujete zařízení poprvé a zaregistrujete ho do projektu Azure Migrate.

    Pokud chcete zařízení nastavit, postupujte podle podrobných pokynů v [tomto článku](how-to-set-up-appliance-hyper-v.md) .

## <a name="prepare-hyper-v-hosts"></a>Příprava hostitelů technologie Hyper-V

1. V Azure Migrate Project > **servery**v části **Azure Migrate: Migrace serveru**klikněte na **Vyhledat**.
2. V rozevíracích **seznamech počítače** > **jsou vaše počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**.
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
    - Zadejte název proxy serveru **http://ip-address** nebo **http://FQDN** . Proxy servery HTTPS nejsou podporované.
   

6. Ujistěte se, že zprostředkovatel má přístup k [požadovaným adresám URL](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts).
7. V části **registrace**po registraci hostitele klikněte na **Dokončit**.

## <a name="replicate-hyper-v-vms"></a>Replikace virtuálních počítačů Hyper-V

Po dokončení zjišťování můžete zahájit replikaci virtuálních počítačů Hyper-V do Azure.

> [!NOTE]
> Můžete replikovat až 10 počítačů dohromady. Pokud potřebujete replikovat více, proveďte jejich replikaci současně v dávkách po 10.

1. V Azure Migrate Project > **servery** **Azure Migrate: Migrace serveru**klikněte na **replikovat**.
2. V případě **replikace**> **Nastavení zdroje** > **jsou vaše počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**. Pak klikněte na **Další: virtuální počítače**.
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

    - Vyberte **Ne**, pokud nechcete využít Zvýhodněné hybridní využití Azure. Pak klikněte na **Další**.
    - Vyberte **Ano**, pokud máte počítače s Windows Serverem s aktivním Software Assurance nebo předplatným Windows Serveru a u migrovaných počítačů chcete využít tuto výhodu. Pak klikněte na tlačítko **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-hyper-v/target-settings.png)

10. V části **Výpočetní prostředky** zkontrolujte název, velikost, typ disku s operačním systémem a skupinu dostupnosti virtuálního počítače. Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro vyhodnocení, bude rozevírací seznam velikost virtuálního počítače obsahovat doporučenou velikost. Jinak Azure Migrate vybere velikost na základě nejbližší shody v předplatném Azure. Případně můžete velikost vybrat ručně v části **Velikost virtuálního počítače Azure**. 
    - **Disk s operačním systémem**: zadejte operační systém (spouštěcí) disk pro virtuální počítač. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč a instalační program operačního systému. 
    - **Skupina dostupnosti**: Pokud má být virtuální počítač v sadě dostupnosti Azure po migraci, zadejte sadu. Skupina musí být v cílové skupině prostředků, kterou pro migraci zadáte.

    ![Nastavení výpočetního virtuálního počítače](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. V části **Disky** zadejte, jestli se mají disky virtuálních počítačů replikovat do Azure, a vyberte typ disků (disky SSD nebo HDD úrovně Standard nebo spravované disky úrovně Premium) v Azure. Pak klikněte na tlačítko **Další**.
    - Disky můžete z replikace vyloučit.
    - Pokud disky vyloučíte, po migraci nebudou na virtuálním počítači Azure. 

    ![Disky](./media/tutorial-migrate-hyper-v/disks.png)

10. V části **Kontrola a zahájení replikace** zkontrolujte nastavení a kliknutím na **Replikovat** spusťte počáteční replikaci serverů.

> [!NOTE]
> Před zahájením replikace můžete nastavení replikace kdykoli aktualizovat v části **Správa** > **Replikace počítačů**. Po spuštění replikace není možné nastavení změnit.

## <a name="provisioning-for-the-first-time"></a>Zřizování pro první čas

Pokud se jedná o první virtuální počítač, který budete replikovat v projektu Azure Migrate, Azure Migrate: Migrace serveru automaticky zřídí tyto prostředky ve stejné skupině prostředků jako projekt.

- **Service Bus**: Azure Migrate: Migrace serveru používá Service Bus k posílání zpráv orchestrace replikace do zařízení.
- **Účet úložiště brány**: Azure Migrate: Migrace serveru používá účet úložiště brány k ukládání informací o stavu virtuálních počítačů, které se replikují.
- **Účet úložiště protokolu**: zařízení Azure Migrate nahrává protokoly replikace pro virtuální počítače do účtu úložiště protokolu. Azure Migrate použije informace o replikaci na disky spravované replikou.
- **Trezor klíčů**: zařízení Azure Migrate používá Trezor klíčů ke správě připojovacích řetězců pro Service Bus a přístup k klíčům pro účty úložiště používané v replikaci. Měli byste nastavit oprávnění, která Trezor klíčů potřebuje k přístupu k účtu úložiště, když jste připravili. [Připravte](tutorial-prepare-hyper-v.md#prepare-azure) se na Azure pro vyhodnocení a migraci virtuálních počítačů Hyper-V. 


## <a name="track-and-monitor"></a>Sledování a sledování


- Po kliknutí na **replikace** se spustí úloha spustit replikaci. 
- Po úspěšném dokončení úlohy spuštění replikace začnou počítače počáteční replikaci do Azure.
- Po dokončení počáteční replikace se spustí rozdílová replikace. Přírůstkové změny místních disků se pravidelně replikují do Azure.

Stav úlohy můžete sledovat v oznámeních na portálu.

Stav replikace můžete sledovat kliknutím na **servery replikace** v **Azure Migrate: Migrace serveru**.
![monitorování replikace](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Spuštění testu migrace


Po zahájení rozdílové replikace můžete spustit testovací migraci pro virtuální počítače před spuštěním úplné migrace do Azure. Důrazně doporučujeme, abyste to pro každý počítač provedli aspoň jednou, a teprve potom ho migrujete.

- Při spuštění testu migrace proběhne kontrola, že migrace bude fungovat podle očekávání, aniž by to ovlivnilo místní počítače, které zůstávají v provozu, a pokračuje v replikaci. 
- Testovací migrace simuluje migraci tím, že vytvoří virtuální počítač Azure pomocí replikovaných dat (obvykle se migruje do neprodukční virtuální sítě Azure ve vašem předplatném Azure).
- Replikovaný virtuální počítač Azure můžete použít k ověření migrace, provádění testování aplikace a řešení všech problémů před úplnou migrací.

Proveďte migraci testu následujícím způsobem:


1. V ** > ** **cíle migrace** > **Azure Migrate: Migrace serveru**klikněte na **test migrovaných serverů**.

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

1. V Azure Migrate Project > **servery** > **Azure Migrate: Migrace serveru**klikněte na **replikovat servery**.

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


## <a name="next-steps"></a>Další kroky

Prozkoumejte [cestu k migraci do cloudu](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) v rozhraní Azure cloudu pro přijetí.
