---
title: Zálohování úloh do Azure pomocí serveru Azure Backup | Microsoft Docs
description: Použijte Azure Backup Server při ochraně nebo zálohovat úlohy na portál Azure.
services: backup
documentationcenter: ''
author: PVRK
manager: shivamg
editor: ''
keywords: Azure backup server; chránit úlohy; zálohování úloh
ms.assetid: e7fb1907-9dc1-4ca1-8c61-50423d86540c
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/5/2018
ms.author: masaran;trinadhk;pullabhk;markgal;adigan
ms.openlocfilehash: 3b37afc9d768313f6cc202eeecca22528cc57b07
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="preparing-to-back-up-workloads-using-azure-backup-server"></a>Příprava zálohování úloh pomocí Azure Backup Serveru
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Tento článek vysvětluje postup přípravy prostředí pro zálohování úloh pomocí serveru Azure Backup. Pomocí serveru Azure Backup Server může chránit úlohy aplikace například virtuální počítače Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange a klienty systému Windows z jediné konzoly.

> [!NOTE]
> Azure Backup Server nyní chrání virtuální počítače VMware a poskytuje vyšší úroveň zabezpečení možnosti. Nainstalujte produkt, jak je popsáno v následující části obsahují; Použijte Update 1 a nejnovější Azure Backup Agent. Další informace o zálohování serverů VMware s Azure Backup Server, najdete v článku [serveru Azure Backup pomocí zálohování serveru VMware](backup-azure-backup-server-vmware.md). Další informace o funkcích zabezpečení, najdete v tématu [zabezpečení Azure backup funkce dokumentaci](backup-azure-security-feature.md).
>
>

Můžete taky chránit infrastruktury jako služby (IaaS) zatížení jako virtuální počítače v Azure.

> [!NOTE]
> Azure má dva modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek obsahuje informace a postupy pro obnovení virtuální počítače nasazené pomocí modelu Resource Manager.
>
>

Azure Backup Server většinu funkcí zálohování zatížení dědí z Data Protection Manager (DPM). Tento článek obsahuje odkazy na dokumentace k aplikaci DPM vysvětlit některé ze sdílených funkcí. Když Azure Backup Server sdílí většinu stejné funkce jako aplikace DPM. Azure Backup Server není zálohování na pásku, ani umožňuje integraci s nástrojem System Center.

## <a name="choose-an-installation-platform"></a>Zvolte instalační platformy
Prvním krokem k serveru Azure Backup zprovoznění je nastavit systém Windows Server. Váš server může být v Azure nebo místně.

### <a name="using-a-server-in-azure"></a>Pomocí serveru v Azure
Při výběru serveru ke spuštění serveru Azure Backup, doporučuje se, že začínáte s Galerie bitové kopie systému Windows Server 2012 R2 Datacenter nebo Windows Server 2016 Datacenter. V článku [vytvořit svůj první virtuální počítač Windows v portálu Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), obsahuje návod pro zahájení práce s doporučenou virtuálního počítače v Azure, i když jste Azure před nepoužívali. Doporučená minimální požadavky na virtuální počítač (VM) serveru by měl být: A2 standardní dvě jádra a 3.5 GB paměti RAM.

Ochrana zatížení pomocí serveru Azure Backup má mnoho drobné odlišnosti. V článku [instalaci aplikace DPM jako virtuální počítač Azure](https://technet.microsoft.com/library/jj852163.aspx), pomáhá popisují tyto drobné odlišnosti. Před nasazením počítače, přečtěte si tento článek úplně.

### <a name="using-an-on-premises-server"></a>Pomocí místního serveru
Pokud nechcete spustit základní server v Azure, můžete spustit server na virtuální počítač Hyper-V, virtuální počítače VMware nebo fyzické hostitele. Doporučená minimální požadavky na hardware serveru jsou dvě jádra a 4 GB paměti RAM. Podporované operační systémy jsou uvedené v následující tabulce:

| Operační systém | Platforma | Skladová jednotka (SKU) |
|:--- | --- |:--- |
| Windows Server 2016 a nejnovější aktualizace Service packu |64bitová verze |Standard, Datacenter, Essentials (MABS v2 a vyšší) |
| Windows Server 2012 R2 a nejnovější aktualizace Service Packu |64bitová verze |Standard, Datacenter, Foundation |
| Windows Server 2012 a nejnovější aktualizace Service Packu |64bitová verze |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 a nejnovější aktualizace Service Packu |64bitová verze |Standard, Workgroup |
| Windows Storage Server 2012 a nejnovější aktualizace Service Packu |64bitová verze |Standard, Workgroup |

Úložiště DPM využívající Windows Server odstranění duplicitních dat můžete duplicity odstranit. Další informace o [aplikace DPM a odstranění duplicit](https://technet.microsoft.com/library/dn891438.aspx) spolupracují při nasazení ve virtuálních počítačích Hyper-V.

> [!NOTE]
> Azure Backup Server je určená ke spuštění na vyhrazeném samostatném jednoúčelovém serveru. Azure Backup Server nejde nainstalovat na:
> - Počítač spuštěný jako řadič domény
> - Počítač, na kterém je nainstalovaná role aplikačního serveru
> - Počítač, který je serverem pro správu nástroje System Center Operations Manager
> - Počítač, na kterém je spuštěný server Exchange
> - Počítač, který je uzlem clusteru

Vždycky připojení k serveru Azure Backup k doméně. Pokud budete chtít přesunout server do jiné domény, doporučujeme připojení serveru k nové doméně před instalací serveru pro zálohování Azure. Přesunutí existující počítač serveru Azure Backup do nové domény po nasazení *nepodporuje*.

## <a name="recovery-services-vault"></a>Trezor služby Recovery Services
Ať už odesílání zálohovaných dat do Azure nebo udržovat místně, softwaru musí být připojen k Azure. Jako další konkrétní, počítač serveru Azure Backup musí zaregistrovat u trezoru služeb zotavení.

Vytvoření trezoru Recovery Services:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce centra klikněte na **Procházet** a v seznamu prostředků zadejte **Recovery Services**. Seznam se průběžně filtruje podle zadávaného textu. Klikněte na **Trezor Recovery Services**.

    ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png) <br/>

    Zobrazí se seznam trezorů služeb zotavení.
3. V nabídce **Trezory Recovery Services** klikněte na **Přidat**.

    ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-azure-microsoft-azure-backup/rs-vault-menu.png)

    Otevře se okno trezoru Recovery Services s výzvou k vyplnění polí **Název**, **Předplatné**, **Skupina prostředků** a **Oblast**.

    ![Vytvoření trezoru Recovery Services – krok 5](./media/backup-azure-microsoft-azure-backup/rs-vault-attributes.png)
4. Jako **Název** zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný v rámci předplatného Azure. Zadejte název v rozsahu 2 až 50 znaků. Musí začínat písmenem a může obsahovat pouze písmena, číslice a pomlčky.
5. Kliknutím na **Předplatné** zobrazíte seznam dostupných předplatných. Pokud si nejste jisti, jaké předplatné použít, použijte výchozí (nebo navrhované) předplatné. Více možností je dostupných, jen pokud je váš účet organizace přidružený k více předplatným Azure.
6. Kliknutím na **Skupina prostředků** zobrazíte seznam dostupných skupin prostředků, nebo klikněte na **Nová**, chcete-li vytvořit novou skupinu prostředků. Úplnější informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md)
7. Klikněte na **Oblast** a vyberte zeměpisnou oblast trezoru.
8. Klikněte na možnost **Vytvořit**. Vytvoření trezoru Služeb zotavení může chvíli trvat. Sledujte oznámení o stavu v horní pravé části portálu.
   Po vytvoření svůj trezor otevře na portálu.

### <a name="set-storage-replication"></a>Nastavení replikace úložiště
Možnost replikace úložiště umožňuje výběr mezi geograficky redundantním úložištěm a místně redundantním úložištěm. Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště. Pokud tento trezor trezoru primární, ponechte možnost úložiště do geograficky redundantní úložiště. Zvolte místně redundantní úložiště, pokud chcete levnější možnost, která není tak trvanlivá. Další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy-grs.md) a [místně redundantního](../storage/common/storage-redundancy-lrs.md) úložiště naleznete v tématu [Přehled replikace Azure Storage](../storage/common/storage-redundancy.md).

Chcete-li upravit nastavení replikace úložiště:

1. Vyberte svůj trezor pro otevření řídícího panelu trezoru a okna Nastavení. Pokud se okno **Nastavení** neotevře, klikněte na **Všechna nastavení** v řídicím panelu trezoru.
2. Kliknutím na **Infrastruktura zálohování** > **Konfigurace zálohování** v okně **Nastavení** otevřete okno **Konfigurace zálohování**. V okně **Konfigurace zálohování** zvolte pro svůj trezor možnost replikace úložiště.

    ![Seznam trezorů záloh](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Po výběru možnosti úložiště pro svůj trezor jste připraveni k přidružení virtuálního počítače k trezoru. Chcete-li začít přidružení, měli byste vyhledat a zaregistrovat virtuální počítače Azure.

## <a name="software-package"></a>Balíček softwaru
### <a name="downloading-the-software-package"></a>Stažení balíčku softwaru
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Pokud již máte otevřený trezor služeb zotavení, pokračujte krokem 3. Pokud nemáte otevřený trezor Služeb zotavení, ale jste na portálu Azure, klikněte na **Procházet** v Nabídce centra.

   * V seznamu prostředků zadejte **Služby zotavení**.
   * Během zadávání se seznam bude filtrovat podle zadávaného textu. Až uvidíte **Trezory Služeb zotavení**, klikněte na ně.

     ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Objeví se seznam trezorů Služeb zotavení.
   * Ze seznamu trezorů Služeb zotavení vyberte trezor.

     Otevře se řídicí panel vybraného trezoru.

     ![Otevřené okno trezoru](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. **Nastavení** okno otevře ve výchozím nastavení. Pokud je zavřený, klikněte na **nastavení** otevřete okno nastavení.

    ![Otevřené okno trezoru](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Klikněte na tlačítko **zálohování** otevřete Průvodce Začínáme.

    ![Zálohování Začínáme](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    V **Začínáme se zálohováním** okno, které se otevře, **zálohování cíle** bude automaticky vybrána.

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. V **cíl zálohování** okně z **kde běží vaše úlohy** nabídce vyberte možnost **místní**.

    ![místní a úloh jako cíle](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Z **co chcete zálohovat?** rozevírací nabídky vyberte úlohy, které chcete chránit pomocí zálohování serveru Azure a pak klikněte na tlačítko **OK**.

    **Začínáme se zálohováním** Průvodce přepínače **připravit infrastrukturu** možnost zálohování úloh do Azure.

   > [!NOTE]
   > Pokud chcete zálohovat soubory a složky, doporučujeme pomocí agenta Azure Backup a následující pokyny v článku, [první pohled: zálohování souborů a složek](backup-try-azure-backup-in-10-mins.md). Pokud chcete chránit více než soubory a složky, nebo plánování rozšíření ochrany potřeby v budoucnu, vyberte tyto úlohy.
   >
   >

    ![Získávání změnu Průvodce Začínáme](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. V **připravit infrastrukturu** okno, které se otevře, klikněte na tlačítko **Stáhnout** odkazy pro instalaci serveru Azure Backup a přihlašovací údaje trezoru stáhnout. Při registraci serveru Azure Backup do trezoru služeb zotavení použijte přihlašovací údaje trezoru. Odkazů se dostanete na webu Stažení softwaru, kde si můžete stáhnout balíček softwaru.

    ![Příprava infrastruktury pro Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Vyberte všechny soubory a klikněte na **Další**. Stáhnout všechny soubory přicházející ze stránky pro stažení aplikace Microsoft Azure Backup a umístěte všechny soubory ve stejné složce.

    ![Stažení softwaru 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Vzhledem k tomu, že stažení velikost všech souborů je dohromady > 3G, na 10 MB/s stahoval odkazu, který může trvat až 60 minut ke stažení pro dokončení.

### <a name="extracting-the-software-package"></a>Extrahování balíček softwaru
Po stažení všechny soubory, klikněte na tlačítko **MicrosoftAzureBackupInstaller.exe**. Tato akce spustí **Průvodce instalací aplikace Microsoft Azure Backup** Extrahujte instalační soubory do umístění, které jste zadali. Postupujte podle pokynů průvodce a klikněte na **extrahovat** tlačítko Zahájit proces extrakce.

> [!WARNING]
> K extrakci instalačních souborů se vyžaduje minimálně 4GB volného místa.
>
>

![Průvodce instalací zálohování Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Po dokončení procesu extrahování políčko Spustit čerstvě extrahované *setup.exe* k zahájení instalace serveru Microsoft Azure Backup a klikněte na **Dokončit** tlačítko.

### <a name="installing-the-software-package"></a>Instalace balíčku softwaru
1. Klikněte na tlačítko **Microsoft Azure Backup** spustíte Průvodce instalací.

    ![Průvodce instalací zálohování Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Na úvodní obrazovce klikněte **Další** tlačítko. Tím přejdete *požadovaných součástí kontroluje* části. Na této obrazovce klikněte na tlačítko **zkontrolujte** k určení, pokud byly splněny požadavky hardware a software pro Azure Backup Server. Pokud jsou úspěšně splněny všechny požadavky, zobrazí zprávu s upozorněním, že počítač splňuje požadavky. Klikněte na **Další** tlačítko.

    ![Zkontrolujte Server Azure Backup - uvítací a požadavky](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup Server vyžaduje SQL Server Standard. Navíc instalační balíček serveru Azure Backup obsahuje připojené příslušné binární soubory systému SQL Server nutný v případě, že nechcete používat vlastní SQL. Při spouštění s novou instalací serveru Azure Backup, měli byste vybrat možnost **nainstalovat nové Instance systému SQL Server s tímto nastavením** a klikněte na **zkontrolovat a nainstalovat** tlačítko. Jakmile požadavky jsou úspěšně nainstalováni, klikněte na možnost **Další**.

    ![Server Azure Backup - kontrola SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Pokud dojde k selhání s doporučení k restartování tohoto počítače a klikněte na tlačítko **zkontrolujte znovu**.

   > [!NOTE]
   > Azure Backup Server nebude fungovat se vzdálenou instanci systému SQL Server. Instance serveru Azure Backup používá musí být místní.
   >
   >
4. Zadejte umístění pro instalaci souborů serveru Microsoft Azure Backup a klikněte na **Další**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Pomocné umístění je požadavek pro zálohování na Azure. Zkontrolujte, zda pomocné umístění nejméně 5 % dat plánované zálohování do cloudu. U ochrany disku samostatné disky potřeba nakonfigurovat po dokončení instalace. Další informace týkající se fondů úložiště najdete v tématu [nakonfigurujte fondy úložiště a disk úložiště](https://technet.microsoft.com/library/hh758075.aspx).
5. Zadejte silné heslo pro omezené místní uživatelské účty a klikněte na **Další**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Vyberte, zda chcete použít *Microsoft Update* vyhledávat aktualizace, a klikněte na tlačítko **Další**.

   > [!NOTE]
   > Doporučujeme vám, že přesměrování na webu Microsoft Update nabízí aktualizace zabezpečení a důležité aktualizace pro Windows a další produkty, jako je Microsoft Azure Backup Server služby Windows Update.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Zkontrolujte *souhrn nastavení* a klikněte na tlačítko **nainstalovat**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Instalace probíhá fáze. V první fázi agenta služeb zotavení Microsoft Azure je nainstalován na serveru. Průvodce také zkontroluje připojení k Internetu. Pokud je k dispozici připojení k Internetu může pokračovat v instalaci, pokud ne, budete muset zadat podrobnosti o proxy serveru pro připojení k Internetu.

    Dalším krokem je konfigurace agenta služeb zotavení Microsoft Azure. Jako součást konfigurace budete muset zadávat svoje přihlašovací údaje trezoru, registraci počítače do trezoru služeb zotavení. Bude také zadat přístupové heslo k šifrování a dešifrování dat posílaných mezi Azure a vaše místní. Může automaticky generovat přístupové heslo nebo zadejte minimální heslo 16 znaků. Pokračujte v průvodci, dokud agent nebyl nakonfigurován.

    ![Azure Backup Serer PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Po úspěšném dokončení registrace Microsoft Azure Backup server pokračuje celkové Průvodce instalací k instalaci a konfiguraci systému SQL Server a součásti serveru Azure Backup. Po dokončení instalace součástí systému SQL Server, jsou nainstalované komponenty serveru Azure Backup.

    ![Server Azure Backup](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Po dokončení kroku instalace produktu ikony na ploše se vytvořily také. Právě dvakrát klikněte na ikonu pro spuštění produktu.

### <a name="add-backup-storage"></a>Přidání úložiště záloh
První záložní kopie se ukládají na úložiště připojená k počítači Azure Backup Server. Další informace o přidávání disků, najdete v tématu [nakonfigurujte fondy úložiště a disk úložiště](https://technet.microsoft.com/library/hh758075.aspx).

> [!NOTE]
> Je nutné přidat úložiště záloh, i v případě, že chcete odesílat data do Azure. V architektuře aktuální Azure Backup Server úložiště záloh Azure obsahuje *druhý* kopie dat, zatímco místní úložiště obsahuje záložní kopie první (a povinné).
>
>

## <a name="network-connectivity"></a>Připojení k síti
Azure Backup Server vyžaduje připojení ke službě Azure Backup pro produkt, nemusí fungovat správně. Chcete-li ověřit, zda je počítač připojen k Azure, použijte ```Get-DPMCloudConnection``` rutiny v konzole prostředí PowerShell pro Server Azure Backup. Pokud výstup rutiny je TRUE připojení existuje, jinak je k dispozici žádné připojení.

Předplatné Azure ve stejnou dobu, musí být v dobrém stavu. Chcete-li zjistit stav předplatného a k její správě, přihlaste se k [předplatné portál](https://account.windowsazure.com/Subscriptions).

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
Pokud máte bránu firewall nebo proxy server, který je brání přístupu k Azure, musíte do seznamu povolených IP adres následující domény adresy v profilu brány firewall a proxy:

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Po připojení k Azure byla obnovena do počítače serveru Azure Backup, operace, které lze provést Určuje stav předplatného Azure. Výše uvedené tabulce obsahuje podrobnosti o činnosti povolené počítač po "připojení".

### <a name="handling-subscription-states"></a>Zpracování – stavy předplatného
Je možné provést z předplatného Azure *platnost vypršela* nebo *Deprovisioned* do stavu *Active* stavu. To má ale některé vliv na chování produktu při stav není *Active*:

* A *Deprovisioned* předplatné ztratí funkce pro dobu, po kterou je zrušit. Na měnící *Active*, je obnovená produktu funkce zálohování a obnovení. Také možné načíst zálohovaná data na místní disk, pokud byla u s dobou uchování dostatečně velké. Zálohování dat v Azure je však nenahraditelně ztraceny po zadání předplatné *Deprovisioned* stavu.
* *Platnost vypršela* předplatné pouze ztratí funkce pro, dokud byly provedeny *Active* znovu. Všechny záloh naplánovaných pro dobu, po kterou byl odběr *platnost vypršela* se nespustí.

## <a name="troubleshooting"></a>Řešení potíží
Pokud Microsoft Azure Backup server selže s chybami během fáze instalace (nebo zálohování nebo obnovení), podívejte se na to [dokumentu kódy chyb](https://support.microsoft.com/kb/3041338) Další informace.
Můžete se také podívat na [nejčastější dotazy týkající se Azure Backup](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Další postup
Můžete získat podrobné informace [Příprava prostředí pro aplikaci DPM](https://technet.microsoft.com/library/hh758176.aspx) na webu Microsoft TechNet. Také obsahuje informace o podporovaných konfiguracích, na kterých serveru Azure Backup lze nasadit a použít.

Tyto články vám pomůže získat lepší představu o ochrana pracovního vytížení pomocí Microsoft Azure Backup server.

* [Zálohování serveru SQL Server](backup-azure-backup-sql.md)
* [Zálohování serveru SharePoint](backup-azure-backup-sharepoint.md)
* [Alternativní server zálohování](backup-azure-alternate-dpm-server.md)
