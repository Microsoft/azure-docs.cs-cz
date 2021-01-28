---
title: Instalace Azure Backup Serveru v Azure Stacku
description: V tomto článku se dozvíte, jak pomocí Azure Backup Server chránit nebo zálohovat úlohy v Azure Stack.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 12dfd15c2bd43816dd361fdf45995bcbcd6fba56
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987001"
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

Každý Azure Stack virtuální počítač obsahuje dočasné diskové úložiště, které je k dispozici uživateli jako svazek `D:\` . Místní pracovní oblast, kterou vyžaduje Azure Backup, se dá nakonfigurovat tak, aby se nacházela v `D:\` a umístění mezipaměti se dá umístit na `C:\` . Tímto způsobem není potřeba žádné úložiště Carved z datových disků připojených k Azure Backup Servermu virtuálnímu počítači.

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

Při volbě serveru pro Azure Backup Server začněte s imagí Windows Server 2012 R2 Datacenter nebo Windows Server 2016 Datacenter. [V tomto článku vytvořte svůj první virtuální počítač s Windows ve službě Azure Portal](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json), který poskytuje kurz, jak začít s doporučeným virtuálním počítačem. Doporučené minimální požadavky pro virtuální počítač serveru (VM) by měly být: a2 Standard se dvěma jádry a 3,5-GB RAM.

Ochrana úloh pomocí Azure Backup Server má spoustu drobné odlišnosti. Tato drobné odlišnosti je popsána v [matrici ochrany pro MABS](./backup-mabs-protection-matrix.md) . Než počítač nasadíte, přečtěte si tento článek kompletně.

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

Možnost replikace úložiště Recovery Services trezoru umožňuje výběr mezi geograficky redundantním úložištěm a místně redundantním úložištěm. Ve výchozím nastavení používají trezory Recovery Services geograficky redundantní úložiště. Pokud je tento trezor vaším primárním trezorem, ponechte možnost úložiště nastavenou na geograficky redundantní úložiště. Pokud chcete levnější možnost, která je méně trvalá, vyberte možnost místně redundantní úložiště. V tématu [Přehled replikace Azure Storage](../storage/common/storage-redundancy.md)najdete další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy.md#geo-redundant-storage), [místně redundantního](../storage/common/storage-redundancy.md#locally-redundant-storage)a [redundantního](../storage/common/storage-redundancy.md#zone-redundant-storage) úložiště pro zóny.

Chcete-li upravit nastavení replikace úložiště:

1. Vyberte svůj trezor a otevřete tak řídicí panel trezoru a nabídku nastavení. Pokud se nabídka **Nastavení** neotevře, vyberte **všechna nastavení** na řídicím panelu trezoru.
2. V nabídce **Nastavení** vyberte zálohovat zálohování   >  **Konfigurace zálohování** a otevřete nabídku **Konfigurace zálohování** . V nabídce **Konfigurace zálohování** vyberte pro svůj trezor možnost replikace úložiště.

    ![Seznam trezorů záloh](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Stažení instalačního programu Azure Backup Server

Existují dva způsoby, jak stáhnout instalační službu Azure Backup Server. Instalační program Azure Backup Server můžete stáhnout z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=55269). Při konfiguraci trezoru Recovery Services si také můžete stáhnout Azure Backup Server instalační program. Následující kroky vás provedou stažením instalačního programu z Azure Portal při konfiguraci trezoru Recovery Services.

1. Z Azure Stack virtuálního počítače se [přihlaste ke svému předplatnému Azure v Azure Portal](https://portal.azure.com/).
2. V nabídce na levé straně vyberte **všechny služby**.

    ![Výběr možnosti všechny služby v hlavní nabídce](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. V dialogovém okně **všechny služby** zadejte *Recovery Services*. Když začnete psát, váš vstupní seznam prostředků se vyfiltruje. Jakmile se zobrazí, vyberte **Recovery Services trezory**.

    ![Zadejte Recovery Services v dialogovém okně všechny služby.](./media/backup-mabs-install-azure-stack/all-services.png)

    Zobrazí se seznam trezorů služby Recovery Services v předplatném.

4. V seznamu trezorů Recovery Services vyberte svůj trezor a otevřete jeho řídicí panel.

    ![Vyberte svůj trezor a otevřete tak řídicí panel.](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. V nabídce Začínáme trezoru vyberte **Backup** a otevřete tak Průvodce Začínáme.

    ![Začínáme se zálohováním](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Otevře se nabídka zálohování.

    ![Zálohování – cíle – výchozí – otevřeno](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. V nabídce zálohovat v nabídce **kde je spuštěná vaše úloha** , vyberte **místní**. V rozevírací nabídce **co chcete zálohovat?** vyberte úlohy, které chcete chránit pomocí Azure Backup Server. Pokud si nejste jistí, jaké úlohy vybrat, vyberte **Hyper-V Virtual Machines** a pak vyberte **připravit infrastrukturu**.

    ![místní a úlohy jako cíle](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Otevře se nabídka **připravit infrastrukturu** .

7. V nabídce **připravit infrastrukturu** vyberte **Stáhnout** a otevřete webovou stránku pro stažení Azure Backup Server instalačních souborů.

    ![Změna Průvodce Začínáme](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Otevře se webová stránka společnosti Microsoft, která je hostitelem souborů ke stažení pro Azure Backup Server.

8. Na stránce pro stažení serveru Microsoft Azure Backup vyberte jazyk a vyberte **Stáhnout**.

    ![Otevře se centrum stažení.](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Instalační program Azure Backup Server se skládá z osmi souborů – instalační program a sedm souborů. bin. Zaškrtnutím políčka **název souboru** vyberte všechny požadované soubory a vyberte **Další**. Všechny soubory si můžete stáhnout do stejné složky.

    ![Stažení softwaru, vybrané soubory](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Velikost stahovaných souborů všech instalačních souborů je větší než 3 GB. Na odkaz pro stažení 10 MB/s může stahování všech instalačních souborů trvat až 60 minut. Soubory se stáhnou do zadaného umístění pro stahování.

## <a name="extract-azure-backup-server-install-files"></a>Extrakce Azure Backup Server instalačních souborů

Po stažení všech souborů na virtuální počítač s Azure Stack přejdete do umístění pro stahování. První fází instalace Azure Backup Server je extrakce souborů.

![Stáhnout instalační program MABS](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Chcete-li spustit instalaci, vyberte ze seznamu stažených souborů možnost **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > K extrakci instalačních souborů je potřeba aspoň 4 GB volného místa.
    >

2. V průvodci Azure Backup Server pokračujte výběrem **Další** .

    ![Průvodce instalací Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Zvolte cestu pro Azure Backup Server soubory a vyberte **Další**.

   ![Vybrat cíl pro soubory](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Ověřte umístění pro extrakci a vyberte **extrahovat**.

   ![Ověřit umístění pro extrakci](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Průvodce extrahuje soubory a přečte proces instalace.

   ![Průvodce extrahuje soubory.](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Po dokončení procesu extrakce vyberte **Dokončit**. Ve výchozím nastavení je vybrána možnost **spustit setup.exe** . Po výběru **dokončit** Setup.exe nainstaluje Microsoft Azure Backup Server do zadaného umístění.

   ![Instalační program extrahuje soubory Microsoft Azure Backupho serveru.](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Instalace softwarového balíčku

V předchozím kroku vyberete **Dokončit** pro ukončení fáze extrakce a spustíte průvodce instalací Azure Backup Server.

![Spustí se Průvodce instalací Microsoft Azure Backup.](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server sdílí kód s Data Protection Manager. V instalačním programu Azure Backup Server uvidíte odkazy na Data Protection Manager a DPM. I když Azure Backup Server a Data Protection Manager jsou samostatné produkty, tyto produkty úzce souvisejí.

1. Chcete-li spustit Průvodce instalací, vyberte možnost **Microsoft Azure Backup Server**.

   ![Vybrat Microsoft Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Na **úvodní** obrazovce vyberte **Další**.

    ![Azure Backup Server – Vítejte](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Na obrazovce **kontroly požadovaných součástí** zaškrtněte **políčko zaškrtněte** , pokud chcete zjistit, jestli jsou splněné požadavky na hardware a software pro Azure Backup Server.

    ![Azure Backup Server – kontrolu předpokladů](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Pokud má vaše prostředí nezbytné požadavky, zobrazí se zpráva oznamující, že počítač splňuje požadavky. Vyberte **Další**.  

    ![Azure Backup Server – byla úspěšná kontrolu předpokladů.](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Pokud vaše prostředí nesplňuje nezbytné požadavky, budou uvedené problémy. Požadavky, které nebyly splněny, jsou uvedeny také v protokolu DpmSetup. log. Vyřešte chyby požadovaných součástí a pak znovu spusťte **kontrolu**. Instalace nemůže pokračovat, dokud nebudou splněny všechny požadavky.

    ![Azure Backup Server – požadavky instalace nejsou splněné](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server vyžaduje SQL Server. Instalační balíček Azure Backup Server obsahuje balíčky s odpovídajícími binárními soubory SQL Server. Pokud chcete použít vlastní instalaci SQL, můžete. Doporučená volba ale umožňuje, aby instalační program přidal novou instanci SQL Server. Pokud chcete mít jistotu, že volba funguje ve vašem prostředí, vyberte **zkontrolovat a nainstalovat**.

   > [!NOTE]
   > Azure Backup Server nebude fungovat s instancí vzdáleného SQL Server. Instance, kterou používá Azure Backup Server, musí být místní.
   >

    ![Nastavení Azure Backup Server-SQL](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Po ověření, jestli má virtuální počítač nezbytné předpoklady pro instalaci Azure Backup Server, vyberte **Další**.

    ![Azure Backup Server – splněné požadavky](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Pokud dojde k selhání s doporučením pro restartování počítače, restartujte počítač. Po restartování počítače restartujte instalační program a až se dostanete na obrazovku **nastavení SQL** , vyberte **znovu kontrolu**.

5. V **nastavení instalace** zadejte umístění instalace souborů Microsoft Azure Backup serveru a vyberte **Další**.

    ![Zadejte umístění pro instalaci souborů.](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    K zálohování do Azure se vyžaduje pomocné umístění. Zajistěte, aby velikost pravého umístění byla stejná jako aspoň 5% dat, která se mají zálohovat do Azure. V případě ochrany disku je potřeba po dokončení instalace nakonfigurovat samostatné disky. Další informace o fondech úložiště najdete v tématu [Příprava úložiště dat](/system-center/dpm/plan-long-and-short-term-data-storage).

6. Na obrazovce **nastavení zabezpečení** zadejte silné heslo pro omezené místní uživatelské účty a vyberte **Další**.

    ![Obrazovka nastavení zabezpečení](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Na obrazovce **Microsoft Update opt-in** vyberte, jestli chcete pro kontrolu aktualizací použít *Microsoft Update* a vyberte **Další**.

   > [!NOTE]
   > Doporučujeme, abyste web Windows Update přesměrování na Microsoft Update, což nabízí zabezpečení a důležité aktualizace pro Windows a další produkty, jako je Microsoft Azure Backup Server.
   >

    ![Obrazovka Microsoft Update Opt-In](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Zkontrolujte *Souhrn nastavení* a vyberte **nainstalovat**.

    ![Souhrn nastavení](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Po dokončení instalace Azure Backup Server instalační program okamžitě spustí instalační program agenta Microsoft Azure Recovery Services.

9. Spustí se instalační program agenta Microsoft Azure Recovery Services a zkontroluje připojení k Internetu. Pokud je k dispozici připojení k Internetu, pokračujte v instalaci. Pokud není k dispozici žádné připojení, zadejte podrobnosti o proxy serveru pro připojení k Internetu. Po zadání nastavení proxy serveru vyberte **Další**.

    ![Konfigurace proxy serveru](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Chcete-li nainstalovat agenta Microsoft Azure Recovery Services, vyberte možnost **instalovat**.

    ![Instalace agenta](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Agent Microsoft Azure Recovery Services, označovaný také jako agent Azure Backup, nakonfiguruje Azure Backup Server na Recovery Services trezor. Po nakonfigurování bude Azure Backup Server vždy zálohovat data do stejného trezoru Recovery Services.

11. Jakmile Microsoft Azure Recovery Services agenta dokončí instalaci, vyberte **Další** a spusťte další fázi: registrace Azure Backup Server s Recovery Services trezorem.

    ![Instalace agenta byla úspěšně dokončena.](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Instalační program spustí **Průvodce registrací serveru**.

12. Přepněte do svého předplatného Azure a svého Recovery Servicesho trezoru. V nabídce **připravit infrastrukturu** vyberte **Stáhnout** a Stáhněte si přihlašovací údaje trezoru. Pokud tlačítko **Stáhnout** v kroku 2 není aktivní, vyberte možnost **již staženo nebo použití nejnovější Azure Backup Server instalace** pro aktivaci tlačítka. Přihlašovací údaje trezoru se stáhnou do umístění, kam budete ukládat soubory ke stažení. Uvědomte si toto umístění, protože ho budete potřebovat pro další krok.

    ![Stažení přihlašovacích údajů trezoru](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. V nabídce **Identifikace trezoru** vyberte **procházet** a najděte Recovery Services přihlašovací údaje trezoru.

    ![Nabídka identifikace trezoru](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    V dialogovém okně **vybrat přihlašovací údaje trezoru** přejdete do umístění pro stahování, vyberete přihlašovací údaje trezoru a vyberete **otevřít**.

    Cesta k přihlašovacím údajům se zobrazí v nabídce identifikace trezoru. Výběrem možnosti **Další** přejděte k **nastavení šifrování**.

14. V dialogovém okně **nastavení šifrování** zadejte heslo pro šifrování záloh a umístění pro uložení hesla a vyberte **Další**.

    ![Nastavení šifrování](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Můžete zadat vlastní přístupové heslo, nebo ho vytvořit pomocí generátoru přístupového hesla. Přístupové heslo je vaše a Microsoft toto heslo neuloží ani nespravuje. Při přípravě na havárii uložte své heslo do přístupného umístění.

    Jakmile vyberete **Další**, Azure Backup Server je zaregistrované v trezoru Recovery Services. Instalační program pokračuje v instalaci SQL Server a Azure Backup Server.

    ![Instalační program nainstaluje SQL a Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Po dokončení instalačního programu se ve **stavu** zobrazí, že veškerý software byl úspěšně nainstalován.

    ![Software byl úspěšně nainstalován.](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Po dokončení instalace se na ploše serveru vytvoří Konzola Azure Backup Server a Azure Backup Server PowerShellové ikony.

## <a name="add-backup-storage"></a>Přidat úložiště zálohování

První záložní kopie je udržována v úložišti připojeném k Azure Backup Servermu počítači. Další informace o přidávání disků najdete v tématu [Přidání moderního úložiště záloh](/system-center/dpm/add-storage).

> [!NOTE]
> Úložiště zálohování je nutné přidat i v případě, že plánujete odeslat data do Azure. V architektuře Azure Backup Server ukládá trezor Recovery Services *druhou* kopii dat, zatímco místní úložiště obsahuje první (a povinnou) záložní kopii.
>
>

## <a name="network-connectivity"></a>Připojení k síti

Aby produkt mohl úspěšně fungovat, Azure Backup Server vyžaduje připojení ke službě Azure Backup. Pokud chcete ověřit, jestli má počítač připojení k Azure, použijte ```Get-DPMCloudConnection``` rutinu v konzole Azure Backup Server PowerShellu. Pokud má výstup rutiny hodnotu TRUE, pak připojení existuje, jinak není dostupné žádné připojení.

Kromě toho musí být předplatné Azure v dobrém stavu. Pokud chcete zjistit stav předplatného a spravovat ho, přihlaste se na [portál předplatného](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Jakmile budete znát stav připojení Azure a předplatného Azure, můžete pomocí následující tabulky zjistit dopad na nabízené funkce zálohování a obnovení.

| Stav připojení | Předplatné Azure | Zálohování do Azure | Zálohovat na disk | Obnovení z Azure | Obnovení z disku |
| --- | --- | --- | --- | --- | --- |
| Připojeno |Aktivní |Povoleno |Povoleno |Povoleno |Povoleno |
| Připojeno |Platnost vypršela |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Připojeno |Zajištění zrušeno |Zastaveno |Zastaveno |Zastaveno a body obnovení Azure byly odstraněny |Zastaveno |
| Ztracené připojení > 15 dní |Aktivní |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Ztracené připojení > 15 dní |Platnost vypršela |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Ztracené připojení > 15 dní |Zajištění zrušeno |Zastaveno |Zastaveno |Zastaveno a body obnovení Azure byly odstraněny |Zastaveno |

### <a name="recovering-from-loss-of-connectivity"></a>Obnovování ze ztráty připojení

Pokud má počítač omezený přístup k Internetu, zajistěte, aby nastavení brány firewall na počítači nebo proxy umožňovalo následující adresy URL a IP adresy:

* Adresy URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP adresy
  * 20.190.128.0/18
  * 40.126.0.0/18


Po obnovení připojení k Azure do Azure Backup Server stav předplatného Azure určí operace, které je možné provést. Po **připojení** serveru použijte tabulku v [Možnosti připojení k síti](backup-mabs-install-azure-stack.md#network-connectivity) , abyste viděli dostupné operace.

### <a name="handling-subscription-states"></a>Zpracování stavů předplatného

Je možné změnit předplatné Azure ze stavu, ve kterém *vypršela platnost* , nebo *Zrušit* stav na *aktivní* . I když stav předplatného není *aktivní*:

- Při *zrušení zřízení* předplatného ztratí funkčnost. Obnovení předplatného na *aktivní* revives funkce zálohování a obnovení. Pokud se zálohovaná data na místním disku uchovávají s dostatečně velkým obdobím uchovávání, můžou se data ze zálohy načíst. Data záloh v Azure se ale irretrievably ztratí, jakmile předplatné vstoupí do stavu *zrušení zřízení* .
- I když platnost předplatného *vypršela*, ztratí funkčnost. Po *vypršení platnosti* předplatného se nespustí naplánované zálohy.

## <a name="troubleshooting"></a>Řešení potíží

Pokud Microsoft Azure Backup server selhává s chybami při fázi nastavení (nebo zálohování nebo obnovení), přečtěte si [dokument kódy chyb](https://support.microsoft.com/kb/3041338).
Můžete se také podívat na [Azure Backup souvisejících nejčastějších](backup-azure-backup-faq.md) dotazech.

## <a name="next-steps"></a>Další kroky

Článek [Příprava prostředí pro aplikaci DPM](/system-center/dpm/prepare-environment-for-dpm)obsahuje informace o podporovaných konfiguracích Azure Backup Server.

Následující články vám pomohou získat hlubší princip ochrany zatížení pomocí Microsoft Azure Backup serveru.

- [Zálohování SQL Server](./backup-mabs-sql-azure-stack.md)
- [Zálohování serveru SharePoint](./backup-mabs-sharepoint-azure-stack.md)
- [Alternativní zálohování serveru](backup-azure-alternate-dpm-server.md)
