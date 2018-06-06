---
title: Nainstalujte Azure Backup Server na Azure zásobníku | Microsoft Docs
description: Použijte Azure Backup Server při ochraně nebo zálohování úloh v zásobníku Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: Azure backup server; chránit úlohy; zálohování úloh
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/5/2018
ms.author: markgal
ms.openlocfilehash: c79ca93138961e294f03e283466dd66250472dae
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801734"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Nainstalujte Azure Backup Server na Azure zásobníku

Tento článek vysvětluje postup instalace serveru Azure Backup v zásobníku Azure. Pomocí serveru Azure Backup Server můžete chránit úlohy aplikace běžící v Azure zásobníku z jediné konzoly.

> [!NOTE]
> Další informace o funkcích zabezpečení, najdete v tématu [Azure Backup bezpečnostní funkce dokumentaci](backup-azure-security-feature.md).
>

Můžete taky chránit infrastruktury jako služby (IaaS) zatížení jako virtuální počítače v Azure.

Prvním krokem k serveru Azure Backup zprovoznění je nastavení virtuálního počítače v Azure zásobníku.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Pomocí virtuálních počítačů IaaS v Azure zásobníku

Při výběru serveru pro Azure Backup Server, začněte bitové kopie systému Windows Server 2012 R2 Datacenter nebo Windows Server 2016 Datacenter galerie. V článku [vytvořit svůj první virtuální počítač Windows v portálu Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), obsahuje návod pro zahájení práce s doporučenou virtuálního počítače. Doporučená minimální požadavky na virtuální počítač (VM) serveru by měl být: A2 standardní dvě jádra a 3.5 GB paměti RAM.

Ochrana zatížení pomocí serveru Azure Backup má mnoho drobné odlišnosti. V článku [instalaci aplikace DPM jako virtuální počítač Azure](https://technet.microsoft.com/library/jj852163.aspx), pomáhá popisují tyto drobné odlišnosti. Před nasazením počítače, přečtěte si tento článek úplně.

> [!NOTE]
> Azure Backup Server je určená ke spuštění na vyhrazeném, jednoúčelové virtuálního počítače. Azure Backup Server nejde nainstalovat na:
> - Počítač spuštěný jako řadič domény
> - Počítač, na kterém je nainstalovaná role aplikačního serveru
> - Počítač, na kterém je spuštěný server Exchange
> - Počítač, který je uzlem clusteru

Vždycky připojení k serveru Azure Backup k doméně. Pokud potřebujete přesunout do jiné domény serveru Azure Backup, nejprve nainstalovat Server pro zálohování Azure a připojení k nové doméně. Jakmile nasadíte serveru Azure Backup, nemůžete ho přesunout do nové domény.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Nastavení replikace úložiště

Možnost replikace úložiště trezoru služeb zotavení umožňuje výběr mezi geograficky redundantním úložištěm a místně redundantního úložiště. Ve výchozím nastavení používají trezory služeb zotavení geograficky redundantní úložiště. Pokud tento trezor trezoru primární, ponechte možnost úložiště do geograficky redundantní úložiště. Zvolte místně redundantní úložiště, pokud chcete levnější možnost, která je menší odolný. Další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy-grs.md) a [místně redundantního](../storage/common/storage-redundancy-lrs.md) úložiště naleznete v tématu [Přehled replikace Azure Storage](../storage/common/storage-redundancy.md).

Chcete-li upravit nastavení replikace úložiště:

1. Vyberte svůj trezor pro otevření panelu trezoru a v nabídce nastavení. Pokud **nastavení** nabídky neotevře, klikněte na tlačítko **všechna nastavení** v řídícím panelu trezoru.
2. Na **nastavení** nabídky, klikněte na tlačítko **infrastruktura zálohování** > **konfigurace zálohování** otevřete **konfigurace zálohování**nabídky. Na **konfigurace zálohování** nabídce zvolte možnost replikace úložiště pro svůj trezor.

    ![Seznam trezorů záloh](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Stažení instalačního programu serveru Azure Backup

Po vytvoření trezoru služeb zotavení, použijte nabídku Začínáme v trezoru služeb zotavení pro stažení instalačního programu serveru Azure Backup k virtuálnímu počítači Azure zásobníku. Tyto kroky provést ve vašem předplatném Azure.

1. Z virtuálního počítače Azure zásobníku [Přihlaste se k předplatnému Azure na portálu Azure](https://portal.azure.com/).
2. V levé nabídce vyberte **všechny služby**.

    ![Vyberte možnost všechny služby v hlavní nabídce](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. V **všechny služby** dialogové okno, typ *služeb zotavení*. Během zadávání váš vstup filtrování seznamu prostředků. Jakmile se zobrazí, vyberte **trezory služeb zotavení**.

    ![Typ služeb zotavení v dialogovém okně všechny služby](./media/backup-mabs-install-azure-stack/all-services.png)

    Zobrazí se seznam trezorů služeb zotavení v rámci předplatného.

4. Ze seznamu trezorů služeb zotavení vyberte svůj trezor pro otevření jeho řídicího panelu.

    ![Typ služeb zotavení v dialogovém okně všechny služby](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. V nabídce Začínáme k trezoru, klikněte na tlačítko **zálohování** otevřete Průvodce Začínáme.

    ![Zálohování Začínáme](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Otevře se nabídka zálohování.

    ![Zálohování cíle – výchozí – otevřít](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. V nabídce zálohování z **kde běží vaše úlohy** nabídce vyberte možnost **místní**. Z **co chcete zálohovat?** rozevírací nabídce vyberte možnost úlohy, které chcete chránit pomocí Azure Backup Server. Pokud si nejste jisti, které úlohy a vyberte, zvolte **virtuální počítače Hyper-V** a pak klikněte na **Příprava infrastruktury**.

    ![místní a úloh jako cíle](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    **Připravit infrastrukturu** otevře se nabídka.

7. V **připravit infrastrukturu** nabídky, klikněte na tlačítko **Stáhnout** k otevření webové stránky ke stažení instalačních souborů serveru Azure Backup.

    ![Získávání změnu Průvodce Začínáme](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Na stránce společnosti Microsoft, který je hostitelem soubory ke stažení pro Azure Backup Server, otevře se okno.

8. Na stránce stažení serveru Microsoft Azure Backup Server vyberte jazyk a klikněte na tlačítko **Stáhnout**.

    ![Stáhněte si otevře center](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Instalační program serveru Azure Backup se skládá z osm soubory – Instalační program a sedm .bin soubory. Zkontrolujte **název souboru** vyberte všechny požadované soubory a klikněte na tlačítko **Další**. Stažení všech souborů do stejné složky.

    ![Stažení softwaru 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Vzhledem k tomu, že stažení velikost všech souborů je > 3G, na 10 MB/s stahoval odkazu, který může trvat až 60 minut ke stažení pro dokončení. Soubory stáhne vaše zadané umístění pro stahování.

## <a name="extract-azure-backup-server-install-files"></a>Extrahujte soubory instalace serveru Azure Backup

Po stažení všech souborů k virtuálnímu počítači, přejděte k umístění stahování.

![Stažení softwaru 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Chcete-li spustit instalaci, ze seznamu stažené soubory, klikněte na tlačítko **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > K extrakci instalačních souborů se vyžaduje minimálně 4GB volného místa.
    >

2. V instalačním programu serveru Azure Backup, klikněte na tlačítko **Další** spusťte průvodce.

    ![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Zvolit umístění, kam chcete nainstalovat Server pro zálohování Azure a klikněte na tlačítko **Další**.

   ![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Zkontrolujte umístění instalace a klikněte na tlačítko **extrahovat**.

   ![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Instalační program extrahuje soubory a připravíte proces instalace.

   ![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Po dokončení procesu extrakce, klikněte na **Dokončit** spustíte *setup.exe*. Setup.exe nainstaluje Microsoft Azure Backup Server.

   ![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Instalovat balíček softwaru

V předchozím kroku jste klikli na **Dokončit** ukončete fázi extrakce, a spusťte Průvodce instalací serveru Azure Backup.

![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server sdílí kód s aplikací Data Protection Manager. Zobrazí se odkazy na Data Protection Manager a aplikace DPM v instalačním programu serveru Azure Backup. Když Azure Backup Server a Data Protection Manager jsou samostatných produktů, odkazy nebo nástroje, které mají Data Protection Manager nebo aplikace DPM, platí pro Azure Backup Server.

1. Chcete-li spustit Průvodce instalací, klikněte na tlačítko **Microsoft Azure Backup**.

   ![Průvodce instalací zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Na úvodní obrazovce klikněte na tlačítko **Další**.

    ![Zkontrolujte Server Azure Backup - uvítací a požadavky](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Na *požadovaných součástí kontroluje* obrazovky, klikněte na tlačítko **zkontrolujte** k určení, pokud byly splněny požadavky hardware a software pro Azure Backup Server.

    ![Zkontrolujte Server Azure Backup - uvítací a požadavky](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Pokud vaše prostředí disponuje nezbytných předpokladů, zobrazí se zpráva s oznámením, že počítač splňuje požadavky. Klikněte na **Další**.

    ![Server Azure Backup - požadavky při kontrole vyhověl.](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

4. Microsoft Azure Backup Server vyžaduje SQL Server. Instalační balíček serveru Azure Backup obsahuje připojené příslušné binární soubory systému SQL Server nutný v případě, že nechcete používat vlastní SQL. Doporučené volba je umožnit instalační program, přidejte novou instanci systému SQL Server. Chcete-li zajistit používání prostředí systému SQL Server, klikněte na tlačítko **zkontrolovat a nainstalovat**.

   > [!NOTE]
   > Azure Backup Server nebude fungovat se vzdálenou instanci systému SQL Server. Musí být místní instance, používá Server pro zálohování Azure.
   >

    ![Zkontrolujte Server Azure Backup - uvítací a požadavky](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Po kontrole, pokud má počítač nezbytných předpokladů pro instalaci serveru pro zálohování Azure, klikněte na tlačítko **Další**.

    ![Zkontrolujte Server Azure Backup - uvítací a požadavky](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Pokud dojde k selhání s doporučení k restartování počítače, tak učinit, klikněte na tlačítko restartovat instalační program na této obrazovce **zkontrolujte znovu**.

5. V **nastavení instalace**, zadejte umístění pro instalaci souborů serveru Microsoft Azure Backup a klikněte na **Další**.

    ![PreReq2 zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Pomocné umístění je nezbytné k zálohování do Azure. Zajistěte, aby že velikost pomocné umístění je ekvivalentní minimálně 5 % dat plánované zálohování do Azure. U ochrany disku samostatné disky potřeba nakonfigurovat po dokončení instalace. Další informace týkající se fondů úložiště najdete v tématu [nakonfigurujte fondy úložiště a disk úložiště](https://technet.microsoft.com/library/hh758075.aspx).

6. Na **nastavení zabezpečení** obrazovce zadejte silné heslo pro omezené místní uživatelské účty a klikněte na tlačítko **Další**.

    ![PreReq2 zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Na **Microsoft Update Opt-In** obrazovky, určete, jestli chcete použít *Microsoft Update* vyhledávat aktualizace, a klikněte na tlačítko **Další**.

   > [!NOTE]
   > Doporučujeme vám, že přesměrování na webu Microsoft Update nabízí aktualizace zabezpečení a důležité aktualizace pro Windows a další produkty, jako je Microsoft Azure Backup Server služby Windows Update.
   >

    ![PreReq2 zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Zkontrolujte *souhrn nastavení* a klikněte na tlačítko **nainstalovat**.

    ![PreReq2 zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Po dokončení instalace serveru Azure Backup instalační program okamžitě spustí instalační program agenta služeb zotavení Microsoft Azure.

9. Otevře se instalační program agenta služeb zotavení Microsoft Azure a kontroly pro připojení k Internetu. Pokud je k dispozici připojení k Internetu, pokračujte v instalaci. Pokud není k dispozici žádné připojení, zadejte podrobnosti o proxy serveru pro připojení k Internetu. Jakmile jste určili vaše nastavení proxy serveru, klikněte na možnost **Další**.

    ![PreReq2 zálohování Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Chcete-li nainstalovat agenta služeb zotavení Microsoft Azure, klikněte na tlačítko **nainstalovat**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Agent služeb zotavení Microsoft Azure, označované taky jako agenta Azure Backup nakonfiguruje serveru Azure Backup do trezoru služeb zotavení. Po nakonfigurování Azure Backup Server bude vždy je třeba zálohovaná data ke stejnému trezoru služeb zotavení.

11. Po dokončení instalace agenta služeb zotavení Microsoft Azure klikněte na **Další** spustit v další fázi: registrace serveru Azure Backup trezor služeb zotavení.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Instalační program spustí **Průvodce registrací serveru**.

12. Přepněte do vašeho předplatného Azure a svůj trezor služeb zotavení. V **Příprava infrastruktury** nabídky, klikněte na tlačítko **Stáhnout** stáhnout přihlašovací údaje trezoru. Pokud **Stáhnout** tlačítko v kroku 2 není aktivní, vyberte **již stáhnout nebo pomocí nejnovější instalaci serveru Azure Backup** aktivovat tlačítko. Přihlašovací údaje trezoru stáhnout do umístění, kam můžete ukládat soubory ke stažení. Pamatujte na toto umístění vzhledem k tomu, že ho budete potřebovat pro další krok.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. V **identifikaci trezoru** nabídky, klikněte na tlačítko **Procházet** najít přihlašovací údaje trezoru služeb zotavení.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    V **vyberte přihlašovací údaje trezoru** dialogové okno, přejděte do umístění stahování, vyberte svoje přihlašovací údaje trezoru a klikněte na tlačítko **otevřete**.

    Cesta k přihlašovací údaje se zobrazí v nabídce identifikaci trezoru. Klikněte na tlačítko **Další** přejdete k nastavení šifrování.

14. V **nastavení šifrování** dialogové okno, zadejte heslo pro šifrování záloh a umístění pro uložení přístupového hesla a klikněte na **Další**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Můžete zadat své vlastní heslo, nebo pomocí generátoru přístupové heslo můžete vytvořit za vás. Heslo je váš a Microsoft uložit nebo spravovat toto heslo. Příprava pro havárie, uložte do přístupného umístění heslo.

    Po kliknutí na tlačítko **Další**, serveru Azure Backup je zaregistrován v trezoru služeb zotavení. Instalační program pokračuje v instalaci systému SQL Server a Azure Backup Server.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Když instalační program dokončí, je ve stavu úspěšně nainstalován veškerý software.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Po dokončení instalace, vytvoří se na ploše serveru konzole serveru Azure Backup a ikony prostředí PowerShell pro Server Azure Backup.

### <a name="add-backup-storage"></a>Přidání úložiště záloh

První záložní kopie se ukládají na úložiště připojená k počítači Azure Backup Server. Další informace o přidávání disků, najdete v tématu [nakonfigurujte fondy úložiště a disk úložiště](https://technet.microsoft.com/library/hh758075.aspx).

> [!NOTE]
> Je nutné přidat úložiště záloh, i v případě, že chcete odesílat data do Azure. V architektuře aktuální Azure Backup Server úložiště záloh Azure obsahuje *druhý* kopie dat, zatímco místní úložiště obsahuje záložní kopie první (a povinné).
>
>

## <a name="network-connectivity"></a>Připojení k síti

Azure Backup Server vyžaduje připojení ke službě Azure Backup pro produkt, nemusí fungovat správně. Chcete-li ověřit, zda je počítač připojen k Azure, použijte ```Get-DPMCloudConnection``` rutiny v konzole prostředí PowerShell pro Server Azure Backup. Pokud výstup rutiny hodnotu TRUE, pak připojení existuje, jinak je k dispozici žádné připojení.

Předplatné Azure ve stejnou dobu, musí být v dobrém stavu. Chcete-li zjistit stav předplatného a k její správě, přihlaste se k [předplatné portál](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Jakmile víte o stavu připojení Azure a předplatné Azure, můžete zjistit dopad na zálohování a obnovení funkce nabízené následující tabulka.

| Stav připojení | předplatné Azure | Zálohovat do Azure | Zálohování na disk | Obnovení z Azure | Obnovení z disku |
| --- | --- | --- | --- | --- | --- |
| Připojeno |Aktivní |Povoleno |Povoleno |Povoleno |Povoleno |
| Připojeno |Vypršela platnost |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Připojeno |Zrušit |Zastaveno |Zastaveno |Body obnovení zastaven a Azure odstranit |Zastaveno |
| Ke ztrátě připojení > 15 dnů |Aktivní |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Ke ztrátě připojení > 15 dnů |Vypršela platnost |Zastaveno |Zastaveno |Povoleno |Povoleno |
| Ke ztrátě připojení > 15 dnů |Zrušit |Zastaveno |Zastaveno |Body obnovení zastaven a Azure odstranit |Zastaveno |

### <a name="recovering-from-loss-of-connectivity"></a>Obnovení z ztráty připojení

Pokud je brána firewall nebo proxy server je brání přístupu k Azure, seznam povolených adres tuto doménu adresy v profilu brány firewall a proxy:

- www.msftncsi.com
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Po obnovení připojení k Azure k serveru Azure Backup stav předplatného Azure určuje operace, které lze provést. Jakmile bude server **připojeno**, pomocí tabulky uvedené v [připojení k síti](backup-mabs-install-azure-stack.md#network-connectivity) chcete zobrazit dostupné operace.

### <a name="handling-subscription-states"></a>Zpracování – stavy předplatného

Je možné změnit předplatné Azure z *platnost vypršela* nebo *Deprovisioned* stavu na *Active* stavu. Přestože stav předplatného není *Active*:

- Předplatné je *Deprovisioned*, se ztratí funkce. Obnovení odběr *Active*, revives funkci zálohování a obnovení. Pokud se zálohovaná data na místním disku byla zachována s dobou uchování dostatečně velký, zálohování dat mohou být načteny. Zálohování dat v Azure je však nenahraditelně ztraceny po zadání předplatné *Deprovisioned* stavu.
- Předplatné je *platnost vypršela*, se ztratí funkce. Naplánovaných záloh se nespustí, zatímco je předplatné *platnost vypršela*.

## <a name="troubleshooting"></a>Řešení potíží

Pokud Microsoft Azure Backup server selže s chybami během fáze instalace (nebo zálohování nebo obnovení), najdete v článku [dokumentu kódy chyb](https://support.microsoft.com/kb/3041338).
Můžete se také podívat na [nejčastější dotazy týkající se Azure Backup](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Další postup

V článku [Příprava prostředí pro aplikaci DPM](https://technet.microsoft.com/library/hh758176.aspx), obsahuje informace o podporovaných konfiguracích serveru Azure Backup.

V následujících článcích můžete získat lepší představu o ochrana pracovního vytížení pomocí serveru služby Microsoft Azure Backup.

- [Zálohování serveru SQL Server](backup-azure-backup-sql.md)
- [Zálohování serveru SharePoint](backup-azure-backup-sharepoint.md)
- [Alternativní server zálohování](backup-azure-alternate-dpm-server.md)
