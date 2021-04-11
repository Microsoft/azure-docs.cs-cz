---
title: Migrace virtuálních počítačů Hyper-V do Azure pomocí migrace serveru Azure Migrate
description: Přečtěte si, jak migrovat místní virtuální počítače Hyper-V do Azure pomocí migrace Azure Migrate serveru.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 0072ce81fc619c39770eba52e24dc5a0c57280a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604572"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrace virtuálních počítačů Hyper-V do Azure 

V tomto článku se dozvíte, jak migrovat místní virtuální počítače Hyper-V do Azure s využitím nástroje [Azure Migrate: Server pro migraci](migrate-services-overview.md#azure-migrate-server-migration-tool) .

Tento kurz je třetí v řadě, který ukazuje, jak vyhodnocovat a migrovat počítače do Azure. 

> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. V těchto kurzech se v rámci možností používají jen výchozí možnosti a neuvádějí se všechny varianty nastavení ani všechny cesty. 

 V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidejte nástroj Azure Migrate: Server pro migraci.
> * Vyhledejte virtuální počítače, které chcete migrovat.
> * Zahajte replikaci virtuálních počítačů.
> * Spusťte test migrace a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci virtuálního počítače.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Požadavky


Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

1. [Zkontrolujte](hyper-v-migration-architecture.md) architekturu migrace technologie Hyper-V.
2. [Kontrola](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) Požadavky na hostitele Hyper-V pro migraci a adresy URL Azure, ke kterým hostitelé a clustery Hyper-V potřebují přístup pro migraci virtuálních počítačů.
3. [Projděte si](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) požadavky na virtuální počítače Hyper-V, které chcete migrovat do Azure.
4. Doporučujeme, abyste před migrací na Azure  [vyhodnotili virtuální počítače Hyper-V](tutorial-assess-hyper-v.md) , ale nemusíte je používat.
5. Přejít do již vytvořeného projektu nebo [vytvořit nový projekt](./create-manage-projects.md)
6. Ověřte oprávnění pro účet Azure – váš účet Azure potřebuje oprávnění k vytvoření virtuálního počítače a zápis na spravovaný disk Azure.

## <a name="download-and-install-the-provider"></a>Stažení a instalace poskytovatele

Pro migraci virtuálních počítačů Hyper-V Azure Migrate: Migrace serveru nainstaluje poskytovatele softwaru (Microsoft Azure poskytovatele Site Recovery a agenta Microsoft Azure Recovery Service) na hostitele Hyper-V nebo na uzly clusteru. Všimněte si, že [zařízení Azure Migrate](migrate-appliance.md) se nepoužívá pro migraci technologie Hyper-V.

1. V Azure Migrate Project > **servery** v části **Azure Migrate: Migrace serveru** klikněte na **Vyhledat**.
2. V rozevíracích **seznamech počítačů**  >  , ve **kterých jsou počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**.
3. V části **cílová oblast** vyberte oblast Azure, do které chcete migrovat počítače.
6. Vyberte **potvrdit, že cílová oblast pro migraci je název regionu**.
7. Klikněte na **vytvořit prostředky**. Tím dojde k vytvoření trezoru Azure Site Recovery na pozadí.
    - Pokud jste už nastavili migraci pomocí migrace serveru Azure Migrate, tato možnost se nezobrazí, protože se předtím nastavily prostředky.
    - Po kliknutí na toto tlačítko nemůžete změnit cílovou oblast tohoto projektu.
    - Všechny následné migrace jsou v této oblasti.
    
8. V části **Příprava hostitelských serverů Hyper-v** Stáhněte zprostředkovatele replikace technologie Hyper-v a soubor registračního klíče.
    - Registrační klíč je nutný k registraci hostitele Hyper-V s migrací serveru Azure Migrate.
    - Klíč je platný pět dní od jeho vygenerování.

    ![Stažení poskytovatele a klíče](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Zkopírujte instalační soubor zprostředkovatele a soubor registračního klíče na každého hostitele Hyper-V (nebo uzlu clusteru) se spuštěnými virtuálními počítači, které chcete replikovat.
5. Spusťte instalační soubor zprostředkovatele na každém hostiteli, jak je popsáno v následujícím postupu.
6. Po instalaci poskytovatele na hostitele v části **zjistit počítače** klikněte na **Dokončit registraci**.

    ![Dokončit registraci](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Po dokončení registrace může trvat až 15 minut, než se zjištěné virtuální počítače objeví v Azure Migrate migraci serveru. Po zjištění virtuálních počítačů se počet **zjištěných serverů** zvyšuje.

![Zjištěné servery](./media/tutorial-migrate-hyper-v/discovered-servers.png)


## <a name="replicate-hyper-v-vms"></a>Replikace virtuálních počítačů Hyper-V

Po dokončení zjišťování můžete zahájit replikaci virtuálních počítačů Hyper-V do Azure.

> [!NOTE]
> Můžete replikovat až 10 počítačů dohromady. Pokud potřebujete replikovat více, proveďte jejich replikaci současně v dávkách po 10.

1. V Azure Migrate Project > **servery** **Azure Migrate: Migrace serveru** klikněte na **replikovat**.
2. V případě **replikace**> **Nastavení zdroje**  >  **jsou vaše počítače virtualizované?** vyberte **Ano, s technologií Hyper-V**. Pak klikněte na **Další: virtuální počítače**.
3. V části **Virtuální počítače** vyberte počítače, které chcete replikovat.
    - Pokud jste pro virtuální počítače spustili posouzení, můžete použít doporučenou velikost a typ disku (Premium nebo Standard) z výsledků posouzení. Pokud to chcete provést, v části **Importovat nastavení migrace z posouzení Azure Migrate?** vyberte možnost **Ano**.
    - Pokud jste neprovedli posouzení nebo pokud nechcete použít nastavení posouzení, vyberte možnost **Ne**.
    - Pokud jste se rozhodli použít posouzení, vyberte skupinu virtuálních počítačů a název posouzení.

        ![Výběr posouzení](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. V části **Virtuální počítače** vyhledejte požadované virtuální počítače a zkontrolujte všechny virtuální počítače, které chcete migrovat. Pak klikněte na **Další: nastavení cíle**.

    ![Vybrat virtuální počítače](./media/tutorial-migrate-hyper-v/select-vms.png)

5. V části **Nastavení cíle** vyberte cílovou oblast, do které budete migrovat, předplatné a skupinu prostředků, ve kterých se virtuální počítače Azure po migraci budou nacházet.
7. V části **účet úložiště replikace** vyberte účet Azure Storage, ve kterém budou replikovaná data uložená v Azure.
8. **Virtual Network** vyberte virtuální síť nebo podsíť Azure, ke které se po migraci připojí virtuální počítače Azure.
9. V **Možnosti dostupnost** vyberte:
    -  Zóna dostupnosti pro připnutí migrovaného počítače ke konkrétní zóně dostupnosti v oblasti Tuto možnost použijte k distribuci serverů, které tvoří aplikační vrstvu s více uzly napříč Zóny dostupnosti. Pokud vyberete tuto možnost, budete muset zadat zónu dostupnosti, která se má použít pro každý z vybraných počítačů na kartě Compute. Tato možnost je dostupná jenom v případě, že cílová oblast vybraná pro migraci podporuje Zóny dostupnosti
    -  Skupina dostupnosti umístí migrovaný počítač do skupiny dostupnosti. Vybraná cílová skupina prostředků musí mít jednu nebo víc skupin dostupnosti, aby bylo možné tuto možnost použít.
    - Není nutná žádná možnost redundance infrastruktury, pokud nepotřebujete žádnou z těchto konfigurací dostupnosti pro migrované počítače.
10. V části **Zvýhodněné hybridní využití Azure**:

    - Vyberte **Ne**, pokud nechcete využít Zvýhodněné hybridní využití Azure. Pak klikněte na tlačítko **Další**.
    - Vyberte **Ano**, pokud máte počítače s Windows Serverem s aktivním Software Assurance nebo předplatným Windows Serveru a u migrovaných počítačů chcete využít tuto výhodu. Potom klikněte na **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-hyper-v/target-settings.png)

11. V části **COMPUTE** Zkontrolujte název virtuálního počítače, velikost, typ disku s operačním systémem a konfiguraci dostupnosti (Pokud jste zvolili v předchozím kroku). Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro vyhodnocení, bude rozevírací seznam velikost virtuálního počítače obsahovat doporučenou velikost. Jinak Azure Migrate vybere velikost na základě nejbližší shody v předplatném Azure. Případně můžete velikost vybrat ručně v části **Velikost virtuálního počítače Azure**. 
    - **Disk s operačním systémem**: zadejte operační systém (spouštěcí) disk pro virtuální počítač. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč a instalační program operačního systému. 
    - **Skupina dostupnosti**: Pokud má být virtuální počítač v sadě dostupnosti Azure po migraci, zadejte sadu. Skupina musí být v cílové skupině prostředků, kterou pro migraci zadáte.

    ![Nastavení výpočetního virtuálního počítače](./media/tutorial-migrate-hyper-v/compute-settings.png)

12. V části **disky** zadejte disky virtuálních počítačů, které se musí replikovat do Azure. Potom klikněte na **Další**.
    - Disky můžete z replikace vyloučit.
    - Pokud disky vyloučíte, po migraci nebudou na virtuálním počítači Azure. 

    ![Snímek obrazovky se zobrazí v dialogovém okně replikace na kartě disky.](./media/tutorial-migrate-hyper-v/disks.png)

13. V části **Kontrola a zahájení replikace** zkontrolujte nastavení a kliknutím na **Replikovat** spusťte počáteční replikaci serverů.

> [!NOTE]
> Nastavení replikace můžete aktualizovat kdykoli před spuštěním replikace, v nástroji **Spravovat**  >  **replikační počítače**. Po spuštění replikace není možné nastavení změnit.

## <a name="provision-for-the-first-time"></a>Poprvé zřídit

Pokud se jedná o první virtuální počítač, který budete replikovat v projektu Azure Migrate, Azure Migrate: Migrace serveru automaticky zřídí tyto prostředky ve stejné skupině prostředků jako projekt.
- **Účet úložiště mezipaměti**: software poskytovatele Azure Site Recovery, který je nainstalovaný na Hyper-V, nahrává data replikace pro virtuální počítače nakonfigurované pro replikaci do účtu úložiště (v rámci vašeho předplatného se označuje jako účet úložiště mezipaměti nebo účet úložiště protokolů). Služba Azure Migrate pak zkopíruje nahraná replikační data z účtu úložiště na disky spravované replikami, které odpovídají příslušnému virtuálnímu počítači. Při konfiguraci replikace pro virtuální počítač je potřeba zadat účet úložiště mezipaměti a Azure Migrate portál automaticky vytvoří jeden pro Azure Migrate projekt, když je replikace poprvé nakonfigurovaná v projektu.

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


1. V Azure Migrate **cíle migrace**  >  na **servery**  >  **: Migrace serveru** klikněte na **test migrovaných serverů**.

     ![Test migrovaných serverů](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Klikněte pravým tlačítkem na virtuální počítač, který chcete otestovat, a klikněte na **Testovací migrace**.

    ![Testovací migrace](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. V části **test migrace** vyberte virtuální síť Azure, ve které bude virtuální počítač Azure umístěný po migraci. Doporučujeme použít virtuální síť, která není v provozu.
4. Spustí se úloha **Testovací migrace**. Tuto úlohu můžete monitorovat pomocí oznámení portálu.
5. Po dokončení migrace si můžete migrovaný virtuální počítač Azure prohlédnout na webu Azure Portal v části **Virtuální počítače**. Název počítače má příponu **-Test**.
6. Po dokončení testu v části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač Azure a klikněte na **Vyčistit testovací migraci**.

    ![Vyčištění migrace](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrace virtuálních počítačů

Po ověření, že migrace testu funguje podle očekávání, můžete migrovat místní počítače.

1. V Azure Migrate Project > **servery**  >  **Azure Migrate: Migrace serveru** klikněte na **replikace serverů**.

    ![Replikace serverů](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. V části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač a vyberte **Migrovat**.
3. V   >  **nástroji migrovat vypínání virtuálních počítačů a provádění plánované migrace bez ztráty dat** vyberte **Ano**  >  .
    - Azure Migrate ve výchozím nastavení vypne místní virtuální počítač a spustí replikaci na vyžádání, při které se synchronizují všechny změny virtuálního počítače, ke kterým došlo od poslední replikace. Tím se zajistí, že nedojde ke ztrátě dat.
    - Pokud virtuální počítač nechcete vypnout, vyberte **Ne**.
4. Pro virtuální počítač se spustí úloha migrace. Tuto úlohu můžete sledovat pomocí oznámení Azure.
5. Po dokončení úlohy můžete virtuální počítač zobrazit a spravovat na stránce **Virtuální počítače**.

## <a name="complete-the-migration"></a>Dokončete migraci

1. Po dokončení migrace klikněte pravým tlačítkem na virtuální počítač > **zastavit migraci**. Provede následující akce:
    - Zastaví replikaci místního počítače.
    - Odebere počítač z počtu **replikačních serverů** v Azure Migrate: Migrace serveru.
    - Vyčistí informace o stavu replikace pro virtuální počítač.
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