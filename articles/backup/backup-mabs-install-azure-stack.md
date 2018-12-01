---
title: Instalace Azure Backup serveru v Azure stacku | Dokumentace Microsoftu
description: Použití Azure Backup serveru k ochraně nebo zálohování úloh ve službě Azure Stack.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: Azure backup serveru. Chraňte úlohy; zálohování úloh
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 6/5/2018
ms.author: markgal
ms.openlocfilehash: 14379d82b4b60c0ea555388ac61c9c7b8fbe9e4f
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721425"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Instalace Azure Backup Serveru v Azure Stacku

Tento článek vysvětluje, jak nainstalovat Azure Backup serveru ve službě Azure Stack. S Azure Backup Server dokáže chránit infrastruktury jako služby (IaaS) úloh, jako jsou virtuální počítače spuštěné ve službě Azure Stack. Výhodou použití Azure Backup serveru k ochraně vašich úloh je, že všechny úlohy ochrany můžete spravovat z jediné konzoly.

> [!NOTE]
> Další informace o funkcích zabezpečení, naleznete [zabezpečení Azure Backup nabízí dokumentaci](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Systém ochrany Azure Backup Serveru
Azure Backup Server chrání následující úlohy virtuálních počítačů Azure Stack.

| Chráněný zdroj dat. | Ochrana a obnovení |
| --------------------- | ----------------------- |
| Půlroční kanál Windows Server částečně - Datacenter nebo Enterprise/Standard | Svazky, soubory a složky |
| Windows Server 2016 - Datacenter nebo Enterprise/Standard | Svazky, soubory a složky |
| Windows Server 2012 R2 – Datacenter nebo Enterprise/Standard | Svazky, soubory a složky |
| Windows Server 2012 – Datacenter/Entprise/Standard | Svazky, soubory a složky |
| Windows Server 2008 R2 – Datacenter nebo Enterprise/Standard | Svazky, soubory a složky |
| SQL Server 2016 | Databáze |
| SQL Server 2014 | Databáze |
| SQL Server 2012 SP1 | Databáze |
| SharePoint 2016 | Farma, databáze, front-endu, webový server |
| SharePoint 2013 | Farma, databáze, front-endu, webový server |
| SharePoint 2010 | Farma, databáze, front-endu, webový server |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Požadavky na prostředí Azure Backup serveru

Při instalaci Azure Backup serveru ve vašem prostředí Azure Stack, zvažte doporučení v této části. Azure Backup Server instalační program zkontroluje, že vaše prostředí obsahuje nezbytné požadavky, ale ušetříte čas při přípravě před instalací.

### <a name="determining-size-of-virtual-machine"></a>Určení velikosti virtuálního počítače
Ke spuštění Azure Backup serveru na virtuálním počítači Azure Stack, použijte velikosti A2 nebo větší. Pomoc při výběru velikosti virtuálního počítače, stáhněte si [kalkulačky velikost virtuálního počítače Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuální sítě na virtuálních počítačích Azure Stack
Všechny virtuální počítače použité v úloze Azure Stack musí patřit do stejné virtuální síti Azure a předplatné Azure.

### <a name="azure-backup-server-vm-performance"></a>Výkon Azure Backup Server VM
Pokud sdílené s jinými virtuálními počítači, účet úložiště, velikosti a IOPS omezení dopad výkonu virtuálních počítačů Azure Backup Server. Z tohoto důvodu by měla používat samostatný účet úložiště pro virtuální počítač Azure Backup serveru. Azure Backup agenta spuštěného v Azure Backup Server potřebuje dočasné úložiště pro:
- vlastní použití (umístění mezipaměti),
- data obnovená z cloudu (místní pracovní plocha)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurace úložiště na dočasném disku Azure Backup
Každý virtuální počítač Azure Stack je součástí úložiště na dočasném disku, který je k dispozici uživateli jako svazek `D:\`. Místní pracovní oblasti vyžadované nástrojem Azure Backup můžete nakonfigurovat uložená v `D:\`, a umístění mezipaměti můžete umístit na `C:\`. Tímto způsobem je potřeba carved od datových disků připojených k virtuálnímu počítači Azure Backup serveru žádné úložiště.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Ukládání zálohovaných dat na místním disku a v Azure
Azure Backup Server ukládá zálohovaná data na disky Azure připojené k virtuálnímu počítači pro obnovení. Jakmile disky a prostoru úložiště jsou připojené k virtuálnímu počítači, Azure Backup Server spravuje úložiště za vás. Úložiště zálohování dat závisí na počtu a velikosti disků připojených ke každému [virtuálním počítači Azure Stack](../azure-stack/user/azure-stack-storage-overview.md). Jednotlivé velikosti virtuálního počítače Azure Stack je maximální počet disků, které lze připojit k virtuálnímu počítači. Například je A2 čtyři disky. A3 je osm disky. A4 je 16 disků. Znovu velikosti a počtu disků, určuje fondu celkový úložiště záloh.

> [!IMPORTANT]
> Měli byste **není** zachovat provozní obnovení (backup) data na disky připojené k Azure Backup Server více než pět dní.
>

Ukládání zálohovaných dat do Azure sníží infrastruktury zálohování ve službě Azure Stack. Pokud jsou data starší než pět dní, by měl být uložený v Azure.

Pokud chcete ukládat zálohovaná data v Azure, vytvořte nebo použijte trezor služby Recovery Services. Při přípravě k zálohování úloh Azure Backup serveru můžete [konfigurace trezoru služby Recovery Services](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Po nakonfigurování pokaždé, když se spouští úloha zálohování, bodem obnovení se vytvoří v trezoru. Každý trezor služby Recovery Services obsahuje až 9999 bodů obnovení. V závislosti na počtu vytváření bodů obnovení a jak dlouho jsou uchovávány můžete zachovat zálohovaná data po mnoho let. Například může vytvořit měsíční bodů obnovení a udrželi si je pět let.
 
### <a name="scaling-deployment"></a>Škálování nasazení
Pokud chcete změnit měřítko nasazení, máte následující možnosti:
  - Vertikální navýšení kapacity – zvýšíte velikost virtuálního počítače Azure Backup serveru z řady pro řadu D a místní úložiště [podle pokynů virtuálním počítači Azure Stack](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Přesměrování zpracování dat – odešlete starší data do Azure a zachovat pouze nejnovější data v úložišti připojeném k serveru Azure Backup.
  - Horizontální navýšení kapacity – přidat další servery pro zálohování Azure k ochraně úloh.

### <a name="net-framework"></a>.NET Framework

Rozhraní .NET framework 3.5 SP1 nebo novější musí být nainstalována na virtuálním počítači.

### <a name="joining-a-domain"></a>Připojení k doméně

Virtuální počítač Azure Backup serveru musí být připojené k doméně. Uživatel domény s oprávněními správce, musíte nainstalovat Azure Backup serveru na virtuálním počítači.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Pomocí virtuálních počítačů IaaS v Azure stacku

Při výběru serveru Azure Backup serveru, spusťte s Galerie imagí Windows serveru 2012 R2 Datacenter nebo Windows Server 2016 Datacenter. V článku [vytvořit svůj první virtuální počítač s Windows na webu Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), kurz pro zahájení práce s doporučenou virtuálního počítače. Doporučená minimální požadavky pro virtuální počítač (VM) serveru by měly být: A2 standardní dvě jádra a 3.5 GB paměti RAM.

Ochrana zatížení pomocí Azure Backup serveru má mnoho detailů. V článku [instalace aplikace DPM jako virtuální počítač Azure](https://technet.microsoft.com/library/jj852163.aspx), pomáhá popisují tyto drobné rozdíly. Před nasazením počítače, přečtěte si tento článek úplně.

> [!NOTE]
> Azure Backup serveru je navržen pro spouštění na virtuálním počítači vyhrazené, jednoúčelovém. Azure Backup serveru nejde nainstalovat na:
> - Počítač spuštěný jako řadič domény
> - Počítač, na kterém je nainstalovaná role aplikačního serveru
> - Počítač, na kterém je spuštěný server Exchange
> - Počítač, který je uzlem clusteru

Vždy připojte se k Azure Backup serveru k doméně. Pokud potřebujete přesunout Azure Backup serveru k jiné doméně, nejprve instalace Azure Backup serveru a pak ho připojit k nové doméně. Jakmile nasadíte Azure Backup Server, nemůžete ho přesunout do nové domény.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Nastavení replikace úložiště

Možnost replikace úložiště trezor služby Recovery Services umožňuje výběr mezi geograficky redundantním úložištěm a místně redundantní úložiště. Ve výchozím nastavení používají trezory služby Recovery Services geograficky redundantní úložiště. Pokud se tento trezor je primární úložiště, ponechte možnost úložiště pro geograficky redundantní úložiště. Pokud chcete levnější možnost, která je menší trvalý, vyberte místně redundantní úložiště. Další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy-grs.md) a [místně redundantního](../storage/common/storage-redundancy-lrs.md) úložiště naleznete v tématu [Přehled replikace Azure Storage](../storage/common/storage-redundancy.md).

Chcete-li upravit nastavení replikace úložiště:

1. Vyberte svůj trezor pro otevření řídícího panelu trezoru a nabídky nastavení. Pokud **nastavení** nabídky neotevře, klikněte na tlačítko **všechna nastavení** v řídicím panelu trezoru.
2. Na **nastavení** nabídky, klikněte na tlačítko **infrastruktura zálohování** > **konfigurace zálohování** otevřít **konfigurace zálohování**nabídky. Na **konfigurace zálohování** nabídek, zvolte pro svůj trezor možnost replikace úložiště.

    ![Seznam trezorů záloh](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Stáhněte si instalační program Azure Backup serveru

Existují dva způsoby, jak stáhnout instalační program Azure Backup serveru. Můžete stáhnout instalační program Azure Backup serveru z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55269). Nakonfigurujete trezor služby Recovery Services můžete také stáhnout instalační program Azure Backup serveru. Následující postup vás provede stažením Instalační služby z webu Azure portal při konfiguraci trezor služby Recovery Services.

1. Z vašeho virtuálního počítače Azure Stack [přihlásit ke svému předplatnému Azure na webu Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo vyberte **všechny služby**.

    ![V hlavní nabídce zvolte možnost všechny služby](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. V **všechny služby** dialogové okno, zadejte *služby Recovery Services*. Když začnete psát, váš vstup filtruje seznam prostředků. Jakmile se zobrazí, vyberte **trezory služby Recovery Services**.

    ![Zadejte do dialogového okna všechny služby Recovery Services](./media/backup-mabs-install-azure-stack/all-services.png)

    Zobrazí se seznam trezorů služby Recovery Services v rámci předplatného.

4. Ze seznamu trezorů služby Recovery Services vyberte svůj trezor otevřete jeho řídicí panel.

    ![Zadejte do dialogového okna všechny služby Recovery Services](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. V nabídce Začínáme v trezoru, klikněte na tlačítko **zálohování** otevřete Průvodce Začínáme.

    ![Začínáme se službou Backup](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Otevře se nabídka zálohování.

    ![Backup – cíle – výchozí – otevřít](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. V nabídce backup z **ve kterém je spuštěná vaše úloha** nabídce vyberte možnost **On-premises**. Z **co chcete zálohovat?** rozevírací nabídce vyberte možnost úlohy, které chcete chránit pomocí Azure Backup Server. Pokud si nejste jistí, úlohy, které k výběru, zvolte **virtuálních počítačů Hyper-V** a potom klikněte na tlačítko **připravit infrastrukturu**.

    ![místní a úlohy jako cíle](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    **Připravit infrastrukturu** otevře se nabídka.

7. V **připravit infrastrukturu** nabídky, klikněte na tlačítko **Stáhnout** otevřít webovou stránku ke stažení instalačních souborů Azure Backup serveru.

    ![Získávání změnu Průvodce Začínáme](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Otevře se na webu společnosti Microsoft, který je hostitelem soubory ke stažení pro Azure Backup serveru.

8. Na stránce pro stažení aplikace Microsoft Azure Backup Server, vyberte jazyk a klikněte na tlačítko **Stáhnout**.

    ![Otevře se Centrum stahování](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Instalační program Azure Backup serveru se skládá z osm soubory – Instalační program a sedm souborů Bin. Zkontrolujte **název_souboru** vyberte všechny požadované soubory a klikněte na **Další**. Stažení všech souborů do stejné složky.

    ![Stažení softwaru 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Velikost ke stažení všech instalačních souborů je větší než 3 GB. Při stahování 10 MB/s odkaz Stáhnout všechny instalační soubory, může trvat až 60 minut. Soubory stáhnout vaše zadané umístění pro stahování.

## <a name="extract-azure-backup-server-install-files"></a>Extrahujte soubory instalace Azure Backup serveru

Po stažení všechny soubory ke svému virtuálnímu počítači Azure Stack, přejděte k umístění stahování. První fáze instalace Azure Backup serveru se extrahovat soubory.

![Stažení softwaru 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Chcete-li spustit instalaci, ze seznamu stažené soubory, klikněte na tlačítko **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Minimálně 4GB volného místa je potřeba extrahovat soubory instalačního programu.
    >

2. V Průvodci Azure Backup serveru klikněte na tlačítko **Další** pokračujte.

    ![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Vyberte cestu pro soubory Azure Backup serveru a klikněte na tlačítko **Další**.

   ![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Ověřte umístění extrakce a klikněte na tlačítko **extrahovat**.

   ![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Průvodce extrahuje soubory a připravíte proces instalace.

   ![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Po dokončení procesu extrakce, klikněte na tlačítko **Dokončit**. Ve výchozím nastavení **spusťte setup.exe** zaškrtnuto. Po kliknutí na **Dokončit**, Setup.exe nainstaluje Microsoft Azure Backup Server do zadaného umístění.

   ![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Nainstalujte balíček softwaru

V předchozím kroku jste klikli na **Dokončit** fázi extrakce ukončete a spusťte Průvodce instalací Azure Backup serveru.

![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup serveru sdílí kód pomocí aplikace Data Protection Manager. Zobrazí se odkazy na aplikace Data Protection Manager a aplikace DPM v instalačním programu Azure Backup serveru. I když jsou samostatné produkty Azure Backup serveru a Data Protection Manager, tyto produkty jsou úzce souvisí.

1. Chcete-li spustit Průvodce instalací, klikněte na tlačítko **Microsoft Azure Backup serveru**.

   ![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Na **úvodní** obrazovce, klikněte na tlačítko **Další**.

    ![Kontrola Azure Backup Server – Vítejte a požadavky](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Na **kontrol požadovaných součástí** obrazovce, klikněte na tlačítko **zkontrolujte** k určení, pokud byly splněny hardwarové a softwarové požadavky pro Azure Backup serveru.

    ![Kontrola Azure Backup Server – Vítejte a požadavky](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Pokud prostředí obsahuje nezbytné požadavky, zobrazí se zpráva označující, zda počítač splňuje požadavky. Klikněte na **Další**.  

    ![Azure Backup Server – Kontrola předpokladů se dokončila](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Pokud vaše prostředí nemá splňovat nezbytné požadavky, bude se problémy. Které nebyly splněny požadavky jsou uvedené také na DpmSetup.log. Vyřešit chyby požadovaných součástí a pak spusťte **zkontrolovat znovu**. Instalace nemůže pokračovat, dokud jsou splněny všechny požadavky.

    ![Azure Backup Server – požadavky na instalaci nebyly splněny](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server vyžaduje SQL Server. Instalační balíček Azure Backup serveru se dodává jako součást balíčku s odpovídající binární soubory systému SQL Server. Pokud chcete použít vlastní instalace systému SQL, můžete. Doporučená volba je nechat instalační program, přidejte novou instanci systému SQL Server. Abyste se ujistili, podle vašeho výběru funguje s vaším prostředím, klikněte na tlačítko **zkontrolovat a nainstalovat**.

   > [!NOTE]
   > Azure Backup serveru nebude fungovat u vzdálené instance systému SQL Server. Instance, používá Azure Backup serveru musí být místní.
   >

    ![Kontrola Azure Backup Server – Vítejte a požadavky](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Po kontrole, pokud má virtuální počítač nezbytné požadavky pro instalaci Azure Backup Server, klikněte na tlačítko **Další**.

    ![Kontrola Azure Backup Server – Vítejte a požadavky](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Pokud dojde k selhání s doporučením k restartování počítače, potom restartujte počítač. Po restartování počítače, znovu spusťte instalační program, a když se zobrazí **nastavení SQL** obrazovce, klikněte na tlačítko **zkontrolovat znovu**.

5. V **nastavení instalace**, zadejte umístění pro instalační soubory aplikace Microsoft Azure Backup serveru a klikněte na **Další**.

    ![PreReq2 zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Pomocné umístění je nezbytné k zálohování do Azure. Zajistěte, aby že velikost pomocné umístění je ekvivalentní k nejméně 5 % dat plánované zálohování do Azure. Pro ochranu na disku je potřeba nakonfigurovat po dokončení instalace různých discích. Další informace o fondech úložiště, najdete v části [nakonfigurujte fondy úložiště a diskové úložiště](https://technet.microsoft.com/library/hh758075.aspx).

6. Na **nastavení zabezpečení** obrazovky, zadejte silné heslo pro omezené místní uživatelské účty a klikněte na tlačítko **Další**.

    ![PreReq2 zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Na **Microsoft Update Opt-In** obrazovky, vyberte, zda chcete použít *Microsoft Update* vyhledat aktualizace, a klikněte na tlačítko **Další**.

   > [!NOTE]
   > Doporučujeme mít aktualizaci Windows přesměrována na Microsoft Update, která nabízí zabezpečení a důležité aktualizace pro Windows a dalších produktů, jako je Microsoft Azure Backup serveru.
   >

    ![PreReq2 zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Zkontrolujte *souhrn nastavení* a klikněte na tlačítko **nainstalovat**.

    ![PreReq2 zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Po dokončení instalace Azure Backup Server instalační program okamžitě spustí instalační program agenta Microsoft Azure Recovery Services.

9. Otevře se instalační program agenta Microsoft Azure Recovery Services a kontroly pro připojení k Internetu. Pokud je k dispozici připojení k Internetu, pokračujte v instalaci. Pokud není k dispozici připojení, zadejte podrobnosti o proxy serveru pro připojení k Internetu. Po zadání nastavení serveru proxy, klikněte na tlačítko **Další**.

    ![PreReq2 zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Chcete-li nainstalovat agenta Microsoft Azure Recovery Services, klikněte na tlačítko **nainstalovat**.

    ![PreReq2 Azure Backup serveru](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Agent Microsoft Azure Recovery Services, také označovaný jako agent Azure Backup lze konfigurovat Azure Backup serveru k trezoru služby Recovery Services. Po nakonfigurování Azure Backup Server vždy zálohovat data do stejného trezoru služby Recovery Services.

11. Po dokončení instalace agenta Microsoft Azure Recovery Services klikněte na tlačítko **Další** spustit další fázi: registrace Azure Backup serveru k trezoru služby Recovery Services.

    ![PreReq2 Azure Backup serveru](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Spustí instalační program **Průvodce registrací serveru**.

12. Přepnout do vašeho předplatného Azure a trezoru služby Recovery Services. V **připravit infrastrukturu** nabídky, klikněte na tlačítko **Stáhnout** stáhnout přihlašovací údaje trezoru. Pokud **Stáhnout** tlačítko v kroku 2 není aktivní, vyberte **už stažené nebo se používá nejnovější instalace Azure Backup serveru** aktivovat tlačítko. Přihlašovací údaje trezoru stáhnout do umístění, kam můžete ukládat soubory ke stažení. Mějte na paměti tato umístění, protože ji budete potřebovat další krok.

    ![PreReq2 Azure Backup serveru](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. V **trezor identifikace** nabídky, klikněte na tlačítko **Procházet** najít přihlašovací údaje trezoru služby Recovery Services.

    ![PreReq2 Azure Backup serveru](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    V **vyberte přihlašovací údaje trezoru** dialogové okno, přejděte do umístění stažení vyberte svoje přihlašovací údaje trezoru a klikněte na tlačítko **otevřít**.

    Cesta k přihlašovací údaje se zobrazí v nabídce trezoru identifikace. Klikněte na tlačítko **Další** pro přechod na nastavení šifrování.

14. V **nastavení šifrování** dialogové okno, zadejte heslo pro šifrování záloh a umístění, kam chcete uložit heslo a klikněte na tlačítko **Další**.

    ![PreReq2 Azure Backup serveru](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Můžete zadat vlastní heslo nebo pomocí generátoru heslo můžete vytvořit za vás. Přístupové heslo je ta vaše, a Microsoft uložit nebo spravovat toto heslo. Příprava k havárii, vaše heslo uložte na dostupném místě.

    Po kliknutí na **Další**, Azure Backup serveru je zaregistrované do trezoru služby Recovery Services. Instalační program pokračuje v instalaci SQL serveru a Azure Backup serveru.

    ![PreReq2 Azure Backup serveru](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Po dokončení instalačního programu ve stavu zobrazuje úspěšně nainstalován veškerý software.

    ![PreReq2 Azure Backup serveru](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Po dokončení instalace se vytvoří konzoly Azure Backup serveru a prostředí PowerShell pro Azure Backup Server ikony na ploše serveru.

## <a name="add-backup-storage"></a>Přidání úložiště záloh

První zálohy se ukládají na úložiště připojené k počítači Azure Backup Server. Další informace o přidávání disků najdete v tématu [přidání moderního úložiště záloh](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Budete muset přidat úložiště pro zálohování i v případě, že máte v úmyslu odesílání dat do Azure. V architektuře Azure Backup Server, trezoru služby Recovery Services obsahuje *druhý* kopii dat při místní úložiště obsahuje záložní kopie prvnímu (zároveň povinná).
>
>

## <a name="network-connectivity"></a>Připojení k síti

Azure Backup Server vyžaduje připojení ke službě Azure Backup pro produkt nemusí fungovat správně. Chcete-li ověřit, zda je počítač připojen k Azure, použijte ```Get-DPMCloudConnection``` rutiny v konzole prostředí PowerShell pro Azure Backup Server. Pokud výstup této rutiny je hodnota TRUE, pak připojení existuje, jinak není žádné připojení.

Předplatné Azure, které ve stejnou dobu, musí být ve stavu v pořádku. Zjistěte stav svého předplatného a spravovat, přihlaste se k [portál předplatného](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Jakmile budete vědět o stavu možnosti připojení Azure a předplatné Azure, můžete zjistit dopad na zálohování a obnovení funkce nabízené v tabulce níže.

| Stav připojení | předplatné Azure | Zálohování do Azure | Zálohování na disk | Obnovení z Azure | Obnovení z disku |
| --- | --- | --- | --- | --- | --- |
| Připojeno |Aktivní |Povoleno |Povoleno |Povoleno |Povoleno |
| Připojeno |Vypršela platnost |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Připojeno |Zrušení zřízení |Zastaveno |Zastaveno |Body obnovení zastavena a Azure odstranit |Zastaveno |
| Došlo ke ztrátě připojení > 15 dnů |Aktivní |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Došlo ke ztrátě připojení > 15 dnů |Vypršela platnost |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Došlo ke ztrátě připojení > 15 dnů |Zrušení zřízení |Zastaveno |Zastaveno |Body obnovení zastavena a Azure odstranit |Zastaveno |

### <a name="recovering-from-loss-of-connectivity"></a>Obnovit ze ztráty připojení

Pokud brána firewall nebo proxy serveru brání přístupu k Azure, seznam povolených následující domény adresy v profilu nebo proxy server brány firewall:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Po obnovení připojení k Azure do Azure Backup serveru stav předplatného Azure určuje operace, které lze provést. Jakmile je server **připojeno**, pomocí tabulky uvedené v [připojení k síti](backup-mabs-install-azure-stack.md#network-connectivity) zobrazíte dostupné operace.

### <a name="handling-subscription-states"></a>Zpracování – stavy předplatného

Je možné změnit předplatné Azure z *neplatné* nebo *zajištění zrušeno* do stavu *aktivní* stavu. Stav předplatného není *aktivní*:

- Když se předplatné *zajištění zrušeno*, dojde ke ztrátě funkčnosti. Obnovení odběr *aktivní*, revives funkce zálohování a obnovení. Pokud s dobou uchování dostatečně velký, která je možné načíst data záloh se uchovávají zálohovaná data na místním disku. Data záloh v Azure je však nenahraditelně ztraceny, jakmile vstoupí předplatného *zajištění zrušeno* stavu.
- Když se předplatné *neplatné*, ztratí funkce. Při přihlášení k odběru nelze spustit naplánované zálohy *neplatné*.

## <a name="troubleshooting"></a>Řešení potíží

Pokud se Microsoft Azure Backup serveru nezdaří s chybami během fáze instalace (nebo zálohování nebo obnovení), najdete v článku [chybové kódy dokumentu](https://support.microsoft.com/kb/3041338).
Můžete také odkazovat na [nejčastější dotazy týkající se Azure Backup](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Další postup

V článku [Příprava prostředí pro aplikaci DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801), obsahuje informace o podporovaných konfiguracích Azure Backup serveru.

Abyste získali lepší představu o ochranu úloh pomocí Microsoft Azure Backup serveru můžete použít v následujících článcích.

- [Zálohování serveru SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [Zálohování serveru SharePoint](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Alternativní server backup](backup-azure-alternate-dpm-server.md)
