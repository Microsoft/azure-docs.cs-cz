---
title: Použití Azure Backup Server k zálohování úloh
description: V tomto článku se dozvíte, jak připravit prostředí pro ochranu a zálohování úloh pomocí Microsoft Azure Backup serveru (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 7379992eeb441372a9140621f9d90b337ad0d2e2
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172994"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Instalace a upgrade Azure Backup Server

> [!div class="op_single_selector"]
>
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Platí pro: MABS v3. (MABS v2 už není podporovaný. Pokud používáte verzi starší než MABS v3, upgradujte prosím na nejnovější verzi.)

Tento článek vysvětluje, jak připravit prostředí pro zálohování úloh pomocí serveru Microsoft Azure Backup (MABS). Pomocí Azure Backup Server můžete chránit pracovní zatížení aplikací, jako jsou například virtuální počítače Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange a klienti Windows z jediné konzoly.

> [!NOTE]
> Azure Backup Server teď můžou chránit virtuální počítače VMware a poskytovat vylepšené možnosti zabezpečení. Nainstalujte produkt, jak je vysvětleno níže v následujících částech a nejnovějším agentem Azure Backup. Další informace o zálohování serverů VMware pomocí Azure Backup Server najdete v článku [použití Azure Backup Server k zálohování serveru VMware](backup-azure-backup-server-vmware.md). Další informace o možnostech zabezpečení najdete v [dokumentaci k funkcím zabezpečení Azure Backup](backup-azure-security-feature.md).
>
>

MABS nasazené na virtuálním počítači Azure může zálohovat virtuální počítače v Azure, ale měly by být ve stejné doméně, aby se povolila operace zálohování. Proces pro zálohování virtuálního počítače Azure zůstává stejný jako zálohování virtuálních počítačů místně, ale nasazení MABS v Azure má určitá omezení. Další informace o omezeních najdete v tématu [DPM jako virtuální počítač Azure](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites) .

> [!NOTE]
> Azure má dva modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a Classic](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek popisuje informace a postupy pro obnovení virtuálních počítačů nasazených pomocí modelu Správce prostředků.
>
>

Azure Backup Server zdědí většinu funkcí zálohování úloh z Data Protection Manager (DPM). Tento článek obsahuje odkazy na dokumentaci aplikace DPM a vysvětlení některých sdílených funkcí. I když Azure Backup Server sdílí většinu stejných funkcí jako DPM, Azure Backup Server nezálohuje na pásku ani Neintegruje s nástrojem System Center.

## <a name="choose-an-installation-platform"></a>Zvolit instalační platformu

Prvním krokem k zahájení a spuštění Azure Backup Server je nastavení Windows serveru. Váš server může být v Azure nebo v místním prostředí.

### <a name="using-a-server-in-azure"></a>Použití serveru v Azure

Když zvolíte Server, na kterém běží Azure Backup Server, doporučujeme začít s imagí Galerie Windows serveru 2016 Datacenter nebo Windows Server 2019 Datacenter. V tomto článku [vytvořte svůj první virtuální počítač s Windows na Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), který poskytuje kurz pro zahájení práce s doporučeným virtuálním počítačem v Azure, a to i v případě, že jste Azure ještě nikdy nepoužívali. Doporučené minimální požadavky pro virtuální počítač serveru (VM) by měly být: Standard_A4_v2 se čtyřmi jádry a 8 GB paměti RAM.

Ochrana úloh pomocí Azure Backup Server má spoustu drobné odlišnosti. Tento článek vám pomůže vysvětlit tyto drobné odlišnostiy, [nainstalovat DPM jako virtuální počítač Azure](https://technet.microsoft.com/library/jj852163.aspx). Než počítač nasadíte, přečtěte si tento článek kompletně.

### <a name="using-an-on-premises-server"></a>Použití místního serveru

Pokud nechcete spustit základní server v Azure, můžete server spustit na virtuálním počítači Hyper-V, na virtuálním počítači VMware nebo na fyzickém hostiteli. Doporučené minimální požadavky na hardware serveru jsou dvě jádra a 8 GB paměti RAM. Podporované operační systémy jsou uvedené v následující tabulce:

| Operační systém | Platforma | Skladová položka |
|:--- | --- |:--- |
| Windows Server 2019 |64bitová verze |Standard, Datacenter, Essentials |
| Windows Server 2016 a nejnovější aktualizace service packu |64bitová verze |Standard, Datacenter, Essentials  |

Pomocí odstranění duplicitních dat systému Windows Server můžete odstranit duplicitu úložiště aplikace DPM. Přečtěte si další informace o tom [, jak aplikace DPM a odstraňování duplicitních dat](https://technet.microsoft.com/library/dn891438.aspx) při nasazení na virtuálních počítačích Hyper-V spolupracují.

> [!NOTE]
> Azure Backup Server je navržená tak, aby běžela na vyhrazeném serveru s jedním účelem. Nemůžete nainstalovat Azure Backup Server na:
>
> * Počítač spuštěný jako řadič domény
> * Počítač, na kterém je nainstalovaná role aplikačního serveru
> * Počítač, který je serverem pro správu nástroje System Center Operations Manager
> * Počítač, na kterém je spuštěný server Exchange
> * Počítač, který je uzlem clusteru

Vždy připojte Azure Backup Server k doméně. Pokud plánujete přesunout server do jiné domény, nainstalujte nejprve Azure Backup Server a pak připojte Server k nové doméně. Přesunutí stávajícího Azure Backup Serverho počítače do nové domény po nasazení se *nepodporuje*.

Bez ohledu na to, jestli odesíláte zálohovaná data do Azure, nebo je uložíte místně, Azure Backup Server musí být zaregistrovaná v Recovery Servicesm trezoru.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Nastavení replikace úložiště

Možnost replikace úložiště umožňuje výběr mezi geograficky redundantním úložištěm a místně redundantním úložištěm. Ve výchozím nastavení používají trezory Recovery Services geograficky redundantní úložiště. Pokud je tento trezor vaším primárním trezorem, ponechte možnost úložiště nastavenou na geograficky redundantní úložiště. Zvolte místně redundantní úložiště, pokud chcete levnější možnost, která není tak trvanlivá. Další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy-grs.md) a [místně redundantního](../storage/common/storage-redundancy-lrs.md) úložiště naleznete v tématu [Přehled replikace Azure Storage](../storage/common/storage-redundancy.md).

Chcete-li upravit nastavení replikace úložiště:

1. V okně **Trezory služby Recovery Services** klikněte na nový trezor. V části **Nastavení** klikněte na **vlastnosti**.
2. V části **vlastnosti**v části **Konfigurace zálohování**klikněte na **aktualizovat**.

3. Vyberte typ replikace úložiště a klikněte na **Uložit**.

     ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Softwarový balíček

### <a name="downloading-the-software-package"></a>Stažení softwarového balíčku

1. Přihlásit se na [Azure Portal](https://portal.azure.com/).
2. Pokud už máte otevřený trezor Recovery Services, pokračujte krokem 3. Pokud nemáte otevřený trezor Recovery Services, ale nachází se v Azure Portal, v hlavní nabídce klikněte na tlačítko **Procházet**.

   * V seznamu prostředků zadejte **Služby zotavení**.
   * Během zadávání se seznam bude filtrovat podle zadávaného textu. Až uvidíte **Trezory Služeb zotavení**, klikněte na ně.

     ![Vytvoření trezoru Služeb zotavení – krok 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Objeví se seznam trezorů Služeb zotavení.
   * Ze seznamu trezorů Služeb zotavení vyberte trezor.

     Otevře se řídicí panel vybraného trezoru.

     ![Otevřené okno trezoru](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Ve výchozím nastavení se otevře okno **Nastavení** . Pokud je zavřená, kliknutím na **Nastavení** otevřete okno nastavení.

    ![Otevřené okno trezoru](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Kliknutím na **zálohovat** otevřete Průvodce Začínáme.

    ![Začínáme se zálohováním](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    V okně **Začínáme se zálohováním** , které se otevře, se automaticky vyberou **cíle zálohování** .

    ![Zálohování – cíle – výchozí – otevřeno](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. V okně **cíl zálohování** v nabídce **kde je spuštěná vaše úloha** , vyberte **místní**.

    ![místní a úlohy jako cíle](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    V rozevírací nabídce **co chcete zálohovat?** vyberte úlohy, které chcete chránit pomocí Azure Backup Server a pak klikněte na **OK**.

    Průvodce **Začínáme se zálohováním** přepne možnost **připravit infrastrukturu** pro zálohování úloh do Azure.

   > [!NOTE]
   > Pokud chcete zálohovat jenom soubory a složky, doporučujeme použít agenta Azure Backup a podle pokynů v článku [první pohled: zálohování souborů a složek](backup-try-azure-backup-in-10-mins.md). Pokud budete chránit více než soubory a složky nebo plánujete rozšířit požadavky na ochranu v budoucnu, vyberte tyto úlohy.
   >
   >

    ![Změna Průvodce Začínáme](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. V okně **připravit infrastrukturu** , které se otevře, klikněte na **Stáhnout** odkazy pro instalaci Azure Backup Server a Stáhněte si přihlašovací údaje trezoru. Přihlašovací údaje trezoru použijete během registrace Azure Backup Server do trezoru služby Recovery Services. Odkazy vás provedou do centra pro stahování, kde se dá stáhnout balíček softwaru.

    ![Příprava infrastruktury pro Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Vyberte všechny soubory a klikněte na **Další**. Stáhněte všechny soubory, které pocházejí ze stránky pro stažení Microsoft Azure Backup, a všechny soubory umístěte do stejné složky.

    ![Stažení centra 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Vzhledem k tomu, že velikost stahovaných souborů všech souborů je > 3G, na odkaz pro stažení 10 MB může trvat až 60 minut, než se stahování dokončí.

### <a name="extracting-the-software-package"></a>Extrakce softwarového balíčku

Po stažení všech souborů klikněte na **MicrosoftAzureBackupInstaller. exe**. Spustí se **Průvodce instalací Microsoft Azure Backup** , který extrahuje instalační soubory do umístění určeného vámi. Pokračujte v průvodci a kliknutím na tlačítko **extrahovat** zahajte proces extrakce.

> [!WARNING]
> K extrakci instalačních souborů je potřeba aspoň 4 GB volného místa.
>
>

![Průvodce instalací Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Po dokončení procesu extrakce zaškrtněte políčko pro spuštění programu čerstvě extrahovaný soubor *Setup. exe* , aby se začal instalovat Microsoft Azure Backup Server, a klikněte na tlačítko **Dokončit** .

### <a name="installing-the-software-package"></a>Instalace softwarového balíčku

1. Kliknutím na **Microsoft Azure Backup** spusťte Průvodce instalací nástroje.

    ![Průvodce instalací Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Na úvodní obrazovce klikněte na tlačítko **Další** . Tím přejdete na část *kontroly požadovaných součástí* . Na této obrazovce klikněte na tlačítko **ověřit** a určete, zda byly splněny požadavky na hardware a software pro Azure Backup Server. V případě úspěšného splnění všech požadavků se zobrazí zpráva oznamující, že počítač splňuje požadavky. Klikněte na tlačítko **Další** .

    ![Azure Backup Server – uvítání a kontrolu předpokladů](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup Server vyžaduje SQL Server Enterprise. Instalační balíček Azure Backup Server navíc obsahuje příslušné SQL Server binární soubory, které potřebují, pokud nechcete používat vlastní SQL. Když začnete s novou Azure Backup Server instalací, měli byste vybrat možnost **nainstalovat novou instanci SQL Server s tímto nastavením** a kliknout na tlačítko pro **kontrolu a instalaci** . Po úspěšné instalaci požadovaných součástí klikněte na tlačítko **Další**.

    ![Kontroly Azure Backup Server-SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Pokud dojde k selhání s doporučením pro restartování počítače, udělejte to a znovu klikněte na **znovu spustit**. Pokud dojde k nějakým problémům s konfigurací SQL, překonfigurujte SQL podle pokynů pro SQL a zkuste instalaci/upgrade MABS pomocí existující instance SQL.

   > [!NOTE]
   > Azure Backup Server nebudou fungovat s instancí vzdáleného SQL Server. Instance, kterou používá Azure Backup Server, musí být místní. V případě, že používáte stávající SQL Server pro MABS, instalace MABS podporuje pouze použití *pojmenovaných instancí* systému SQL Server.

   **Ruční konfigurace**

   Pokud používáte vlastní instanci SQL, nezapomeňte přidat builtin\Administrators do role sysadmin do hlavní databáze.

    **Konfigurace SSRS s SQL 2017**

    Pokud používáte svou vlastní instanci SQL 2017, musíte SSRS nakonfigurovat ručně. Po konfiguraci služby SSRS zajistěte, aby byla vlastnost- *inicializovaná* u služby SSRS nastavena na *hodnotu true*. Pokud je tato hodnota nastavená na true, MABS předpokládá, že služba SSRS je už nakonfigurovaná, a přeskočí konfiguraci služby SSRS.

    Pro konfiguraci služby SSRS použijte následující hodnoty:
    * Účet služby: použít předdefinovaný účet by měl být síťová služba
    * Adresa URL webové služby: virtuální adresář by měl být ReportServer_\<SQLInstanceName >
    * Databáze: DatabaseName by měla být ReportServer $\<SQLInstanceName >
    * Adresa URL webového portálu: virtuální adresář by měl být Reports_\<SQLInstanceName >

    [Přečtěte si další informace](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) o konfiguraci služby SSRS.

    > [!NOTE]
    > Licencování pro SQL Server používané jako databáze pro MABS se řídí [podmínkami služeb Microsoft Online Services](https://www.microsoft.com/licensing/product-licensing/products) (OST). V závislosti na formátu OST je SQL Server balíčkům s MABS použít jenom jako databázi pro MABS.

4. Zadejte umístění instalace souborů Microsoft Azure Backup serveru a klikněte na tlačítko **Další**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Pracovní umístění je požadavkem pro zálohování do Azure. Ujistěte se, že pracovní umístění je alespoň 5% plánovaného zálohování dat do cloudu. V případě ochrany disku je potřeba po dokončení instalace nakonfigurovat samostatné disky. Další informace o fondech úložiště najdete v tématu [Konfigurace fondů úložiště a diskového úložiště](https://technet.microsoft.com/library/hh758075.aspx).
5. Zadejte silné heslo pro omezené místní uživatelské účty a klikněte na **Další**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Vyberte, zda chcete vyhledat aktualizace pomocí *Microsoft Update* a klikněte na tlačítko **Další**.

   > [!NOTE]
   > Doporučujeme, abyste web Windows Update přesměrování na Microsoft Update, což nabízí zabezpečení a důležité aktualizace pro Windows a další produkty, jako je Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Zkontrolujte *Souhrn nastavení* a klikněte na **nainstalovat**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Instalace proběhne ve fázích. V první fázi se agent Microsoft Azure Recovery Services nainstaluje na server. Průvodce také kontroluje připojení k Internetu. Pokud je k dispozici připojení k Internetu, můžete pokračovat v instalaci. Pokud ne, musíte zadat podrobnosti o proxy serveru pro připojení k Internetu.

    Dalším krokem je konfigurace agenta Microsoft Azure Recovery Services. V rámci konfigurace budete muset zadat své přihlašovací údaje k trezoru pro registraci počítače do trezoru služby Recovery Services. Zadáte také heslo pro šifrování/dešifrování dat odesílaných mezi Azure a vaším místním prostředím. Můžete automaticky vygenerovat heslo nebo zadat vlastní minimální přístupové heslo 16 znaků. Pokračujte v průvodci, dokud neproběhne konfigurace agenta.

    ![Azure Backup Server PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Po úspěšném dokončení registrace Microsoft Azure Backup serveru přejde průvodce celkovým nastavením na instalaci a konfiguraci SQL Server a Azure Backup Server komponenty. Po dokončení instalace součásti SQL Server se nainstalují Azure Backup Server součásti.

    ![Server Azure Backup](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Po dokončení instalace se vytvoří taky ikony na ploše produktu. Stačí dvakrát kliknout na ikonu a spustit produkt.

### <a name="add-backup-storage"></a>Přidat úložiště zálohování

První záložní kopie je udržována v úložišti připojeném k Azure Backup Servermu počítači. Další informace o přidávání disků najdete v tématu [Konfigurace fondů úložiště a diskového úložiště](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Úložiště zálohování je nutné přidat i v případě, že plánujete odeslat data do Azure. V aktuální architektuře Azure Backup Server ukládá trezor Azure Backup *druhou* kopii dat, zatímco místní úložiště obsahuje první (a povinnou) záložní kopii.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Instalace a aktualizace agenta Data Protection Manager Protection

MABS používá agenta ochrany Data Protection Manager System Center. [Tady je postup](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) instalace agenta ochrany na servery ochrany.

Následující části popisují, jak aktualizovat agenty ochrany pro klientské počítače.

1. V konzole správce záložního serveru vyberte > **agenti** **pro správu** .

2. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenta ochrany.

   > [!NOTE]
   > Sloupec **aktualizace agenta** určuje, kdy je k dispozici aktualizace agenta ochrany pro každý chráněný počítač. V podokně **Akce** je akce **aktualizace** k dispozici pouze v případě, že je vybrán chráněný počítač a jsou k dispozici aktualizace.
   >
   >

3. Chcete-li nainstalovat aktualizované agenty ochrany na vybrané počítače, vyberte v podokně **Akce** možnost **aktualizovat**.

4. Pro klientský počítač, který není připojený k síti, až do chvíle, kdy je počítač připojený k síti, zobrazí se ve sloupci **Stav agenta** stav **aktualizace čeká na vyřízení**.

   Po připojení klientského počítače k síti zobrazí sloupec **aktualizace agenta** pro klientský počítač stav **aktualizace**.

## <a name="move-mabs-to-a-new-server"></a>Přesunout MABS na nový server

Tady je postup, pokud potřebujete přesunout MABS na nový server a zachovat úložiště. To se dá udělat jenom v případě, že jsou všechna data na Moderní úložiště zálohování.

  > [!IMPORTANT]
  >
  > * Název nového serveru musí být stejný jako původní instance Azure Backup Server. Název nové instance Azure Backup Server nemůžete změnit, pokud chcete použít předchozí fond úložiště a databázi MABS (DPMDB), abyste zachovali body obnovení.
  > * Musíte mít zálohu databáze MABS (DPMDB). Bude nutné obnovit databázi.

1. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenta ochrany.
2. Vypněte původní server Azure Backup nebo ho odpojte od tohoto drátu.
3. Resetujte účet počítače ve službě Active Directory.
4. Nainstalujte Server 2016 na nový počítač a pojmenujte ho na stejný název počítače jako na původním serveru Azure Backup.
5. Připojit k doméně
6. Nainstalovat Azure Backup Server V3 nebo novější (přesunout disky fondu úložiště MABS z původního serveru a importovat)
7. Obnovte DPMDB provedených v kroku 1.
8. Připojte úložiště k novému serveru z původního záložního serveru.
9. Z SQL obnovení DPMDB
10. Z příkazového řádku správce na novém serveru CD pro Microsoft Azure Backup umístění instalace a složka bin

    Příklad cesty: C:\Windows\System32 > CD "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. Do Azure Backup spusťte příkaz DPMSYNC-SYNC.

    Pokud jste přidali nové disky do fondu úložiště DPM místo přesunutí starých těch, spusťte příkaz DPMSYNC-Reallocatereplica.

## <a name="network-connectivity"></a>Připojení k síti

Aby produkt mohl úspěšně fungovat, Azure Backup Server vyžaduje připojení ke službě Azure Backup. Pokud chcete ověřit, jestli má počítač připojení k Azure, použijte rutinu ```Get-DPMCloudConnection``` v konzole PowerShellu Azure Backup Server. Pokud má výstup rutiny hodnotu TRUE, pak připojení existuje, jinak není dostupné žádné připojení.

Kromě toho musí být předplatné Azure v dobrém stavu. Pokud chcete zjistit stav předplatného a spravovat ho, přihlaste se na [portál předplatného](https://account.windowsazure.com/Subscriptions).

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

Pokud máte bránu firewall nebo proxy server, který brání přístupu k Azure, je potřeba v profilu brány firewall/proxy serveru povolený následující adresy domény:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*. windows.net

Po obnovení připojení k Azure na Azure Backup Server počítač se operace, které jde provést, stanoví ve stavu předplatného Azure. Výše uvedená tabulka obsahuje podrobnosti o operacích, které jsou povolené, když je počítač připojený.

### <a name="handling-subscription-states"></a>Zpracování stavů předplatného

Je možné přebírat předplatné Azure ze stavu, *jehož platnost vypršela* *, nebo je* stav *aktivní* . To ale má vliv na chování produktu, zatímco stav není *aktivní*:

* *Zrušení zřízené* předplatné ztratí funkčnost po dobu, kdy je zrušeno zřízení. Při zapínání na *aktivní*se funkce zálohování a obnovení obnovená. Záložní data na místním disku můžete také načíst, pokud byla držena s dostatečně velkým obdobím uchovávání. Data záloh v Azure se ale irretrievably ztratí, jakmile předplatné vstoupí do stavu *zrušeno* .
* Předplatné, *jehož platnost vypršela* , pouze ztratí funkčnost, dokud nebude znovu *aktivováno* . U všech záloh naplánovaných po dobu, kdy *vypršela platnost* předplatného, se nespustí.

## <a name="upgrade-mabs"></a>Upgradovat MABS

K upgradu MABS použijte následující postupy.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Upgrade z MABS v2 na V3

> [!NOTE]
>
> MABS v2 není předpokladem pro instalaci MABS v3. Upgrade na MABS V3 však můžete provést pouze z MABS v2.

K upgradu MABS použijte následující postup:

1. Pokud chcete upgradovat z MABS v2 na MABS v3, upgradujte operační systém na Windows Server 2016 nebo Windows Server 2019, pokud je to potřeba.

2. Upgradujte Server. Postup je podobný jako při [instalaci](#install-and-upgrade-azure-backup-server). Pro nastavení SQL se ale zobrazí možnost upgradovat instanci SQL na SQL 2017 nebo použít vlastní instanci SQL serveru 2017.

   > [!NOTE]
   >
   > Neukončujte během upgradu instance SQL, ukončení odinstaluje instanci generování sestav SQL, a proto se pokus o opětovné provedení upgradu MABS nezdaří.

   > [!IMPORTANT]
   >
   >  V rámci upgradu SQL 2017 zálohujte šifrovací klíče SQL a odinstalujeme služby generování sestav. Po upgradu SQL serveru se služba Reporting Service (14.0.6827.4788) nainstaluje & šifrovací klíče se obnoví.
   >
   > Pokud ručně konfigurujete SQL 2017, přečtěte si část *Konfigurace SSRS s sql 2017* v části pokyny k instalaci.

3. Aktualizujte agenty ochrany na chráněných serverech.
4. Zálohování by mělo pokračovat bez nutnosti restartovat provozní servery.
5. Teď můžete začít chránit svoje data. Pokud provádíte upgrade na Moderní úložiště zálohování a zároveň chráníte, můžete také vybrat svazky, ve kterých chcete ukládat zálohy, a v části zřízené místo ověřit. [Další informace](backup-mabs-add-storage.md)

## <a name="troubleshooting"></a>Řešení potíží

Pokud Microsoft Azure Backup server selhává s chybami během fáze nastavení (nebo zálohování nebo obnovení), najdete další informace v [dokumentu s kódy chyb](https://support.microsoft.com/kb/3041338) .
Můžete se také podívat na [Azure Backup souvisejících nejčastějších](backup-azure-backup-faq.md) dotazech.

## <a name="next-steps"></a>Další kroky

Podrobné informace o [přípravě prostředí pro aplikaci DPM](https://technet.microsoft.com/library/hh758176.aspx) najdete na webu Microsoft TechNet. Obsahuje také informace o podporovaných konfiguracích, na kterých Azure Backup Server lze nasadit a použít. K provádění různých operací můžete použít řadu [rutin PowerShellu](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) .

Pomocí těchto článků můžete získat hlubší přehled o ochraně zatížení pomocí Microsoft Azure Backup serveru.

* [Zálohování SQL Server](backup-azure-backup-sql.md)
* [Zálohování serveru SharePoint](backup-azure-backup-sharepoint.md)
* [Alternativní zálohování serveru](backup-azure-alternate-dpm-server.md)
