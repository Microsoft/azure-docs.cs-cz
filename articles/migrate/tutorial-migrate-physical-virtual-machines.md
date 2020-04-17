---
title: Migrujte počítače jako fyzický server do Azure pomocí Migrace Azure.
description: Tento článek popisuje, jak migrovat fyzické počítače do Azure pomocí Migrace Azure.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: MVC
ms.openlocfilehash: 1824fc6c7cbc0fd0390770027f4a15d9130139de
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535379"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Migrace počítačů jako fyzických serverů do Azure

Tento článek ukazuje, jak migrovat počítače jako fyzické servery do Azure pomocí nástroje Migrace:Server Azure. Migrace počítačů tak, že je zacházíte jako s fyzickými servery, je užitečná v řadě scénářů:

- Migrujte místní fyzické servery.
- Migrujte virtuální počítače virtualizované platformami, jako je Xen, KVM.
- Migrace virtuálních měn Hyper-V nebo VMware, pokud z nějakého důvodu nemůžete použít standardní proces migrace pro migraci [Hyper-V](tutorial-migrate-hyper-v.md)nebo [VMware.](server-migrate-overview.md)
- Migrujte virtuální chod v privátních cloudech.
- Migrovat virtuální servery běžící ve veřejných cloudech, jako jsou Amazon Web Services (AWS) nebo Google Cloud Platform (GCP).


Tento kurz je třetí v řadě, která ukazuje, jak posoudit a migrovat fyzické servery do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte se na používání Azure s migrací:migrace serveru Azure.
> * Zkontrolujte požadavky na počítače, které chcete migrovat, a připravte počítač pro replikační zařízení Azure Migrate, které se používá ke zjišťování a migraci počítačů do Azure.
> * Přidejte nástroj migrace serveru Azure v centru Migrace Azure.
> * Nastavte zařízení replikace.
> * Nainstalujte službu Mobility do počítačů, které chcete migrovat.
> * Povolte replikaci.
> * Spusťte testovací migraci a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci do Azure.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář, takže můžete rychle nastavit proof-of-concept. Kurzy používají výchozí možnosti tam, kde je to možné, a nezobrazují všechna možná nastavení a cesty. Podrobné pokyny najdete v návodech pro Azure Migrate.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/pricing/free-trial/) než začnete.


## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

[Zkontrolujte](migrate-architecture.md) architekturu migrace.




## <a name="prepare-azure"></a>Příprava Azure

Připravte Azure na migraci pomocí migrace serveru.

**Úkol** | **Podrobnosti**
--- | ---
**Vytvoření projektu migrace Azure** | Váš účet Azure potřebuje oprávnění Contributer nebo Owner k vytvoření projektu.
**Ověření oprávnění pro svůj účet Azure** | Váš účet Azure potřebuje oprávnění k vytvoření virtuálního počítače a zápisu na spravovaný disk Azure.


### <a name="assign-permissions-to-create-project"></a>Přiřazení oprávnění k vytvoření projektu

1. Na webu Azure Portal otevřete předplatné a vyberte **řízení přístupu (IAM).**
2. V **části Zkontrolovat přístup**vyhledejte příslušný účet a kliknutím na něj zobrazte oprávnění.
3. Měli byste mít oprávnění **přispěvatele** nebo **vlastníka.**
    - Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem předplatného.
    - Pokud nejste vlastníkem předplatného, spolupracujte s vlastníkem a přiřaďte roli.


### <a name="assign-azure-account-permissions"></a>Přiřazení oprávnění účtu Azure

Přiřaďte roli přispěvatele virtuálního počítače k účtu Azure. To poskytuje oprávnění k:

    - Vytvoření virtuálního počítače ve vybrané skupině prostředků
    - Vytvoření virtuálního počítače ve vybrané virtuální síti
    - Zapisovat na spravovaný disk Azure. 

### <a name="create-an-azure-network"></a>Vytvoření sítě Azure

[Nastavte](../virtual-network/manage-virtual-network.md#create-a-virtual-network) virtuální síť Azure (VNet). Když se replikujete do Azure, virtuální počítače Azure se vytvoří a připojí k virtuální síti Azure, kterou zadáte při nastavování migrace.

## <a name="prepare-for-migration"></a>Příprava migrace

Chcete-li se připravit na migraci fyzického serveru, je třeba ověřit nastavení fyzického serveru a připravit se na nasazení zařízení pro replikaci.

### <a name="check-machine-requirements-for-migration"></a>Zkontrolujte požadavky na počítač pro migraci

Ujistěte se, že počítače splňují požadavky na migraci do Azure. 

> [!NOTE]
> Při migraci fyzických počítačů používá migrace Azure:Server Migration stejnou architekturu replikace jako zotavení po havárii založené na agentovi ve službě Azure Site Recovery a některé součásti sdílejí stejný základ kódu. Některý obsah může odkazovat na dokumentaci site recovery.

1. [Ověřte](migrate-support-matrix-physical-migration.md#physical-server-requirements) požadavky na fyzický server.
2. Ověřte, že místní počítače, které replikujete do Azure, splňují [požadavky virtuálních počítačů Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Příprava počítače pro zařízení pro replikaci

Migrace Azure:Migrace serveru používá k replikaci počítačů do Azure zařízení replikace. Zařízení replikace spouští následující součásti.

- **Konfigurační server**: Konfigurační server koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat.
- **Procesní server**: Procesový server funguje jako replikační brána. Přijímá data replikace; optimalizuje ji pomocí ukládání do mezipaměti, komprese a šifrování a odesílá ji do účtu úložiště mezipaměti v Azure. 

Připravte se na nasazení zařízení takto:

- Připravíte počítač pro hostování zařízení replikace. [Zkontrolujte](migrate-replication-appliance.md#appliance-requirements) požadavky na stroj. Zařízení by nemělo být nainstalováno na zdrojovém počítači, který chcete replikovat.
- Replikační zařízení používá MySQL. Přečtěte si [možnosti](migrate-replication-appliance.md#mysql-installation) instalace MySQL na zařízení.
- Zkontrolujte adresy URL Azure požadované pro zařízení replikace pro přístup k [veřejným](migrate-replication-appliance.md#url-access) a [vládním](migrate-replication-appliance.md#azure-government-url-access) cloudům.
- Zkontrolujte požadavky na přístup [port] (migrate-replication-appliance.md#port-access) pro zařízení replikace.

## <a name="add-the-server-migration-tool"></a>Přidání nástroje pro migraci serveru

Nastavte projekt Migrace Azure a pak do něj přidejte nástroj migrace serveru.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.
3. V části **Přehled** klikněte na **Posoudit a migrovat servery**.
4. V části **Zjistit, vyhodnoťte a migrujte servery**na **možnost Posoudit a migrovat servery**.

    ![Zjišťování a posuzování serverů](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. V části **Zjistit, posoudit a migrovat servery** klikněte na **Přidat nástroje**.
6. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.
7. V části **Podrobnosti o projektu** zadejte název projektu a zeměpisnou oblast, ve které chcete projekt vytvořit, a klikněte na **Další**. Zkontrolujte podporované zeměpisné oblasti pro [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [vládní cloudy](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Vytvoření projektu migrace Azure](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

8. V **nástroji pro výběr hodnocení**vyberte přeskočit přidání nástroje pro hodnocení pro tuto **chvíli** > **Další**.
9. V **nástroji Pro výběr migrace**vyberte Možnost Migrace **Azure: Migrace** > serveru**Další**.
10. V části **Zkontrolovat a přidat nástroje** zkontrolujte nastavení a klikněte na **Přidat nástroje**.
11. Po přidání nástroje se zobrazí v**nástrojích**Migrace projektu Azure > **servery** > .

## <a name="set-up-the-replication-appliance"></a>Nastavení zařízení pro replikaci

Prvním krokem migrace je nastavení zařízení replikace. Chcete-li nastavit zařízení pro fyzickou migraci serveru, stáhněte soubor instalačního programu pro zařízení a spusťte jej na [počítači, který jste připravili](#prepare-a-machine-for-the-replication-appliance). Po instalaci zařízení jej zaregistrujete pomocí migrace serveru Azure.


### <a name="download-the-replication-appliance-installer"></a>Stažení instalačního programu replikačního zařízení

1. V projektu Azure Migrate > **servery**klikněte v **části Migrace: Migrace serveru**na **zjistit**.

    ![Vyhledání virtuálních počítačů](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. V **discover strojích** > **Jsou vaše počítače virtualizované?**, klikněte na **Není virtualizováno/Jiné**.
4. V **oblasti Cíl**vyberte oblast Azure, do které chcete počítače migrovat.
5. Vyberte **Možnost Potvrdit, že cílová oblast pro migraci je název oblasti**.
6. Klepněte na **tlačítko Vytvořit zdroje**. Tím se vytvoří trezor azure site recovery na pozadí.
    - Pokud jste už nastavili migraci s migrací serveru Azure, nelze cílovou možnost nakonfigurovat, protože prostředky byly nastaveny dříve.
    - Cílovou oblast pro tento projekt nelze změnit po klepnutí na toto tlačítko.
    - Všechny následné migrace jsou do této oblasti.

7. V části Chcete nainstalovat nové zařízení pro **Install a replication appliance** **replikaci?**
9. V **části Stažení a instalace softwaru zařízení pro replikaci**stáhněte instalační program zařízení a registrační klíč. Chcete-li přístroj zaregistrovat, musíte k klíči. Klíč je platný po dobu pěti dnů po jeho stažení.

    ![Zprostředkovatel stahování](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Zkopírujte soubor nastavení zařízení a soubor klíčů do počítače se systémem Windows Server 2016, který jste pro zařízení vytvořili.
11. Spusťte instalační soubor zařízení replikace, jak je popsáno v dalším postupu. Po dokončení instalace se automaticky spustí Průvodce konfigurací zařízení (Průvodce můžete spustit také ručně pomocí zástupce cspsconfigtool, který je vytvořen na ploše zařízení). Na kartě Spravovat účty průvodce přidejte podrobnosti o účtu, které se použijí pro nabízenou instalaci služby Mobility. V tomto kurzu budeme ručně instalovat službu mobility na počítačích, které mají být replikovány, takže vytvořte fiktivní účet v tomto kroku a pokračujte.

12. Po restartování zařízení po instalaci vyberte v **počítačích Discover**nové zařízení v **příkazu Vybrat konfigurační server**a klepněte na tlačítko **Dokončit registraci**. Dokončení registrace provádí několik závěrečných úkolů k přípravě zařízení replikace.

    ![Dokončení registrace](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Může trvat nějakou dobu po dokončení registrace, dokud se zjištěné počítače nezobrazí v migraci serveru Azure. Jak jsou zjištěny virtuální chodů, počet **zjištěných serverů** stoupá.

![Zjištěné servery](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalace služby Mobility

Na počítačích, které chcete migrovat, je třeba nainstalovat agenta služby Mobility. Instalátory agentů jsou k dispozici na zařízení replikace. Najdete správný instalační program a nainstalujte agenta do každého počítače, který chcete migrovat. Proveďte to následujícím způsobem:

1. Přihlaste se k zařízení pro replikaci.
2. Přejděte na **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Vyhledejte instalační program operačního systému a verze počítače. Zkontrolujte [podporované operační systémy](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Zkopírujte soubor instalačního programu do počítače, který chcete migrovat.
5. Ujistěte se, že máte přístupové heslo, které bylo vygenerováno při nasazení zařízení.
    - Uložte soubor do dočasného textového souboru v počítači.
    - Přístupové heslo můžete získat na zařízení replikace. Z příkazového řádku spusťte **c:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** a zobrazte aktuální přístupové heslo.
    - Neobnovujte přístupové heslo. Tím dojde k přerušení připojení a budete muset znovu zaregistrovat zařízení replikace.


### <a name="install-on-windows"></a>Instalace v systému Windows

1. Extrahujte obsah instalačního souboru do místní složky (například C:\Temp) v počítači, a to následovně:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Spusťte Instalační službu mobility:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Zaregistrujte agenta u zařízení pro replikaci:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Instalace v systému Linux

1. Extrahujte obsah instalátoru tarballu do místní složky (například /tmp/MobSvcInstaller) v počítači, a to následovně:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Spusťte skript instalačního programu:
    ```
    sudo ./install -r MS -q
    ```
3. Zaregistrujte agenta u zařízení pro replikaci:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replikovat stroje

Nyní vyberte počítače pro migraci. 

> [!NOTE]
> Můžete replikovat až 10 počítačů dohromady. Pokud potřebujete replikovat více, replikujte je současně v dávkách po 10.

1. V projektu Azure Migrate > **servery** **, Azure Migrate: Migrace serveru**, klikněte na **Replikovat**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. V **části Replikovat**> **Not virtualized/Other**nastavení >  **zdroje**Jsou vaše**počítače virtualizovány?**
3. V **místním zařízení**vyberte název zařízení Azure Migrate, které jste nastavili.
4. V **části Procesní server**vyberte název zařízení pro replikaci.
6. V **části Pověření hosta**zadáte fiktivní účet, který bude použit k ruční instalaci služby Mobility (nabízená instalace není ve fyzickém režimu podporována). Pak klikněte na **Další: Virtuální počítače**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. Ve **Virtuálních počítačích**v **části Importnastavení migrace z hodnocení?**, ponechte výchozí nastavení **Ne, nastavení migrace zadám ručně**.
8. Zkontrolujte každý virtuální virtuální mísa, který chcete migrovat. Pak klepněte na tlačítko **Další: Nastavení cíle**.

    ![Výběr virtuálních disponecí](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. V části **Nastavení cíle** vyberte předplatné a cílovou oblast migrace a zadejte skupinu prostředků, ve které se po migraci budou nacházet virtuální počítače Azure.
10. V části **Virtuální síť** vyberte virtuální síť a podsíť Azure, ke kterým se po migraci připojí virtuální počítače Azure.
11. V části **Zvýhodněné hybridní využití Azure**:

    - Vyberte **Ne**, pokud nechcete využít Zvýhodněné hybridní využití Azure. Pak klikněte na **Další**.
    - Vyberte **Ano**, pokud máte počítače s Windows Serverem s aktivním Software Assurance nebo předplatným Windows Serveru a u migrovaných počítačů chcete využít tuto výhodu. Pak klikněte na **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. V části **Výpočetní prostředky** zkontrolujte název, velikost, typ disku s operačním systémem a skupinu dostupnosti virtuálního počítače. Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Velikost virtuálního počítače:** Ve výchozím nastavení migrace migrace serveru Azure vybere velikost na základě nejbližší shody v předplatném Azure. Případně můžete velikost vybrat ručně v části **Velikost virtuálního počítače Azure**. 
    - **Disk operačního systému**: Zadejte disk operačního systému (boot) pro virtuální počítače. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč a instalační program operačního systému. 
    - **Dostupnost set**: Pokud virtuální počítač by měl být v sadě dostupnosti Azure po migraci, zadejte sadu. Skupina musí být v cílové skupině prostředků, kterou pro migraci zadáte.

    ![Nastavení výpočetních prostředků](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. V **části Disky**určete, jestli mají být disky virtuálního počítače replikovány do Azure, a vyberte typ disku (standardní Disk SSD/HDD nebo disky spravovaný prémií) v Azure. Pak klikněte na **Další**.
    - Disky můžete z replikace vyloučit.
    - Pokud disky vyloučíte, po migraci nebudou na virtuálním počítači Azure. 

    ![Nastavení disku](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. V části **Kontrola a zahájení replikace** zkontrolujte nastavení a kliknutím na **Replikovat** spusťte počáteční replikaci serverů.

> [!NOTE]
> Nastavení replikace můžete aktualizovat kdykoli před zahájením replikace, **Správa** > **replikačních počítačů**. Po spuštění replikace není možné nastavení změnit.



## <a name="track-and-monitor"></a>Sledování a sledování

- Po klepnutí na tlačítko **Replikovat** úlohu Zahájit replikaci se spustí. 
- Po úspěšném dokončení úlohy Spustit replikaci začnou počítače zahájit počáteční replikaci do Azure.
- Po dokončení počáteční replikace začíná rozdílová replikace. Přírůstkové změny na místních discích se pravidelně replikují na repliky disků v Azure.


Stav úlohy můžete sledovat v oznámeních portálu.

Stav replikace můžete sledovat kliknutím na **Replikace serverů** v **Azure Migrate: Server Migration**.
![Monitorování replikace](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Spuštění testu migrace


Když začíná rozdílová replikace, můžete spustit testovací migraci pro virtuální počítače, před spuštěním úplné migrace do Azure. Důrazně doporučujeme provést alespoň jednou pro každý počítač, před migrací.

- Spuštění testovací migrace kontroluje, že migrace bude fungovat podle očekávání, aniž by to mělo vliv na místní počítače, které zůstávají funkční a pokračují v replikaci. 
- Migrace testu simuluje migraci vytvořením virtuálního počítače Azure pomocí replikovaných dat (obvykle migrace na neprodukční virtuální síť v předplatném Azure).
- Replikovaný testovací virtuální počítač Azure můžete použít k ověření migrace, provedení testování aplikací a řešení všech problémů před úplnou migrací.

Proveďte testovací migraci následujícím způsobem:


1. V **oblasti cílů** > migrace**Servery, které** > **Azure migruje: Migrace serveru**, klikněte na testovat **migrované servery**.

     ![Test migrovaných serverů](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Klikněte pravým tlačítkem na virtuální počítač, který chcete otestovat, a klikněte na **Testovací migrace**.

    ![Testovací migrace](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. V části **Testovací migrace** vyberte virtuální síť Azure, ve které se po migraci bude nacházet virtuální počítač Azure. Doporučujeme použít jinou než produkční virtuální síť.
4. Spustí se úloha **Testovací migrace**. Tuto úlohu můžete monitorovat pomocí oznámení portálu.
5. Po dokončení migrace si můžete migrovaný virtuální počítač Azure prohlédnout na webu Azure Portal v části **Virtuální počítače**. Název počítače má příponu **-Test**.
6. Po dokončení testu v části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač Azure a klikněte na **Vyčistit testovací migraci**.

    ![Vyčištění migrace](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrace virtuálních počítačů

Po ověření, že migrace testu funguje podle očekávání, můžete migrovat místní počítače.

1. V projektu Azure Migrate > **servery, které** > **Azure migruje: Migrace serveru**klikněte na **Replikace serverů**.

    ![Replikace serverů](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. V části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač a vyberte **Migrovat**.
3. V **migrovat** > **Vypnout virtuální počítače a provést plánovanou migraci bez ztráty dat**vyberte **Ano** > **OK**.
    - Pokud virtuální počítač nechcete vypnout, vyberte **Ne**.

    Poznámka: Pro migraci fyzického serveru je doporučeno snížit aplikaci jako součást okna migrace (nedovolte, aby aplikace přijaly žádná připojení) a potom zahájit migraci (Server musí být spuštěn, takže zbývající změny lze synchronizovat) před dokončením migrace.

4. Pro virtuální počítač se spustí úloha migrace. Tuto úlohu můžete sledovat pomocí oznámení Azure.
5. Po dokončení úlohy můžete virtuální počítač zobrazit a spravovat na stránce **Virtuální počítače**.

## <a name="complete-the-migration"></a>Dokončení migrace

1. Po dokončení migrace klikněte pravým tlačítkem myši na **> zastavit migraci**virtuálního . Tím postupujte takto:
    - Zastaví replikaci pro místní počítač.
    - Odebere počítač z počtu **replikačních serverů** v Azure Migrate: Migrace serveru.
    - Vyčistí informace o stavu replikace pro počítač.
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
    - Zvažte nasazení služby [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), která bude monitorovat využití prostředků a útratu.


## <a name="next-steps"></a>Další kroky

Prozkoumejte [cestu migrace do cloudu](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) v rámci azure cloudpřijetí.
