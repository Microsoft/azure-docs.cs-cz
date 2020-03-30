---
title: Instalace Azure Backup Serveru v Azure Stacku
description: V tomto článku se dozvíte, jak pomocí Azure Backup Server chránit nebo zálohovat úlohy v Azure Stacku.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: b78e5a662bdcf23ad38cb33292658d4d2455e579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583431"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Instalace Azure Backup Serveru v Azure Stacku

Tento článek vysvětluje, jak nainstalovat Azure Backup Server na Azure Stack. Pomocí Azure Backup Server můžete chránit úlohy infrastruktury jako služby (IaaS), jako jsou virtuální počítače spuštěné v Azure Stacku. Výhodou použití Azure Backup Serveru k ochraně vašich úloh je správa veškeré ochrany úloh z jedné konzoly.

> [!NOTE]
> Další informace o možnostech zabezpečení najdete v dokumentaci k [funkcím zabezpečení Azure Backup](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Systém ochrany Azure Backup Serveru

Azure Backup Server chrání následující úlohy virtuálních strojů Azure Stack.

| Chráněné zdroje dat | Ochrana a obnovení |
| --------------------- | ----------------------- |
| Pololetní kanál Windows Serveru – datové centrum/podnik/standard | Svazky, soubory a složky |
| Windows Server 2016 – datové centrum/podnik/standard | Svazky, soubory a složky |
| Windows Server 2012 R2 – datové centrum/podnik/standard | Svazky, soubory a složky |
| Windows Server 2012 – datové centrum/podnik/standard | Svazky, soubory a složky |
| Windows Server 2008 R2 – datové centrum/podnik/standard | Svazky, soubory a složky |
| SQL Server 2016 | Databáze |
| SQL Server 2014 | Databáze |
| SQL Server 2012 SP1 | Databáze |
| SharePoint 2016 | Farma, databáze, frontend, webový server |
| SharePoint 2013 | Farma, databáze, frontend, webový server |
| SharePoint 2010 | Farma, databáze, frontend, webový server |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Požadavky pro prostředí Azure Backup Server

Při instalaci Azure Backup Serveru v prostředí Azure Stacku zvažte doporučení v této části. Instalační program Serveru zálohování Azure zkontroluje, zda vaše prostředí má nezbytné předpoklady, ale při přípravě před instalací ušetříte čas.

### <a name="determining-size-of-virtual-machine"></a>Určení velikosti virtuálního počítače

Pokud chcete spouštět Azure Backup Server na virtuálním počítači Azure Stack, použijte velikost A2 nebo větší. Pro pomoc při výběru velikosti virtuálního počítače, stáhněte si [kalkulačku velikosti virtuálního počítače Azure zásobníku](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuální sítě na virtuálních počítačích Azure Stack

Všechny virtuální počítače používané v zatížení Azure Stack musí patřit do stejné virtuální sítě Azure a předplatného Azure.

### <a name="azure-backup-server-vm-performance"></a>Výkon virtuálního počítače azure backup serveru

Pokud je sdílena s jinými virtuálními počítači, velikost účtu úložiště a omezení viop vliv na výkon virtuálních počítačů Azure Backup Server. Z tohoto důvodu byste měli použít samostatný účet úložiště pro virtuální počítač Azure Backup Server. Agent Azure Backup spuštěný na Azure Backup Serveru potřebuje dočasné úložiště pro:

- vlastní použití (umístění mezipaměti),
- data obnovená z cloudu (místní pracovní oblast)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurace dočasného úložiště disků Azure Backup

Každý virtuální počítač Azure Stack je dodáván s dočasným `D:\`úložištěm disku, které je k dispozici uživateli jako svazek . Místní pracovní oblast potřebnou službou Azure Backup `D:\`lze nakonfigurovat tak, `C:\`aby se nakresleti v a umístění mezipaměti lze umístit do aplikace . Tímto způsobem není potřeba žádné úložiště vyřezávat od datových disků připojených k virtuálnímu počítači Azure Backup Server.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Ukládání záložních dat na místní disk a v Azure

Azure Backup Server ukládá záložní data na disky Azure připojené k virtuálnímu počítači pro provozní obnovení. Jakmile jsou disky a úložný prostor připojeny k virtuálnímu počítači, Azure Backup Server spravuje úložiště za vás. Velikost úložiště záložních dat závisí na počtu a velikosti disků připojených ke každému [virtuálnímu počítači Azure Stack](/azure-stack/user/azure-stack-storage-overview). Každá velikost virtuálního počítače azure zásobníku má maximální počet disků, které lze připojit k virtuálnímu počítači. Například A2 je čtyři disky. A3 je osm disků. A4 je 16 disků. Velikost a počet disků opět určuje celkový fond úložiště záloh.

> [!IMPORTANT]
> Neměli **not** byste uchovávat data provozního obnovení (zálohování) na discích připojených k serveru Zálohování Azure více než pět dní.
>

Ukládání záložních dat v Azure snižuje infrastrukturu zálohování v Azure Stacku. Pokud jsou data starší než pět dní, měla by být uložena v Azure.

Chcete-li ukládat záložní data v Azure, vytvořte nebo použijte trezor služby Recovery Services. Při přípravě na zálohování úlohy serveru Zálohování Azure [nakonfigurujete trezor služby Recovery Services](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Po konfiguraci je při každém spuštění úlohy zálohování vytvořen bod obnovení v úschovně. Každý trezor služby Recovery Services pojme až 9999 bodů obnovení. V závislosti na počtu vytvořených bodů obnovení a jejich uchování po dobu jejich uchování můžete uchovávat záložní data po mnoho let. Můžete například vytvořit měsíční body obnovení a uchovat je po dobu pěti let.

### <a name="scaling-deployment"></a>Škálování nasazení

Pokud chcete škálovat nasazení, máte následující možnosti:

- Škálování nahoru – zvětšit velikost virtuálního počítače Azure Backup Server z řady A na řady D a zvýšit místní úložiště [podle pokynů virtuálního počítače azure zásobníku](/azure-stack/user/azure-stack-manage-vm-disks).
- Přetížit data – odesílat starší data do Azure a uchovávat jenom nejnovější data v úložišti připojeném k Azure Backup Serveru.
- Horizontální navýšení kapacity – přidejte další servery zálohování Azure k ochraně úloh.

### <a name="net-framework"></a>.NET Framework

Rozhraní .NET Framework 3.5 SP1 nebo vyšší musí být nainstalováno ve virtuálním počítači.

### <a name="joining-a-domain"></a>Připojení k doméně

Virtuální počítač Azure Backup Server musí být připojen k doméně. Uživatel domény s oprávněními správce musí nainstalovat Azure Backup Server na virtuálním počítači.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Použití virtuálního počítače IaaS v Azure Stacku

Při výběru serveru pro Azure Backup Server začněte s bitovou kopií datového centra Windows Server 2012 R2 nebo galerie datového centra Windows Server 2016. Tento článek [Create your first Windows virtual machine in the Azure portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), provides a tutorial for getting start with the recommended virtual machine. Doporučené minimální požadavky na serverový virtuální počítač (VM) by měly být: A2 Standard se dvěma jádry a 3,5 GB paměti RAM.

Ochrana úloh pomocí Azure Backup Server má mnoho nuancí. Článek [Instalace aplikace DPM jako virtuálního počítače Azure](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12))pomáhá vysvětlit tyto nuance. Před nasazením počítače si přečtěte tento článek úplně.

> [!NOTE]
> Azure Backup Server je navržený tak, aby běžel na vyhrazeném jednoúčelovém virtuálním počítači. Azure Backup Server nelze nainstalovat na:
>
> - Počítač spuštěný jako řadič domény
> - Počítač, na kterém je nainstalovaná role aplikačního serveru
> - Počítač, na kterém je spuštěný server Exchange
> - Počítač, který je uzlem clusteru

Vždy připojte Azure Backup Server k doméně. Pokud potřebujete přesunout Azure Backup Server do jiné domény, nejprve nainstalujte Azure Backup Server a pak se k němu připojte k nové doméně. Po nasazení Serveru Zálohování Azure ho nemůžete přesunout do nové domény.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Nastavení replikace úložiště

Možnost replikace úložiště úložiště služby Recovery Services umožňuje vybrat si mezi geograficky redundantním úložištěm a místně redundantním úložištěm. Ve výchozím nastavení využívají trezory služby Recovery Services geograficky redundantní úložiště. Pokud je tento trezor primárním trezorem, ponechejte možnost úložiště nastavenou na geograficky redundantní úložiště. Zvolte místně redundantní úložiště, pokud chcete levnější možnost, která je méně trvanlivá. Další informace o [geograficky redundantních](../storage/common/storage-redundancy-grs.md) a [místně redundantních](../storage/common/storage-redundancy-lrs.md) možnostech úložiště najdete v [přehledu replikace úložiště Azure.](../storage/common/storage-redundancy.md)

Chcete-li upravit nastavení replikace úložiště:

1. Výběrem trezoru otevřete řídicí panel trezoru a nabídku Nastavení. Pokud se nabídka **Nastavení** neotevře, klikněte na řídicím panelu trezoru na **Všechna nastavení.**
2. V nabídce **Nastavení** klepněte na**položku** Konfigurace zálohování **infrastruktury** > a otevřete nabídku **Konfigurace zálohování.** V nabídce **Konfigurace zálohování** zvolte možnost replikace úložiště pro úložiště.

    ![Seznam trezorů záloh](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Stažení instalačního programu serveru Azure Backup Server

Existují dva způsoby, jak stáhnout instalační program Azure Backup Server. Instalační program serveru Azure Backup Server si můžete stáhnout ze služby [Stažení softwaru .](https://www.microsoft.com/download/details.aspx?id=55269) Instalační službu Azure Backup Server můžete také stáhnout při konfiguraci trezoru služby Recovery Services. Následující kroky vás provedou stažením instalačního programu z webu Azure Portal při konfiguraci trezoru služby Recovery Services.

1. Z virtuálního počítače Azure Stack [se přihlaste k předplatnému Azure na webu Azure Portal](https://portal.azure.com/).
2. V levé nabídce vyberte **všechny služby**.

    ![Volba Možnost všechny služby v hlavní nabídce](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. V dialogovém okně **Všechny služby** zadejte *služby obnovení*. Při psaní vstupní filtry seznamu prostředků. Jakmile se zobrazí, vyberte **trezory služby Recovery Services**.

    ![Zadejte služby obnovení v dialogovém okně Všechny služby.](./media/backup-mabs-install-azure-stack/all-services.png)

    Zobrazí se seznam trezorů služby Recovery Services v předplatném.

4. Ze seznamu trezorů služby Recovery Services vyberte trezor a otevřete řídicí panel.

    ![Zadejte služby obnovení v dialogovém okně Všechny služby.](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. V nabídce Začínáme v úschovně klepnutím na **tlačítko Zálohovat** otevřete Průvodce začínáme.

    ![Zálohování začíná](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Otevře se nabídka zálohování.

    ![Výchozí otevření cílů zálohování](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. V nabídce zálohování vyberte v nabídce **Kde je spuštěná úloha** **možnost Místní**. V rozevírací nabídce **Co chcete zálohovat?** Pokud si nejste jistí, které úlohy vybrat, zvolte **Virtuální počítače Hyper-V** a klikněte na **Připravit infrastrukturu**.

    ![místní a pracovní vytížení jako cíle](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Otevře se nabídka **Připravit infrastrukturu.**

7. V nabídce **Připravit infrastrukturu** kliknutím na **stáhnout** otevřete webovou stránku a stáhněte instalační soubory serveru Azure Backup Server.

    ![Změna průvodce Začínáme](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Otevře se webová stránka Microsoftu, která je hostitelem souborů ke stažení pro Azure Backup Server.

8. Na stránce pro stažení serveru Microsoft Azure Backup Server vyberte jazyk a klepněte na tlačítko **Stáhnout**.

    ![Otevře se centrum stahování.](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Instalační program Azure Backup Server se skládá z osmi souborů – instalačního programu a sedmi souborů .bin. **Zaškrtnutím políčka Název souboru** vyberte všechny požadované soubory a klepněte na tlačítko **Další**. Stáhněte si všechny soubory do stejné složky.

    ![Centrum stahování 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Velikost stahování všech instalačních souborů je větší než 3 GB. Na 10 Mb/s odkaz ke stažení může stahování všech instalačních souborů trvat až 60 minut. Soubory se stahují do zadaného umístění pro stahování.

## <a name="extract-azure-backup-server-install-files"></a>Extrahování instalačních souborů serveru Azure Backup Server

Po stažení všech souborů do virtuálního počítače Azure Stack přejděte do umístění pro stahování. První fáze instalace Azure Backup Server je extrahovat soubory.

![Centrum stahování 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Chcete-li spustit instalaci, klepněte v seznamu stažených souborů na soubor **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > K extrahování instalačních souborů je zapotřebí alespoň 4 GB volného místa.
    >

2. V průvodci Server zálohování Azure klikněte na **Další** a pokračujte.

    ![Průvodce instalací zálohování microsoft azure](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Zvolte cestu pro soubory Azure Backup Server a klepněte na tlačítko **Další**.

   ![Průvodce instalací zálohování microsoft azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Ověřte místo extrakce a klepněte na **tlačítko Extrahovat**.

   ![Průvodce instalací zálohování microsoft azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Průvodce extrahuje soubory a připravuje proces instalace.

   ![Průvodce instalací zálohování microsoft azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Po dokončení procesu extrakce klepněte na tlačítko **Dokončit**. Ve výchozím nastavení je vybrán **příkaz Spustit soubor setup.exe.** Po klepnutí na tlačítko **Dokončit**nainstaluje program Setup.exe server Microsoft Azure Backup Server do zadaného umístění.

   ![Průvodce instalací zálohování microsoft azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Instalace softwarového balíčku

V předchozím kroku jste klepnutím na tlačítko **Dokončit** ukončili fázi extrakce a spusťte průvodce nastavením serveru Zálohování Azure.

![Průvodce instalací zálohování microsoft azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server sdílí kód se Správcem ochrany dat. Odkazy na Správce ochrany dat a DPM se zobrazí v instalačním programu Serveru zálohování Azure. Přestože Azure Backup Server a Správce ochrany dat jsou samostatné produkty, tyto produkty jsou úzce související.

1. Chcete-li spustit průvodce instalací, klepněte na položku **Microsoft Azure Backup Server**.

   ![Průvodce instalací zálohování microsoft azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Na obrazovce **Vítejte** klikněte na **Další**.

    ![Azure Backup Server – kontrola uvítání a předpokladů](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Na obrazovce **Kontrola předpokladů** klikněte na **zkontrolovat** a zjistěte, zda byly splněny požadavky hardwaru a softwaru pro Server zálohování Azure.

    ![Azure Backup Server – kontrola uvítání a předpokladů](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Pokud vaše prostředí má nezbytné požadavky, zobrazí se zpráva oznamující, že počítač splňuje požadavky. Klikněte na **Další**.  

    ![Azure Backup Server – byla předána kontrola předpokladů](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Pokud vaše prostředí nesplňuje nezbytné požadavky, budou určeny problémy. Požadavky, které nebyly splněny, jsou také uvedeny v souboru DpmSetup.log. Vyřešte chyby předpokladů a **spusťte znovu spustit klávesu Check again**. Instalace nemůže pokračovat, dokud nejsou splněny všechny požadavky.

    ![Azure Backup Server – nejsou splněny požadavky na instalaci](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server vyžaduje SQL Server. Instalační balíček Azure Backup Server je dodáván s příslušnými binárními soubory sql serveru. Pokud chcete použít vlastní instalaci SQL, můžete. Doporučenou volbou je však nechat instalační program přidat novou instanci SQL Server. Chcete-li zajistit, aby vaše volba fungovala s vaším prostředím, klepněte na tlačítko **Zkontrolovat a nainstalovat**.

   > [!NOTE]
   > Azure Backup Server nebude fungovat se vzdálenou instancí serveru SQL Server. Instance používaná serverem Azure Backup Server musí být místní.
   >

    ![Azure Backup Server – kontrola uvítání a předpokladů](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Pokud má virtuální počítač po kontrole nezbytné předpoklady k instalaci serveru Azure Backup Server, klikněte po použití na tlačítko **Další**.

    ![Azure Backup Server – kontrola uvítání a předpokladů](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Pokud dojde k selhání s doporučením restartovat počítač, restartujte počítač. Po restartování počítače restartujte instalační program a po zobrazení na obrazovce **Nastavení SQL** klepněte na tlačítko **Znovu**zkontrolovat .

5. V **nastavení instalace**zadejte umístění pro instalaci souborů serveru Microsoft Azure Backup a klepněte na tlačítko **Další**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Odškrtnuté umístění je potřeba zálohovat do Azure. Ujistěte se, že velikost odkládací umístění je ekvivalentní alespoň 5 % plánovaných dat, která mají být zálohována do Azure. Z důvodu ochrany disku je třeba po dokončení instalace nakonfigurovat samostatné disky. Další informace týkající se fondů úložiště naleznete v [tématu Konfigurace fondů úložišť a diskového úložiště](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).

6. Na obrazovce **Nastavení zabezpečení** zadejte silné heslo pro místní uživatelské účty s omezeným přístupem a klepněte na tlačítko **Další**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Na obrazovce **Přihlášení k webu Microsoft Update** vyberte, zda chcete pomocí služby Microsoft *Update* vyhledat aktualizace, a klepněte na tlačítko **Další**.

   > [!NOTE]
   > Doporučujeme přesměrovat službu Windows Update na službu Microsoft Update, která nabízí aktualizace zabezpečení a důležité aktualizace pro systém Windows a další produkty, jako je Microsoft Azure Backup Server.
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Projděte si *souhrn nastavení* a klepněte na tlačítko **Instalovat**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Po dokončení instalace serveru Azure Backup Server instalační program okamžitě spustí instalační program agenta služby Microsoft Azure Recovery Services.

9. Otevře se instalační program agenta služby Microsoft Azure Recovery Services a zkontroluje připojení k Internetu. Pokud je k dispozici připojení k Internetu, pokračujte v instalaci. Pokud není k dispozici připojení, zadejte podrobnosti proxy pro připojení k Internetu. Po zadání nastavení proxy serveru klepněte na tlačítko **Další**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Chcete-li nainstalovat agenta služby Microsoft Azure Recovery Services, klepněte na tlačítko **Nainstalovat**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Agent služby Microsoft Azure Recovery Services, nazývaný také agent Azure Backup, konfiguruje server Zálohování Azure do trezoru služby Recovery Services. Po konfiguraci bude Server Zálohování Azure vždy zálohovat data do stejného trezoru služby Recovery Services.

11. Po dokončení instalace agenta služby Microsoft Azure Recovery Services klikněte na **Další** a spusťte další fázi: registrace serveru Azure Backup Server pomocí trezoru služby Recovery Services.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Instalační program spustí **Průvodce registrem serveru**.

12. Přepněte na předplatné Azure a trezor služeb pro obnovení. V nabídce **Připravit infrastrukturu** klikněte na **Stáhnout a** stáhněte přihlašovací údaje trezoru. Pokud tlačítko **Stáhnout** v kroku 2 není aktivní, vyberte **možnost Již staženo nebo pomocí nejnovější instalace serveru Azure Backup Server** tlačítko aktivovat. Přihlašovací údaje trezoru se stáhnou do umístění, kde ukládáte soubory ke stažení. Uvědomte si toto umístění, protože ho budete potřebovat pro další krok.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. V nabídce **Identifikace úložiště** klikněte na **Procházet** a vyhledejte přihlašovací údaje trezoru služby Recovery Services.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    V dialogovém okně **Vybrat pověření trezoru** přejděte do umístění pro stahování, vyberte přihlašovací údaje trezoru a klepněte na **tlačítko Otevřít**.

    Cesta k pověřením se zobrazí v nabídce Identifikace úložiště. Klepnutím na tlačítko **Další** překroušěte nastavení šifrování.

14. V dialogovém okně **Nastavení šifrování** zadejte přístupové heslo pro šifrování záloh a umístění pro uložení přístupové fráze a klepněte na tlačítko **Další**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Můžete zadat vlastní přístupové heslo, nebo použít generátor přístupových frází k vytvoření jednoho pro vás. Přístupové heslo je vaše a společnost Microsoft toto přístupové heslo neukládá ani nespravuje. Chcete-li se připravit na havárii, uložte přístupové heslo na přístupné místo.

    Po klepnutí na tlačítko **Další**se server Azure Backup Server zaregistruje v trezoru služby Recovery Services. Instalační program pokračuje v instalaci serveru SQL Server a serveru Zálohování Azure.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Po dokončení instalačního programu stav zobrazí, že byl úspěšně nainstalován veškerý software.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Po dokončení instalace se na ploše serveru vytvoří konzola Azure Backup Server a ikony Azure Backup Server PowerShell.

## <a name="add-backup-storage"></a>Přidání úložiště záloh

První záložní kopie se uchovává v úložišti připojeném k počítači Azure Backup Server. Další informace o přidávání disků naleznete v tématu [Přidání úložiště moderního zálohování](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Úložiště záloh je potřeba přidat, i když plánujete odesílat data do Azure. V architektuře Azure Backup Server trezoru služby recovery Services obsahuje *druhou* kopii dat, zatímco místní úložiště obsahuje první (a povinné) záložní kopie.
>
>

## <a name="network-connectivity"></a>Připojení k síti

Azure Backup Server vyžaduje připojení ke službě Azure Backup, aby produkt úspěšně fungoval. Chcete-li ověřit, zda má počítač ```Get-DPMCloudConnection``` připojení k Azure, použijte rutinu v konzole PowerShell serveru Zálohování Azure. Pokud je výstup rutiny TRUE, pak připojení existuje, jinak neexistuje žádné připojení.

Současně předplatné Azure musí být ve stavu v pořádku. Chcete-li zjistit stav předplatného a spravovat ho, přihlaste se na [portál předplatného](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Jakmile budete znát stav připojení Azure a předplatného Azure, můžete pomocí následující tabulky zjistit dopad na nabízené funkce zálohování a obnovení.

| Stav připojení | předplatné Azure | Zálohování do Azure | Zálohování na disk | Obnovení z Azure | Obnovení z disku |
| --- | --- | --- | --- | --- | --- |
| Připojeno |Aktivní |Povoleno |Povoleno |Povoleno |Povoleno |
| Připojeno |Platnost vypršela |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Připojeno |Zrušení zřízení |Zastaveno |Zastaveno |Zastaveno a odstraněny body obnovení Azure |Zastaveno |
| Ztráta připojení > 15 dní |Aktivní |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Ztráta připojení > 15 dní |Platnost vypršela |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Ztráta připojení > 15 dní |Zrušení zřízení |Zastaveno |Zastaveno |Zastaveno a odstraněny body obnovení Azure |Zastaveno |

### <a name="recovering-from-loss-of-connectivity"></a>Zotavuje se ze ztráty připojení

Pokud brána firewall nebo proxy server brání přístupu k Azure, přidejte do seznamu povolených profilů brány firewall/proxy následující adresy domén:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Po obnovení připojení k Azure na Azure Backup Server, stav předplatného Azure určuje operace, které lze provést. Jakmile je server **připojen**, použijte tabulku v [části Připojení k síti,](backup-mabs-install-azure-stack.md#network-connectivity) abyste viděli dostupné operace.

### <a name="handling-subscription-states"></a>Zpracování stavů předplatného

Je možné změnit předplatné Azure z *vypršela* nebo *deprovisioned* stavu *aktivní* ho stavu. Zatímco stav předplatného není *aktivní*:

- Zatímco předplatné je *deprovisioned*, ztratí funkce. Obnovení předplatného *active*, oživí funkce zálohování a obnovení. Pokud byla záložní data na místním disku zachována s dostatečně velkou dobou uchování, lze tato záložní data načíst. Záložní data v Azure se však nenávratně ztratí, jakmile předplatné vstoupí do stavu *Deprovisioned.*
- Při vypršení *Expired*platnosti předplatného ztratí funkce. Plánované zálohy se nespustí, pokud *vypršela platnost*předplatného .

## <a name="troubleshooting"></a>Řešení potíží

Pokud server Microsoft Azure Backup selže s chybami během fáze instalace (nebo zálohování nebo obnovení), přečtěte si [dokument kódů chyb](https://support.microsoft.com/kb/3041338).
Můžete také odkazovat na [nejčastější dotazy týkající se azure backup](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Další kroky

Článek [Příprava prostředí pro aplikaci DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801)obsahuje informace o podporovaných konfiguracích serveru Zálohování Azure.

Následující články můžete získat hlubší znalosti ochrany úloh pomocí Microsoft Azure Backup Server.

- [Zálohování serveru SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [Zálohování sharepointového serveru](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Alternativní zálohování serveru](backup-azure-alternate-dpm-server.md)
