---
title: Nastavení serveru Microsoft Azure Backup pro řešení Azure VMware (AVS)
description: Nastavte prostředí Azure VMware Solution (AVS) pro zálohování virtuálních počítačů pomocí serveru Microsoft Azure Backup.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 23c29f453587ac7a232c21e43fa6fb45193881bc
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613107"
---
# <a name="set-up-microsoft-azure-backup-server-for-avs"></a>Nastavení serveru Microsoft Azure Backup pro funkci AVS

Microsoft Azure Backup Server je robustní podnikový systém pro zálohování a obnovení, který přispívá k strategii pro provozní kontinuitu a zotavení po havárii (BCDR). Během používání programu AVS Preview můžete konfigurovat jenom zálohování na úrovni virtuálního počítače pomocí Azure Backup Server. 

Azure Backup Server můžou ukládat data zálohy do:

- **Disk**: u krátkodobého úložiště Azure Backup Server zálohuje data na fondy disků.

- **Azure**: u krátkodobého a dlouhodobého úložiště mimo pracoviště můžete Azure Backup Server data uložená v fondech disků zálohovat do cloudu Microsoft Azure pomocí služby Azure Backup.

Pokud dojde k výpadku a zdrojová data nejsou k dispozici, můžete použít Azure Backup Server k snadnému obnovení dat do zdroje nebo do alternativního umístění. Tímto způsobem platí, že pokud jsou původní data nedostupná kvůli plánovaným nebo neočekávaným problémům, můžete data snadno obnovit do alternativního umístění.

V tomto článku vám pomůžeme připravit vaše prostředí služby AVS na zálohování virtuálních počítačů pomocí Azure Backup Server.  Provede vás kroky: 

> [!div class="checklist"]
> * Určete doporučený typ a velikost disku virtuálního počítače, které se mají použít.
> * Vytvoření trezoru Recovery Services, který ukládá body obnovení
> * Nastavení replikace úložiště pro Recovery Services trezor
> * Přidání úložiště do Azure Backup Serveru

## <a name="supported-vmware-features"></a>Podporované funkce VMware

- **Zálohování bez agentů:** Azure Backup Server nevyžaduje instalaci agenta na Server vCenter nebo ESXi pro zálohování virtuálního počítače. Místo toho stačí zadat IP adresu nebo plně kvalifikovaný název domény (FQDN) a přihlašovací údaje, které se použijí k ověření serveru VMware pomocí Azure Backup Server.

- **Zálohování integrované v cloudu:** Azure Backup Server chrání úlohy na disk a Cloud. Pracovní postup zálohování a obnovení Azure Backup Server pomáhá spravovat dlouhodobé uchovávání a zálohování mimo pracoviště.

- **Detekce a ochrana virtuálních počítačů spravovaných vCenter:** Azure Backup Server detekuje a chrání virtuální počítače nasazené na serveru vCenter nebo ESXi. Azure Backup Server taky detekuje virtuální počítače spravované serverem vCenter, což vám umožní chránit Velká nasazení.

- **Automatická ochrana na úrovni složek:** vCenter umožňuje organizovat virtuální počítače ve složkách virtuálních počítačů. Azure Backup Server tyto složky detekuje a umožňuje chránit virtuální počítače na úrovni složky a zahrnuje všechny podsložky. Když chráníte složky, Azure Backup Server nejen chrání jenom virtuální počítače v této složce, ale také chrání virtuální počítače přidané později. Azure Backup Server detekuje nové virtuální počítače denně a automaticky je chrání. Při uspořádávání virtuálních počítačů do rekurzivních složek Azure Backup Server automaticky detekuje a chrání nové virtuální počítače nasazené ve rekurzivních složkách.

- **Azure Backup Server nadále chránit virtuální počítače s vMotioned v rámci clusteru:** Protože virtuální počítače jsou vMotioned pro vyrovnávání zatížení v rámci clusteru, Azure Backup Server automaticky detekuje a pokračuje v ochraně virtuálního počítače.

- **Obnovte potřebné soubory rychleji:** Azure Backup Server může obnovit soubory nebo složky z virtuálního počítače s Windows bez obnovení celého virtuálního počítače.

## <a name="limitations"></a>Omezení

- Je nutné nainstalovat kumulativní aktualizaci 1 pro Azure Backup Server v3.

- Snímky uživatelů nemůžete zálohovat před prvním Azure Backup Server zálohy. Jakmile Azure Backup Server dokončí první zálohování, můžete zálohovat snímky uživatelů.

- Azure Backup Server nemůže chránit virtuální počítače VMware pomocí průchozích disků a fyzických mapování nezpracovaných zařízení (pRDM).

- Azure Backup Server nemůže detekovat nebo chránit vApp VMware.

**Pokud chcete nastavit Microsoft Azure Backup Server pro řešení Azure VMware (AVS), musíte provést následující kroky:**

- Nastavení požadavků a prostředí

- Vytvoření trezoru služby Azure Recovery Services

- Stažení a instalace Azure Backup Server 

- Přidání úložiště do Azure Backup Serveru 

**Architektura nasazení**  
Microsoft Azure Backup Server je nasazený jako virtuální počítač Azure IaaS pro ochranu virtuálních počítačů služby AVS.

:::image type="content" source="media/avs-backup/deploy-mabs-avs-diagram.png" alt-text="Architektura nasazení AVS" border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Předpoklady pro prostředí Azure Backup Server

Při instalaci Azure Backup Server do prostředí Azure zvažte doporučení v této části.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Ujistěte se, že jste [v Azure nakonfigurovali sítě pro privátní cloud VMware](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-virtual-machine"></a>Určení velikosti virtuálního počítače

V rámci virtuální sítě, kterou jste vytvořili v předchozím kroku, je nutné vytvořit virtuální počítač s Windows. Když zvolíte Server, na kterém běží Azure Backup Server, doporučujeme začít s imagí Galerie Windows serveru 2019 Datacenter. [Vytvoření prvního virtuálního počítače s Windows v kurzu Azure Portal](../virtual-machines/windows/quick-create-portal.md) vám pomůže začít s DOPORUČENým virtuálním počítačem v Azure, a to i v případě, že jste Azure nikdy nepoužívali.

Následující tabulka shrnuje maximální počet chráněných úloh pro jednotlivé Azure Backup Server velikosti virtuálních počítačů. Informace jsou založeny na interním výkonu a testování škálování pomocí kanonických hodnot velikosti pracovního vytížení a klidového vytížení. Skutečná velikost pracovního vytížení může být větší, ale měla by být ovlivněna disky připojenými k Azure Backup Servermu virtuálnímu počítači.

| Maximální počet chráněných pracovních vytížení | Průměrná velikost pracovního vytížení | Průměrné klidové vytížení (denní) | Minimální IOPS úložiště | Doporučený typ/velikost disku      | Doporučená velikost virtuálního počítače |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Čisté klidové vytížení 5 %                   | 2000             | HDD úrovně Standard (8 TB nebo vyšší velikost na disk)  | A4V2       |
| 40                      | 150 GB                | Čisté klidové vytížení 10 %                  | 4500             | SSD úrovně Premium * (1 TB nebo vyšší velikost na disk) | DS3_V2     |
| 60                      | 200 GB                | Čisté klidové vytížení 10 %                  | 10500            | SSD úrovně Premium × (8 TB nebo vyšší velikost na disk) | DS3_V2     |

* Chcete-li získat požadované IOPs, použijte minimální nebo vyšší velikost disku. Menší velikost disku nabízí nižší IOPs.

> [!NOTE]
> Azure Backup Server je navržená tak, aby běžela na vyhrazeném serveru s jedním účelem. Do počítače nelze nainstalovat Azure Backup Server:
> * Spuštění jako řadič domény
> * Má nainstalovanou roli Aplikační Server
> * To je System Center Operations Manager management server
> * Používání systému Exchange Server
> * To je uzel clusteru.

### <a name="disks-and-storage"></a>Disky a úložiště

Azure Backup Server vyžadují disky k instalaci, včetně systémových souborů, instalačních souborů, požadovaného softwaru, souborů databáze a vyhrazených disků pro fond úložiště.

| Požadavek                      | Doporučená velikost  |
|----------------------------------|-------------------------|
| Instalace Azure Backup Server                | Umístění instalace: 3 GB<br />Jednotka souborů databáze: 900 MB<br />Systémová jednotka: 1 GB pro instalaci SQL<br /><br />Kromě toho budete potřebovat místo pro Azure Backup Server ke zkopírování katalogu souborů do dočasného umístění instalace při archivaci.      |
| Disk pro fond úložiště<br />(Používá základní svazky, nemůže být na dynamickém disku.) | 2 až 3 časy velikosti chráněných dat<br />Podrobný výpočet úložiště najdete v [Capacity Planner DPM](https://www.microsoft.com/download/details.aspx?id=54301).   |

[Připojení spravovaného datového disku k virtuálnímu počítači s Windows pomocí Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md) článků ukazují, jak připojit nový spravovaný datový disk k EXISTUJÍCÍmu virtuálnímu počítači Azure.

> [!NOTE]
> Jeden Azure Backup Server má pro fond úložiště měkké omezení 120 TB.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Ukládání zálohovaných dat na místní disk a v Azure

Ukládání zálohovaných dat v Azure omezuje infrastrukturu zálohování na Azure Backup Serverm virtuálním počítači. Pro provozní obnovení (zálohování) Azure Backup Server ukládá zálohovaná data na disky Azure připojené k virtuálnímu počítači. Jakmile jsou disky a prostory úložiště připojené k virtuálnímu počítači, Azure Backup Server spravuje úložiště. Velikost úložiště záložních dat závisí na počtu a velikosti disků připojených ke každému virtuálnímu počítači Azure a každá velikost virtuálního počítače Azure má maximální počet disků, které se dají připojit. Například a2 je čtyři disky. A3 je osm disků. A4 je 16 disků. Opět velikost a počet disků určují celkovou kapacitu fondu úložiště zálohování.

> [!IMPORTANT]
> Po dobu delší než pět dnů byste **neměli uchovávat data** o provozním obnovení na discích Azure Backup Server připojených. Pokud jsou data starší než pět dnů, uložte je do služby Azure Recovery Services trezor.

Pokud chcete ukládat zálohovaná data v Azure, vytvořte nebo použijte Recovery Services trezor. Při přípravě zálohování Azure Backup Server úlohy [nakonfigurujete trezor Recovery Services](#create-a-recovery-services-vault). Po nakonfigurování pokaždé, když se spustí úloha online zálohování, se v trezoru vytvoří bod obnovení. Každý trezor Recovery Services uchovává až 9999 bodů obnovení. V závislosti na počtu vytvořených bodů obnovení a dobu, po kterou jsou zachována, můžete data zálohy uchovávat mnoho let. Můžete například vytvořit měsíční body obnovení a uchovávat je po dobu pěti let.

> [!IMPORTANT]
> Bez ohledu na to, jestli odesíláte zálohovaná data do Azure nebo je uložíte v místním počítači, se musíte zaregistrovat Azure Backup Server s trezorem Recovery Services.

### <a name="scale-deployment"></a>Škálování nasazení

Pokud chcete škálovat nasazení, máte následující možnosti:

- **Horizontální** navýšení kapacity – zvětšete velikost Azure Backup Server virtuálního počítače z řady na řady DS3 a zvyšte místní úložiště.

- **Přesměrování dat** – odešlete starší data do Azure a zachovejte jenom nejnovější data v úložišti připojeném k Azure Backup Server.

- **Škálování** na více instancí – přidejte další Azure Backup servery, abyste chránili úlohy.

### <a name="net-framework"></a>.NET Framework

Virtuální počítač musí mít nainstalovanou aktualizaci .NET Framework 3,5 SP1 nebo novější.

### <a name="join-a-domain"></a>Připojení k doméně

Virtuální počítač Azure Backup Server musí být připojený k doméně a uživatel domény s oprávněními správce na VIRTUÁLNÍm počítači musí nainstalovat Azure Backup Server.

I když v době verze Preview není podporované, Azure Backup Server nasazené na virtuálním počítači Azure může zálohovat úlohy na virtuálních počítačích v rámci služby AVS, ale měly by být ve stejné doméně, aby se povolila operace zálohování.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Recovery Services trezor je entita úložiště, která ukládá body obnovení vytvořené v průběhu času. Obsahuje také zásady zálohování, které jsou přidruženy k chráněným položkám.

1. Přihlaste se k předplatnému v [Azure Portal](https://portal.azure.com/).

1. V nabídce vlevo vyberte **všechny služby**.

   ![Vybrat všechny služby](../backup/media/backup-create-rs-vault/click-all-services.png)

1. V dialogovém okně **všechny služby** zadejte *Recovery Services* a v seznamu vyberte **Recovery Services trezory** .

   ![Zadejte a vyberte trezory Recovery Services.](../backup/media/backup-create-rs-vault/all-services.png)

   Zobrazí se seznam trezorů Recovery Services v předplatném.

1. Na řídicím panelu **trezorů Recovery Services** vyberte **Přidat**.

   ![Přidání trezoru Recovery Services](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   Otevře se dialogové okno **Recovery Services trezor** .

1. Zadejte hodnoty pro **název**, **předplatné**, **skupinu prostředků**a **umístění**.

   ![Konfigurace trezoru Recovery Services](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Název**: zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný pro předplatné Azure. Zadejte název, který má alespoň dva, ale ne více než 50 znaků. Název musí začínat písmenem a obsahovat jenom písmena, číslice a spojovníky.

   - **Předplatné**: vyberte předplatné, které chcete použít. Pokud jste členem jenom jednoho předplatného, uvidíte tento název. Pokud si nejste jistí, které předplatné se má použít, použijte výchozí (navrhované) předplatné. K dispozici je více možností pouze v případě, že je váš pracovní nebo školní účet spojen s více než jedním předplatným Azure.

   - **Skupina prostředků**: použijte existující skupinu prostředků nebo vytvořte novou. Pokud chcete zobrazit seznam dostupných skupin prostředků ve vašem předplatném, vyberte **použít existující**a pak v rozevíracím seznamu vyberte prostředek. Pokud chcete vytvořit novou skupinu prostředků, vyberte **vytvořit novou** a zadejte název.

   - **Umístění**: vyberte zeměpisnou oblast trezoru. Pokud chcete vytvořit trezor pro ochranu virtuálních počítačů se systémem AVS, *musí* být trezor ve stejné oblasti jako privátní cloud služby AVS.

1. Až budete připraveni k vytvoření trezoru Recovery Services, vyberte **vytvořit**.

   ![Vytvoření trezoru Recovery Services](../backup/media/backup-create-rs-vault/click-create-button.png)

   Vytvoření trezoru Recovery Services může chvíli trvat. Sledujte oznámení o stavu v oblasti **oznámení** v pravém horním rohu portálu. Až se váš trezor vytvoří, zobrazí se v seznamu trezorů Recovery Services. Pokud váš trezor nevidíte, vyberte **aktualizovat**.

   ![Aktualizuje seznam trezorů služby Backup.](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Nastavení replikace úložiště

Možnost replikace úložiště vám umožní vybrat mezi geograficky redundantním úložištěm (výchozím) a místně redundantním úložištěm. Geograficky redundantní úložiště kopíruje data v účtu úložiště do sekundární oblasti, takže vaše data budou trvalá. Místně redundantní úložiště je levnější možnost, která není jako trvalá. Přečtěte si další informace o možnostech geograficky redundantního a místně redundantního úložiště v [Azure Storage redundanci](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Změna **typu replikace úložiště** (místně redundantní/geograficky redundantní) pro trezor služby Recovery Services se musí provést před konfigurací záloh v trezoru. Když nakonfigurujete zálohování, možnost změny je zakázaná a **typ replikace úložiště**nemůžete změnit.

1. V části **trezory Recovery Services**klikněte na nový trezor. 

1. V části **Nastavení**vyberte **vlastnosti**a v části **Konfigurace zálohování**klikněte na **aktualizovat**.

1. Vyberte typ replikace úložiště a klikněte na **Uložit**.

   ![Nastavení konfigurace úložiště pro nový trezor](../backup/media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="download-and-install-software-package"></a>Stáhnout a nainstalovat balíček softwaru

### <a name="downloading-the-software-package"></a>Stažení softwarového balíčku

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. Pokud už máte otevřený trezor Recovery Services, pokračujte k dalšímu kroku. Pokud nemáte otevřený trezor Recovery Services, ale nachází se v Azure Portal, v hlavní nabídce klikněte na tlačítko **Procházet**.

   1. V seznamu prostředků zadejte **Recovery Services**.

   1. Seznam se průběžně filtruje podle zadávaného textu. Až uvidíte **Trezory Recovery Services**, klikněte na ně.

   ![Vytvoření trezoru Recovery Services – krok 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Ze seznamu trezorů Služeb zotavení vyberte trezor.

   Otevře se řídicí panel vybraného trezoru.

   ![Otevřené okno trezoru](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   **Nastavení** se zobrazí ve výchozím nastavení. Pokud jste zavřeli, vyberte **Nastavení** a otevřete ho.

   ![Otevřené okno trezoru](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Kliknutím na **zálohovat** otevřete Průvodce Začínáme.

   ![Začínáme se zálohováním](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. V okně, které se otevře, udělejte toto:

   1. V nabídce **kde je spuštěná vaše úloha** , vyberte **místní**.

      :::image type="content" source="media/avs-backup/deploy-mabs-on-premises-workload.png" alt-text="Kde běží vaše zatížení?":::

   1. V nabídce **co chcete zálohovat** vyberte úlohy, které chcete chránit pomocí Azure Backup Server.

   1. Kliknutím na **připravit infrastrukturu** Stáhněte a nainstalujte Azure Backup Server a přihlašovací údaje trezoru.

      :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Příprava infrastruktury":::

1. V okně **připravit infrastrukturu** , které se otevře, udělejte toto:

   1. Kliknutím na odkaz **Stáhnout** nainstalujte Azure Backup Server.

   1. Stáhněte si přihlašovací údaje trezoru tak, že vyberete **již staženou nebo pomocí nejnovější instalace Azure Backup Server** a potom kliknete na **Stáhnout**. Přihlašovací údaje trezoru použijete během registrace Azure Backup Server do trezoru služby Recovery Services. Odkazy vás propojí do služby Stažení softwaru, kde stáhnete softwarový balíček.

   :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Příprava infrastruktury – Azure Backup Server":::

1. Na stránce pro stažení vyberte všechny soubory a klikněte na **Další**.

   > [!NOTE]
   > Všechny soubory je nutné stáhnout do stejné složky.  Vzhledem k tomu, že velikost souborů ke stažení společně > povolenou, může stažení trvat až 60 minut. 

   ![Stažení centra 1](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extracting-the-software-package"></a>Extrakce softwarového balíčku

Pokud jste balíček softwaru stáhli na jiný server, zkopírujte soubory do virtuálního počítače, který jste vytvořili pro nasazení Azure Backup Server.

> [!WARNING]
> K extrakci instalačních souborů je potřeba aspoň 4 GB volného místa.

1. Po stažení všech souborů poklikejte na **MicrosoftAzureBackupInstaller. exe** a otevřete tak **Průvodce instalací Microsoft Azure Backup** a pak klikněte na **Další**.

1. Vyberte umístění, do kterého chcete soubory extrahovat, a klikněte na tlačítko **Další**.

1. Kliknutím na **extrahovat** zahajte proces extrakce.

   ![Průvodce instalací Microsoft Azure Backup](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. Po extrakci vyberte možnost spuštění souboru **Setup. exe** a poté klikněte na tlačítko **Dokončit**.

> [!TIP]
> Soubor Setup. exe můžete také najít ze složky, do které jste extrahovali softwarový balíček.

### <a name="installing-the-software-package"></a>Instalace softwarového balíčku

1. V okně nastavení v části instalovat klikněte na **Microsoft Azure Backup** . otevře se Průvodce instalací nástroje.

   ![Průvodce instalací Microsoft Azure Backup](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. Na úvodní obrazovce klikněte na tlačítko **Další** a pokračujte na kontroly požadovaných součástí.

1. Kliknutím na tlačítko **ověřit** určete, zda jsou splněny požadavky na hardware a software pro Azure Backup Server. V případě úspěšného splnění klikněte na tlačítko **Další**.

   ![Azure Backup Server – uvítání a kontrolu předpokladů](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Instalační balíček Azure Backup Server obsahuje balíčky s požadovanými binárními soubory SQL Server. Při spuštění nové instalace Azure Backup Server vyberte možnost **instalovat novou instanci SQL Server s touto možností instalace** a klikněte na tlačítko **ověřit a nainstalovat**.

   ![Kontroly Azure Backup Server-SQL](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Pokud chcete použít vlastní SQL Server, podporované verze SQL Server jsou SQL Server 2014 SP1 nebo vyšší, 2016 a 2017. Všechny SQL Server verze by měly být Standard nebo Enterprise 64-bit. Azure Backup Server nepracuje s instancí vzdáleného SQL Server. Instance, kterou používá Azure Backup Server, musí být místní. Pokud používáte existující server SQL pro Azure Backup Server, instalace podporuje pouze *pojmenované instance* systému SQL Server.

   Pokud dojde k selhání s doporučením pro restartování počítače, udělejte to a znovu klikněte na **znovu spustit**. Pokud dojde k nějakým problémům s konfigurací SQL, překonfigurujte SQL podle pokynů pro SQL a zkuste instalaci/upgrade Azure Backup Server pomocí existující instance SQL.

   **Ruční konfigurace**

   Pokud používáte vlastní instanci SQL, nezapomeňte přidat builtin\Administrators do role sysadmin do hlavní databáze.

   **Konfigurace SSRS s SQL 2017**

   Pokud používáte vlastní instanci SQL 2017, je potřeba nakonfigurovat SSRS ručně. Po konfiguraci služby SSRS zajistěte, aby byla vlastnost- *inicializovaná* u služby SSRS nastavena na *hodnotu true*. Pokud je tato hodnota nastavená na true, MABS předpokládá, že služba SSRS je už nakonfigurovaná, a přeskočí konfiguraci služby SSRS.

   Chcete-li zjistit stav konfigurace služby SSRS, spusťte následující příkaz:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Pro konfiguraci služby SSRS použijte následující hodnoty:
   * Účet služby: použít předdefinovaný účet by měl být síťová služba
   * Adresa URL webové služby: virtuální adresář by měl být ReportServer_\<SQLInstanceName>
   * Databáze: DatabaseName by měl být ReportServer $\<SQLInstanceName>
   * Adresa URL webového portálu: virtuální adresář by měl být Reports_\<SQLInstanceName>

   [Přečtěte si další informace](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) o konfiguraci služby SSRS.

   > [!NOTE]
   > [Podmínky poskytování služeb Microsoft Online Services](https://www.microsoft.com/licensing/product-licensing/products) (OST) řídí licencování pro SQL Server používané jako databáze pro Azure Backup Server. V závislosti na OST SQL Server sady s Azure Backup Server používat jenom jako databázi pro Azure Backup Server.

1. Po úspěšné instalaci klikněte na **Další**.

1. Zadejte umístění instalace souborů Microsoft Azure Backup serveru a klikněte na tlačítko **Další**.

   > [!NOTE]
   > Pro zálohování do Azure se vyžaduje pomocné umístění. Ujistěte se, že pracovní umístění je alespoň 5% plánovaného zálohování dat do cloudu. V případě ochrany disku je potřeba po dokončení instalace nakonfigurovat samostatné disky. Další informace o fondech úložiště najdete v tématu [Konfigurace fondů úložiště a diskového úložiště](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Zadejte silné heslo pro omezené místní uživatelské účty a klikněte na **Další**.

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Vyberte, zda chcete vyhledat aktualizace pomocí Microsoft Update a klikněte na tlačítko **Další**.

   > [!NOTE]
   > Doporučujeme, abyste web Windows Update přesměrování na Microsoft Update, což nabízí zabezpečení a důležité aktualizace pro Windows a další produkty, jako je Microsoft Azure Backup Server.

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Zkontrolujte *Souhrn nastavení* a klikněte na **nainstalovat**.

   Instalace proběhne ve fázích. První fáze nainstaluje agenta Microsoft Azure Recovery Services a druhá fáze zkontroluje připojení k Internetu. Pokud je k dispozici připojení k Internetu, můžete pokračovat v instalaci. v takovém případě je nutné zadat podrobnosti o proxy serveru pro připojení k Internetu. Poslední fáze zkontroluje nezbytný software a pokud není nainstalovaný, veškerý chybějící software se nainstaluje společně s agentem Microsoft Azure Recovery Services.

1. Kliknutím na **Procházet** vyhledejte přihlašovací údaje trezoru a zaregistrujte počítač do trezoru Recovery Services a potom klikněte na **Další**.

1. Vyberte heslo pro šifrování/dešifrování dat odesílaných mezi Azure a vaším místním prostředím.

   > [!TIP]
   > Můžete automaticky vygenerovat heslo nebo zadat vlastní minimální přístupové heslo 16 znaků.

1. Zadejte umístění pro uložení hesla a potom kliknutím na tlačítko **Další** Zaregistrujte server.

   > [!IMPORTANT]
   > Je důležité také uložit heslo do bezpečného umístění, které je jiné než místní server. Microsoft důrazně navrhuje použití Azure Key Vault pro uložení hesla.

   Po dokončení instalace agenta Microsoft Azure Recovery Services se přesunete do instalace a konfigurace SQL Server a Azure Backup Server komponenty.

   ![Server Azure Backup](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. Až se krok instalace dokončí, klikněte na **Zavřít**.

### <a name="install-update-rollup-1"></a>Nainstalovat kumulativní aktualizaci 1

Před ochranou úloh je instalace kumulativní aktualizace 1 pro Microsoft Azure Backup Server V3 povinná. Pokud chcete zobrazit seznam oprav chyb a pokyny k instalaci Microsoft Azure Backup serveru V3 UR1, přečtěte si článek znalostní báze [4534062](https://support.microsoft.com/en-us/help/4534062/).

## <a name="add-storage-to-azure-backup-server"></a>Přidání úložiště do Azure Backup Serveru

Azure Backup Server V3 podporuje Moderní úložiště zálohování, které nabízí:

-  Úspory úložiště s 50%

-  Tři zálohy rychleji

-  Efektivnější úložiště

-  Úložiště s podporou úloh

### <a name="volumes-in-backup-server"></a>Svazky na záložním serveru

Přidejte datové disky s požadovanou kapacitou úložiště do virtuálního počítače serveru Azure Backup, pokud ještě není přidané.

Záložní server V3 akceptuje pouze svazky úložiště. Když přidáte svazek, záložní server naformátuje svazek na odolný systém souborů (ReFS), který Moderní úložiště zálohování vyžaduje.

### <a name="add-volumes-to-backup-server-disk-storage"></a>Přidání svazků do úložiště disku záložního serveru

1. V podokně **Správa** znovu zkontrolujte úložiště a pak vyberte **Přidat**. 

1. Vyberte z dostupných svazků, které chcete přidat do fondu úložiště. 

1. Po přidání dostupných svazků poskytněte jim popisný název, který vám bude pomáhat s jejich správou. 

1. Kliknutím na **OK** naformátujte tyto svazky na ReFS, aby mohl záložní server využívat výhody moderní úložiště zálohování.

![Přidat dostupné svazky](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu kurzu, kde se dozvíte, jak nakonfigurovat zálohování virtuálních počítačů VMware běžících na řešení Azure VMware (AVS) pomocí Azure Backup Server.

> [!div class="nextstepaction"]
> [Konfigurace zálohování virtuálních počítačů AVS](backup-avs-vms-with-mabs.md)

