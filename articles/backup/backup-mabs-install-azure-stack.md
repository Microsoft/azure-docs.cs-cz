---
title: Instalace Azure Backup Serveru v Azure Stacku
description: V tomto článku se dozvíte, jak pomocí Azure Backup Server chránit nebo zálohovat úlohy v Azure Stack.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: b78e5a662bdcf23ad38cb33292658d4d2455e579
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77583431"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Instalace Azure Backup Serveru v Azure Stacku

Tento článek vysvětluje, jak nainstalovat Azure Backup Server v Azure Stack. Pomocí Azure Backup Server můžete chránit úlohy IaaS (infrastruktura jako služba), jako jsou virtuální počítače běžící v Azure Stack. Výhodou použití Azure Backup Server k ochraně vašich úloh můžete spravovat veškerou ochranu úloh z jediné konzoly.

> [!NOTE]
> Další informace o možnostech zabezpečení najdete v [dokumentaci k funkcím Azure Backup zabezpečení](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Systém ochrany Azure Backup Serveru

Azure Backup Server chrání následující Azure Stack úlohy virtuálních počítačů.

| Chráněné zdroje dat | Ochrana a obnovení |
| --------------------- | ----------------------- |
| Windows Server – pololetní kanál – Datacenter/Enterprise/Standard | Svazky, soubory a složky |
| Windows Server 2016 – Datacenter/Enterprise/Standard | Svazky, soubory a složky |
| Windows Server 2012 R2 – Datacenter/Enterprise/Standard | Svazky, soubory a složky |
| Windows Server 2012 – Datacenter/Enterprise/Standard | Svazky, soubory a složky |
| Windows Server 2008 R2 – Datacenter/Enterprise/Standard | Svazky, soubory a složky |
| SQL Server 2016 | databáze |
| SQL Server 2014 | databáze |
| SQL Server 2012 SP1 | databáze |
| SharePoint 2016 | Farma, databáze, front-end, webový server |
| SharePoint 2013 | Farma, databáze, front-end, webový server |
| SharePoint 2010 | Farma, databáze, front-end, webový server |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Předpoklady pro prostředí Azure Backup Server

Při instalaci Azure Backup Server ve vašem prostředí Azure Stack zvažte doporučení v této části. Instalační služba Azure Backup Server kontroluje, jestli vaše prostředí splňuje nezbytné požadavky, ale ušetříte čas tím, že se připraví před instalací.

### <a name="determining-size-of-virtual-machine"></a>Určení velikosti virtuálního počítače

Pokud chcete spustit Azure Backup Server na virtuálním počítači s Azure Stack, použijte velikost a2 nebo větší. Pokud potřebujete pomoc při výběru velikosti virtuálního počítače, Stáhněte si [Azure Stack kalkulačku velikosti virtuálního](https://www.microsoft.com/download/details.aspx?id=56832)počítače.

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuální sítě na virtuálních počítačích s Azure Stack

Všechny virtuální počítače, které se používají v úlohách Azure Stack, musí patřit do stejné služby Azure Virtual Network a předplatného Azure.

### <a name="azure-backup-server-vm-performance"></a>Výkon virtuálního počítače s Azure Backup Server

Pokud sdílíte s jinými virtuálními počítači, omezení velikosti a IOPS účtu úložiště ovlivní Azure Backup Server výkon virtuálního počítače. Z tohoto důvodu byste měli pro Azure Backup Server virtuální počítač použít samostatný účet úložiště. Agent Azure Backup spuštěný v Azure Backup Server potřebuje dočasné úložiště pro:

- vlastní použití (umístění mezipaměti),
- data obnovená z cloudu (místní pracovní oblast)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurace Azure Backup dočasného diskového úložiště

Každý Azure Stack virtuální počítač obsahuje dočasné diskové úložiště, které je k dispozici uživateli jako svazek `D:\`. Místní pracovní oblast, kterou vyžaduje Azure Backup, se dá nakonfigurovat tak, aby `D:\`se nacházela v a umístění mezipaměti se `C:\`dá umístit na. Tímto způsobem není potřeba žádné úložiště Carved z datových disků připojených k Azure Backup Servermu virtuálnímu počítači.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Ukládání zálohovaných dat na místní disk a v Azure

Azure Backup Server ukládá data záloh na discích Azure připojených k virtuálnímu počítači pro provozní obnovení. Jakmile jsou disky a prostory úložiště připojené k virtuálnímu počítači, Azure Backup Server pro vás spravuje úložiště. Velikost úložiště záloh dat závisí na počtu a velikosti disků připojených ke každému [Azure Stackmu virtuálnímu počítači](/azure-stack/user/azure-stack-storage-overview). Každá velikost Azure Stack virtuálního počítače má maximální počet disků, které lze připojit k virtuálnímu počítači. Například a2 je čtyři disky. A3 je osm disků. A4 je 16 disků. Opět velikost a počet disků určuje celkový fond úložiště záloh.

> [!IMPORTANT]
> **Neměli byste uchovávat data** obnovení (zálohování) na discích s Azure Backup Server po dobu delší než pět dní.
>

Ukládání zálohovaných dat v Azure omezuje infrastrukturu zálohování na Azure Stack. Pokud jsou data starší než pět dnů, měla by být uložena v Azure.

Pokud chcete ukládat zálohovaná data v Azure, vytvořte nebo použijte Recovery Services trezor. Při přípravě zálohování Azure Backup Server úlohy [nakonfigurujete trezor Recovery Services](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Po nakonfigurování pokaždé, když se úloha zálohování spustí, se v trezoru vytvoří bod obnovení. Každý trezor Recovery Services uchovává až 9999 bodů obnovení. V závislosti na počtu vytvořených bodů obnovení a dobu, po kterou jsou zachována, můžete data zálohy uchovávat mnoho let. Můžete například vytvořit měsíční body obnovení a uchovávat je po dobu pěti let.

### <a name="scaling-deployment"></a>Škálování nasazení

Pokud chcete škálovat nasazení, máte následující možnosti:

- Horizontální navýšení kapacity – zvětšete velikost Azure Backup Server virtuálního počítače z řady na řady D a zvyšte místní úložiště [podle pokynů pro virtuální počítače Azure Stack](/azure-stack/user/azure-stack-manage-vm-disks).
- Přesměrování dat – odešlete starší data do Azure a zachovejte jenom nejnovější data v úložišti připojeném k Azure Backup Server.
- Horizontální navýšení kapacity – přidejte další Azure Backup servery, abyste chránili úlohy.

### <a name="net-framework"></a>.NET Framework

Na virtuálním počítači musí být nainstalovaná .NET Framework 3,5 SP1 nebo novější.

### <a name="joining-a-domain"></a>Připojení k doméně

Azure Backup Server virtuální počítač musí být připojený k doméně. Na virtuálním počítači se musí nainstalovat Azure Backup Server uživatel domény s oprávněními správce.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Použití virtuálního počítače s IaaS v Azure Stack

Při volbě serveru pro Azure Backup Server začněte s imagí Windows Server 2012 R2 Datacenter nebo Windows Server 2016 Datacenter. [V tomto článku vytvořte svůj první virtuální počítač s Windows ve službě Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), který poskytuje kurz, jak začít s doporučeným virtuálním počítačem. Doporučené minimální požadavky pro virtuální počítač serveru (VM) by měly být: a2 Standard se dvěma jádry a 3,5-GB RAM.

Ochrana úloh pomocí Azure Backup Server má spoustu drobné odlišnosti. Tento článek vám pomůže vysvětlit tyto drobné odlišnostiy, [nainstalovat DPM jako virtuální počítač Azure](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12)). Než počítač nasadíte, přečtěte si tento článek kompletně.

> [!NOTE]
> Azure Backup Server je navržená tak, aby běžela na vyhrazeném virtuálním počítači s jedním účelem. Nemůžete nainstalovat Azure Backup Server na:
>
> - Počítač spuštěný jako řadič domény
> - Počítač, na kterém je nainstalovaná role aplikačního serveru
> - Počítač, na kterém je spuštěný server Exchange
> - Počítač, který je uzlem clusteru

Vždy připojte Azure Backup Server k doméně. Pokud potřebujete přesunout Azure Backup Server do jiné domény, napřed nainstalujte Azure Backup Server a pak ho připojte k nové doméně. Když nasadíte Azure Backup Server, nemůžete ho přesunout do nové domény.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Nastavení replikace úložiště

Možnost replikace úložiště Recovery Services trezoru umožňuje výběr mezi geograficky redundantním úložištěm a místně redundantním úložištěm. Ve výchozím nastavení používají trezory Recovery Services geograficky redundantní úložiště. Pokud je tento trezor vaším primárním trezorem, ponechte možnost úložiště nastavenou na geograficky redundantní úložiště. Pokud chcete levnější možnost, která je méně trvalá, vyberte možnost místně redundantní úložiště. Další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy-grs.md) a [místně redundantního](../storage/common/storage-redundancy-lrs.md) úložiště najdete v tématu [Přehled replikace Azure Storage](../storage/common/storage-redundancy.md).

Chcete-li upravit nastavení replikace úložiště:

1. Vyberte svůj trezor a otevřete tak řídicí panel trezoru a nabídku nastavení. Pokud se nabídka **Nastavení** neotevře, klikněte na **všechna nastavení** na řídicím panelu trezoru.
2. V nabídce **Nastavení** klikněte na **zálohovat** > **Konfigurace zálohování** infrastruktury a otevřete nabídku **Konfigurace zálohování** . V nabídce **Konfigurace zálohování** vyberte pro svůj trezor možnost replikace úložiště.

    ![Seznam trezorů záloh](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Stažení instalačního programu Azure Backup Server

Existují dva způsoby, jak stáhnout instalační službu Azure Backup Server. Instalační program Azure Backup Server můžete stáhnout z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=55269). Můžete si také stáhnout instalační program Azure Backup Server, když konfigurujete Recovery Services trezor. Následující kroky vás provedou stažením instalačního programu z Azure Portal při konfiguraci trezoru Recovery Services.

1. Z Azure Stack virtuálního počítače se [přihlaste ke svému předplatnému Azure v Azure Portal](https://portal.azure.com/).
2. V nabídce na levé straně vyberte **všechny služby**.

    ![Výběr možnosti všechny služby v hlavní nabídce](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. V dialogovém okně **všechny služby** zadejte *Recovery Services*. Když začnete psát, váš vstupní seznam prostředků se vyfiltruje. Jakmile se zobrazí, vyberte **Recovery Services trezory**.

    ![Zadejte Recovery Services v dialogovém okně všechny služby.](./media/backup-mabs-install-azure-stack/all-services.png)

    Zobrazí se seznam trezorů Recovery Services v předplatném.

4. V seznamu trezorů Recovery Services vyberte svůj trezor a otevřete jeho řídicí panel.

    ![Zadejte Recovery Services v dialogovém okně všechny služby.](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. V nabídce Začínáme trezoru klikněte na **zálohování** a otevřete Průvodce Začínáme.

    ![Začínáme se zálohováním](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Otevře se nabídka zálohování.

    ![Zálohování – cíle – výchozí – otevřeno](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. V nabídce zálohovat v nabídce **kde je spuštěná vaše úloha** , vyberte **místní**. V rozevírací nabídce **co chcete zálohovat?** vyberte úlohy, které chcete chránit pomocí Azure Backup Server. Pokud si nejste jistí, jaké úlohy vybrat, vyberte **Hyper-V Virtual Machines** a pak klikněte na **připravit infrastrukturu**.

    ![místní a úlohy jako cíle](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Otevře se nabídka **připravit infrastrukturu** .

7. V nabídce **připravit infrastrukturu** klikněte na **Stáhnout** a otevřete webovou stránku pro stažení Azure Backup Server instalačních souborů.

    ![Změna Průvodce Začínáme](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Otevře se webová stránka společnosti Microsoft, která je hostitelem souborů ke stažení pro Azure Backup Server.

8. Na stránce pro stažení serveru Microsoft Azure Backup vyberte jazyk a klikněte na tlačítko **Stáhnout**.

    ![Otevře se centrum stažení.](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Instalační program Azure Backup Server se skládá z osmi souborů – instalační program a sedm souborů. bin. Zaškrtnutím políčka **název souboru** vyberte všechny požadované soubory a klikněte na **Další**. Všechny soubory si můžete stáhnout do stejné složky.

    ![Stažení centra 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Velikost stahovaných souborů všech instalačních souborů je větší než 3 GB. Na odkaz pro stažení 10 MB/s může stahování všech instalačních souborů trvat až 60 minut. Soubory se stáhnou do zadaného umístění pro stahování.

## <a name="extract-azure-backup-server-install-files"></a>Extrakce Azure Backup Server instalačních souborů

Po stažení všech souborů na virtuální počítač s Azure Stack přejdete do umístění pro stahování. První fází instalace Azure Backup Server je extrakce souborů.

![Stažení centra 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Pokud chcete spustit instalaci, ze seznamu stažených souborů klikněte na **MicrosoftAzureBackupserverInstaller. exe**.

    > [!WARNING]
    > K extrakci instalačních souborů je potřeba aspoň 4 GB volného místa.
    >

2. V průvodci Azure Backup Server pokračujte kliknutím na tlačítko **Další** .

    ![Průvodce instalací Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Vyberte cestu pro Azure Backup Server soubory a klikněte na **Další**.

   ![Průvodce instalací Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Ověřte umístění pro extrakci a klikněte na **extrahovat**.

   ![Průvodce instalací Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Průvodce extrahuje soubory a přečte proces instalace.

   ![Průvodce instalací Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Po dokončení procesu extrakce klikněte na tlačítko **Dokončit**. Ve výchozím nastavení je zvolena možnost **Spustit soubor Setup. exe** . Po kliknutí na **Dokončit**nainstaluje Setup. exe Microsoft Azure Backup Server do zadaného umístění.

   ![Průvodce instalací Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Instalace softwarového balíčku

V předchozím kroku jste klikli na tlačítko **Dokončit** a ukončili fázi extrakce a spustíte Průvodce instalací nástroje Azure Backup Server.

![Průvodce instalací Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server sdílí kód s Data Protection Manager. V instalačním programu Azure Backup Server se zobrazí odkazy na Data Protection Manager a DPM. I když Azure Backup Server a Data Protection Manager jsou samostatné produkty, tyto produkty úzce souvisejí.

1. Chcete-li spustit Průvodce instalací nástroje, klikněte na možnost **Microsoft Azure Backup Server**.

   ![Průvodce instalací Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Na obrazovce **Vítejte** klikněte na **Další**.

    ![Azure Backup Server – uvítání a kontrolu předpokladů](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Na obrazovce **kontroly požadovaných součástí** klikněte na tlačítko **zkontrolovat** a určete, zda byly splněny požadavky na hardware a software pro Azure Backup Server.

    ![Azure Backup Server – uvítání a kontrolu předpokladů](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Pokud má vaše prostředí nezbytné požadavky, zobrazí se zpráva oznamující, že počítač splňuje požadavky. Klikněte na **Další**.  

    ![Azure Backup Server – byla úspěšná kontrolu předpokladů.](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Pokud vaše prostředí nesplňuje nezbytné požadavky, budou uvedené problémy. Požadavky, které nebyly splněny, jsou uvedeny také v protokolu DpmSetup. log. Vyřešte chyby požadovaných součástí a pak znovu spusťte **kontrolu**. Instalace nemůže pokračovat, dokud nebudou splněny všechny požadavky.

    ![Azure Backup Server – požadavky instalace nejsou splněné](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server vyžaduje SQL Server. Instalační balíček Azure Backup Server obsahuje balíčky s odpovídajícími binárními soubory SQL Server. Pokud chcete použít vlastní instalaci SQL, můžete. Doporučená volba ale umožňuje, aby instalační program přidal novou instanci SQL Server. Pokud chcete mít jistotu, že vaše volba funguje ve vašem prostředí, klikněte na **zkontrolovat a nainstalovat**.

   > [!NOTE]
   > Azure Backup Server nebudou fungovat s instancí vzdáleného SQL Server. Instance, kterou používá Azure Backup Server, musí být místní.
   >

    ![Azure Backup Server – uvítání a kontrolu předpokladů](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Po ověření, jestli má virtuální počítač nezbytné předpoklady pro instalaci Azure Backup Server, klikněte na **Další**.

    ![Azure Backup Server – uvítání a kontrolu předpokladů](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Pokud dojde k selhání s doporučením pro restartování počítače, restartujte počítač. Po restartování počítače restartujte instalační program a až se dostanete na obrazovku **nastavení SQL** , klikněte na **znovu ověřit**.

5. V **nastavení instalace**zadejte umístění instalace souborů Microsoft Azure Backup serveru a klikněte na **Další**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    K zálohování do Azure se vyžaduje pomocné umístění. Zajistěte, aby velikost pravého umístění byla stejná jako aspoň 5% dat, která se mají zálohovat do Azure. V případě ochrany disku je potřeba po dokončení instalace nakonfigurovat samostatné disky. Další informace o fondech úložiště najdete v tématu [Konfigurace fondů úložiště a diskového úložiště](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).

6. Na obrazovce **nastavení zabezpečení** zadejte silné heslo pro omezené místní uživatelské účty a klikněte na **Další**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Na obrazovce **Microsoft Update opt-in** vyberte, jestli chcete ke kontrole aktualizací použít *Microsoft Update* , a klikněte na **Další**.

   > [!NOTE]
   > Doporučujeme, abyste web Windows Update přesměrování na Microsoft Update, což nabízí zabezpečení a důležité aktualizace pro Windows a další produkty, jako je Microsoft Azure Backup Server.
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Zkontrolujte *Souhrn nastavení* a klikněte na **nainstalovat**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Po dokončení instalace Azure Backup Server instalační program okamžitě spustí instalační program agenta Microsoft Azure Recovery Services.

9. Spustí se instalační program agenta Microsoft Azure Recovery Services a zkontroluje připojení k Internetu. Pokud je k dispozici připojení k Internetu, pokračujte v instalaci. Pokud není k dispozici žádné připojení, zadejte podrobnosti o proxy serveru pro připojení k Internetu. Po zadání nastavení proxy serveru klikněte na **Další**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Chcete-li nainstalovat agenta Microsoft Azure Recovery Services, klikněte na tlačítko **nainstalovat**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Agent Microsoft Azure Recovery Services, označovaný také jako agent Azure Backup, nakonfiguruje Azure Backup Server na Recovery Services trezor. Po nakonfigurování bude Azure Backup Server vždy zálohovat data do stejného trezoru Recovery Services.

11. Po dokončení instalace agenta Microsoft Azure Recovery Services klikněte na **Další** a spusťte další fázi: registrace Azure Backup Server pomocí Recovery Servicesho trezoru.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Instalační program spustí **Průvodce registrací serveru**.

12. Přepněte do svého předplatného Azure a svého Recovery Servicesho trezoru. V nabídce **připravit infrastrukturu** klikněte na **Stáhnout** a stáhněte přihlašovací údaje trezoru. Pokud tlačítko **Stáhnout** v kroku 2 není aktivní, vyberte možnost **již staženo nebo použití nejnovější instalace Azure Backup Server** k aktivaci tlačítka. Přihlašovací údaje trezoru se stáhnou do umístění, kam budete ukládat soubory ke stažení. Uvědomte si toto umístění, protože ho budete potřebovat pro další krok.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. V nabídce **Identifikace trezoru** klikněte na **procházet** a najděte Recovery Services přihlašovací údaje trezoru.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    V dialogovém okně **vybrat přihlašovací údaje trezoru** přejděte do umístění pro stahování, vyberte své přihlašovací údaje trezoru a klikněte na **otevřít**.

    Cesta k přihlašovacím údajům se zobrazí v nabídce identifikace trezoru. Kliknutím na tlačítko **Další** přejdete k nastavení šifrování.

14. V dialogovém okně **nastavení šifrování** zadejte heslo pro šifrování záloh a umístění pro uložení hesla a klikněte na **Další**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Můžete zadat vlastní přístupové heslo, nebo ho vytvořit pomocí generátoru přístupového hesla. Přístupové heslo je vaše a Microsoft toto heslo neuloží ani nespravuje. Pro přípravu na havárii uložte heslo do přístupného umístění.

    Po kliknutí na tlačítko **Další**je Azure Backup Server zaregistrován v trezoru Recovery Services. Instalační program pokračuje v instalaci SQL Server a Azure Backup Server.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Po dokončení instalačního programu se ve stavu zobrazí, že veškerý software byl úspěšně nainstalován.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Po dokončení instalace se na ploše serveru vytvoří Konzola Azure Backup Server a Azure Backup Server PowerShellové ikony.

## <a name="add-backup-storage"></a>Přidat úložiště zálohování

První záložní kopie je udržována v úložišti připojeném k Azure Backup Servermu počítači. Další informace o přidávání disků najdete v tématu [Přidání moderního úložiště záloh](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Úložiště zálohování je nutné přidat i v případě, že plánujete odeslat data do Azure. V architektuře Azure Backup Server ukládá trezor Recovery Services *druhou* kopii dat, zatímco místní úložiště obsahuje první (a povinnou) záložní kopii.
>
>

## <a name="network-connectivity"></a>Připojení k síti

Aby produkt mohl úspěšně fungovat, Azure Backup Server vyžaduje připojení ke službě Azure Backup. Pokud chcete ověřit, jestli má počítač připojení k Azure, použijte ```Get-DPMCloudConnection``` rutinu v konzole Azure Backup Server PowerShellu. Pokud má výstup rutiny hodnotu TRUE, pak připojení existuje, jinak není dostupné žádné připojení.

Kromě toho musí být předplatné Azure v dobrém stavu. Pokud chcete zjistit stav předplatného a spravovat ho, přihlaste se na [portál předplatného](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Jakmile budete znát stav připojení Azure a předplatného Azure, můžete pomocí následující tabulky zjistit dopad na nabízené funkce zálohování a obnovení.

| Stav připojení | předplatné Azure | Zálohování do Azure | Zálohovat na disk | Obnovení z Azure | Obnovení z disku |
| --- | --- | --- | --- | --- | --- |
| Připojeno |Aktivní |Povoleno |Povoleno |Povoleno |Povoleno |
| Připojeno |Platnost vypršela |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Připojeno |Zajištění zrušeno |Zastaveno |Zastaveno |Zastaveno a body obnovení Azure byly odstraněny |Zastaveno |
| Ztracené připojení > 15 dní |Aktivní |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Ztracené připojení > 15 dní |Platnost vypršela |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Ztracené připojení > 15 dní |Zajištění zrušeno |Zastaveno |Zastaveno |Zastaveno a body obnovení Azure byly odstraněny |Zastaveno |

### <a name="recovering-from-loss-of-connectivity"></a>Obnovování ze ztráty připojení

Pokud brána firewall nebo proxy server brání v přístupu k Azure, přidejte následující adresy domény do seznamu povolených profilů brány firewall nebo proxy serveru:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Po obnovení připojení k Azure do Azure Backup Server stav předplatného Azure určí operace, které je možné provést. Po **připojení**serveru použijte tabulku v [Možnosti připojení k síti](backup-mabs-install-azure-stack.md#network-connectivity) , abyste viděli dostupné operace.

### <a name="handling-subscription-states"></a>Zpracování stavů předplatného

Je možné změnit předplatné Azure ze stavu, ve kterém *vypršela platnost* , nebo *Zrušit* stav na *aktivní* . I když stav předplatného není *aktivní*:

- Při *zrušení zřízení*předplatného ztratí funkčnost. Obnovení předplatného na *aktivní*revives funkce zálohování a obnovení. Pokud se zálohovaná data na místním disku uchovávají s dostatečně velkým obdobím uchovávání, můžou se data ze zálohy načíst. Data záloh v Azure se ale irretrievably ztratí, jakmile předplatné vstoupí do stavu *zrušení zřízení* .
- I když platnost předplatného *vypršela*, ztratí funkčnost. Po *vypršení platnosti*předplatného se naplánované zálohy nespustí.

## <a name="troubleshooting"></a>Řešení potíží

Pokud Microsoft Azure Backup server selhává s chybami při fázi nastavení (nebo zálohování nebo obnovení), přečtěte si [dokument kódy chyb](https://support.microsoft.com/kb/3041338).
Můžete se také podívat na [Azure Backup souvisejících nejčastějších](backup-azure-backup-faq.md) dotazech.

## <a name="next-steps"></a>Další kroky

Článek [Příprava prostředí pro aplikaci DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801)obsahuje informace o podporovaných konfiguracích Azure Backup Server.

Následující články vám pomohou získat hlubší princip ochrany zatížení pomocí Microsoft Azure Backup serveru.

- [Zálohování SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [Zálohování serveru SharePoint](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Alternativní zálohování serveru](backup-azure-alternate-dpm-server.md)
