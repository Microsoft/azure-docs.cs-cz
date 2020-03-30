---
title: Zálohování úloh pomocí Azure Backup Serveru
description: V tomto článku se dozvíte, jak připravit prostředí na ochranu a zálohování úloh pomocí Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: dd506668f9d75523ff7494bccb2979bf0785990d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273407"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Instalace a upgrade serveru Azure Backup Server

> [!div class="op_single_selector"]
>
> * [Zálohovací server Azure](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Platí pro: MABS v3. (MABS v2 již není podporován. Pokud používáte verzi starší než MABS v3, upgradujte na nejnovější verzi.)

Tento článek vysvětluje, jak připravit prostředí pro zálohování úloh pomocí Microsoft Azure Backup Server (MABS). Pomocí Azure Backup Server můžete chránit úlohy aplikací, jako jsou virtuální počítače Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange a klienti Windows, z jedné konzoly.

> [!NOTE]
> Azure Backup Server teď umí chránit virtuální počítače VMware a poskytuje vylepšené možnosti zabezpečení. Nainstalujte produkt, jak je vysvětleno v následujících částech a nejnovější agent zálohování Azure. Další informace o zálohování serverů VMware pomocí Azure Backup Server najdete v článku [Azure Backup Server k zálohování serveru VMware](backup-azure-backup-server-vmware.md). Další informace o možnostech zabezpečení najdete v dokumentaci k [funkcím zabezpečení zálohování Azure](backup-azure-security-feature.md).
>
>

MABS nasazené v virtuálním počítači Azure můžete zálohovat virtuální počítače v Azure, ale měly by být ve stejné doméně povolit operace zálohování. Proces zálohování virtuálního počítače Azure zůstává stejný jako zálohování virtuálních počítačů v místním prostředí, ale nasazení MABS v Azure má určitá omezení. Další informace o omezení najdete v tématu [DPM jako virtuální počítač Azure](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> Azure má dva modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasické](../azure-resource-manager/management/deployment-models.md). Tento článek obsahuje informace a postupy pro obnovení virtuálních mích nasazených pomocí modelu Správce prostředků.
>
>

Azure Backup Server dědí většinu funkce zálohování úloh ze Správce ochrany dat (DPM). Tento článek odkazuje na dokumentaci aplikace DPM, která vysvětluje některé sdílené funkce. Přestože Azure Backup Server sdílí většinu stejné funkce jako DPM, Azure Backup Server není zálohovat na pásku, ani se integrovat s System Center.

## <a name="choose-an-installation-platform"></a>Vyberte si instalační platformu

Prvním krokem k zprovoznění serveru Azure Backup Server je nastavení Windows Serveru. Váš server může být v Azure nebo místně.

### <a name="using-a-server-in-azure"></a>Použití serveru v Azure

Při výběru serveru pro spuštění Azure Backup Serveru se doporučuje začít s bitovou kopii galerie Datového centra Windows Server 2016 nebo Windows Serveru 2019 Datacenter. Tento článek [Create your first Windows virtual machine in the Azure portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), provides a tutorial for getting start with the recommended virtual machine in Azure, even if you've never used Azure before. Doporučené minimální požadavky na serverový virtuální počítač (VM) by měly být: Standard_A4_v2 se čtyřmi jádry a 8 GB paměti RAM.

Ochrana úloh pomocí Azure Backup Server má mnoho nuancí. Článek [Instalace aplikace DPM jako virtuálního počítače Azure](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12))pomáhá vysvětlit tyto nuance. Před nasazením počítače si přečtěte tento článek úplně.

### <a name="using-an-on-premises-server"></a>Použití místního serveru

Pokud nechcete spustit základní server v Azure, můžete spustit server na virtuálním počítači Hyper-V, virtuálním počítači VMware nebo fyzickém hostiteli. Doporučené minimální požadavky na hardware serveru jsou dvě jádra a 8 GB paměti RAM. Podporované operační systémy jsou uvedeny v následující tabulce:

| Operační systém | Platforma | Skladová jednotka (SKU) |
|:--- | --- |:--- |
| Windows Server 2019 |64bitová verze |Standard, Datacenter, Essentials |
| Windows Server 2016 a nejnovější sps |64bitová verze |Standard, Datacenter, Essentials  |

Úložiště DPM můžete deduplikovat pomocí odstranění duplicit pomocí odstranění duplicit systému Windows Server. Přečtěte si další informace o tom, jak [aplikace DPM a odstranění duplicit](https://docs.microsoft.com/system-center/dpm/deduplicate-dpm-storage?view=sc-dpm-2019) spolupracují při nasazení ve virtuálních terminálech Hyper-V.

> [!NOTE]
> Azure Backup Server je navržený tak, aby běžel na vyhrazeném jednoúčelovém serveru. Azure Backup Server nelze nainstalovat na:
>
> * Počítač spuštěný jako řadič domény
> * Počítač, na kterém je nainstalovaná role aplikačního serveru
> * Počítač, který je serverem pro správu nástroje System Center Operations Manager
> * Počítač, na kterém je spuštěný server Exchange
> * Počítač, který je uzlem clusteru
>
> Instalace serveru Azure Backup Server není podporována na windows serverovém jádru nebo serveru Microsoft Hyper-V Server.

Vždy připojte Azure Backup Server k doméně. Pokud plánujete přesunout server do jiné domény, nainstalujte nejprve Server Zálohování Azure a pak připojte server k nové doméně. Přesunutí existujícího počítače Azure Backup Server do nové domény po nasazení *není podporováno*.

Ať už odesíláte záložní data do Azure nebo je uchováváte místně, server Zálohování Azure musí být zaregistrován v trezoru služby Recovery Services.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Nastavení replikace úložiště

Možnost replikace úložiště umožňuje výběr mezi geograficky redundantním úložištěm a místně redundantním úložištěm. Ve výchozím nastavení využívají trezory služby Recovery Services geograficky redundantní úložiště. Pokud je tento trezor primárním trezorem, ponechejte možnost úložiště nastavenou na geograficky redundantní úložiště. Zvolte místně redundantní úložiště, pokud chcete levnější možnost, která není tak trvanlivá. Další informace o [geograficky redundantních](../storage/common/storage-redundancy-grs.md) a [místně redundantních](../storage/common/storage-redundancy-lrs.md) možnostech úložiště najdete v [přehledu replikace úložiště Azure.](../storage/common/storage-redundancy.md)

Chcete-li upravit nastavení replikace úložiště:

1. V okně **Trezory služby Recovery Services** klikněte na nový trezor. V části **Nastavení** klikněte na **vlastnosti**.
2. V **části Vlastnosti**klepněte v části **Konfigurace zálohování**na tlačítko **Aktualizovat**.

3. Vyberte typ replikace úložiště a klepněte na **tlačítko Uložit**.

     ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Softwarový balíček

### <a name="downloading-the-software-package"></a>Stažení softwarového balíčku

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Pokud již máte otevřený trezor služby Recovery Services, přejděte ke kroku 3. Pokud nemáte otevřený trezor služby Recovery Services, ale jste na webu Azure Portal, klikněte v hlavní nabídce na **procházet**.

   * V seznamu prostředků zadejte **Recovery Services**.
   * Během zadávání se seznam bude filtrovat podle zadávaného textu. Až uvidíte **Trezory Recovery Services**, klikněte na ně.

     ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Objeví se seznam trezorů Služeb zotavení.
   * Ze seznamu trezorů Služeb zotavení vyberte trezor.

     Otevře se řídicí panel vybraného trezoru.

     ![Otevřené okno trezoru](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Ve výchozím nastavení se otevře okno **Nastavení.** Pokud je zavřená, kliknutím na **Nastavení** otevřete okno nastavení.

    ![Otevřené okno trezoru](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Kliknutím na **Zálohovat** otevřete Průvodce začínáme.

    ![Zálohování začíná](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    V okně **Začínáme s otevřeným záložním** programem budou automaticky **vybrány cíle zálohování.**

    ![Výchozí otevření cílů zálohování](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. V okně **Cíl zálohování** vyberte v nabídce Kde **je spuštěná úloha** **možnost Místní**.

    ![místní a pracovní vytížení jako cíle](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    V rozevírací nabídce **Co chcete zálohovat?** **OK**

    Průvodce **začínáme se zálohováním** přepne možnost **Připravit infrastrukturu** a zálohovat úlohy do Azure.

   > [!NOTE]
   > Pokud chcete pouze zálohovat soubory a složky, doporučujeme použít agenta Azure Backup a postupujte podle pokynů v článku [První pohled: zálohování souborů a složek](backup-try-azure-backup-in-10-mins.md). Pokud budete chránit více než soubory a složky nebo plánujete v budoucnu rozšířit potřeby ochrany, vyberte tyto úlohy.
   >
   >

    ![Změna průvodce Začínáme](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. V okně **Připravit infrastrukturu,** které se otevře, klikněte na odkazy **ke stažení** pro instalaci serveru Azure Backup Server a přihlašovací údaje ke stažení trezoru. Přihlašovací údaje trezoru se používají při registraci serveru Azure Backup Server do trezoru služeb pro obnovení. Odkazy vás přenese do služby Stažení softwaru, kde lze stáhnout softwarový balíček.

    ![Příprava infrastruktury pro Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Vyberte všechny soubory a klepněte na tlačítko **Další**. Stáhněte si všechny soubory přicházející ze stránky pro stažení služby Microsoft Azure Backup a umístěte všechny soubory do stejné složky.

    ![Centrum stahování 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Vzhledem k tomu, velikost stahování všech souborů dohromady je > 3G, na 10-Mbps odkaz ke stažení může trvat až 60 minut ke stažení k dokončení.

### <a name="extracting-the-software-package"></a>Extrahování softwarového balíčku

Po stažení všech souborů klepněte na soubor **MicrosoftAzureBackupInstaller.exe**. Tím se spustí **Průvodce instalací zálohování Microsoft Azure** extrahovat instalační soubory do vámi určeného umístění. Pokračujte průvodcem a kliknutím na tlačítko **Extrahovat** zahájíte proces extrakce.

> [!WARNING]
> K extrahování instalačních souborů je zapotřebí alespoň 4 GB volného místa.
>
>

![Průvodce instalací zálohování microsoft azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Po dokončení procesu extrakce zaškrtněte políčko a spusťte čerstvě extrahovaný *soubor setup.exe,* abyste zahájili instalaci microsoft azure backup serveru a klikněte na tlačítko **Dokončit.**

### <a name="installing-the-software-package"></a>Instalace softwarového balíčku

1. Kliknutím na **Microsoft Azure Backup** spusťte průvodce instalací.

    ![Průvodce instalací zálohování microsoft azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Na úvodní obrazovce klikněte na tlačítko **Další.** Tím přejdete do části *Kontroly předpokladů.* Na této obrazovce klikněte na **zkontrolovat** a zjistěte, jestli byly splněny požadavky hardwaru a softwaru pro Azure Backup Server. Pokud jsou všechny požadavky úspěšně splněny, zobrazí se zpráva oznamující, že počítač splňuje požadavky. Klikněte na tlačítko **Další.**

    ![Azure Backup Server – kontrola uvítání a předpokladů](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Instalační balíček Azure Backup Server je dodáván s příslušnými binárnísoubory SQL Serveru. Při spuštění nové instalace serveru Zálohování Azure vyberte možnost **Nainstalovat novou instanci serveru SQL Server s touto instalací** a klepněte na tlačítko Zkontrolovat a **nainstalovat.** Po úspěšné instalaci požadavků klepněte na tlačítko **Další**.

    >[!NOTE]
    >Pokud chcete použít vlastní SQL server, podporované verze SQL Serveru jsou SQL Server 2014 SP1 nebo vyšší, 2016 a 2017.  Všechny verze serveru SQL Server by měly být standardní nebo 64bitové.
    >Azure Backup Server nebude fungovat se vzdálenou instancí serveru SQL Server. Instance používaná serverem Azure Backup Server musí být místní. Pokud používáte existující sql server pro MABS, nastavení MABS podporuje pouze použití *pojmenovaných instancí* serveru SQL.

    ![Azure Backup Server – kontrola SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Pokud dojde k selhání s doporučením restartovat počítač, uvažte a klepněte na tlačítko **Znovu zkontrolovat**. Pokud existují nějaké problémy s konfigurací SQL, překonfigurujte SQL podle pokynů SQL a opakujte instalaci nebo upgrade MABS pomocí existující instance SQL.

   **Ruční konfigurace**

   Při použití vlastní instance SQL, ujistěte se, že přidáte builtin\Administrators do sysadmin roli master DB.

    **Konfigurace SSRS s SQL 2017**

    Pokud používáte vlastní instanci SQL 2017, je třeba ručně nakonfigurovat SSRS. Po konfiguraci SSRS se ujistěte, že je vlastnost *IsInitialized* ssrs nastavena na *hodnotu True*. Pokud je tato možnost nastavena na hodnotu True, mabs předpokládá, že SSRS je již nakonfigurován a přeskočí konfiguraci SSRS.

    Pro konfiguraci SSRS použijte následující hodnoty:
    * Účet služby: "Použít předdefinovaný účet" by měla být síťová služba
    * Adresa URL webové služby: Virtuální\<adresář by měl být ReportServer_> SQLInstanceName
    * Databáze: Název databáze by\<měl být ReportServer$ SQLInstanceName>
    * Adresa URL webového portálu:\<Virtuální adresář by měl být Reports_> SQLInstanceName

    [Přečtěte si další informace](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) o konfiguraci SSRS.

    > [!NOTE]
    > Licencování serveru SQL Server používaného jako databáze mabs se řídí [smluvními podmínkami služeb Online (OST) společnosti Microsoft Online](https://www.microsoft.com/licensing/product-licensing/products) Services. Podle OST, SQL Server svázaný s MABS lze použít pouze jako databáze pro MABS.

4. Zadejte umístění pro instalaci souborů serveru Microsoft Azure Backup a klepněte na tlačítko **Další**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Odkládací umístění je požadavek pro zálohování do Azure. Ujistěte se, že odškrtnuté místo je alespoň 5 % plánovaných dat, která mají být zálohována do cloudu. Z důvodu ochrany disku je třeba po dokončení instalace nakonfigurovat samostatné disky. Další informace týkající se fondů úložiště naleznete v [tématu Konfigurace fondů úložišť a diskového úložiště](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).
5. Zadejte silné heslo pro místní uživatelské účty s omezeným přístupem a klepněte na tlačítko **Další**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Vyberte, zda chcete pomocí služby *Microsoft Update* vyhledat aktualizace, a klepněte na tlačítko **Další**.

   > [!NOTE]
   > Doporučujeme přesměrovat službu Windows Update na službu Microsoft Update, která nabízí aktualizace zabezpečení a důležité aktualizace pro systém Windows a další produkty, jako je Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Projděte si *souhrn nastavení* a klepněte na tlačítko **Instalovat**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Instalace probíhá ve fázích. V první fázi je na serveru nainstalován agent služby Microsoft Azure Recovery Services. Průvodce také zkontroluje připojení k Internetu. Pokud je k dispozici připojení k Internetu, můžete pokračovat v instalaci, pokud ne, je třeba zadat podrobnosti proxy pro připojení k Internetu.

    Dalším krokem je konfigurace agenta služby Microsoft Azure Recovery Services. Jako součást konfigurace budete muset zadat pověření trezoru k registraci počítače do trezoru služeb pro obnovení. Poskytnete také přístupové heslo k šifrování/dešifrování dat odeslaných mezi Azure a vašimi prostory. Můžete automaticky vygenerovat přístupové heslo nebo zadat vlastní minimálně 16místné přístupové heslo. Pokračujte v průvodci, dokud nebude agent nakonfigurován.

    ![Azure Backup Server PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Po úspěšném dokončení registrace serveru Microsoft Azure Backup pokračuje průvodce celkovým nastavením k instalaci a konfiguraci serveru SQL Server a součástí serveru Zálohování Azure. Po dokončení instalace součásti serveru SQL Server jsou nainstalovány součásti Azure Backup Server.

    ![Server Azure Backup](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Po dokončení kroku instalace budou vytvořeny také ikony na ploše produktu. Stačí poklepat na ikonu a produkt spustit.

### <a name="add-backup-storage"></a>Přidání úložiště záloh

První záložní kopie se uchovává v úložišti připojeném k počítači Azure Backup Server. Další informace o přidávání disků naleznete v [tématu Konfigurace fondů úložišť a diskového úložiště](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Úložiště záloh je potřeba přidat, i když plánujete odesílat data do Azure. V aktuální architektuře Azure Backup Server úložiště Azure Backup obsahuje *druhou* kopii dat, zatímco místní úložiště obsahuje první (a povinné) záložní kopie.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Instalace a aktualizace agenta ochrany správce ochrany dat

MABS používá agenta ochrany dat system center. [Zde jsou kroky](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) k instalaci agenta ochrany na servery ochrany.

Následující části popisují, jak aktualizovat agenty ochrany pro klientské počítače.

1. V konzole správce zálohovacího serveru vyberte **možnost Agenti správy** > **Agents**.

2. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenta ochrany.

   > [!NOTE]
   > Sloupec **Aktualizace agenta** označuje, kdy je pro každý chráněný počítač k dispozici aktualizace agenta ochrany. V podokně **Akce** je akce **Aktualizace** k dispozici pouze v případě, že je vybrán chráněný počítač a jsou k dispozici aktualizace.
   >
   >

3. Chcete-li nainstalovat aktualizované agenty ochrany do vybraných počítačů, vyberte v podokně **Akce** **možnost Aktualizovat**.

4. U klientského počítače, který není připojen k síti, dokud není počítač připojen k síti, zobrazuje sloupec **Stav agenta** stav **čekající na aktualizaci**.

   Po připojení klientského počítače k síti zobrazuje sloupec **Aktualizace agenta** pro klientský počítač stav **Aktualizace**.

## <a name="move-mabs-to-a-new-server"></a>Přesunutí MABS na nový server

Zde jsou kroky, pokud potřebujete přesunout MABS na nový server při zachování úložiště. To lze provést pouze v případě, že všechna data jsou na moderní úložiště zálohování.

  > [!IMPORTANT]
  >
  > * Nový název serveru musí mít stejný název jako původní instance serveru Zálohování Azure. Název nové instance Serveru zálohování Azure nelze změnit, pokud chcete k zachování bodů obnovení použít předchozí fond úložiště a databázi MABS (DPMDB).
  > * Musíte mít zálohu databáze MABS (DPMDB). Budete muset obnovit databázi.

1. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenta ochrany.
2. Vypněte původní záložní server Azure nebo ho vypněte.
3. Obnovte účet počítače ve službě Active Directory.
4. Nainstalujte Server 2016 do nového počítače a pojmenujte ho stejným názvem počítače jako původní server Azure Backup.
5. Připojte se k doméně
6. Instalace serveru Azure Backup V3 nebo novějšího (přesunutí disků fondu úložiště MABS ze starého serveru a import)
7. Obnovte dpmdb přijatou v kroku 1.
8. Připojte úložiště z původního záložního serveru k novému serveru.
9. Z obnovení SQL DPMDB
10. Příkazový řádek správce na novém disku CD-ROM na instalační umístění a složku přihrádky aplikace Microsoft Azure Backup

    Příklad cesty: C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. Chcete-li zálohu Azure, spusťte DPMSYNC -SYNC

    Pokud jste do fondu úložiště DPM přidali nové disky namísto přesunutí starých, spusťte dpmsync -Reallocatereplica

## <a name="network-connectivity"></a>Připojení k síti

Azure Backup Server vyžaduje připojení ke službě Azure Backup, aby produkt úspěšně fungoval. Chcete-li ověřit, zda má počítač ```Get-DPMCloudConnection``` připojení k Azure, použijte rutinu v konzole PowerShell serveru Zálohování Azure. Pokud je výstup rutiny TRUE, pak připojení existuje, jinak neexistuje žádné připojení.

Současně předplatné Azure musí být ve stavu v pořádku. Chcete-li zjistit stav předplatného a spravovat ho, přihlaste se na [portál předplatného](https://account.windowsazure.com/Subscriptions).

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

Pokud máte bránu firewall nebo proxy server, který brání přístupu k Azure, musíte v profilu brány firewall/proxy povolit následující adresy domén:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Pokud používáte partnerský vztah Společnosti ExpressRoute microsoftu, vyberte následující služby nebo oblasti:

* Služba Azure Active Directory (12076:5060)
* Oblast Microsoft Azure (podle umístění trezoru služby Recovery Services)
* Azure Storage (podle umístění trezoru služby Recovery Services)

Další podrobnosti naleznete v [požadavcích na směrování ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

Po obnovení připojení k Azure do počítače Azure Backup Server jsou operace, které se dá provádět, určovány stavem předplatného Azure. Výše uvedená tabulka obsahuje podrobnosti o povolených operacích, jakmile je stroj "Připojeno".

### <a name="handling-subscription-states"></a>Zpracování stavů předplatného

Je možné převzít předplatné Azure ze stavu *Vypršela nebo* *Zrušeno do* stavu *Active.* To však má některé důsledky pro chování produktu, zatímco stav není *aktivní*:

* *Deprovisioned* předplatné ztratí funkce pro období, které je zrušeno. Při zapnutí *active*, funkce produktu zálohování / obnovení je oživen. Záložní data na místním disku lze také načíst, pokud byla uchovávána s dostatečně velkou dobou uchování. Záložní data v Azure se však nenávratně ztratí, jakmile předplatné vstoupí do stavu *Deprovisioned.*
* Předplatné *s ukončenou platností* pouze ztratí funkce, dokud nebyla provedena *Aktivní* znovu. Všechny zálohy naplánované pro období, po které vypršela platnost předplatného, nebudou *spuštěny.*

## <a name="upgrade-mabs"></a>Upgrade MABS

Pomocí následujících postupů můžete upgradovat MABS.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Upgrade z MABS V2 na V3

> [!NOTE]
>
> MABS V2 není předpokladem pro instalaci MABS V3. Však můžete upgradovat na MABS V3 pouze z MABS V2.

Pomocí následujících kroků můžete upgradovat MABS:

1. Chcete-li upgradovat z MABS V2 na MABS V3, upgradujte operační systém na Windows Server 2016 nebo Windows Server 2019, pokud je to potřeba.

2. Inovujte server. Kroky jsou podobné [instalaci](#install-and-upgrade-azure-backup-server). Však pro nastavení SQL získáte možnost upgradovat instanci SQL sql 2017 nebo použít vlastní instanci SQL server 2017.

   > [!NOTE]
   >
   > Neukončujte během upgradu instance SQL, ukončení odinstaluje instanci vykazování SQL, a proto se nezdaří pokus o opětovný upgrade MABS.

   > [!IMPORTANT]
   >
   >  V rámci upgradu SQL 2017 zálohujeme šifrovací klíče SQL a odinstalujeme služby pro vykazování. Po upgradu serveru SQL server je nainstalována služba pro vykazování (14.0.6827.4788) & obnoveníši šifrovacích klíčů.
   >
   > Při ruční konfiguraci SQL 2017, naleznete v *konfiguraci SSRS s SQL 2017* části pokyny k instalaci.

3. Aktualizujte agenty ochrany na chráněných serverech.
4. Zálohy by měly pokračovat bez nutnosti restartování produkčních serverů.
5. Nyní můžete začít chránit data. Pokud upgradujete na moderní úložiště zálohování a zároveň chráníte, můžete také zvolit svazky, do kterých chcete zálohy uložit, a vyhledat je v části Zřízené místo. [Další informace](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Řešení potíží

Pokud server Microsoft Azure Backup selže s chybami během fáze instalace (nebo zálohování nebo obnovení), naleznete v tomto [dokumentu kódy chyb](https://support.microsoft.com/kb/3041338) další informace.
Můžete také odkazovat na [nejčastější dotazy týkající se azure backup](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Další kroky

Podrobné informace zde naleznete o [přípravě prostředí pro aplikaci DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-2019). Obsahuje také informace o podporovaných konfiguracích, na kterých lze nasadit a používat Azure Backup Server. Můžete použít řadu [rutiny prostředí PowerShell](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) pro provádění různých operací.

Pomocí těchto článků můžete získat hlubší znalosti o ochraně úloh pomocí serveru Microsoft Azure Backup.

* [Zálohování serveru SQL Server](backup-azure-backup-sql.md)
* [Zálohování sharepointového serveru](backup-azure-backup-sharepoint.md)
* [Alternativní zálohování serveru](backup-azure-alternate-dpm-server.md)
