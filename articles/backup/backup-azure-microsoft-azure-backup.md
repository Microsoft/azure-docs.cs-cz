---
title: Použití Azure Backup Server k zálohování úloh
description: V tomto článku se dozvíte, jak připravit prostředí pro ochranu a zálohování úloh pomocí Microsoft Azure Backup serveru (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: d476c228a619f03f798c1a2cd6854a8d603c3637
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987018"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Instalace a upgrade Azure Backup Server

> [!div class="op_single_selector"]
>
> * [Server Azure Backup](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Platí pro: MABS v3. (MABS v2 už není podporovaný. Pokud používáte verzi starší než MABS v3, upgradujte prosím na nejnovější verzi.)

Tento článek vysvětluje, jak připravit prostředí pro zálohování úloh pomocí serveru Microsoft Azure Backup (MABS). Pomocí Azure Backup Server můžete chránit pracovní zatížení aplikací, jako jsou například virtuální počítače Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange a klienti Windows z jediné konzoly.

> [!NOTE]
> Azure Backup Server teď můžou chránit virtuální počítače VMware a poskytovat vylepšené možnosti zabezpečení. Nainstalujte produkt, jak je vysvětleno níže v následujících částech a nejnovějším agentem Azure Backup. Další informace o zálohování serverů VMware pomocí Azure Backup Server najdete v článku [použití Azure Backup Server k zálohování serveru VMware](backup-azure-backup-server-vmware.md). Další informace o možnostech zabezpečení najdete v [dokumentaci k funkcím Azure Backup zabezpečení](backup-azure-security-feature.md).
>
>

MABS nasazené na virtuálním počítači Azure může zálohovat virtuální počítače v Azure, ale měly by být ve stejné doméně, aby se povolila operace zálohování. Proces pro zálohování virtuálního počítače Azure zůstává stejný jako zálohování virtuálních počítačů místně, ale nasazení MABS v Azure má určitá omezení. Další informace o omezeních najdete v tématu [DPM jako virtuální počítač Azure](/system-center/dpm/install-dpm#setup-prerequisites) .

> [!NOTE]
> Azure má dva modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a Classic](../azure-resource-manager/management/deployment-models.md). Tento článek popisuje informace a postupy pro obnovení virtuálních počítačů nasazených pomocí modelu Správce prostředků.
>
>

Azure Backup Server zdědí většinu funkcí zálohování úloh z Data Protection Manager (DPM). Tento článek obsahuje odkazy na dokumentaci aplikace DPM a vysvětlení některých sdílených funkcí. I když Azure Backup Server sdílí většinu stejných funkcí jako DPM, Azure Backup Server se nezálohuje na pásku ani Neintegruje s nástrojem System Center.

## <a name="choose-an-installation-platform"></a>Zvolit instalační platformu

Prvním krokem k zahájení a spuštění Azure Backup Server je nastavení Windows serveru. Váš server může být v Azure nebo v místním prostředí.

* K ochraně místních úloh se musí MABS Server nacházet místně.
* Aby bylo možné chránit úlohy běžící na virtuálních počítačích Azure, musí být server MABS umístěný v Azure, který běží jako virtuální počítač Azure.

### <a name="using-a-server-in-azure"></a>Použití serveru v Azure

Když zvolíte Server, na kterém běží Azure Backup Server, doporučujeme začít s imagí Galerie Windows serveru 2016 Datacenter nebo Windows Server 2019 Datacenter. V tomto článku [vytvořte svůj první virtuální počítač s Windows na Azure Portal](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json), který poskytuje kurz pro zahájení práce s doporučeným virtuálním počítačem v Azure, a to i v případě, že jste Azure ještě nikdy nepoužívali. Doporučené minimální požadavky pro virtuální počítač serveru (VM) by měly být: Standard_A4_v2 se čtyřmi jádry a 8 GB paměti RAM.

Ochrana úloh pomocí Azure Backup Server má spoustu drobné odlišnosti. Tato drobné odlišnosti je popsána v [matrici ochrany pro MABS](./backup-mabs-protection-matrix.md) . Než počítač nasadíte, přečtěte si tento článek kompletně.

### <a name="using-an-on-premises-server"></a>Použití místního serveru

Pokud nechcete spustit základní server v Azure, můžete server spustit na virtuálním počítači Hyper-V, na virtuálním počítači VMware nebo na fyzickém hostiteli. Doporučené minimální požadavky na hardware serveru jsou dvě jádra a 8 GB paměti RAM. Podporované operační systémy jsou uvedené v následující tabulce:

| Operační systém | Platforma | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64bitová verze |Standard, Datacenter, Essentials |
| Windows Server 2016 a nejnovější aktualizace service packu |64bitová verze |Standard, Datacenter, Essentials  |

Pomocí odstranění duplicitních dat systému Windows Server můžete odstranit duplicitu úložiště aplikace DPM. Přečtěte si další informace o tom [, jak aplikace DPM a odstraňování duplicitních dat](/system-center/dpm/deduplicate-dpm-storage) při nasazení na virtuálních počítačích Hyper-V spolupracují.

> [!NOTE]
> Azure Backup Server je navržená tak, aby běžela na vyhrazeném serveru s jedním účelem. Nemůžete nainstalovat Azure Backup Server na:
>
> * Počítač spuštěný jako řadič domény
> * Počítač, na kterém je nainstalovaná role aplikačního serveru
> * Počítač, který je System Center Operations Manager management server
> * Počítač, na kterém je spuštěný server Exchange
> * Počítač, který je uzlem clusteru
>
> Instalace Azure Backup Server není podporovaná na jádru Windows serveru nebo na serveru Microsoft Hyper-V.

Vždy připojte Azure Backup Server k doméně. Pokud plánujete přesunout server do jiné domény, nainstalujte nejprve Azure Backup Server a pak připojte Server k nové doméně. Přesunutí stávajícího Azure Backup Serverho počítače do nové domény po nasazení se *nepodporuje*.

Bez ohledu na to, jestli odesíláte zálohovaná data do Azure, nebo je uložíte místně, Azure Backup Server musí být zaregistrovaná v Recovery Servicesm trezoru.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Nastavení replikace úložiště

Možnost replikace úložiště umožňuje výběr mezi geograficky redundantním úložištěm a místně redundantním úložištěm. Ve výchozím nastavení používají trezory Recovery Services geograficky redundantní úložiště. Pokud je tento trezor vaším primárním trezorem, ponechte možnost úložiště nastavenou na geograficky redundantní úložiště. Zvolte místně redundantní úložiště, pokud chcete levnější možnost, která není tak trvanlivá. V tématu [Přehled replikace Azure Storage](../storage/common/storage-redundancy.md)najdete další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy.md#geo-redundant-storage), [místně redundantního](../storage/common/storage-redundancy.md#locally-redundant-storage)a [redundantního](../storage/common/storage-redundancy.md#zone-redundant-storage) úložiště pro zóny.

Chcete-li upravit nastavení replikace úložiště:

1. V podokně **Recovery Services trezory** vyberte nový trezor. V části **Nastavení** vyberte  **vlastnosti**.
2. V části **vlastnosti** v části **Konfigurace zálohování** vyberte **aktualizovat**.

3. Vyberte typ replikace úložiště a vyberte **Uložit**.

     ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Softwarový balíček

### <a name="downloading-the-software-package"></a>Stažení softwarového balíčku

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Pokud už máte otevřený trezor Recovery Services, pokračujte krokem 3. Pokud nemáte otevřený trezor Recovery Services, ale nachází se v Azure Portal, vyberte v hlavní nabídce možnost **Procházet**.

   * V seznamu prostředků zadejte **Recovery Services**.
   * Během zadávání se seznam bude filtrovat podle zadávaného textu. Když vidíte **Recovery Services trezory**, vyberte ji.

     ![Vytvoření trezoru Recovery Services – Krok 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Objeví se seznam trezorů Služeb zotavení.
   * Ze seznamu trezorů Služeb zotavení vyberte trezor.

     Otevře se řídicí panel vybraného trezoru.

     ![Řídicí panel trezoru](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Ve výchozím nastavení se otevře podokno **Nastavení** . Pokud je zavřená, vyberte **Nastavení** a otevřete podokno nastavení.

    ![Podokno nastavení](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Vyberte **zálohování** a otevřete tak Průvodce Začínáme.

    ![Začínáme se zálohováním](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    V podokně **Začínáme se zálohováním** , které se otevře, se automaticky vyberou **cíle zálohování** .

    ![Zálohování – cíle – výchozí – otevřeno](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. V podokně **cíl zálohování** v nabídce **kde je spuštěná vaše úloha** , vyberte **místní**.

    ![místní a úlohy jako cíle](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    V rozevírací nabídce **co chcete zálohovat?** vyberte úlohy, které chcete chránit pomocí Azure Backup Server a pak vyberte **OK**.

    Průvodce **Začínáme se zálohováním** přepne možnost **připravit infrastrukturu** pro zálohování úloh do Azure.

   > [!NOTE]
   > Pokud chcete zálohovat jenom soubory a složky, doporučujeme použít agenta Azure Backup a podle pokynů v článku [první pohled: zálohování souborů a složek](./backup-windows-with-mars-agent.md). Pokud budete chránit více než soubory a složky nebo plánujete rozšířit požadavky na ochranu v budoucnu, vyberte tyto úlohy.
   >
   >

    ![Změna Průvodce Začínáme](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. V podokně **připravit infrastrukturu** , které se otevře, vyberte **Stáhnout** odkazy pro instalaci Azure Backup Server a stáhnout přihlašovací údaje trezoru. Přihlašovací údaje trezoru použijete během registrace Azure Backup Server do trezoru Recovery Services. Odkazy vás provedou do centra pro stahování, kde se dá stáhnout balíček softwaru.

    ![Příprava infrastruktury pro Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Vyberte všechny soubory a vyberte **Další**. Stáhněte všechny soubory, které pocházejí ze stránky pro stažení Microsoft Azure Backup, a všechny soubory umístěte do stejné složky.

    ![Stažení centra 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Vzhledem k tomu, že velikost stahovaných souborů všech souborů je > 3 GB, na odkaz pro stažení 10 MB může trvat až 60 minut, než se stahování dokončí.

### <a name="extracting-the-software-package"></a>Extrakce softwarového balíčku

Po stažení všech souborů vyberte **MicrosoftAzureBackupInstaller.exe**. Spustí se **Průvodce instalací Microsoft Azure Backup** , který extrahuje instalační soubory do umístění určeného vámi. Pokračujte v průvodci a výběrem tlačítka pro **extrakci** zahajte proces extrakce.

> [!WARNING]
> K extrakci instalačních souborů je potřeba aspoň 4 GB volného místa.
>
>

![Instalační program extrahuje soubory pro instalaci.](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Po dokončení extrakce zaškrtnutím políčka spusťte čerstvě extrahovanou *setup.exe* a začněte s instalací serveru Microsoft Azure Backup a vyberte tlačítko **Dokončit** .

### <a name="installing-the-software-package"></a>Instalace softwarového balíčku

1. Výběrem **Microsoft Azure Backup** spusťte Průvodce instalací nástroje.

    ![Průvodce instalací Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Na úvodní obrazovce vyberte tlačítko **Další** . Tím přejdete na část *kontroly požadovaných součástí* . Na této obrazovce vyberte možnost **zaškrtnutí** a určete, zda byly splněny požadavky na hardware a software pro Azure Backup Server. V případě úspěšného splnění všech požadavků se zobrazí zpráva oznamující, že počítač splňuje požadavky. Vyberte tlačítko **Další**.

    ![Azure Backup Server – uvítání a kontrolu předpokladů](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Instalační balíček Azure Backup Server obsahuje balíčky s požadovanými binárními soubory SQL Server. Při spuštění nové instalace Azure Backup Server vyberte možnost **nainstalovat novou instanci SQL Server s tímto instalačním programem** a vyberte tlačítko pro **kontrolu a instalaci** . Po úspěšné instalaci požadovaných součástí vyberte **Další**.

    >[!NOTE]
    >Pokud chcete použít vlastní SQL Server, podporované verze SQL Server jsou SQL Server 2014 SP1 nebo vyšší, 2016 a 2017.  Všechny SQL Server verze by měly být Standard nebo Enterprise 64-bit.
    >Azure Backup Server nebude fungovat s instancí vzdáleného SQL Server. Instance, kterou používá Azure Backup Server, musí být místní. Pokud používáte stávající SQL Server pro MABS, instalace MABS podporuje pouze použití *pojmenovaných instancí* systému SQL Server.

    ![Kontroly Azure Backup Server-SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Pokud dojde k selhání s doporučením pro restartování počítače, udělejte to a znovu vyberte **kontrolu**. Pokud dojde k nějakým problémům s konfigurací SQL, překonfigurujte SQL podle pokynů SQL a zkuste instalaci/upgrade MABS pomocí existující instance SQL.

   **Ruční konfigurace**

   Pokud používáte vlastní instanci SQL, nezapomeňte přidat builtin\Administrators do role sysadmin do hlavní databáze.

    **Konfigurace SSRS s SQL 2017**

    Pokud používáte svou vlastní instanci SQL 2017, musíte SSRS nakonfigurovat ručně. Po konfiguraci služby SSRS zajistěte, aby byla vlastnost- *inicializovaná* u služby SSRS nastavena na *hodnotu true*. Pokud je tato hodnota nastavená na true, MABS předpokládá, že služba SSRS je už nakonfigurovaná, a přeskočí konfiguraci služby SSRS.

    Pro konfiguraci služby SSRS použijte následující hodnoty:
    * Účet služby: použít předdefinovaný účet by měl být síťová služba
    * Adresa URL webové služby: virtuální adresář by měl být ReportServer_\<SQLInstanceName>
    * Databáze: DatabaseName by měl být ReportServer $\<SQLInstanceName>
    * Adresa URL webového portálu: virtuální adresář by měl být Reports_\<SQLInstanceName>

    [Přečtěte si další informace](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) o konfiguraci služby SSRS.

    > [!NOTE]
    > Licencování pro SQL Server používané jako databáze pro MABS se řídí [podmínkami služeb Microsoft Online Services](https://www.microsoft.com/licensing/product-licensing/products) (OST). V závislosti na formátu OST je SQL Server balíčkům s MABS použít jenom jako databázi pro MABS.

4. Zadejte umístění instalace souborů Microsoft Azure Backup serveru a vyberte **Další**.

    ![Zadejte umístění pro instalaci souborů.](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Pracovní umístění je požadavkem pro zálohování do Azure. Ujistěte se, že pracovní umístění je alespoň 5% plánovaného zálohování dat do cloudu. V případě ochrany disku je potřeba po dokončení instalace nakonfigurovat samostatné disky. Další informace o fondech úložiště najdete v tématu [Příprava úložiště dat](/system-center/dpm/plan-long-and-short-term-data-storage).

    Požadavky na kapacitu pro úložiště na disku závisí hlavně na velikosti chráněných dat, velikosti denních bodů obnovení, očekávané míře nárůstu objemu dat a cílech rozsahu uchování. Doporučujeme, abyste úložiště disku dvakrát změnili velikost chráněných dat. To předpokládá každodenní velikost bodů obnovení odpovídající 10 % velikosti chráněných dat a 10denní rozsah uchování. Chcete-li získat dobrý odhad velikosti, zkontrolujte [Capacity Planner DPM](https://www.microsoft.com/download/details.aspx?id=54301). 

5. Zadejte silné heslo pro omezené místní uživatelské účty a vyberte **Další**.

    ![Zadat silné heslo](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Vyberte, zda chcete vyhledat aktualizace pomocí *Microsoft Update* a vyberte možnost **Další**.

   > [!NOTE]
   > Doporučujeme, abyste web Windows Update přesměrování na Microsoft Update, což nabízí zabezpečení a důležité aktualizace pro Windows a další produkty, jako je Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Update Opt-In](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Zkontrolujte *Souhrn nastavení* a vyberte **nainstalovat**.

    ![Souhrn nastavení](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Instalace proběhne ve fázích. V první fázi se agent Microsoft Azure Recovery Services nainstaluje na server. Průvodce také kontroluje připojení k Internetu. Pokud je k dispozici připojení k Internetu, můžete pokračovat v instalaci. V takovém případě je potřeba zadat podrobnosti o proxy serveru pro připojení k Internetu.

    Dalším krokem je konfigurace agenta Microsoft Azure Recovery Services. V rámci konfigurace budete muset zadat přihlašovací údaje trezoru, abyste počítač zaregistrovali do trezoru Recovery Services. Zadáte také heslo pro šifrování/dešifrování dat odesílaných mezi Azure a vaším místním prostředím. Můžete automaticky vygenerovat heslo nebo zadat vlastní minimální přístupové heslo 16 znaků. Pokračujte v průvodci, dokud neproběhne konfigurace agenta.

    ![Průvodce registrací serveru](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Po úspěšném dokončení registrace Microsoft Azure Backup serveru přejde průvodce celkovým nastavením na instalaci a konfiguraci SQL Server a Azure Backup Server komponenty. Po dokončení instalace součásti SQL Server se nainstalují Azure Backup Server součásti.

    ![Azure Backup Server průběh instalace](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Po dokončení instalace se vytvoří taky ikony na ploše produktu. Dvojitým kliknutím na ikonu spustíte produkt.

### <a name="add-backup-storage"></a>Přidat úložiště zálohování

První záložní kopie je udržována v úložišti připojeném k Azure Backup Servermu počítači. Další informace o přidávání disků najdete v tématu [Konfigurace fondů úložiště a diskového úložiště](./backup-mabs-add-storage.md).

> [!NOTE]
> Úložiště zálohování je nutné přidat i v případě, že plánujete odeslat data do Azure. V aktuální architektuře Azure Backup Server ukládá trezor Azure Backup *druhou* kopii dat, zatímco místní úložiště obsahuje první (a povinnou) záložní kopii.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Instalace a aktualizace agenta Data Protection Manager Protection

MABS používá agenta ochrany Data Protection Manager System Center. [Tady je postup](/system-center/dpm/deploy-dpm-protection-agent) instalace agenta ochrany na servery ochrany.

Následující části popisují, jak aktualizovat agenty ochrany pro klientské počítače.

1. V konzole správce záložního serveru vyberte   >  **agenti** pro správu.

2. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenta ochrany.

   > [!NOTE]
   > Sloupec **aktualizace agenta** určuje, kdy je k dispozici aktualizace agenta ochrany pro každý chráněný počítač. V podokně **Akce** je akce **aktualizace** k dispozici pouze v případě, že je vybrán chráněný počítač a jsou k dispozici aktualizace.
   >
   >

3. Chcete-li nainstalovat aktualizované agenty ochrany na vybrané počítače, vyberte v podokně **Akce** možnost **aktualizovat**.

4. V případě klientského počítače, který není připojený k síti, až do chvíle, kdy je počítač připojený k síti, zobrazí se ve sloupci **Stav agenta** stav **aktualizace čeká na vyřízení**.

   Po připojení klientského počítače k síti zobrazí sloupec **aktualizace agenta** pro klientský počítač stav **aktualizace**.

## <a name="move-mabs-to-a-new-server"></a>Přesunout MABS na nový server

Tady je postup, pokud potřebujete přesunout MABS na nový server a zachovat úložiště. To se dá udělat jenom v případě, že jsou všechna data na Moderní úložiště zálohování.

  > [!IMPORTANT]
  >
  > * Název nového serveru musí mít stejný název jako původní instance Azure Backup Server. Název nové instance Azure Backup Server nemůžete změnit, pokud chcete použít předchozí fond úložiště a databázi MABS (DPMDB), abyste zachovali body obnovení.
  > * Musíte mít zálohu databáze MABS (DPMDB). Budete ho potřebovat k obnovení databáze.

1. V podokně zobrazení vyberte klientské počítače, pro které chcete aktualizovat agenta ochrany.
2. Vypněte původní Azure Backup Server nebo ho převeďte do režimu offline.
3. Resetujte účet počítače ve službě Active Directory.
4. Nainstalujte Server 2016 na nový počítač a poskytněte mu stejný název počítače jako původní Azure Backup Server.
5. Připojte se k doméně.
6. Nainstalujte Azure Backup Server V3 nebo novější (přesuňte disky fondu úložiště MABS z původního serveru a importujte).
7. Obnovte DPMDB provedených v kroku 1.
8. Připojte úložiště k novému serveru z původního záložního serveru.
9. Z SQL Obnovte DPMDB.
10. Na novém serveru spusťte CMD (jako správce). Přejít do umístění instalačního Microsoft Azure Backup a složky bin

    Příklad cesty: `C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"`

11. Pokud se chcete připojit k Azure Backup, spusťte `DPMSYNC -SYNC`

    Pokud jste přidali **nové** disky do fondu úložiště DPM místo přesunutí starých těch, spusťte příkaz `DPMSYNC -Reallocatereplica` .

## <a name="network-connectivity"></a>Připojení k síti

Aby produkt mohl úspěšně fungovat, Azure Backup Server vyžaduje připojení ke službě Azure Backup. Pokud chcete ověřit, jestli má počítač připojení k Azure, použijte ```Get-DPMCloudConnection``` rutinu v konzole Azure Backup Server PowerShellu. Pokud má výstup rutiny hodnotu TRUE, pak připojení existuje, jinak není dostupné žádné připojení.

Kromě toho musí být předplatné Azure v dobrém stavu. Pokud chcete zjistit stav předplatného a spravovat ho, přihlaste se na [portál předplatného](https://account.windowsazure.com/Subscriptions).

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

Pokud používáte partnerský vztah Microsoftu ExpressRoute, vyberte následující služby nebo oblasti:

* Azure Active Directory (12076:5060)
* Oblast Microsoft Azure (podle umístění vašeho trezoru Recovery Services)
* Azure Storage (podle umístění vašeho trezoru Recovery Services)

Další podrobnosti najdete v [požadavcích na směrování ExpressRoute](../expressroute/expressroute-routing.md).

Po obnovení připojení k Azure na Azure Backup Server počítač se operace, které jde provést, stanoví ve stavu předplatného Azure. Výše uvedená tabulka obsahuje podrobnosti o operacích, které jsou povolené, když je počítač připojený.

### <a name="handling-subscription-states"></a>Zpracování stavů předplatného

Je možné převzít předplatné Azure ze stavu s *ukončenou platností* nebo *zrušenou* stavem do *aktivního* stavu. To ale má vliv na chování produktu, když stav není *aktivní*:

* *Zrušení zřízené* předplatné ztratí funkčnost v období, ve kterém je zrušeno. Při zapínání na *aktivní* se funkce zálohování a obnovení obnovená. Záložní data na místním disku můžete také načíst, pokud byla držena s dostatečně velkým obdobím uchovávání. Data záloh v Azure se ale irretrievably ztratí, jakmile předplatné vstoupí do stavu *zrušeno* .
* Předplatné, *jehož platnost vypršela* , pouze ztratí funkčnost, dokud nebude znovu *aktivováno* . U všech záloh naplánovaných po dobu, kdy *vypršela platnost* předplatného, se nespustí.

## <a name="upgrade-mabs"></a>Upgradovat MABS

K upgradu MABS použijte následující postupy.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Upgrade z MABS v2 na V3

> [!NOTE]
>
> MABS v2 není předpokladem pro instalaci MABS v3. Upgrade na MABS V3 však můžete provést pouze z MABS v2.

K upgradu MABS použijte následující postup:

1. Pokud chcete upgradovat z MABS v2 na MABS v3, upgradujte operační systém na Windows Server 2016 nebo Windows Server 2019, pokud je to potřeba.

2. Upgradujte Server. Postup je podobný jako při [instalaci](#install-and-upgrade-azure-backup-server). Pro nastavení SQL ale získáte možnost upgradovat instanci SQL na SQL 2017 nebo použít vlastní instanci SQL serveru 2017.

   > [!NOTE]
   >
   > Neukončím během upgradu instance SQL. Ukončením dojde k odinstalaci instance SQL Reporting, takže pokus o opětovné provedení upgradu MABS se nezdaří.

   > [!IMPORTANT]
   >
   >  V rámci upgradu SQL 2017 zálohujte šifrovací klíče SQL a odinstalujeme služby generování sestav. Po upgradu SQL serveru se služba Reporting Service (14.0.6827.4788) nainstaluje & šifrovací klíče se obnoví.
   >
   > Pokud ručně konfigurujete SQL 2017, přečtěte si část *Konfigurace SSRS s sql 2017* v části pokyny k instalaci.

3. Aktualizujte agenty ochrany na chráněných serverech.
4. Zálohování by mělo pokračovat bez nutnosti restartovat provozní servery.
5. Teď můžete začít chránit svoje data. Pokud provádíte upgrade na Moderní úložiště zálohování a zároveň chráníte, můžete také vybrat svazky, ve kterých chcete ukládat zálohy, a v části zřízené místo ověřit. [Přečtěte si další informace](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Řešení potíží

Pokud Microsoft Azure Backup server selhává s chybami během fáze nastavení (nebo zálohování nebo obnovení), najdete další informace v [dokumentu s kódy chyb](https://support.microsoft.com/kb/3041338)  .
Můžete se také podívat na [Azure Backup souvisejících nejčastějších](backup-azure-backup-faq.md) dotazech.

## <a name="next-steps"></a>Další kroky

Zde najdete podrobné informace o [přípravě prostředí pro aplikaci DPM](/system-center/dpm/prepare-environment-for-dpm). Obsahuje také informace o podporovaných konfiguracích, na kterých Azure Backup Server lze nasadit a použít. K provádění různých operací můžete použít řadu [rutin PowerShellu](/powershell/module/dataprotectionmanager/) .

Pomocí těchto článků můžete získat hlubší přehled o ochraně zatížení pomocí Microsoft Azure Backup serveru.

* [Zálohování SQL Server](backup-azure-backup-sql.md)
* [Zálohování serveru SharePoint](backup-azure-backup-sharepoint.md)
* [Alternativní zálohování serveru](backup-azure-alternate-dpm-server.md)
