---
title: Zjišťování, vyhodnocení a migrace instancí virtuálních počítačů Google Cloud Platform (GCP) do Azure
description: Tento článek popisuje, jak migrovat virtuální počítače s GCP do Azure pomocí Azure Migrate.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 9965557115206cd512450d3411a70390f2249153
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713496"
---
# <a name="discover-assess-and-migrate-google-cloud-platform-gcp-vms-to-azure"></a>Zjišťování, vyhodnocení a migrace virtuálních počítačů Google Cloud Platform (GCP) do Azure

V tomto kurzu se dozvíte, jak zjišťovat, hodnotit a migrovat virtuální počítače s Google Cloud Platform (GCP) do virtuálních počítačů Azure pomocí Azure Migrate: posouzení serveru a Azure Migrate: nástroje pro migraci serveru.

V tomto kurzu se naučíte, jak:
> [!div class="checklist"]
>
> * Ověřte předpoklady pro migraci.
> * Příprava prostředků Azure pomocí Azure Migrate: Migrace serveru Nastavte oprávnění pro účet a prostředky Azure pro práci s Azure Migrate.
> * Připravte instance virtuálních počítačů GCP pro migraci.
> * Přidejte nástroj Azure Migrate: Server pro migraci do centra Azure Migrate.
> * Nastavte zařízení replikace a nasaďte konfigurační server.
> * Nainstalujte službu mobility na virtuální počítače s GCP, které chcete migrovat.
> * Povolte replikace pro virtuální počítače.
> * Sledovat a monitorovat stav replikace. 
> * Spusťte test migrace a ujistěte se, že vše funguje podle očekávání.
> * Spusťte úplnou migraci do Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="discover-and-assess"></a>Zjišťování a vyhodnocení

Před migrací na Azure doporučujeme provést vyhodnocení pro zjišťování a migraci virtuálních počítačů. Toto posouzení pomáhá zajistit správnou velikost virtuálních počítačů s GCP pro migraci do Azure a odhad potenciálních nákladů na běh Azure.

Proveďte vyhodnocení následujícím způsobem:

1. Postupujte podle [kurzu](./tutorial-discover-gcp.md) a nastavte Azure a připravte si virtuální počítače s GCP pro posouzení. Poznámky:

    - Azure Migrate používá ověřování hesla při zjišťování instancí virtuálních počítačů GCP. Instance GCP ve výchozím nastavení nepodporují ověřování hesla. Než budete moct zjistit, musíte povolit ověřování heslem.
        - U počítačů s Windows povolte port WinRM 5985 (HTTP). To umožňuje vzdálené volání rozhraní WMI.
        - Pro počítače se systémem Linux:
            1. Přihlaste se ke každému počítači se systémem Linux.
            2. Otevřete sshd_config soubor: VI/etc/ssh/sshd_config
            3. V souboru vyhledejte řádek **PasswordAuthentication** a změňte hodnotu na **Ano**.
            4. Uložte soubor a zavřete ho. Restartujte službu SSH.
    - Pokud ke zjištění virtuálních počítačů se systémem Linux používáte uživatele root, ujistěte se, že se na virtuálních počítačích povoluje přihlašovací jméno uživatele root.
        1. Přihlaste se ke každému počítači se systémem Linux
        2. Otevřete sshd_config soubor: VI/etc/ssh/sshd_config
        3. V souboru vyhledejte řádek **PermitRootLogin** a změňte hodnotu na **Ano**.
        4. Uložte soubor a zavřete ho. Restartujte službu SSH.

2. Pak postupujte podle tohoto [kurzu](./tutorial-assess-gcp.md) a nastavte Azure Migrate projekt a zařízení pro zjišťování a vyhodnocení vašich virtuálních počítačů s GCP.

I když doporučujeme, abyste si vyzkoušeli posouzení, provádění posouzení není povinným krokem, aby bylo možné migrovat virtuální počítače.



## <a name="prerequisites"></a>Požadavky 

- Zajistěte, aby virtuální počítače s GCP, které chcete migrovat, používaly podporovanou verzi operačního systému. Virtuální počítače s GCP se považují za účely migrace jako fyzické. Přečtěte si [podporované operační systémy a verze jádra](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) pro pracovní postup migrace fyzického serveru. Ke kontrole verzí operačního systému a jádra pro virtuální počítače se systémem Linux můžete použít standardní příkazy, jako je *hostnamectl* nebo *uname-a* .  Doporučujeme provést test, abyste ověřili, jestli virtuální počítač funguje podle očekávání, než budete pokračovat v rámci aktuální migrace.
- Zajistěte, aby virtuální počítače s GCP splňovaly [podporované konfigurace](./migrate-support-matrix-physical-migration.md#physical-server-requirements) pro migraci do Azure.
- Ověřte, že virtuální počítače s GCP, které se replikují do Azure, splňují [požadavky na virtuální počítače Azure.](./migrate-support-matrix-physical-migration.md#azure-vm-requirements)
- Před migrací do Azure jsou na virtuálních počítačích potřeba nějaké změny.
    - Pro některé operační systémy Azure Migrate provede tyto změny automaticky.
    - Před zahájením migrace je důležité tyto změny provést. Pokud před provedením změny migrujete virtuální počítač, nemusí se virtuální počítač spustit v Azure.
Zkontrolujte změny v [systému Windows](prepare-for-migration.md#windows-machines) a [Linux](prepare-for-migration.md#linux-machines) , které je třeba provést.

### <a name="prepare-azure-resources-for-migration"></a>Příprava prostředků Azure pro migraci

Připravte Azure pro migraci pomocí Azure Migrate: Server Migration Tool.

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

[Nastavte](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Azure Virtual Network (VNET). Při replikaci do Azure se virtuální počítače Azure, které jsou vytvořeny, připojí k virtuální síti Azure, kterou určíte při nastavování migrace.

## <a name="prepare-gcp-instances-for-migration"></a>Příprava instancí GCP na migraci

K přípravě na migraci GCP do Azure je potřeba připravit a nasadit replikační zařízení pro migraci.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Příprava počítače na zařízení replikace

Azure Migrate: Migrace serveru používá pro replikaci počítačů do Azure zařízení replikace. Zařízení replikace spouští následující komponenty.

- **Konfigurační server**: konfigurační server koordinuje komunikaci mezi virtuálními počítači GCP a Azure a spravuje replikaci dat.
- **Procesový Server**: procesový server funguje jako brána replikace. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do účtu úložiště mezipaměti v Azure.

Připravte se na nasazení zařízení následujícím způsobem:

- Nastavte samostatný virtuální počítač GCP pro hostování replikačního zařízení. V této instanci musí být spuštěný systém Windows Server 2012 R2 nebo Windows Server 2016. [Zkontrolujte](./migrate-replication-appliance.md#appliance-requirements) požadavky na hardware, software a síť pro dané zařízení.
- Zařízení by nemělo být nainstalované na zdrojovém virtuálním počítači, který chcete replikovat, nebo na zařízení pro zjišťování a hodnocení Azure Migrate, které jste mohli nainstalovat dřív. Měla by být nasazená na jiném virtuálním počítači.
- GCP virtuální počítače, které se mají migrovat, by měly mít síťovou řadu, která by měla být v zařízení replikace. Pokud to chcete povolit, nakonfigurujte potřebná pravidla brány firewall. Doporučuje se, aby bylo zařízení replikace nasazené ve stejné VPC síti jako zdrojové virtuální počítače, které se mají migrovat. Pokud musí být zařízení replikace v jiném VPC, musí se VPCs připojit prostřednictvím partnerského vztahu VPC.
- Zdrojové virtuální počítače s GCP komunikují se zařízením replikace na portech HTTPS 443 (orchestrace řídicích kanálů) a TCP 9443 (přenos dat) pro správu replikace a přenos dat replikace. Zařízení replikace v nástroji dále orchestruje a odesílá data replikace do Azure přes odchozí port HTTPS 443. Pokud chcete tato pravidla nakonfigurovat, upravte pravidla příchozí/odchozí skupiny zabezpečení o příslušné porty a zdrojové informace IP.

   ![Pravidla brány firewall GCP](./media/tutorial-migrate-gcp-virtual-machines/gcp-firewall-rules.png)
     
 
   ![Upravit pravidla brány firewall](./media/tutorial-migrate-gcp-virtual-machines/gcp-edit-firewall-rule.png)

- Zařízení replikace používá MySQL. Projděte si [Možnosti](migrate-replication-appliance.md#mysql-installation) instalace MySQL na zařízení.
- Zkontrolujte adresy URL Azure vyžadované pro zařízení replikace pro přístup k [veřejným](migrate-replication-appliance.md#url-access) a [státním](migrate-replication-appliance.md#azure-government-url-access) cloudům.

## <a name="set-up-the-replication-appliance"></a>Nastavení zařízení replikace

Prvním krokem migrace je nastavení zařízení replikace. Pokud chcete nastavit zařízení pro migraci virtuálních počítačů s GCP, musíte si stáhnout instalační soubor pro dané zařízení a pak ho spustit na [virtuálním počítači, který jste připravili](#prepare-a-machine-for-the-replication-appliance).

### <a name="download-the-replication-appliance-installer"></a>Stažení instalačního programu zařízení replikace

1. V Azure Migrate Project > **servery** v části **Azure Migrate: Migrace serveru** klikněte na **Vyhledat**.

    ![Vyhledání virtuálních počítačů](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. V nabídce **zjistit** počítače  >  **jsou vaše počítače virtualizované?** klikněte na **nevirtualizované/jiné**.
3. V části **cílová oblast** vyberte oblast Azure, do které chcete migrovat počítače.
4. Vyberte **potvrdit, že cílová oblast migrace <název oblasti>**.
5. Klikněte na **vytvořit prostředky**. Tím dojde k vytvoření trezoru Azure Site Recovery na pozadí.
    - Pokud jste už nastavili migraci pomocí migrace serveru Azure Migrate, možnost Target nejde nakonfigurovat, protože se předtím nastavily prostředky.
    - Po kliknutí na toto tlačítko nemůžete změnit cílovou oblast tohoto projektu.
    - Chcete-li migrovat virtuální počítače do jiné oblasti, budete muset vytvořit nový nebo odlišný Azure Migrate projekt. 
    > [!NOTE]
    > Pokud jste při vytvoření projektu Azure Migrate jako metodu připojení vybrali privátní koncový bod, bude pro připojení privátního koncového bodu nakonfigurované taky trezor Recovery Services. Ujistěte se, že jsou privátní koncové body dosažitelné ze zařízení replikace: [ **Další informace**](how-to-use-azure-migrate-with-private-endpoints.md#troubleshoot-network-connectivity)

6. V nástroji chcete **nainstalovat nové replikační zařízení?** vyberte **nainstalovat zařízení replikace**.
7. V části **Stažení a instalace softwaru pro replikaci**, stažení instalačního programu zařízení a registračního klíče. Aby bylo možné zařízení zaregistrovat, musíte ho zadat. Klíč je platný po dobu pěti dní od jeho stažení.

    ![Stáhnout poskytovatele](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Zkopírujte instalační soubor zařízení a soubor klíče do virtuálního počítače s Windows serverem 2016 nebo Windows Server 2012 GCP, který jste vytvořili pro zařízení replikace.
9. Spusťte instalační soubor zařízení replikace, jak je popsáno v následujícím postupu.  
    9.1. Na stránce **Before You Begin** (Než začnete) vyberte **Install the configuration server and process server** (Nainstalovat konfigurační server a procesový server) a potom vyberte **Next** (Další).   
    9,2 v **licenci k softwaru třetích stran** Vyberte Přijímám **licenční smlouvu třetí strany** a pak vyberte **Další**.   
    9,3 v části **registrace** vyberte **Procházet** a pak přejděte do umístění, kam umístíte soubor registračního klíče trezoru. Vyberte **Další**.  
    9,4 v **nastavení Internetu** vyberte **připojit k Azure Site Recovery bez proxy server** a pak vyberte **Další**.  
    9,5 stránka **Kontrola předpokladů** spouští kontrolu několika položek. Po dokončení vyberte **Next** (Další).  
    9,6 v **konfiguraci MySQL** zadejte heslo pro databázi MySQL a pak vyberte **Další**.  
    9,7 v **podrobnostech o prostředí** vyberte **ne**. Nemusíte chránit své virtuální počítače. Pak vyberte **Další**.  
    9,8 v **umístění instalace** přijměte výchozí nastavení kliknutím na tlačítko **Další** .  
    9,9 ve **výběru sítě** přijměte výchozí nastavení výběrem **Další** .  
    9,10 v **souhrnu** vyberte **instalovat**.   
    9,11 **průběh instalace** zobrazuje informace o procesu instalace. Po dokončení vyberte **Finish** (Dokončit). Zobrazí se okno se zprávou o restartování. Vyberte **OK**.   
    9,12, okno zobrazí zprávu o heslu připojení ke konfiguračnímu serveru. Zkopírujte heslo do schránky a uložte ho do dočasného textového souboru na zdrojových virtuálních počítačích. Toto heslo budete potřebovat později během procesu instalace služby mobility.
10. Po dokončení instalace se Průvodce konfigurací zařízení spustí automaticky (můžete také spustit průvodce ručně pomocí zástupce cspsconfigtool, který se vytvoří na ploše zařízení). Na kartě Spravovat účty v průvodci můžete přidat podrobnosti o účtu, které se použijí pro nabízenou instalaci služby mobility. V tomto kurzu budeme ručně nainstalovat službu mobility na zdrojové virtuální počítače, aby se replikoval, takže vytvořte v tomto kroku fiktivní účet a pokračujte. Můžete zadat následující údaje pro vytvoření fiktivního účtu – "host" jako popisný název, "uživatelské jméno" a "heslo" jako heslo pro tento účet. Tento fiktivní účet budete používat ve fázi povolení replikace. 
11. Jakmile se zařízení po instalaci restartuje, vyberte v části **vyhledat počítače** nové zařízení v části **vybrat konfigurační server** a klikněte na **Dokončit registraci**. K dokončení registrace se provede několik koncových úkolů, které připravují zařízení replikace.

    ![Dokončit registraci](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>Instalace služby Mobility

Na zdrojovém virtuálním počítači GCP musí být nainstalovaný agent služby mobility, který se má migrovat. Instalační programy agentů jsou k dispozici na zařízení replikace. Najdete správný instalační program a nainstalujete agenta na každý počítač, který chcete migrovat. Postupujte takto:

1. Přihlaste se k zařízení replikace.
2. Přejděte na **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Najděte instalační program pro zdrojový virtuální počítač s GCP a verzi operačního systému. Zkontrolujte [podporované operační systémy](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines).
4. Zkopírujte instalační soubor na zdrojový virtuální počítač GCP, který chcete migrovat.
5. Ujistěte se, že máte uložený textový soubor s hesly, který byl vytvořen při instalaci zařízení replikace.
    - Pokud jste zapomněli heslo uložit, můžete v tomto kroku Zobrazit přístupové heslo k zařízení replikace. Z příkazového řádku spusťte **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** pro zobrazení aktuálního přístupového hesla.
    - Teď toto heslo zkopírujte do schránky a uložte ho do dočasného textového souboru na zdrojovém virtuálním počítači.

### <a name="installation-guide-for-windows-gcp-vms"></a>Průvodce instalací pro virtuální počítače s Windows GCP

1. Extrahujte obsah instalačního souboru do místní složky (například C:\Temp) na virtuálním počítači s GCP následujícím způsobem:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```  

2. Spusťte instalační program služby mobility:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```  

3. Zaregistrujte agenta u zařízení replikace:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="installation-guide-for-linux-gcp-vms"></a>Průvodce instalací pro virtuální počítače se systémem Linux GCP

1. Extrahujte obsah instalačního programu tarballu do místní složky (například/tmp/MobSvcInstaller) na virtuálním počítači GCP, a to následujícím způsobem:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```  

2. Spusťte skript instalačního programu:
    ```
    sudo ./install -r MS -q
    ```  

3. Zaregistrujte agenta u zařízení replikace:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="enable-replication-for-gcp-vms"></a>Povolení replikace pro virtuální počítače s GCP

> [!NOTE]
> Prostřednictvím portálu můžete přidat až 10 virtuálních počítačů pro replikaci najednou. Pokud chcete replikovat víc virtuálních počítačů najednou, můžete je přidat v dávkách po 10.

1. V Azure Migrate Project > **servery** **Azure Migrate: Migrace serveru** klikněte na **replikovat**.

    ![Replikace virtuálních počítačů](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. V případě **replikace**> **Nastavení zdroje**  >  **jsou vaše počítače virtualizované?** vyberte **nevirtualizované/jiné**.
3. V části **místní zařízení** vyberte název zařízení Azure Migrate, které jste nastavili.
4. V části **procesový Server** vyberte název zařízení replikace. 
5. V části **přihlašovací údaje hosta** vyberte během [instalace instalačního programu replikace](#download-the-replication-appliance-installer) prosím fiktivní účet, který jste dříve vytvořili, abyste službu mobility nainstalovali ručně (nabízená instalace není podporovaná). Pak klikněte na **Další: virtuální počítače**.   
 
    ![Replikovat nastavení](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. V **Virtual Machines** v části **Import nastavení migrace z posouzení** ponechte výchozí nastavení **Ne, nastavení migrace určíte ručně**.
7. Ověřte každý virtuální počítač, který chcete migrovat. Pak klikněte na **Další: nastavení cíle**.

    ![Vybrat virtuální počítače](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. V části **Nastavení cíle** vyberte předplatné a cílovou oblast migrace a zadejte skupinu prostředků, ve které se po migraci budou nacházet virtuální počítače Azure.
9. V části **Virtuální síť** vyberte virtuální síť a podsíť Azure, ke kterým se po migraci připojí virtuální počítače Azure.  
10. V části  **účet úložiště mezipaměti** ponechte výchozí možnost používat účet úložiště mezipaměti, který se automaticky vytvoří pro projekt. Pokud chcete zadat jiný účet úložiště, který se použije jako účet úložiště mezipaměti pro replikaci, použijte rozevírací nabídku. <br/> 
    > [!NOTE]
    >
    > - Pokud jste vybrali možnost privátní koncový bod jako metodu připojení pro Azure Migrate projekt, udělte trezoru Recovery Services přístup k účtu úložiště mezipaměti. [**Další informace**](how-to-use-azure-migrate-with-private-endpoints.md#grant-access-permissions-to-the-recovery-services-vault)
    > - Pro replikaci pomocí ExpressRoute se soukromým partnerským vztahem vytvořte privátní koncový bod pro účet úložiště mezipaměti. [**Další informace**](how-to-use-azure-migrate-with-private-endpoints.md#create-a-private-endpoint-for-the-storage-account-optional) 
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

## <a name="track-and-monitor-replication-status"></a>Sledovat a monitorovat stav replikace

- Po kliknutí na **replikace** se spustí úloha spustit replikaci.
- Po úspěšném dokončení úlohy spuštění replikace začnou virtuální počítače svoji počáteční replikaci do Azure.
- Po dokončení počáteční replikace se spustí rozdílová replikace. Přírůstkové změny disků virtuálních počítačů s GCP se pravidelně replikují na disky replik v Azure.

Stav úlohy můžete sledovat v oznámeních na portálu.

Stav replikace můžete sledovat kliknutím na **servery replikace** v **Azure Migrate: Migrace serveru**.  

![Monitorování replikace](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Spuštění testu migrace

Po zahájení rozdílové replikace můžete spustit testovací migraci pro virtuální počítače před spuštěním úplné migrace do Azure. Testovací migrace se důrazně doporučuje a poskytuje možnost zjistit případné problémy a opravit je před tím, než budete pokračovat ve skutečné migraci. Doporučuje se to pro každý virtuální počítač udělat aspoň jednou, než ho migrujete.

- Při spuštění testu se ověří, že migrace bude fungovat podle očekávání, aniž by to ovlivnilo virtuální počítače s GCP, které zůstávají v provozu, a pokračuje v replikaci.
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


## <a name="migrate-gcp-vms"></a>Migrace virtuálních počítačů s GCP

Až ověříte, že migrace testu funguje podle očekávání, můžete migrovat virtuální počítače GCP.

1. V Azure Migrate Project > **servery**  >  **Azure Migrate: Migrace serveru** klikněte na **replikace serverů**.

    ![Replikace serverů](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. V části **Replikace počítačů** klikněte pravým tlačítkem na virtuální počítač a vyberte **Migrovat**.
3. V   >  **nástroji migrovat vypínání virtuálních počítačů a provádění plánované migrace bez ztráty dat** vyberte **Ano**  >  .
    - Pokud nechcete virtuální počítač vypnout, vyberte **ne**.
4. Pro virtuální počítač se spustí úloha migrace. Stav úlohy můžete zobrazit kliknutím na ikonu zvonku oznámení v pravém horním rohu stránky portálu nebo na stránce úlohy nástroje pro migraci serveru (klikněte na přehled na dlaždici nástroje > v nabídce vlevo vyberte úlohy).
5. Po dokončení úlohy můžete virtuální počítač zobrazit a spravovat na stránce Virtuální počítače.

### <a name="complete-the-migration"></a>Dokončete migraci

1. Po dokončení migrace klikněte pravým tlačítkem na virtuální počítač > **zastavit migraci**. Provede následující akce:
    - Zastaví replikaci pro virtuální počítač GCP.
    - Odebere virtuální počítač GCP z počtu **replikačních serverů** v Azure Migrate: Migrace serveru.
    - Vyčistí informace o stavu replikace pro virtuální počítač.
2. Na migrované počítače nainstalujte agenta Azure VM pro [Windows](../virtual-machines/extensions/agent-windows.md) nebo [Linux](../virtual-machines/extensions/agent-linux.md) .
3. Proveďte všechna vylepšení aplikace po migraci, například aktualizujte databázové připojovací řetězce a nakonfigurujte webové servery.
4. U migrované aplikace, která teď běží v Azure, proveďte finální akceptační testování aplikace a migrace.
5. Vyjmutí provozu do migrované instance virtuálního počítače Azure
6. Aktualizujte veškerou interní dokumentaci tak, aby obsahovala nová umístění a IP adresy virtuálních počítačů Azure. 




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



## <a name="troubleshooting--tips"></a>Řešení potíží a tipy

**Otázka:** V seznamu zjištěných serverů pro migraci se mi nedá Zobrazit můj virtuální počítač s GCP   
**Odpověď:** Ověřte, jestli vaše zařízení replikace splňuje požadavky. Ujistěte se, že je na zdrojovém virtuálním počítači nainstalovaný agent mobility, který se má migrovat a který je registrovaný pro konfigurační server. Ověřte pravidla brány firewall a povolte síťovou cestu mezi zařízením replikace a zdrojovými virtuálními počítači GCP.  

**Otázka:** Návody zjistit, jestli se můj virtuální počítač úspěšně migrovali   
**Odpověď:** Po migraci můžete virtuální počítač zobrazit a spravovat na stránce Virtual Machines. Připojte se k migrovanému virtuálnímu počítači a ověřte.  

**Otázka:** Nedaří se mi importovat virtuální počítače pro migraci z dříve vytvořených výsledků posouzení serveru.   
**Odpověď:** V současné době nepodporujeme import vyhodnocení pro tento pracovní postup. Jako alternativní řešení můžete vyexportovat posouzení a pak ručně vybrat doporučení pro virtuální počítač v kroku povolení replikace.
  
**Otázka:** Při pokusu o zjištění virtuálních počítačů s GCP se zobrazí chyba "Nepodařilo se načíst GUID systému BIOS".   
**Odpověď:** Pro ověřování použijte přihlašovací jméno uživatele root, nikoli žádného pseudo uživatele. Pokud nemůžete použít kořenového uživatele, ujistěte se, že jsou pro uživatele nastavené požadované možnosti podle pokynů uvedených v [matici podpory](migrate-support-matrix-physical.md#physical-server-requirements). Také si přečtěte podporované operační systémy pro virtuální počítače s GCP.  

**Otázka:** Můj stav replikace neprobíhá.   
**Odpověď:** Ověřte, jestli vaše zařízení replikace splňuje požadavky. Ujistěte se, že jste povolili požadované porty na zařízení replikace TCP port 9443 a HTTPS 443 pro přenos dat. Ujistěte se, že nejsou k dispozici žádné zastaralé duplicitní verze replikačního zařízení připojeného ke stejnému projektu.   

**Otázka:** Nedaří se mi zjistit GCP instance pomocí Azure Migrate z důvodu stavového kódu HTTP 504 ze vzdálené služby pro správu systému Windows.    
**Odpověď:** Nezapomeňte si projít požadavky na zařízení migrace do Azure a potřeby přístupu URL. Zajistěte, aby se registrace zařízení neblokovala nastavením proxy.

**Otázka:** Musím před migrací virtuálních počítačů s GCP do Azure udělat nějaké změny   
**Odpověď:** Tyto změny možná budete muset udělat před migrací virtuálních počítačů s EC2 do Azure:

- Pokud pro zřizování virtuálních počítačů používáte Cloud-init, možná budete chtít zakázat Cloud-init na virtuálním počítači, abyste ho mohli replikovat do Azure. Kroky zřizování, které provádí Cloud-init na virtuálním počítači, možná GCP konkrétní a po migraci do Azure nebudou platné.  
- Před migrací do Azure si přečtěte část [požadavky](#prerequisites) , abyste zjistili, jestli jsou v operačním systému potřebné nějaké změny.
- Vždycky doporučujeme před konečnou migrací spustit migraci testu.  

## <a name="next-steps"></a>Další kroky

Prozkoumejte [cestu k migraci do cloudu](/azure/architecture/cloud-adoption/getting-started/migrate) v rozhraní Azure cloudu pro přijetí.