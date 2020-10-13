---
title: Nastavení Azure Backup Server pro řešení Azure VMware
description: Nastavte prostředí pro řešení Azure VMware pro zálohování virtuálních počítačů pomocí Azure Backup Server.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 37fd74f9859813061ff5653fd2c2b0b6cad319e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580009"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Nastavení Azure Backup Server pro řešení Azure VMware

Azure Backup Server je robustní podnikový systém pro zálohování a obnovení, který přispívá k strategii pro provozní kontinuitu a zotavení po havárii (BCDR). Během řešení Azure VMware Preview můžete pomocí Azure Backup Server nakonfigurovat jenom zálohování na úrovni virtuálního počítače. 

Azure Backup Server můžou ukládat data zálohy do:

- **Disk**: u krátkodobého úložiště Azure Backup Server zálohuje data na fondy disků.
- **Azure**: u krátkodobého a dlouhodobého úložiště mimo pracoviště se Azure Backup Server data uložená v fondech disků dají zálohovat do cloudu Microsoft Azure pomocí Azure Backup.

Pokud dojde k výpadku a zdrojová data nejsou k dispozici, můžete použít Azure Backup Server k snadnému obnovení dat do zdroje nebo do alternativního umístění. Tímto způsobem platí, že pokud jsou původní data nedostupná kvůli plánovaným nebo neočekávaným problémům, můžete data snadno obnovit do alternativního umístění.

V tomto článku vám pomůžeme připravit vaše prostředí pro řešení Azure VMware k zálohování virtuálních počítačů pomocí Azure Backup Server. Provede vás kroky: 

> [!div class="checklist"]
> * Určete doporučený typ a velikost disku virtuálního počítače, které se mají použít.
> * Vytvořte Trezor Recovery Services, který uchovává body obnovení.
> * Nastavte replikaci úložiště pro Recovery Services trezor.
> * Přidejte úložiště do Azure Backup Server.

## <a name="supported-vmware-features"></a>Podporované funkce VMware

- **Zálohování bez agentů:** Azure Backup Server nevyžaduje instalaci agenta na Server vCenter nebo ESXi pro zálohování virtuálního počítače. Místo toho stačí zadat IP adresu nebo plně kvalifikovaný název domény (FQDN) a přihlašovací údaje používané k ověření serveru VMware pomocí Azure Backup Server.
- **Zálohování integrované v cloudu:** Azure Backup Server chrání úlohy na disk a Cloud. Pracovní postup zálohování a obnovení Azure Backup Server pomáhá spravovat dlouhodobé uchovávání a zálohování mimo pracoviště.
- **Detekce a ochrana virtuálních počítačů spravovaných vCenter:** Azure Backup Server detekuje a chrání virtuální počítače nasazené na serveru vCenter nebo ESXi. Azure Backup Server taky detekuje virtuální počítače spravované serverem vCenter, abyste mohli chránit Velká nasazení.
- **AutoProtection na úrovni složek:** vCenter umožňuje organizovat virtuální počítače ve složkách virtuálních počítačů. Azure Backup Server tyto složky detekuje a můžete ji použít k ochraně virtuálních počítačů na úrovni složky, která zahrnuje všechny podsložky. Když chráníte složky, Azure Backup Server nejen chrání jenom virtuální počítače v této složce, ale také chrání virtuální počítače přidané později. Azure Backup Server detekuje nové virtuální počítače denně a automaticky je chrání. Při uspořádávání virtuálních počítačů do rekurzivních složek Azure Backup Server automaticky detekuje a chrání nové virtuální počítače nasazené ve rekurzivních složkách.
- **Azure Backup Server nadále chránit virtuální počítače s vMotioned v rámci clusteru:** Protože virtuální počítače jsou vMotioned pro vyrovnávání zatížení v rámci clusteru, Azure Backup Server automaticky detekuje a pokračuje v ochraně virtuálního počítače.
- **Obnovte potřebné soubory rychleji:** Azure Backup Server může obnovit soubory nebo složky z virtuálního počítače s Windows bez obnovení celého virtuálního počítače.

## <a name="limitations"></a>Omezení

- Je nutné nainstalovat kumulativní aktualizaci 1 pro Azure Backup Server v3.
- Před prvním Azure Backup Server zálohování nemůžete zálohovat snímky uživatele. Až Azure Backup Server dokončí první zálohování, můžete zálohovat snímky uživatelů.
- Azure Backup Server nemůže chránit virtuální počítače VMware pomocí průchozích disků a fyzických mapování nezpracovaných zařízení (pRDMs).
- Azure Backup Server nemůže detekovat nebo chránit vApp VMware.

Chcete-li nastavit Azure Backup Server pro řešení Azure VMware, je nutné dokončit následující kroky:

- Nastavte předpoklady a prostředí.
- Vytvořte trezor služby Recovery Services.
- Stáhněte a nainstalujte Azure Backup Server.
- Přidejte úložiště do Azure Backup Server.

### <a name="deployment-architecture"></a>Architektura nasazení

Azure Backup Server je nasazen jako virtuální počítač IaaS (infrastruktura jako služba) Azure pro ochranu virtuálních počítačů řešení Azure VMware.

:::image type="content" source="media/azure-vmware-solution-backup/deploy-backup-server-azure-vmware-solution-diagram.png" alt-text="Azure Backup Server je nasazen jako virtuální počítač IaaS (infrastruktura jako služba) Azure pro ochranu virtuálních počítačů řešení Azure VMware." border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Předpoklady pro prostředí Azure Backup Server

Při instalaci Azure Backup Server do prostředí Azure zvažte doporučení v této části.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Ujistěte se, že jste [v Azure nakonfigurovali sítě pro privátní cloud VMware](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-virtual-machine"></a>Určení velikosti virtuálního počítače

Musíte vytvořit virtuální počítač s Windows ve virtuální síti, kterou jste vytvořili v předchozím kroku. Když zvolíte Server, na kterém běží Azure Backup Server, začněte s imagí Galerie Windows serveru 2019 Datacenter. Kurz [Vytvoření prvního virtuálního počítače s Windows v Azure Portal](../virtual-machines/windows/quick-create-portal.md) vám pomůže začít s DOPORUČENým virtuálním počítačem v Azure, a to i v případě, že jste Azure nikdy nepoužívali.

Následující tabulka shrnuje maximální počet chráněných úloh pro jednotlivé Azure Backup Server velikosti virtuálních počítačů. Informace jsou založeny na interním výkonu a testování škálování pomocí kanonických hodnot velikosti pracovního vytížení a klidového vytížení. Skutečná velikost pracovního vytížení může být větší, ale měla by být ovlivněna disky připojenými k Azure Backup Servermu virtuálnímu počítači.

| Maximální počet chráněných pracovních vytížení | Průměrná velikost pracovního vytížení | Průměrné klidové vytížení (denní) | Minimální IOPS úložiště | Doporučený typ/velikost disku      | Doporučená velikost virtuálního počítače |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Čisté klidové vytížení 5 %                   | 2 000             | HDD úrovně Standard (8 TB nebo vyšší velikost na disk)  | A4V2       |
| 40                      | 150 GB                | Čisté klidové vytížení 10 %                  | 4 500             | SSD úrovně Premium * (1 TB nebo vyšší velikost na disk) | DS3_V2     |
| 60                      | 200 GB                | Čisté klidové vytížení 10 %                  | 10 500            | SSD úrovně Premium × (8 TB nebo vyšší velikost na disk) | DS3_V2     |

* Pokud chcete získat požadované IOPs, použijte minimální doporučené a vyšší velikosti disků. Disky s menší velikostí nabízejí nižší IOPs.

> [!NOTE]
> Azure Backup Server je navržená tak, aby běžela na vyhrazeném serveru s jedním účelem. Azure Backup Server nejde nainstalovat na počítač, který:
> * Spouští se jako řadič domény.
> * Má nainstalovanou roli Aplikační server.
> * Je management server System Center Operations Manager.
> * Spustí Exchange Server.
> * Je uzel clusteru.

### <a name="disks-and-storage"></a>Disky a úložiště

Azure Backup Server vyžadují disky k instalaci, což zahrnuje systémové soubory, instalační soubory, potřebný software, soubory databáze a vyhrazené disky pro fond úložiště.

| Požadavek                      | Doporučená velikost  |
|----------------------------------|-------------------------|
| Instalace Azure Backup Server                | Umístění instalace: 3 GB<br />Jednotka souborů databáze: 900 MB<br />Systémová jednotka: 1 GB pro instalaci SQL Server<br /><br />Budete také potřebovat místo pro Azure Backup Server ke zkopírování katalogu souborů do dočasného umístění instalace při archivaci.      |
| Disk pro fond úložiště<br />(Používá základní svazky, nemůže být na dynamickém disku) | Dvě až trojnásobnou velikost chráněných dat.<br />Podrobný výpočet úložiště najdete v tématu [Capacity Planner DPM](https://www.microsoft.com/download/details.aspx?id=54301).   |

Informace o připojení nového spravovaného datového disku k existujícímu virtuálnímu počítači Azure najdete v tématu [připojení spravovaného datového disku k virtuálnímu počítači s Windows pomocí Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md).

> [!NOTE]
> Jeden Azure Backup Server má pro fond úložiště měkké omezení 120 TB.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Ukládání zálohovaných dat na místní disk a v Azure

Ukládání zálohovaných dat v Azure omezuje infrastrukturu zálohování na Azure Backup Serverm virtuálním počítači. Pro provozní obnovení (zálohování) Azure Backup Server ukládá zálohovaná data na disky Azure připojené k virtuálnímu počítači. Až budou disky a prostory úložiště připojené k virtuálnímu počítači, Azure Backup Server spravuje úložiště. Velikost úložiště záložních dat závisí na počtu a velikosti disků připojených ke každému virtuálnímu počítači Azure. Každá velikost virtuálního počítače Azure má maximální počet disků, které se dají připojit. Například a2 je čtyři disky, a3 je osm disků a A4 je 16 disků. Opět velikost a počet disků určují celkovou kapacitu fondu úložiště zálohování.

> [!IMPORTANT]
> Po dobu delší než pět dnů byste *neměli uchovávat data* o provozním obnovení na discích Azure Backup Server připojených. Pokud jsou data starší než pět dnů, uložte je do trezoru Recovery Services.

Pokud chcete ukládat zálohovaná data v Azure, vytvořte nebo použijte Recovery Services trezor. Když připravujete zálohování Azure Backup Server úlohy, [nakonfigurujete trezor Recovery Services](#create-a-recovery-services-vault). Po nakonfigurování pokaždé, když se spustí úloha online zálohování, se v trezoru vytvoří bod obnovení. Každý trezor Recovery Services uchovává až 9 999 bodů obnovení. V závislosti na počtu vytvořených bodů obnovení a dobu, po kterou jsou zachována, můžete data zálohy uchovávat mnoho let. Můžete například vytvořit měsíční body obnovení a uchovávat je po dobu pěti let.

> [!IMPORTANT]
> Bez ohledu na to, jestli odesíláte zálohovaná data do Azure nebo je uložíte v místním počítači, se musíte zaregistrovat Azure Backup Server s trezorem Recovery Services.

### <a name="scale-deployment"></a>Škálování nasazení

Pokud chcete škálovat nasazení, máte následující možnosti:

- **Horizontální navýšení kapacity**: zvětšete velikost Azure Backup Server virtuálního počítače z řady na řady DS3 a zvyšte místní úložiště.
- **Přesměrování dat**: odešlete starší data do Azure a v úložišti, které je připojené k Azure Backup Servermu počítači, zachovejte jenom nejnovější data.
- **Horizontální**navýšení kapacity: přidejte další Azure Backup Server počítače, abyste chránili úlohy.

### <a name="net-framework"></a>.NET Framework

Virtuální počítač musí mít nainstalovanou aktualizaci .NET Framework 3,5 SP1 nebo novější.

### <a name="join-a-domain"></a>Připojení k doméně

Virtuální počítač Azure Backup Server musí být připojený k doméně a uživatel domény s oprávněními správce na VIRTUÁLNÍm počítači musí nainstalovat Azure Backup Server.

I když se v době verze Preview nepodporuje Azure Backup Server nasazené na virtuálním počítači Azure může zálohovat úlohy na virtuálních počítačích v řešení Azure VMware. Úlohy by měly být ve stejné doméně, aby bylo možné operaci zálohování povolit.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Recovery Services trezor je entita úložiště, která ukládá body obnovení vytvořené v průběhu času. Obsahuje také zásady zálohování, které jsou přidruženy k chráněným položkám.

1. Přihlaste se ke svému předplatnému na webu [Azure Portal](https://portal.azure.com/).

1. V nabídce vlevo vyberte **Všechny služby**.

   ![V nabídce vlevo vyberte Všechny služby.](../backup/media/backup-create-rs-vault/click-all-services.png)

1. V dialogovém okně **všechny služby** zadejte **Recovery Services** a v seznamu vyberte **Recovery Services trezory** .

   ![Zadejte a vyberte trezory Recovery Services.](../backup/media/backup-create-rs-vault/all-services.png)

   Zobrazí se seznam trezorů služby Recovery Services v předplatném.

1. Na řídicím panelu **Trezory služby Recovery Services** vyberte **Přidat**.

   ![Přidejte Recovery Services trezor.](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   Otevře se dialogové okno **Trezor služby Recovery Services**.

1. Zadejte hodnoty pro **název**, **předplatné**, **skupinu prostředků**a **umístění**.

   ![Nakonfigurujte trezor Recovery Services.](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name** (Název): Zadejte popisný název pro identifikaci trezoru. Název musí být v rámci předplatného Azure jedinečný. Zadejte název, který má alespoň dva, ale ne více než 50 znaků. Název musí začínat písmenem a může obsahovat pouze písmena, číslice a pomlčky.
   - **Předplatné:** Vyberte předplatné, které chcete použít. Pokud jste členem jenom jednoho předplatného, název se zobrazí. Pokud si nejste jisti, které předplatné použít, použijte výchozí (navrhované) předplatné. Více možností je dostupných, jen pokud je váš pracovní nebo školní účet přidružený k více předplatným Azure.
   - **Skupina prostředků:** Použijte existující skupinu prostředků, nebo vytvořte novou. Pokud chcete zobrazit seznam dostupných skupin prostředků ve vašem předplatném, vyberte **Použít existující** a pak v rozevíracím seznamu vyberte prostředek. Pokud chcete vytvořit novou skupinu prostředků, vyberte **Vytvořit novou** a zadejte název.
   - **Location** (Umístění): Vyberte zeměpisnou oblast trezoru. Pokud chcete vytvořit trezor pro ochranu virtuálních počítačů řešení VMware Azure, *musí* být trezor ve stejné oblasti jako privátní cloud řešení Azure VMware.

1. Až budete připraveni vytvořit trezor služby Recovery Services, vyberte **Vytvořit**.

   ![Vytvořte Trezor Recovery Services.](../backup/media/backup-create-rs-vault/click-create-button.png)

   Vytvoření trezoru služby Recovery Services může chvíli trvat. Sledujte oznámení o stavu v oblasti **oznámení** v pravém horním rohu portálu. Po vytvoření se trezor zobrazí v seznamu trezorů služby Recovery Services. Pokud trezor nevidíte, vyberte **Aktualizovat**.

   ![Aktualizuje seznam trezorů služby Backup.](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Nastavení replikace úložiště

Možnost replikace úložiště vám umožní vybrat mezi geograficky redundantním úložištěm (výchozím) a místně redundantním úložištěm. Geograficky redundantní úložiště kopíruje data v účtu úložiště do sekundární oblasti, což způsobí, že vaše data budou trvalá. Místně redundantní úložiště je levnější možnost, která není jako trvalá. Další informace o možnostech geograficky redundantního a místně redundantního úložiště najdete v tématu [Azure Storage redundance](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Změna nastavení **typu replikace úložiště místně redundantního/geograficky redundantního** pro Recovery Services trezoru se musí provést před konfigurací záloh v trezoru. Po nakonfigurování záloh je možnost změny zakázaná a typ replikace úložiště nemůžete změnit.

1. V části **trezory Recovery Services**vyberte nový trezor. 

1. V části **Nastavení** vyberte **Vlastnosti**. V části **Konfigurace zálohování**vyberte **aktualizovat**.

1. Vyberte typ replikace úložiště a vyberte **Uložit**.

## <a name="download-and-install-the-software-package"></a>Stažení a instalace softwarového balíčku

Postupujte podle kroků v této části ke stažení, extrakci a instalaci softwarového balíčku.

### <a name="download-the-software-package"></a>Stažení softwarového balíčku

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. Pokud už máte otevřený trezor Recovery Services, pokračujte k dalšímu kroku. Pokud nemáte Recovery Services trezor otevřený, ale jste v Azure Portal, vyberte v hlavní nabídce možnost **Procházet**.

   1. V seznamu prostředků zadejte **Recovery Services**.

   1. Seznam se průběžně filtruje podle zadávaného textu. Když vidíte **Recovery Services trezory**, vyberte ji.

   ![Vytvoření trezoru Recovery Services – Krok 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Ze seznamu trezorů Služeb zotavení vyberte trezor.

   Otevře se řídicí panel vybraného trezoru.

   ![Otevře se řídicí panel vybraného trezoru.](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   Ve výchozím nastavení se otevře možnost **Nastavení** . Pokud jste zavřeli, vyberte **Nastavení** a otevřete ho.

   ![Ve výchozím nastavení se otevře možnost nastavení. Pokud jste zavřeli, vyberte nastavení a otevřete ho.](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Vyberte **zálohování** a otevřete tak průvodce **Začínáme** .

   ![Vyberte zálohování a otevřete tak Průvodce Začínáme.](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. V okně, které se otevře, udělejte toto:

   1. V nabídce **kde běží vaše úlohy?** vyberte **místní**.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-on-premises-workload.png" alt-text="Azure Backup Server je nasazen jako virtuální počítač IaaS (infrastruktura jako služba) Azure pro ochranu virtuálních počítačů řešení Azure VMware.":::

   1. V nabídce **co chcete zálohovat?** vyberte úlohy, které chcete chránit pomocí Azure Backup Server.

   1. Vyberte **Příprava infrastruktury** pro stažení a instalaci Azure Backup Server a přihlašovací údaje trezoru.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Azure Backup Server je nasazen jako virtuální počítač IaaS (infrastruktura jako služba) Azure pro ochranu virtuálních počítačů řešení Azure VMware.":::

1. V okně **připravit infrastrukturu** , které se otevře, udělejte toto:

   1. Vyberte odkaz **ke stažení** , který chcete nainstalovat Azure Backup Server.

   1. Stáhněte si přihlašovací údaje trezoru tak, že vyberete **již staženou nebo pomocí nejnovější instalace Azure Backup Server** a pak vyberete **Stáhnout**. Přihlašovací údaje trezoru použijete během registrace Azure Backup Server do trezoru Recovery Services. Odkazy vás propojí do služby Stažení softwaru, kde stáhnete softwarový balíček.

   :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Azure Backup Server je nasazen jako virtuální počítač IaaS (infrastruktura jako služba) Azure pro ochranu virtuálních počítačů řešení Azure VMware.":::

1. Na stránce pro stažení vyberte všechny soubory a vyberte **Další**.

   > [!NOTE]
   > Všechny soubory je nutné stáhnout do stejné složky. Vzhledem k tomu, že velikost stahovaných souborů je zároveň větší než 3 GB, může stahování trvat až 60 minut. 

   ![Na stránce pro stažení vyberte všechny soubory a vyberte Další.](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>Extrakce softwarového balíčku

Pokud jste balíček softwaru stáhli na jiný server, zkopírujte soubory do virtuálního počítače, který jste vytvořili pro nasazení Azure Backup Server.

> [!WARNING]
> K extrakci instalačních souborů je potřeba aspoň 4 GB volného místa.

1. Po stažení všech souborů poklikejte na **MicrosoftAzureBackupInstaller.exe** a otevře se průvodce instalací **Microsoft Azure Backup** a pak vyberte **Další**.

1. Vyberte umístění, do kterého chcete soubory extrahovat, a vyberte **Další**.

1. Vyberte **extrahovat** a zahajte proces extrakce.

   ![Vyberte extrahovat a zahajte proces extrakce.](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. Po extrakci vyberte možnost **spuštění setup.exe** a pak vyberte **Dokončit**.

> [!TIP]
> Soubor setup.exe můžete také najít ze složky, do které jste extrahovali balíček softwaru.

### <a name="install-the-software-package"></a>Instalace softwarového balíčku

1. V okně nastavení v části **instalovat**vyberte možnost **Microsoft Azure Backup** . otevře se Průvodce instalací nástroje.

   ![V okně nastavení v části instalovat vyberte možnost Microsoft Azure Backup. otevře se Průvodce instalací nástroje.](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. Na **úvodní** obrazovce klikněte na tlačítko **Další** a pokračujte na stránku **kontroly požadovaných součástí** .

1. Zaškrtnutím **políčka znovu** určete, zda jsou splněny požadavky na hardware a software pro Azure Backup Server. V případě úspěšného splnění vyberte **Další**.

   ![ Zaškrtnutím políčka znovu určete, zda jsou splněny požadavky na hardware a software pro Azure Backup Server. V případě úspěšného splnění vyberte Další.](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Instalační balíček Azure Backup Server obsahuje balíčky s příslušnými SQL Server binárních souborů, které jsou potřeba. Po spuštění nové instalace Azure Backup Server vyberte možnost **instalovat novou instanci SQL Server s touto možností instalace** . Pak vyberte **Vyhledat a nainstalovat**.

   ![Instalační balíček Azure Backup Server obsahuje balíčky s příslušnými SQL Server binárních souborů, které jsou potřeba.](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Pokud chcete použít vlastní instanci SQL Server, podporované SQL Server verze jsou SQL Server 2014 SP1 nebo vyšší, 2016 a 2017. Všechny SQL Server verze by měly být Standard nebo Enterprise 64-bit. Azure Backup Server nefunguje s instancí vzdáleného SQL Server. Instance, kterou používá Azure Backup Server, musí být místní. Použijete-li pro Azure Backup Server existující instanci SQL Server, instalace podporuje pouze *pojmenované instance* SQL Server.

   Pokud dojde k selhání s doporučením pro restartování počítače, udělejte to a znovu vyberte **kontrolu**. Pokud dojde k problémům s konfigurací SQL Server, překonfigurujte SQL Server podle pokynů pro SQL Server. Pak se znovu pokuste nainstalovat nebo upgradovat Azure Backup Server pomocí existující instance SQL Server.

   **Ruční konfigurace**

   Pokud používáte vlastní instanci SQL Server, nezapomeňte do hlavní databáze přidat builtin\Administrators do role sysadmin.

   **Konfigurace SSRS s SQL Server 2017**

   Pokud používáte vlastní instanci SQL Server 2017, je nutné ručně nakonfigurovat SQL Server 2017 Reporting Services (SSRS). Po dokončení konfigurace služby SSRS se ujistěte, že je vlastnost- **inicializovaná** vlastnosti služby SSRS nastavená na **hodnotu true**. Pokud je tato vlastnost nastavená na **hodnotu true**, Azure Backup Server předpokládá, že služba SSRS je už nakonfigurovaná, a přeskočí konfiguraci služby SSRS.

   Chcete-li zjistit stav konfigurace služby SSRS, spusťte následující příkaz:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Pro konfiguraci služby SSRS použijte následující hodnoty:

   * **Účet služby**: **použijte předdefinovaný účet** **Network Service**.
   * **Adresa URL webové služby**: **virtuální adresář** by měl být **ReportServer_ \<SQLInstanceName> **.
   * **Databáze**: **DatabaseName** by měl být **ReportServer \<SQLInstanceName> $**.
   * **Adresa URL webového portálu**: **virtuální adresář** by měl být **Reports_ \<SQLInstanceName> **.

   [Přečtěte si další informace](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) o konfiguraci služby SSRS.

   > [!NOTE]
   > [Podmínky poskytování služeb Microsoft Online Services](https://www.microsoft.com/licensing/product-licensing/products) (OST) řídí licencování pro SQL Server používané jako databáze pro Azure Backup Server. V závislosti na OST SQL Server sady s Azure Backup Server používat jenom jako databázi pro Azure Backup Server.

1. Po úspěšné instalaci vyberte **Další**.

1. Zadejte umístění instalace souborů Microsoft Azure Backup serveru a vyberte **Další**.

   > [!NOTE]
   > Pro zálohování do Azure se vyžaduje pomocné umístění. Ujistěte se, že pracovní umístění je alespoň 5% plánovaného zálohování dat do cloudu. V případě ochrany disku je třeba po dokončení instalace nakonfigurovat samostatné disky. Další informace o fondech úložiště najdete v tématu [Konfigurace fondů úložiště a diskového úložiště](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12)).

   ![Zadejte umístění instalace souborů Microsoft Azure Backup serveru a vyberte Další.](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Zadejte silné heslo pro omezené místní uživatelské účty a vyberte **Další**.

   ![Zadejte silné heslo pro omezené místní uživatelské účty a vyberte Další.](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Vyberte, zda chcete vyhledat aktualizace pomocí Microsoft Update a vyberte možnost **Další**.

   > [!NOTE]
   > Doporučujeme, abyste web Windows Update přesměrování na Microsoft Update, což nabízí zabezpečení a důležité aktualizace pro Windows a další produkty, jako je Azure Backup Server.

   ![Vyberte, zda chcete vyhledat aktualizace pomocí Microsoft Update a vyberte možnost Další.](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Zkontrolujte **Souhrn nastavení**a vyberte **nainstalovat**.

   Instalace proběhne ve fázích. První fáze nainstaluje agenta Microsoft Azure Recovery Services a druhá fáze zkontroluje připojení k Internetu. Pokud je k dispozici připojení k Internetu, můžete pokračovat v instalaci. V takovém případě je nutné zadat podrobnosti o proxy serveru pro připojení k Internetu. Poslední fáze kontroluje nezbytný software. Pokud není nainstalován, veškerý chybějící software bude nainstalován společně s agentem Microsoft Azure Recovery Services.

1. Vyberte **Procházet** a vyhledejte přihlašovací údaje trezoru pro registraci počítače do trezoru Recovery Services a pak vyberte **Další**.

1. Vyberte předávací frázi, která šifruje nebo dešifruje data odesílaná mezi Azure a vaším místním prostředím.

   > [!TIP]
   > Můžete automaticky vygenerovat heslo nebo zadat vlastní minimální přístupové heslo 16 znaků.

1. Zadejte umístění, kam chcete uložit frázi, a pak kliknutím na tlačítko **Další** Zaregistrujte server.

   > [!IMPORTANT]
   > Uložte předávací frázi do bezpečného umístění, které je jiné než místní server. Důrazně doporučujeme použít Azure Key Vault k uložení fráze Pass.

   Po dokončení instalace agenta Microsoft Azure Recovery Services se krok instalace přesune k instalaci a konfiguraci SQL Server a Azure Backup Server komponenty.

   ![Po dokončení instalace agenta Microsoft Azure Recovery Services se krok instalace přesune k instalaci a konfiguraci SQL Server a Azure Backup Server komponenty.](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. Po dokončení kroku instalace vyberte **Zavřít**.

### <a name="install-update-rollup-1"></a>Nainstalovat kumulativní aktualizaci 1

Aby bylo možné ochránit úlohy, je instalace kumulativní aktualizace 1 pro Azure Backup Server V3 povinná. Seznam oprav chyb a pokyny k instalaci pro kumulativní aktualizaci 1 pro Azure Backup Server V3 najdete v článku [4534062](https://support.microsoft.com/en-us/help/4534062/)znalostní báze Knowledge Base.

## <a name="add-storage-to-azure-backup-server"></a>Přidání úložiště do Azure Backup Serveru

Azure Backup Server V3 podporuje Moderní úložiště zálohování, které nabízí:

-  Úspory úložiště s 50%.
-  Zálohy, které jsou třikrát rychlejší.
-  Efektivnější úložiště.
-  Úložiště s podporou úloh.

### <a name="volumes-in-azure-backup-server"></a>Svazky v Azure Backup Server

Přidejte datové disky s požadovanou kapacitou úložiště do virtuálního počítače s Azure Backup Server, pokud ještě není přidané.

Azure Backup Server V3 akceptuje pouze svazky úložiště. Když přidáte svazek, Azure Backup Server naformátuje svazek na odolný systém souborů (ReFS), který Moderní úložiště zálohování vyžaduje.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Přidání svazků do úložiště Azure Backup Server disku

1. V podokně **Správa** znovu zkontrolujte úložiště a pak vyberte **Přidat**. 

1. Vyberte z dostupných svazků, které chcete přidat do fondu úložiště. 

1. Po přidání dostupných svazků poskytněte jim popisný název, který vám bude pomáhat s jejich správou. 

1. Vyberte **OK** , pokud chcete tyto svazky naformátovat na ReFS, aby Azure Backup Server mohli využívat výhody moderní úložiště zálohování.

![Přidat dostupné svazky](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu kurzu, kde se dozvíte, jak nakonfigurovat zálohování virtuálních počítačů VMware běžících na řešení VMware Azure pomocí Azure Backup Server.

> [!div class="nextstepaction"]
> [Konfigurace zálohování virtuálních počítačů řešení VMware Azure](backup-azure-vmware-solution-virtual-machines.md)
