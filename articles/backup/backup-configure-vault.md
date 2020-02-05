---
title: Zálohování počítačů s Windows pomocí agenta MARS
description: K zálohování počítačů s Windows použijte agenta Azure Backup Microsoft Recovery Services (MARS).
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 990929cc95d1c25117873ca39415d33370456b91
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025533"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Zálohování počítačů s Windows s využitím agenta MARS služby Azure Backup

Tento článek vysvětluje, jak zálohovat počítače s Windows pomocí služby [Azure Backup](backup-overview.md) a agenta služby Microsoft Azure Recovery Services (MARS), označovaného také jako agent Azure Backup.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Ověřte požadavky a vytvořte Trezor Recovery Services.
> * Stažení a nastavení agenta MARS
> * Vytvořte zásady zálohování a plán.
> * Proveďte zálohování na vyžádání.

## <a name="about-the-mars-agent"></a>O agentovi MARS

Agent MARS používá Azure Backup k zálohování souborů, složek a stavu systému z místních počítačů a virtuálních počítačů Azure do trezoru služby Backup Recovery Services v Azure. Agenta můžete spustit následujícím způsobem:

* Spusťte agenta přímo na místních počítačích s Windows, aby se mohly zálohovat přímo do trezoru služby Backup Recovery Services v Azure.
* Spusťte agenta na virtuálních počítačích Azure s Windows (vedle sebe s rozšířením zálohování virtuálních počítačů Azure), abyste mohli zálohovat konkrétní soubory a složky na virtuálním počítači.
* Spusťte agenta na serveru Microsoft Azure Backup (MABS) nebo na serveru System Center Data Protection Manager (DPM). V tomto scénáři se počítače a úlohy zálohují do MABS/DPM a pak se MABS/DPM zálohuje do trezoru v Azure pomocí agenta MARS.
To, co se dá zálohovat, závisí na tom, kde je agent nainstalovaný.

> [!NOTE]
> Primární metodou pro zálohování virtuálních počítačů Azure je použití rozšíření Azure Backup na virtuálním počítači. Tím se zálohuje celý virtuální počítač. Pokud chcete zálohovat konkrétní soubory a složky na virtuálním počítači, můžete chtít nainstalovat a používat agenta MARS společně s příponou. [Další informace](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Kroky procesu zálohování](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Než začnete

* [Informace o tom, jak](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Azure Backup Zálohuje počítače s Windows pomocí agenta MARS.
* [Přečtěte si o](backup-architecture.md#architecture-back-up-to-dpmmabs) architektuře zálohování, na které běží agent Mars na sekundárním serveru MABS nebo DPM.
* [Přečtěte si](backup-support-matrix-mars-agent.md) , co je podporováno a co je možné zálohovat pomocí agenta Mars.
* Ověřte přístup k Internetu na počítačích, které chcete zálohovat.
* K zálohování serveru nebo klienta do Azure potřebujete účet Azure. Pokud ho nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/free/) .

### <a name="verify-internet-access"></a>Ověřit přístup k Internetu

Pokud má počítač omezený přístup k Internetu, zajistěte, aby nastavení brány firewall na počítači nebo proxy umožňovalo tyto adresy URL a IP adresy:

#### <a name="urls"></a>Adrese

* www\.msftncsi.com
* *.Microsoft.com
* *.WindowsAzure.com
* *.microsoftonline.com
* *.windows.net

#### <a name="ip-addresses"></a>IP adresy

* 20.190.128.0/18
* 40.126.0.0/18

Přístup ke všem adresám URL a IP adresám uvedeným výše používá protokol HTTPS na portu 443.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Trezor Recovery Services ukládá všechny zálohy a body obnovení, které vytvoříte v průběhu času, a obsahuje zásady zálohování použité pro zálohované počítače. Vytvořte Trezor následujícím způsobem:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí svého předplatného Azure.

2. Vyhledejte a vyberte **trezory Recovery Services**.

    ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-configure-vault/open-recovery-services-vaults.png)

3. V nabídce **Recovery Services trezory** klikněte na **+ Přidat**.

    ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. Jako **Název** zadejte popisný název pro identifikaci trezoru.

   * Název musí být jedinečný v rámci předplatného Azure.
   * Může obsahovat 2 až 50 znaků.
   * Musí začínat písmenem a může obsahovat jenom písmena, číslice a spojovníky.

5. Vyberte předplatné Azure, skupinu prostředků a geografickou oblast, ve které se má Trezor vytvořit. Zálohovaná data se odesílají do trezoru. Poté klikněte na **Vytvořit**.

    ![Vytvoření trezoru Recovery Services – krok 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   * Vytvoření trezoru může chvíli trvat.
   * Sledujte oznámení o stavu v pravé horní části portálu. Pokud trezor nevidíte po několika minutách, klikněte na **aktualizovat**.

     ![Kliknutí na tlačítko Obnovit](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Nastavit redundanci úložiště

Azure Backup automaticky zpracovává úložiště pro trezor. Musíte určit způsob replikace tohoto úložiště.

1. V okně **Trezory služby Recovery Services** klikněte na nový trezor. V části **Nastavení** klikněte na **vlastnosti**.
2. V části **vlastnosti**v části **Konfigurace zálohování**klikněte na **aktualizovat**.

3. Vyberte typ replikace úložiště a klikněte na **Uložit**.

      ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

* Pokud používáte Azure jako primární koncový bod úložiště záloh, doporučujeme, abyste používali výchozí **geograficky redundantní** nastavení.
* Pokud Azure nepoužíváte jako primární koncový bod úložiště záloh, vyberte **Místně redundantní** – snížíte tím náklady na úložiště Azure.
* Přečtěte si další informace o [geografické](../storage/common/storage-redundancy-grs.md) a [místní](../storage/common/storage-redundancy-lrs.md) redundanci.

## <a name="download-the-mars-agent"></a>Stáhnout agenta MARS

Stáhněte agenta MARS pro instalaci na počítačích, které chcete zálohovat.

* Pokud jste už agenta nainstalovali na všechny počítače, ujistěte se, že používáte nejnovější verzi.
* Nejnovější verze je k dispozici na portálu nebo pomocí [přímého stažení](https://aka.ms/azurebackup_agent)

1. V trezoru v části **Začínáme**klikněte na **zálohovat**.

    ![Otevřete okno cíle zálohování](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. V nabídce **kde je spuštěná vaše úloha?** vyberte **místní**. Tuto možnost byste měli vybrat, i když chcete agenta MARS nainstalovat na virtuální počítač Azure.
3. V **Možnosti co chcete zálohovat?** vyberte **soubory a složky** a **stav systému**. K dispozici je několik dalších možností, ale ty se podporují jenom v případě, že používáte sekundární záložní server. Klikněte na **Příprava infrastruktury**.

      ![Konfigurace souborů a složek](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. V části **Příprava infrastruktury**v části **instalovat Recovery Services agenta**Stáhněte agenta Mars.

    ![Příprava infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. V místní nabídce stahování klikněte na **Uložit**. Ve výchozím nastavení se soubor **MARSagentinstaller.exe** uloží do složky Stažené soubory.

6. Teď zkontrolujte, jestli **už jste si stáhli nejnovější verzi agenta Recovery Services**, a pak stáhněte přihlašovací údaje trezoru.

    ![stažení přihlašovacích údajů trezoru](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Klikněte na **Uložit**. Soubor se stáhne do složky pro stahování. Nejde otevřít soubor s přihlašovacími údaji trezoru.

## <a name="install-and-register-the-agent"></a>Instalace a registrace agenta

1. Spusťte soubor **soubor marsagentinstaller. exe** na počítačích, které chcete zálohovat.
2. V Průvodci instalací agenta MARS > **nastavení instalace**určete, kam chcete nainstalovat agenta, a umístění, které se má použít pro mezipaměť. Pak klikněte na tlačítko **Další**.
   * Azure Backup používá mezipaměť k ukládání snímků dat před jejich odesláním do Azure.
   * Umístění mezipaměti by mělo mít volné místo, které se rovná nejméně 5% velikosti dat, která budete zálohovat.

    ![Nastavení instalace Průvodce MARS](./media/backup-configure-vault/mars1.png)

3. V části **konfigurace proxy**zadejte, jak se má agent běžící na počítači s Windows připojit k Internetu. Pak klikněte na tlačítko **Další**.

   * Pokud používáte vlastní proxy server, zadejte nastavení proxy serveru a v případě potřeby přihlašovací údaje.
   * Mějte na paměti, že agent potřebuje přístup k [těmto adresám URL](#verify-internet-access).

     ![Průvodce MARS pro Internet přístup](./media/backup-configure-vault/mars2.png)

4. V části **instalace** zkontrolujte kontrolu požadovaných součástí a klikněte na **nainstalovat**.
5. Po instalaci agenta klikněte na **pokračovat k registraci**.
6. V **Průvodci registrací serveru** > **Identifikace trezoru**vyhledejte a vyberte soubor s přihlašovacími údaji, který jste stáhli. Pak klikněte na tlačítko **Další**.

    ![Registrace – přihlašovací údaje trezoru](./media/backup-configure-vault/register1.png)

7. V **nastavení šifrování**zadejte heslo, které se použije k šifrování a dešifrování záloh pro daný počítač.

    * Uložit šifrovací heslo na bezpečné místo.
    * Pokud heslo ztratíte nebo zapomenete, Microsoft nemůže získat zálohu zálohovaných dat. Uložte soubor na bezpečné místo. Budete ho potřebovat k obnovení zálohy.

8. Klikněte na **Dokončit**. Agent je nyní nainstalovaný a váš počítač je registrovaný k trezoru. Jste připraveni nakonfigurovat a naplánovat zálohování.

## <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování určují, kdy pořizovat snímky dat pro vytvoření bodů obnovení a jak dlouho zachovat body obnovení.

* Zásady zálohování můžete nakonfigurovat pomocí agenta MARS.
* Azure Backup automaticky nebere v úvahu čas letního času (DST). To může způsobit nesoulad mezi skutečným časem a plánovaným časem zálohování.

Vytvořte zásadu následujícím způsobem:
1. Po stažení a registraci agenta MARS spusťte konzolu agenta. Najdete ho vyhledáním **Microsoft Azure Backup** ve svém počítači.  
2. V nabídce **Akce**klikněte na **naplánovat zálohování**.

    ![Naplánování zálohování Windows Serveru](./media/backup-configure-vault/schedule-first-backup.png)
3. V Průvodci plánováním zálohování > **Začínáme**klikněte na **Další**.
4. V nabídce **Vyberte položky, které chcete zálohovat**klikněte na **Přidat položky**.

    ![Vyberte položky, které chcete zálohovat.](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. V nabídce **Vybrat položky**vyberte, co chcete zálohovat, a klikněte na **OK**.

    ![Vybrané položky k zálohování](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. Na stránce **Vyberte položky, které chcete zálohovat** klikněte na **Další**.
7. Na stránce **zadat plán zálohování** zadejte, kdy chcete provést denní nebo týdenní zálohování. Pak klikněte na tlačítko **Další**.

    - Bod obnovení se vytvoří při vytvoření zálohy.
    - Počet bodů obnovení vytvořených ve vašem prostředí závisí na plánu zálohování.


8. Můžete naplánovat každodenní zálohování, maximálně třikrát denně. Například snímek obrazovky ukazuje dva denní zálohy, jednu o půlnoc a jednu v 6:00./odp.

    ![Denní plán](./media/backup-configure-vault/day-schedule.png)


9. Můžete spustit i týdenní zálohování. Snímek obrazovky například ukazuje, že se v každé alternativní neděli & středa v 9:30 AM a 1:00 AMy zálohy.

    ![Týdenní plán](./media/backup-configure-vault/week-schedule.png)


10. Na stránce **Vybrat zásady uchovávání informací** určete způsob ukládání historických kopií vašich dat. Pak klikněte na tlačítko **Další**.

    - Nastavení uchovávání určují, které body obnovení by se měly ukládat a jak dlouho se mají ukládat.
    - Když například nastavíte denní nastavení uchovávání, označíte si, že v době určené pro denní uchovávání se bude pro zadaný počet dnů uchovávat nejnovější bod obnovení. Nebo jako jiný příklad můžete zadat měsíční zásady uchovávání informací, které označují, že bod obnovení vytvořený na 30. den v měsíci by měl být uložený na 12 měsíců.
    - Denní a týdenní uchování bodu obnovení se obvykle shoduje s plánem zálohování. To znamená, že když se zálohování spustí podle plánu, bude se bod obnovení vytvořený zálohováním ukládat po dobu určenou v zásadách denního nebo týdenního uchovávání.
    - Například na následujícím snímku obrazovky:

        -   Denní zálohy s půlnocí a 6:00 PM se uchovávají po dobu sedmi dnů.
        -   Zálohy provedené v sobotu s půlnocí a 6:00 PM se uchovávají po dobu čtyř týdnů.
        -   Zálohy provedené v sobotu v posledním týdnu v měsíci s půlnocí a 6:00 PM se uchovávají po dobu 12 měsíců.
        -   Zálohy provedené na sobotu v posledním týdnu v březnu jsou uchovány po dobu 10 let.

        ![Příklad uchování](./media/backup-configure-vault/retention-example.png)


11. V části **zvolit typ prvotní zálohy** rozhodněte, jestli chcete provést prvotní zálohování přes síť, nebo použít offline zálohování (Další informace o offline zálohování najdete v tomto [článku](offline-backup-azure-data-box.md)). Chcete-li provést prvotní zálohování sítě, vyberte možnost **automaticky přes síť** a klikněte na tlačítko **Další**.

    ![Typ prvotní zálohy](./media/backup-azure-manage-mars/choose-initial-backup-type.png)


12. V části **potvrzení**zkontrolujte informace a pak klikněte na **Dokončit**.

    ![Potvrdit typ zálohy](./media/backup-azure-manage-mars/confirm-backup-type.png)


13. Až průvodce dokončí vytváření plánu zálohování, klikněte na **Zavřít**.

    ![Potvrdit úpravu procesu zálohování](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Je nutné vytvořit zásadu na každém počítači, kde je nainstalován agent.

### <a name="perform-the-initial-backup-offline"></a>Provedení prvotního zálohování offline

Počáteční zálohu můžete spustit automaticky přes síť nebo offline. Počáteční osazení offline pro prvotní zálohování je užitečné, pokud máte velké objemy dat, která budou vyžadovat přenos velkou šířkou pásma sítě. Offline přenos provádíte následujícím způsobem:

1. Zálohovaná data zapisujete do pracovního umístění.
2. Pomocí nástroje AzureOfflineBackupDiskPrep můžete kopírovat data z pracovního umístění na jeden nebo více disků SATA.
3. Nástroj vytvoří úlohu importu Azure. [Přečtěte si další informace](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) o importu a exportu Azure.
4. Disky SATA odešlete do datacentra Azure.
5. V datacentru se data disku zkopírují do účtu služby Azure Storage.
6. Azure Backup zkopíruje data z účtu úložiště do trezoru a naplánují se přírůstkové zálohy.

[Přečtěte si další informace](offline-backup-azure-data-box.md) o dosazení offline.

### <a name="enable-network-throttling"></a>Povolit omezení sítě

Povolením omezení sítě můžete řídit, jak se používá šířka pásma sítě v agentovi MARS. Omezování je užitečné, pokud potřebujete zálohovat data během pracovní doby, ale chcete určit, kolik šířky pásma se má pro aktivitu zálohování a obnovení použít.

* Azure Backup omezování sítě používá [technologii QoS (Quality of Service)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) v místním operačním systému.
* Omezení sítě pro zálohování je dostupné v systému Windows Server 2012 a vyšším a Windows 8 a vyšším. Operační systémy by měly používat nejnovější aktualizace Service Pack.

Povolte omezení sítě následujícím způsobem:

1. V části agent MARS klikněte na **změnit vlastnosti**.
2. Na kartě **omezování** zaškrtněte **možnost Povolit omezení využití šířky pásma internetu pro operace zálohování**.

    ![Omezení sítě](./media/backup-configure-vault/throttling-dialog.png)
3. Určete povolenou šířku pásma během práce a mimo pracovní dobu. Hodnoty šířky pásma začínají v 512 kbps a jdou až 1 023 MB/s. Pak klikněte na **OK**.

## <a name="run-an-on-demand-backup"></a>Spuštění zálohování na vyžádání

1. V části agent MARS klikněte na **Zálohovat nyní**.

    ![Zálohovat nyní ve Windows Serveru](./media/backup-configure-vault/backup-now.png)

2. Pokud je verze agenta MARS 2.0.9169.0 nebo novější, je možné nastavit vlastní uchovávání. V části **uchování zálohy** vyberte datum z prezentovaného kalendáře:

   ![Uchovat kalendář zálohy](./media/backup-configure-vault/mars-ondemand.png)

3. V části **potvrzení**zkontrolujte nastavení a klikněte na **zálohovat**.
4. Průvodce zavřete kliknutím na **Zavřít**. Pokud to provedete ještě před dokončením zálohování, průvodce bude nadále běžet na pozadí.
5. Po dokončení prvotní zálohy se v konzole Zálohování zobrazí stav **Úloha byla dokončena**.

## <a name="on-demand-backup-policy-retention-behavior"></a>Chování uchovávání zásad zálohování na vyžádání

>[!NOTE]
>Platí jenom pro verze agenta MARS starší než 2.0.9169.0.
>

* Další informace najdete v kroku 8 tématu [Vytvoření zásady zálohování](backup-configure-vault.md#create-a-backup-policy) .

| Možnost plánu zálohování | Jak dlouho budou zálohovaná data zachovaná?
| -- | --
| Naplánovat zálohování každých: * den | **Výchozí doba uchování**: ekvivalent "uchování ve dnech pro denní zálohy" <br/><br/> **Výjimka**: Pokud se naplánovaná denní doba uchovávání dat pro dlouhodobé uchovávání (týdny, měsíce, roky) nezdařila, pak se pro dlouhodobé uchování považuje zálohování na vyžádání hned po tomto neúspěšném plánovaném zálohování. V opačném případě se další naplánované zálohování považuje za dlouhodobou dobu uchovávání.<br/><br/> **Příklad**: Pokud (řekněme) naplánované zálohování pořízené ve čtvrtek 8:00 se nezdařilo a stejné zálohování bylo považováno za týdenní/měsíční/roční uchování, pak první zálohování na vyžádání aktivované před dalším naplánovaným zálohováním (říká) pátek, 8:00 by se automaticky označilo k týdennímu uchovávání na týden/měsíc/roční uchování, a to ve 8:00 čtvrtek až do 12:00.
| Naplánovat zálohování každých: * týdně | **Výchozí uchovávání**: 1 den <br/> Zálohy na vyžádání pro zdroj dat s týdenními zásadami zálohování se odstraní v následujícím dni, a to i v případě, že se jedná o nejaktuálnější zálohy pro zdroj dat. <br/><br/> **Výjimka**: Pokud je pro dlouhodobou dobu uchovávání nastavená týdenní Naplánovaná záloha (týdny, měsíce, roky), pak se pro dlouhodobé uchování považuje zálohování na vyžádání hned po tomto neúspěšném plánovaném zálohování. V opačném případě se další naplánované zálohování považuje za dlouhodobou dobu uchovávání. <br/><br/> **Příklad**: Pokud (řekněme) naplánované zálohování pořízené ve čtvrtek 8:00 se nepovede a že se stejné zálohování mělo považovat za měsíční a roční uchování, pak první zálohování na vyžádání aktivované před dalším naplánovaným zálohováním (říká), 8:00 8:00 dop.

## <a name="next-steps"></a>Další kroky

[Přečtěte si, jak obnovit soubory](backup-azure-restore-windows-server.md).
