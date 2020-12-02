---
title: Migrace Azure Migrate migrace serveru bez agentů pro virtuální počítače VMware
description: Naučte se spouštět migraci virtuálních počítačů VMware bez agenta pomocí Azure Migrate.
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: mvc
ms.openlocfilehash: 202886bc0a7738f8211cfea6d90a380c7bac82b8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492943"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrace virtuálních počítačů VMware do Azure (bez agenta)

V tomto článku se dozvíte, jak migrovat místní virtuální počítače VMware do Azure pomocí nástroje [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) Tool s migrací bez agenta. Virtuální počítače VMware můžete také migrovat pomocí migrace založené na agentech. [Porovnejte](server-migrate-overview.md#compare-migration-methods) metody.

Tento kurz je třetí v řadě, který ukazuje, jak vyhodnotit a migrovat virtuální počítače VMware do Azure. 

> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. V těchto kurzech se v rámci možností používají jen výchozí možnosti a neuvádějí se všechny varianty nastavení ani všechny cesty. 


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidejte nástroj migrace do Azure: Server pro migraci.
> * Vyhledejte virtuální počítače, které chcete migrovat.
> * Zahajte replikaci virtuálních počítačů.
> * Spusťte test migrace a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci virtuálního počítače.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Dokončete první kurz](./tutorial-discover-vmware.md) pro přípravu Azure a VMware pro migraci.
2. Doporučujeme, abyste dokončili druhý kurz pro [vyhodnocení virtuálních počítačů VMware](./tutorial-assess-vmware-azure-vm.md) před jejich migrací do Azure, ale nemusíte je. 
3. Přejít do již vytvořeného projektu nebo [vytvořit nový projekt](./create-manage-projects.md)
4. Ověřte oprávnění pro účet Azure – váš účet Azure potřebuje oprávnění k vytvoření virtuálního počítače a zápis na spravovaný disk Azure.

## <a name="set-up-the-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate

Migrace Azure Migrate serveru spouští zjednodušené zařízení VMware VM, které se používá pro zjišťování, posuzování a migraci virtuálních počítačů VMware bez agenta. Pokud budete postupovat podle [kurzu hodnocení](./tutorial-assess-vmware-azure-vm.md), již jste nastavili zařízení. Pokud jste to neudělali, nastavte ji nyní pomocí jedné z těchto metod:

- **Šablona vajíček**: [nastavte](how-to-set-up-appliance-vmware.md) na virtuálním počítači VMware pomocí stažené šablony vajíček.
- **Skript**: [nastavte](deploy-appliance-script.md) na virtuálním počítači VMware nebo fyzickém počítači pomocí skriptu instalačního programu PowerShell. Tato metoda by se měla použít, pokud nemůžete nastavit virtuální počítač pomocí šablony pro VAJÍČKa nebo pokud jste v Azure Government.

Po vytvoření zařízení zkontrolujete, že se může připojit k Azure Migrate: posouzení serveru, nakonfigurovat ho poprvé a zaregistrujte ho v projektu Azure Migrate.

## <a name="replicate-vms"></a>Replikace virtuálních počítačů

Po nastavení zařízení a dokončení zjišťování můžete zahájit replikaci virtuálních počítačů VMware do Azure. 

- Můžete spustit až 300 replikace současně.
- Na portálu můžete pro migraci vybrat až 10 virtuálních počítačů najednou. Chcete-li migrovat více počítačů, přidejte je do skupin v dávkách po 10.

Replikaci povolte následujícím způsobem:

1. V Azure Migrate Project > **servery** **Azure Migrate: Migrace serveru** klikněte na **replikovat**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-vmware/select-replicate.png)

2. V části **Replikovat** > **Nastavení zdroje** > **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere**.
3. V části **Místní zařízení** vyberte název zařízení Azure Migrate, které jste nastavili, a klikněte na **OK**. 

    ![Nastavení zdroje](./media/tutorial-migrate-vmware/source-settings.png)

4. V části **Virtuální počítače** vyberte počítače, které chcete replikovat. Pokud chcete použít velikost virtuálního počítače a typ disku z posouzení, pokud jste ho spustili, vyberte v části **Import nastavení migrace z Azure Migrate posouzení?**, vyberte **Ano** a vyberte skupinu virtuálních počítačů a název posouzení. Pokud nepoužíváte nastavení posouzení, vyberte **ne**.
   
    ![Výběr posouzení](./media/tutorial-migrate-vmware/select-assessment.png)

5. Na **virtuální počítače** vyberte virtuální počítače, které chcete migrovat. Pak klikněte na **Další: nastavení cíle**.

    ![Vybrat virtuální počítače](./media/tutorial-migrate-vmware/select-vms.png)

6. V **Nastavení cíl** vyberte předplatné a cílovou oblast. Zadejte skupinu prostředků, ve které se virtuální počítače Azure nacházejí po migraci.
7. V **Virtual Network** vyberte virtuální síť nebo podsíť Azure, ke které se virtuální počítače Azure připojí po migraci.
8. V **Možnosti dostupnost** vyberte:
    -  Zóna dostupnosti pro připnutí migrovaného počítače ke konkrétní zóně dostupnosti v oblasti Tuto možnost použijte k distribuci serverů, které tvoří aplikační vrstvu s více uzly napříč Zóny dostupnosti. Pokud vyberete tuto možnost, budete muset zadat zónu dostupnosti, která se má použít pro každý z vybraných počítačů na kartě Compute. Tato možnost je dostupná jenom v případě, že cílová oblast vybraná pro migraci podporuje Zóny dostupnosti
    -  Skupina dostupnosti umístí migrovaný počítač do skupiny dostupnosti. Vybraná cílová skupina prostředků musí mít jednu nebo víc skupin dostupnosti, aby bylo možné tuto možnost použít.
    - Není nutná žádná možnost redundance infrastruktury, pokud nepotřebujete žádnou z těchto konfigurací dostupnosti pro migrované počítače.

9. V části **Zvýhodněné hybridní využití Azure**:

    - Vyberte **Ne**, pokud nechcete využít Zvýhodněné hybridní využití Azure. Potom klikněte na **Další**.
    - Vyberte **Ano**, pokud máte počítače s Windows Serverem s aktivním Software Assurance nebo předplatným Windows Serveru a u migrovaných počítačů chcete využít tuto výhodu. Potom klikněte na **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-vmware/target-settings.png)

10. V části **COMPUTE**(COMPUTE) Zkontrolujte název virtuálního počítače, velikost, typ disku s operačním systémem a konfiguraci dostupnosti (Pokud jste vybrali v předchozím kroku). Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro vyhodnocení, zobrazuje se v rozevíracím seznamu velikost virtuálního počítače doporučená velikost. Jinak Azure Migrate vybere velikost na základě nejbližší shody v předplatném Azure. Případně můžete velikost vybrat ručně v části **Velikost virtuálního počítače Azure**. 
    - **Disk s operačním systémem**: zadejte operační systém (spouštěcí) disk pro virtuální počítač. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč a instalační program operačního systému. 
    - **Zóna dostupnosti**: Zadejte zónu dostupnosti, která se má použít.
    - **Skupina dostupnosti**: Určete skupinu dostupnosti, která se má použít.

> [!NOTE]
>Pokud chcete pro sady virtuálních počítačů vybrat jinou možnost dostupnosti, pokračujte na krok 1 a po spuštění replikace pro jednu sadu virtuálních počítačů opakujte postup výběrem různých možností dostupnosti.


 ![Nastavení výpočetního virtuálního počítače](./media/tutorial-migrate-vmware/compute-settings.png)

11. V části **Disky** zadejte, jestli se mají disky virtuálních počítačů replikovat do Azure, a vyberte typ disků (disky SSD nebo HDD úrovně Standard nebo spravované disky úrovně Premium) v Azure. Potom klikněte na **Další**.
   
    ![Snímek obrazovky se zobrazí v dialogovém okně replikace na kartě disky.](./media/tutorial-migrate-vmware/disks.png)

12. V části **Kontrola a zahájení replikace** zkontrolujte nastavení a kliknutím na **Replikovat** spusťte počáteční replikaci serverů.

> [!NOTE]
> Nastavení replikace můžete aktualizovat kdykoli před spuštěním replikace (**Správa** replikačních  >  **počítačů**). Po spuštění replikace nelze změnit nastavení.

### <a name="provisioning-for-the-first-time"></a>Zřizování pro první čas

Pokud se jedná o první virtuální počítač, který se v projektu replikuje, migrace serveru tyto prostředky automaticky zřídí ve stejné skupině prostředků jako projekt.

- **Service Bus**: Migrace serveru používá Service Bus k posílání zpráv orchestrace replikace do zařízení.
- **Účet úložiště brány**: Migrace serveru používá účet úložiště brány k ukládání informací o stavu virtuálních počítačů, které se replikují.
- **Účet úložiště protokolu**: zařízení Azure Migrate nahrává protokoly replikace pro virtuální počítače do účtu úložiště protokolu. Azure Migrate použije informace o replikaci na spravované disky repliky.
- **Trezor klíčů**: zařízení Azure Migrate používá Trezor klíčů ke správě připojovacích řetězců pro Service Bus a přístup k klíčům pro účty úložiště používané v replikaci.

## <a name="track-and-monitor"></a>Sledování a sledování

1. Sledujte stav úlohy v oznámeních na portálu.
2. Kliknutím na **replikace serverů** v Azure Migrate Sledujte stav replikace **: Migrace serveru**.

     ![Monitorování replikace](./media/tutorial-migrate-vmware/replicating-servers.png)

Replikace probíhá takto:
- Po úspěšném dokončení úlohy spuštění replikace začnou počítače počáteční replikaci do Azure.
- Během počáteční replikace se vytvoří snímek virtuálního počítače. Data na disku ze snímku se replikují na spravované disky repliky v Azure.
- Po dokončení počáteční replikace se spustí rozdílová replikace. Přírůstkové změny na místních discích se pravidelně replikují na disky replik v Azure.

## <a name="run-a-test-migration"></a>Spuštění testu migrace


Po zahájení rozdílové replikace můžete spustit testovací migraci pro virtuální počítače před spuštěním úplné migrace do Azure. Důrazně doporučujeme, abyste to pro každý počítač provedli aspoň jednou, a teprve potom ho migrujete.

- Při spuštění testu migrace proběhne kontrola, že migrace bude fungovat podle očekávání, aniž by to ovlivnilo místní počítače, které zůstávají v provozu, a pokračuje v replikaci. 
- Testovací migrace simuluje migraci tím, že vytvoří virtuální počítač Azure pomocí replikovaných dat (obvykle se migruje na virtuální síť, která není v produkčním prostředí, ve vašem předplatném Azure).
- Replikovaný virtuální počítač Azure můžete použít k ověření migrace, provádění testování aplikace a řešení všech problémů před úplnou migrací.

Proveďte migraci testu následujícím způsobem:


1. V Azure Migrate **cíle migrace**  >  na **servery**  >  **: Migrace serveru** klikněte na **test migrovaných serverů**.

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

1. V Azure Migrate Project > **servery**  >  **Azure Migrate: Migrace serveru** klikněte na **replikace serverů**.

    ![Replikace serverů](./media/tutorial-migrate-vmware/replicate-servers.png)

2. V části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač a vyberte **Migrovat**.
3. V **Migrate**  >  **nástroji migrovat vypínání virtuálních počítačů a provádění plánované migrace bez ztráty dat** vyberte **Ano**  >  **OK**.
    - Azure Migrate ve výchozím nastavení vypne místní virtuální počítač a spustí replikaci na vyžádání, při které se synchronizují všechny změny virtuálního počítače, ke kterým došlo od poslední replikace. Tím se zajistí, že nedojde ke ztrátě dat.
    - Pokud virtuální počítač nechcete vypnout, vyberte **Ne**.
4. Pro virtuální počítač se spustí úloha migrace. Tuto úlohu můžete sledovat pomocí oznámení Azure.
5. Po dokončení úlohy můžete virtuální počítač zobrazit a spravovat na stránce **Virtuální počítače**.

## <a name="complete-the-migration"></a>Dokončete migraci

1. Po dokončení migrace klikněte pravým tlačítkem na virtuální počítač > **zastavit replikaci**. Tím se zastaví replikace místního počítače a vyčistí se informace o stavu replikace pro virtuální počítač.
2. Na migrované počítače nainstalujte agenta Azure VM pro [Windows](../virtual-machines/extensions/agent-windows.md) nebo [Linux](../virtual-machines/extensions/agent-linux.md) .
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
    - Odblokujte a omezte přístup k příchozímu provozu pomocí [správy v čase Azure Security Center](../security-center/security-center-just-in-time.md).
    - Omezte síťový provoz na koncové body správy pomocí [skupin zabezpečení sítě](../virtual-network/network-security-groups-overview.md).
    - Nasaďte službu [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), která vám pomůže zabezpečit disky a zajistit bezpečnost dat před krádeží a neoprávněným přístupem.
    - Přečtěte si další informace o [zabezpečení prostředků IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) a navštivte [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Pro monitorování a správu:
-  Zvažte nasazení služby [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), která bude monitorovat využití prostředků a útratu.


## <a name="next-steps"></a>Další kroky

Prozkoumejte [cestu k migraci do cloudu](/azure/architecture/cloud-adoption/getting-started/migrate) v rozhraní Azure cloudu pro přijetí.