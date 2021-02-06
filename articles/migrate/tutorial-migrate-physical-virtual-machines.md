---
title: Migrujte počítače jako fyzický server do Azure pomocí Azure Migrate.
description: Tento článek popisuje, jak migrovat fyzické počítače do Azure pomocí Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 01/02/2021
ms.custom: MVC
ms.openlocfilehash: aeeb810174ff5c21a81bcec8aa9265ff100edf91
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626321"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Migrace počítačů jako fyzických serverů do Azure

V tomto článku se dozvíte, jak migrovat počítače jako fyzické servery do Azure pomocí nástroje Azure Migrate: Server pro migraci. Migrace počítačů jejich použitím jako fyzických serverů je užitečná v různých scénářích:

- Migrujte místní fyzické servery.
- Migrujte virtuální počítače virtualizované pomocí platforem, jako je Xen, KVM.
- Migrace virtuálních počítačů s Hyper-V nebo VMware, pokud z nějakého důvodu nemůžete použít standardní proces migrace pro [Hyper-v](tutorial-migrate-hyper-v.md)nebo migraci [VMware](server-migrate-overview.md) .
- Migrujte virtuální počítače běžící v privátních cloudech.
- Migrujte virtuální počítače běžící ve veřejných cloudech, jako je Amazon Web Services (AWS) nebo Google Cloud Platform (GCP).


Tento kurz je třetí v řadě, který ukazuje, jak vyhodnocovat a migrovat fyzické servery do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte se na použití Azure s Azure Migrate: Migrace serveru.
> * Ověřte požadavky na počítače, které chcete migrovat, a připravte počítač pro Azure Migrate replikačního zařízení, které se používá ke zjišťování a migraci počítačů do Azure.
> * Přidejte nástroj Azure Migrate: Server pro migraci do centra Azure Migrate.
> * Nastavte zařízení replikace.
> * Nainstalujte službu mobility na počítače, které chcete migrovat.
> * Povolte replikaci.
> * Spusťte test migrace a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci do Azure.

> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. V těchto kurzech se v rámci možností používají jen výchozí možnosti a neuvádějí se všechny varianty nastavení ani všechny cesty. Podrobné pokyny najdete v tématu How to Azure Migrate.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

[Zkontrolujte](./agent-based-migration-architecture.md) architekturu migrace.

## <a name="prepare-azure"></a>Příprava Azure

Připravte Azure na migraci pomocí Azure Migrate: Migrace serveru.

**Úkol** | **Podrobnosti**
--- | ---
**Vytvoření projektu Azure Migrate** | Váš účet Azure potřebuje oprávnění Přispěvatel nebo Owner k [Vytvoření nového projektu](./create-manage-projects.md).
**Ověření oprávnění pro účet Azure** | Váš účet Azure potřebuje oprávnění k vytvoření virtuálního počítače a zápis na spravovaný disk Azure.


### <a name="assign-permissions-to-create-project"></a>Přiřadit oprávnění k vytvoření projektu

1. Na webu Azure Portal otevřete předplatné a vyberte **Řízení přístupu (IAM)** .
2. V části **kontrolovat přístup** Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění.
3. Měli byste mít oprávnění **Přispěvatel** nebo **Owner** .
    - Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem vašeho předplatného.
    - Pokud nejste vlastníkem předplatného, přidělte odpovídající roli ve spolupráci s vlastníkem.


### <a name="assign-azure-account-permissions"></a>Přiřazení oprávnění účtu Azure

Přiřaďte roli Přispěvatel virtuálních počítačů k účtu Azure. To poskytuje oprávnění k těmto akcím:

- Vytvoření virtuálního počítače ve vybrané skupině prostředků
- Vytvoření virtuálního počítače ve vybrané virtuální síti
- Zapište na spravovaný disk Azure. 

### <a name="create-an-azure-network"></a>Vytvoření sítě Azure

[Nastavte](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Azure Virtual Network (VNET). Při replikaci do Azure se vytvoří virtuální počítače Azure a připojí se k virtuální síti Azure, kterou určíte při nastavování migrace.

## <a name="prepare-for-migration"></a>Příprava migrace

Při přípravě na migraci fyzického serveru musíte ověřit nastavení fyzického serveru a připravit se na nasazení zařízení replikace.

### <a name="check-machine-requirements-for-migration"></a>Ověřit požadavky na počítač pro migraci

Zajistěte, aby počítače splňovaly požadavky na migraci do Azure. 

> [!NOTE]
> Při migraci fyzických počítačů Azure Migrate: Migrace serveru používá stejnou architekturu replikace jako zotavení po havárii na základě agenta ve službě Azure Site Recovery a některé součásti sdílejí stejný základ kódu. Část obsahu může odkazovat na dokumentaci Site Recovery.

1. [Ověřte](migrate-support-matrix-physical-migration.md#physical-server-requirements) požadavky na fyzický server.
2. Ověřte, že místní počítače, které se replikují do Azure, splňují [požadavky na virtuální počítače Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).
3. Před migrací do Azure jsou na virtuálních počítačích potřeba nějaké změny.
    - Pro některé operační systémy Azure Migrate provede tyto změny automaticky. 
    - Před zahájením migrace je důležité tyto změny provést. Pokud před provedením změny migrujete virtuální počítač, nemusí se virtuální počítač spustit v Azure. Zkontrolujte změny v [systému Windows](prepare-for-migration.md#windows-machines) a [Linux](prepare-for-migration.md#linux-machines) , které je třeba provést.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Příprava počítače na zařízení replikace

Azure Migrate: Migrace serveru používá pro replikaci počítačů do Azure zařízení replikace. Zařízení replikace spouští následující komponenty.

- **Konfigurační server**: konfigurační server koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat.
- **Procesový Server**: procesový server funguje jako brána replikace. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do účtu úložiště mezipaměti v Azure. 

Připravte se na nasazení zařízení následujícím způsobem:

- Připravíte počítač k hostování replikačního zařízení. [Zkontrolujte](migrate-replication-appliance.md#appliance-requirements) požadavky na počítač.
- Zařízení replikace používá MySQL. Projděte si [Možnosti](migrate-replication-appliance.md#mysql-installation) instalace MySQL na zařízení.
- Zkontrolujte adresy URL Azure vyžadované pro zařízení replikace pro přístup k [veřejným](migrate-replication-appliance.md#url-access) a [státním](migrate-replication-appliance.md#azure-government-url-access) cloudům.
- Zkontrolujte požadavky na přístup k [portu](migrate-replication-appliance.md#port-access) pro zařízení replikace.

> [!NOTE]
> Zařízení replikace by se nemělo nainstalovat na zdrojový počítač, který chcete replikovat, nebo na zařízení pro zjišťování a hodnocení Azure Migrate, které jste mohli nainstalovat dřív.

## <a name="set-up-the-replication-appliance"></a>Nastavení zařízení replikace

Prvním krokem migrace je nastavení zařízení replikace. Pokud chcete nastavit zařízení pro migraci fyzického serveru, Stáhněte si instalační soubor pro zařízení a pak ho spusťte na [počítači, který jste připravili](#prepare-a-machine-for-the-replication-appliance). Po instalaci zařízení ho zaregistrujete s Azure Migrate: Migrace serveru.


### <a name="download-the-replication-appliance-installer"></a>Stažení instalačního programu zařízení replikace

1. V Azure Migrate Project > **servery** v části **Azure Migrate: Migrace serveru** klikněte na **Vyhledat**.

    ![Vyhledání virtuálních počítačů](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. V nabídce **zjistit** počítače  >  **jsou vaše počítače virtualizované?** klikněte na **nevirtualizované/jiné**.
4. V části **cílová oblast** vyberte oblast Azure, do které chcete migrovat počítače.
5. Vyberte **potvrdit, že cílová oblast pro migraci je název regionu**.
6. Klikněte na **vytvořit prostředky**. Tím dojde k vytvoření trezoru Azure Site Recovery na pozadí.
    - Pokud jste už nastavili migraci pomocí Azure Migrate: Migrace serveru, možnost Target nejde nakonfigurovat, protože se předtím nastavily prostředky.    
    - Po kliknutí na toto tlačítko nemůžete změnit cílovou oblast tohoto projektu.
    - Všechny následné migrace jsou v této oblasti.

7. V nástroji chcete **nainstalovat nové replikační zařízení?** vyberte **nainstalovat zařízení replikace**.
9. V části **Stažení a instalace softwaru pro replikaci**, stažení instalačního programu zařízení a registračního klíče. Aby bylo možné zařízení zaregistrovat, musíte ho zadat. Klíč je platný po dobu pěti dní od jeho stažení.

    ![Stáhnout poskytovatele](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Zkopírujte instalační soubor zařízení a soubor klíče na počítač s Windows serverem 2016, který jste vytvořili pro dané zařízení.
11. Po dokončení instalace se Průvodce konfigurací zařízení spustí automaticky (můžete také spustit průvodce ručně pomocí zástupce cspsconfigtool, který se vytvoří na ploše zařízení). Na kartě Spravovat účty v průvodci můžete přidat podrobnosti o účtu, které se použijí pro nabízenou instalaci služby mobility. V tomto kurzu budeme ručně nainstalovat službu mobility na zdrojové virtuální počítače, aby se replikoval, takže vytvořte v tomto kroku fiktivní účet a pokračujte. Můžete zadat následující údaje pro vytvoření fiktivního účtu – "host" jako popisný název, "uživatelské jméno" a "heslo" jako heslo pro tento účet. Tento fiktivní účet budete používat ve fázi povolení replikace. 

12. Jakmile se zařízení po instalaci restartuje, vyberte v části **vyhledat počítače** nové zařízení v části **vybrat konfigurační server** a klikněte na **Dokončit registraci**. K dokončení registrace se provede několik koncových úkolů, které připravují zařízení replikace.

    ![Dokončit registraci](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Po dokončení registrace může trvat nějakou dobu, než se zjištěné počítače objeví v Azure Migrate: Migrace serveru. Po zjištění virtuálních počítačů se počet **zjištěných serverů** zvyšuje.

![Zjištěné servery](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalace služby Mobility

V počítačích, které chcete migrovat, je potřeba nainstalovat agenta služby mobility. Instalační programy agentů jsou k dispozici na zařízení replikace. Najdete správný instalační program a nainstalujete agenta na každý počítač, který chcete migrovat. Proveďte to následujícím způsobem:

1. Přihlaste se k zařízení replikace.
2. Přejděte na **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Vyhledejte instalační program pro operační systém a verzi počítače. Zkontrolujte [podporované operační systémy](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines). 
4. Zkopírujte instalační soubor do počítače, který chcete migrovat.
5. Ujistěte se, že máte přístupové heslo, které bylo vygenerováno při nasazení zařízení.
    - Uložte soubor do dočasného textového souboru v počítači.
    - Přístupové heslo můžete získat na zařízení replikace. Z příkazového řádku spusťte **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** pro zobrazení aktuálního přístupového hesla.
    - Neobnovujte heslo znovu. Tím dojde k přerušení připojení a bude nutné znovu zaregistrovat zařízení replikace.

> [!NOTE]
> V parametru */Platform* zadáte *VMware* , pokud migrujete virtuální počítače VMware nebo fyzické počítače.

### <a name="install-on-windows"></a>Instalace v systému Windows

1. Extrahujte obsah instalačního souboru do místní složky (například C:\Temp) na počítači následujícím způsobem:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Spusťte instalační program služby mobility:
    ```
   UnifiedAgent.exe /Role "MS" /Platform "VmWare" /Silent
    ```
3. Zaregistrujte agenta u zařízení replikace:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Instalace v systému Linux

1. Extrahujte obsah instalačního programu tarballu do místní složky (například/tmp/MobSvcInstaller) na počítači následujícím způsobem:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Spusťte skript instalačního programu:
    ```
    sudo ./install -r MS -v VmWare -q
    ```
3. Zaregistrujte agenta u zařízení replikace:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replikace počítačů

Teď vyberte počítače pro migraci. 

> [!NOTE]
> Můžete replikovat až 10 počítačů dohromady. Pokud potřebujete replikovat více, proveďte jejich replikaci současně v dávkách po 10.

1. V Azure Migrate Project > **servery** **Azure Migrate: Migrace serveru** klikněte na **replikovat**.

    ![Snímek obrazovky Azure Migrate-servery zobrazující tlačítko replikovat vybrané v Azure Migrate: Migrace serveru v části nástroje pro migraci.](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. V případě **replikace**> **Nastavení zdroje**  >  **jsou vaše počítače virtualizované?** vyberte **nevirtualizované/jiné**.
3. V části **místní zařízení** vyberte název zařízení Azure Migrate, které jste nastavili.
4. V části **procesový Server** vyberte název zařízení replikace.
6. V části **přihlašovací údaje hosta** vyberte během [instalace instalačního programu replikace](#download-the-replication-appliance-installer) prosím fiktivní účet, který jste dříve vytvořili, abyste službu mobility nainstalovali ručně (nabízená instalace není podporovaná). Pak klikněte na **Další: virtuální počítače**.   

    ![Snímek obrazovky karty nastavení zdroje na obrazovce replikace se zvýrazněným polem pověření hosta](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. V **Virtual Machines** v části **Import nastavení migrace z posouzení** ponechte výchozí nastavení **Ne, nastavení migrace určíte ručně**.
8. Ověřte každý virtuální počítač, který chcete migrovat. Pak klikněte na **Další: nastavení cíle**.

    ![Vybrat virtuální počítače](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. V části **Nastavení cíle** vyberte předplatné a cílovou oblast migrace a zadejte skupinu prostředků, ve které se po migraci budou nacházet virtuální počítače Azure.
10. V části **Virtuální síť** vyberte virtuální síť a podsíť Azure, ke kterým se po migraci připojí virtuální počítače Azure.
11. V **Možnosti dostupnost** vyberte:
    -  Zóna dostupnosti pro připnutí migrovaného počítače ke konkrétní zóně dostupnosti v oblasti Tuto možnost použijte k distribuci serverů, které tvoří aplikační vrstvu s více uzly napříč Zóny dostupnosti. Pokud vyberete tuto možnost, budete muset zadat zónu dostupnosti, která se má použít pro každý z vybraných počítačů na kartě Compute. Tato možnost je dostupná jenom v případě, že cílová oblast vybraná pro migraci podporuje Zóny dostupnosti
    -  Skupina dostupnosti umístí migrovaný počítač do skupiny dostupnosti. Vybraná cílová skupina prostředků musí mít jednu nebo víc skupin dostupnosti, aby bylo možné tuto možnost použít.
    - Není nutná žádná možnost redundance infrastruktury, pokud nepotřebujete žádnou z těchto konfigurací dostupnosti pro migrované počítače.
    
12. V **typ šifrování disku** vyberte:
    - Šifrování v klidovém případě pomocí klíče spravovaného platformou
    - Šifrování v klidovém případě pomocí klíče spravovaného zákazníkem
    - Dvojité šifrování pomocí klíčů spravovaných platformou a zákaznických klíčů

   > [!NOTE]
   > Pokud chcete replikovat virtuální počítače s CMK, musíte v cílové skupině prostředků [vytvořit sadu Disk Encryption](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) . Objekty pro nastavení šifrování disku – mapování Managed Disks na Key Vault obsahující CMK, který se má použít pro SSE.
  
13. V části **Zvýhodněné hybridní využití Azure**:

    - Vyberte **Ne**, pokud nechcete využít Zvýhodněné hybridní využití Azure. Potom klikněte na **Další**.
    - Vyberte **Ano**, pokud máte počítače s Windows Serverem s aktivním Software Assurance nebo předplatným Windows Serveru a u migrovaných počítačů chcete využít tuto výhodu. Potom klikněte na **Další**.

    ![Nastavení cíle](./media/tutorial-migrate-vmware/target-settings.png)

14. V části **COMPUTE** Zkontrolujte název virtuálního počítače, velikost, typ disku s operačním systémem a konfiguraci dostupnosti (Pokud jste zvolili v předchozím kroku). Virtuální počítače musí splňovat [požadavky Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Velikost virtuálního počítače**: Pokud používáte doporučení pro vyhodnocení, zobrazuje se v rozevíracím seznamu velikost virtuálního počítače doporučená velikost. Jinak Azure Migrate vybere velikost na základě nejbližší shody v předplatném Azure. Případně můžete velikost vybrat ručně v části **Velikost virtuálního počítače Azure**.
    - **Disk s operačním systémem**: zadejte operační systém (spouštěcí) disk pro virtuální počítač. Disk s operačním systémem je disk, který obsahuje spouštěcí zavaděč a instalační program operačního systému.
    - **Zóna dostupnosti**: Zadejte zónu dostupnosti, která se má použít.
    - **Skupina dostupnosti**: Určete skupinu dostupnosti, která se má použít.

![Nastavení výpočtů](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

15. V části **disky** určete, jestli se mají disky virtuálních počítačů replikovat do Azure, a v Azure vyberte typ disku (standardní disk SSD/HDD nebo Premium Managed Disks). Potom klikněte na **Další**.
    - Disky můžete z replikace vyloučit.
    - Pokud disky vyloučíte, po migraci nebudou na virtuálním počítači Azure. 

    ![Nastavení disku](./media/tutorial-migrate-physical-virtual-machines/disks.png)

16. V části **Kontrola a zahájení replikace** zkontrolujte nastavení a kliknutím na **Replikovat** spusťte počáteční replikaci serverů.

> [!NOTE]
> Nastavení replikace můžete aktualizovat kdykoli před spuštěním replikace, **Spravovat**  >  **replikační počítače**. Po spuštění replikace není možné nastavení změnit.

## <a name="track-and-monitor"></a>Sledování a sledování

- Po kliknutí na **replikace** se spustí úloha spustit replikaci. 
- Po úspěšném dokončení úlohy spuštění replikace začnou počítače počáteční replikaci do Azure.
- Po dokončení počáteční replikace se spustí rozdílová replikace. Přírůstkové změny na místních discích se pravidelně replikují na disky replik v Azure.


Stav úlohy můžete sledovat v oznámeních na portálu.

Stav replikace můžete sledovat kliknutím na **servery replikace** v **Azure Migrate: Migrace serveru**.
![Monitorování replikace](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Spuštění testu migrace


Po zahájení rozdílové replikace můžete spustit testovací migraci pro virtuální počítače před spuštěním úplné migrace do Azure. Důrazně doporučujeme, abyste to pro každý počítač provedli aspoň jednou, a teprve potom ho migrujete.

- Při spuštění testu migrace proběhne kontrola, že migrace bude fungovat podle očekávání, aniž by to ovlivnilo místní počítače, které zůstávají v provozu, a pokračuje v replikaci. 
- Testovací migrace simuluje migraci tím, že vytvoří virtuální počítač Azure pomocí replikovaných dat (obvykle se migruje na virtuální síť, která není v produkčním prostředí, ve vašem předplatném Azure).
- Replikovaný virtuální počítač Azure můžete použít k ověření migrace, provádění testování aplikace a řešení všech problémů před úplnou migrací.

Proveďte migraci testu následujícím způsobem:


1. V Azure Migrate **cíle migrace**  >  na **servery**  >  **: Migrace serveru** klikněte na **test migrovaných serverů**.

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

1. V Azure Migrate Project > **servery**  >  **Azure Migrate: Migrace serveru** klikněte na **replikace serverů**.

    ![Replikace serverů](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. V části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač a vyberte **Migrovat**.
3. V   >  **nástroji migrovat vypínání virtuálních počítačů a provádění plánované migrace bez ztráty dat** vyberte **Ano**  >  .
    - Pokud virtuální počítač nechcete vypnout, vyberte **Ne**.
    
    Poznámka: v případě migrace fyzického serveru doporučujeme, aby se aplikace v rámci okna migrace připnula (neumožní aplikacím přijmout žádná připojení) a pak iniciovat migraci (Server musí být spuštěný, takže zbývající změny se dají synchronizovat) před dokončením migrace.

4. Pro virtuální počítač se spustí úloha migrace. Tuto úlohu můžete sledovat pomocí oznámení Azure.
5. Po dokončení úlohy můžete virtuální počítač zobrazit a spravovat na stránce **Virtuální počítače**.

## <a name="complete-the-migration"></a>Dokončete migraci

1. Po dokončení migrace klikněte pravým tlačítkem na virtuální počítač > **zastavit migraci**. Provede následující akce:
    - Zastaví replikaci místního počítače.
    - Odebere počítač z počtu **replikačních serverů** v Azure Migrate: Migrace serveru.
    - Vyčistí informace o stavu replikace pro tento počítač.
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
    - Zvažte nasazení služby [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), která bude monitorovat využití prostředků a útratu.


## <a name="next-steps"></a>Další kroky

Prozkoumejte [cestu k migraci do cloudu](/azure/architecture/cloud-adoption/getting-started/migrate) v rozhraní Azure cloudu pro přijetí.
