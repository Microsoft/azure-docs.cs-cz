---
title: Zálohování počítačů s Windows s agentem Azure Backup MARS
description: Zálohování počítačů s Windows pomocí agenta Azure Backup Microsoft Recovery Services (MARS).
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: 7a1bd6da68b49481429709c7e4fd37dd5c07ae2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810365"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Zálohování počítačů s Windows s agentem Azure Backup MARS

Tento článek vysvětluje, jak k zálohování počítačů s Windows pomocí [Azure Backup](backup-overview.md) služby a agenta Microsoft Azure Recovery Services (MARS), také označovaný jako agent Azure Backup.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Ověřte požadavky a vytvořte trezor služby Recovery Services.
> * Stažení a nastavení agenta MARS
> * Vytvoření zásady zálohování a plánu.
> * Provedení zálohování ad hoc.

## <a name="about-the-mars-agent"></a>O agenta MARS

Agenta MARS používá Azure Backup k zálohování souborů, složek a stavu systému z místních počítačů a virtuálních počítačů Azure do trezoru služby Recovery Services zálohování v Azure. Agenta můžete spustit následovně:

- Spuštění agenta přímo na místních počítačích Windows tak, aby můžete zálohovat přímo na zálohování trezoru služby Recovery Services v Azure.
- Spuštění virtuálních počítačů Azure agenta pro zálohování konkrétní soubory a složky na virtuálním počítači s Windows (-souběžně s rozšířením zálohování virtuálního počítače Azure).
- Spusťte agenta Microsoft Azure Backup Server (MABS) nebo System Center Data Protection - server Manager (DPM). V tomto scénáři počítače a úlohy zálohování pro MABS/DPM a pak MABS/aplikace DPM zálohovat do trezoru v Azure pomocí agenta MARS.
Co můžete zálohovat, závisí na instalaci agenta.

> [!NOTE]
> Primární metodou pro zálohování virtuálních počítačů Azure je pomocí rozšíření Azure Backup na virtuálním počítači. Zálohuje celý virtuální počítač. Můžete chtít nainstalovat a používat agenta MARS společně s rozšířením, pokud chcete zálohovat konkrétní soubory a složky na virtuálním počítači. [Další informace](backup-architecture.md#architecture-direct-backup-of-azure-vms).

![Kroky procesu zálohování](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Než začnete

- [Zjistěte, jak](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Azure Backup zálohuje počítačů s Windows pomocí agenta MARS.
- [Další informace o](backup-architecture.md#architecture-back-up-to-dpmmabs) zálohování architekturu běžící agenta MARS na sekundárním serveru MABS nebo aplikace DPM.
- [Kontrola](backup-support-matrix-mars-agent.md) co je podporováno a co můžete zálohovat pomocí agenta MARS.
- Ověřte přístup k Internetu na počítačích, které chcete zálohovat.
- Zálohování klienta nebo serveru do Azure, potřebujete účet Azure. Pokud ho nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/) během několika minut.

### <a name="verify-internet-access"></a>Ověřte přístup k Internetu

Pokud váš počítač má omezený přístup k Internetu, ujistěte se, že nastavení brány firewall na počítači nebo proxy serveru povolit, tyto adresy URL a IP adresa:

**Adresy URL**

- www\.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

**IP adresa**

- 20.190.128.0/18
- 40.126.0.0/18


## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Trezor služby Recovery Services uchovává všechny zálohy a body obnovení, které vytvoříte v čase a obsahuje zásady zálohování u zálohovaného počítače. Vytvořte trezor následujícím způsobem:

1. Přihlaste se k [webu Azure Portal](https://portal.azure.com/) pomocí svého předplatného Azure.
2. Do pole hledání zadejte **služby Recovery Services** a klikněte na tlačítko **trezory služby Recovery Services**.

    ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png)

3. Na **trezory služby Recovery Services** nabídky, klikněte na tlačítko **+ přidat**.

    ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. Jako **Název** zadejte popisný název pro identifikaci trezoru.

   - Název musí být jedinečný v rámci předplatného Azure.
   - Může obsahovat 2 až 50 znaků.
   - Musí začínat písmenem a může obsahovat jenom písmena, číslice a pomlčky.

5. Vyberte předplatné Azure, skupinu prostředků a zeměpisné oblasti, ve kterém se vytvoří v trezoru. Zálohovaná data se odesílají do trezoru. Poté klikněte na **Vytvořit**.

    ![Vytvoření trezoru Recovery Services – krok 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   - Může trvat nějakou dobu vytvoření trezoru.
   - Sledujte oznámení o stavu v oblasti pravém horním rohu portálu. Pokud po několika minutách se trezor nezobrazí, klikněte na tlačítko **aktualizovat**.

     ![Kliknutí na tlačítko Obnovit](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Nastavení redundance úložiště

Azure Backup automaticky zpracovává úložiště pro trezor. Je třeba zadat způsob replikace úložiště.

1. V okně **Trezory služby Recovery Services** klikněte na nový trezor. V části **nastavení** klikněte na tlačítko **vlastnosti**.
2. V **vlastnosti**v části **konfigurace zálohování**, klikněte na tlačítko **aktualizace**.

3. Vyberte typ replikace úložiště a klikněte na tlačítko **Uložit**.

      ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

- Doporučujeme vám, pokud používáte Azure jako primární úložiště záloh koncový bod, dál používat výchozí **geograficky redundantní** nastavení.
- Pokud Azure nepoužíváte jako primární koncový bod úložiště záloh, vyberte **Místně redundantní** – snížíte tím náklady na úložiště Azure.
- Další informace o [geograficky](../storage/common/storage-redundancy-grs.md) a [místní](../storage/common/storage-redundancy-lrs.md) redundance.

## <a name="download-the-mars-agent"></a>Stáhnout agenta MARS

Stáhněte agenta MARS pro instalaci na počítačích, které chcete zálohovat.

- Pokud jste již nainstalovali agenta na všechny počítače, ujistěte se, že používáte nejnovější verzi.
- Nejnovější verze je k dispozici na portálu, nebo můžete použít [přímé stažení](https://aka.ms/azurebackup_agent)

1. V trezoru v části **Začínáme**, klikněte na tlačítko **zálohování**.

    ![Otevřete okno cíle zálohování](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. V **ve kterém je spuštěná vaše úloha?** vyberte **On-premises**. Tato možnost by měl vybrat i v případě, že chcete nainstalovat agenta MARS na Virtuálním počítači Azure.
3. V **co chcete zálohovat?** vyberte **souborů a složek** a/nebo **stavu systému**. Nejsou k dispozici řadu dalších možností, ale ty jsou podporovány pouze pokud používáte sekundární záložní server. Klikněte na tlačítko **připravit infrastrukturu**.

      ![Konfigurace souborů a složek](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. Na **připravit infrastrukturu**v části **instalace služby Recovery Services agent**, stáhněte si agenta MARS.

    ![Příprava infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. V místní nabídce stahování klikněte na **Uložit**. Ve výchozím nastavení se soubor **MARSagentinstaller.exe** uloží do složky Stažené soubory.

6. Teď zkontrolujte **nebo pomocí nejnovější Agent Recovery Services již stažení**a pak si stáhnout přihlašovací údaje trezoru.

    ![stažení přihlašovacích údajů trezoru](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Klikněte na **Uložit**. Soubor se stáhne do své složky pro stahování. Nelze otevřít soubor s přihlašovacími údaji trezoru.

## <a name="install-and-register-the-agent"></a>Instalace a registrace agenta

1. Spustit **MARSagentinstaller.exe** souboru na počítačích, které chcete zálohovat.
2. V Průvodci instalací agenta MARS > **nastavení instalace**, zadejte, kam chcete nainstalovat agenta a umístění, které chcete použít pro ukládání do mezipaměti. Pak klikněte na tlačítko **Další**.
   - Azure Backup používá mezipaměť pro ukládání snímků dat před jejich odesláním do Azure.
   - Umístění mezipaměti by měly mít volné místo odpovídající nejméně 5 % velikosti dat, která se bude zálohovat.

     ![Nastavení instalace Průvodce MARS](./media/backup-configure-vault/mars1.png)

2. V **konfiguraci proxy serveru**, určete, jak agenta spuštěného na počítači Windows připojí k Internetu. Pak klikněte na tlačítko **Další**.

   - Pokud používáte vlastní proxy server zadejte nastavení proxy serveru a přihlašovací údaje v případě potřeby.
   - Mějte na paměti, že agent potřebuje přístup k [tyto adresy URL](#verify-internet-access).

     ![Přístup k Internetu pro Průvodce MARS](./media/backup-configure-vault/mars2.png)

3. V **instalace** zkontrolujte kontroly požadavků a klikněte na **nainstalovat**.
4. Po nainstalování agenta klikněte na tlačítko **přejít k registraci**.
5. V **Průvodce registrací serveru** > **trezor identifikace**, procházet a vyberte soubor přihlašovacích údajů, jste si stáhli. Pak klikněte na tlačítko **Další**.

    ![Register – přihlašovací údaje trezoru](./media/backup-configure-vault/register1.png)

6. V **nastavení šifrování**, zadejte přístupové heslo, který se použije k šifrování a dešifrování záloh pro počítač.

    - Uložit šifrovací heslo na bezpečné místo.
    - Pokud ztratíte nebo zapomenete heslo, Microsoft vám nemůže pomoci obnovit zálohovaná data. Uložte soubor na bezpečné místo. Můžete potřebovat k obnovení zálohy.

7. Klikněte na tlačítko **Dokončit**. Agent je nyní nainstalovaný a váš počítač je registrovaný k trezoru. Jste připraveni nakonfigurovat a naplánovat zálohování.

## <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování určují, kdy se mají vytvářet snímky dat k vytvoření bodů obnovení a jak dlouho se má zachovat body obnovení.

- Můžete nakonfigurovat zásady zálohování pomocí agenta MARS.
- Azure Backup nepodporuje zohlednit automaticky letní čas (DST). Může dojít k některé nesrovnalosti mezi skutečný čas a čas plánované zálohování.

Vytvoření zásady takto:

1. Na každém počítači otevřete agenta MARS. Najdete ho vyhledáním **Microsoft Azure Backup** ve svém počítači.
2. V **akce**, klikněte na tlačítko **naplánovat zálohování**.

    ![Naplánování zálohování Windows Serveru](./media/backup-configure-vault/schedule-first-backup.png)

3. V Průvodci plánování zálohování > **Začínáme**, klikněte na tlačítko **Další**.
4. V **výběr položek k zálohování**, klikněte na tlačítko **přidat položky**.
5. V **výběr položek**, vyberte, co chcete zálohovat. Pak klikněte na **OK**.
6. V **výběr položek k zálohování** klikněte na **Další**.
7. V **zadání plánu zálohování** stránky, zadejte, kdy budete chtít denní nebo týdenní zálohování. Pak klikněte na tlačítko **Další**.

    - Vytvoření bodu obnovení se vytvoří při zálohování.
    - Počet bodů obnovení vytvořené ve vašem prostředí je závislá na plán zálohování.

1. Můžete naplánovat denní zálohy, až třikrát za den. Například snímek obrazovky ukazuje dva denní zálohy, jeden půlnoci a jeden na 18: 00.

    ![Denní plán](./media/backup-configure-vault/day-schedule.png)

9. Týdenní zálohy můžete spustit příliš. Na snímku obrazovky vidíte příklad zálohy pořízené každou neděli alternativní & středu v 9:30:00 a 1:00:00.

    ![Týdenní plán](./media/backup-configure-vault/week-schedule.png)

8. Na **výběr zásady uchovávání informací** určete, jak ukládat historické kopie vašich dat,. Pak klikněte na tlačítko **Další**.

   - Nastavení uchování určit, které body obnovení by měla být uložena, a jak dlouho by měla být uložena pro.
   - Například pokud nastavíte denní nastavení uchování, určujete, že v době zadané pro denní uchovávání nejnovější bod obnovení se zachovají pro zadaný počet dnů. Nebo jako jiný příklad můžete zadat měsíční zásady uchovávání informací k označení, že bod obnovení vytvořený 30 každý měsíc by měla být uložena po dobu 12 měsíců.
   - Uchování bodu obnovení denní nebo týdenní se obvykle shoduje se plán zálohování. To znamená, že při zálohování se spustí podle plánu, bodu obnovení vytvořeného záloha uložena po dobu trvání podle denní nebo týdenní zásady uchovávání informací.
   - Jako příklad na následujícím snímku obrazovky:
     - Denní zálohy o půlnoci a 18: 00 uchovávají po dobu sedmi dní.
     - Zálohy pořízené v sobotu o půlnoci a 18: 00 uchovávají po dobu 4 týdnů.
     - Zálohy pořízené v sobotu v poslední týden v měsíci o půlnoci a 18: 00 uchovávají po dobu 12 měsíců. -Zálohy pořízené v sobotu během posledního týdne března uchovávají po dobu 10 let.

   ![Příklad uchování](./media/backup-configure-vault/retention-example.png)

11. V **typu** určete, jak chcete počáteční zálohu vytvořit, přes síť nebo offline. Pak klikněte na tlačítko **Další**.

10. V **potvrzení**, přečtěte si informace a pak klikněte na tlačítko **Dokončit**.
11. Až průvodce dokončí vytváření plánu zálohování, klikněte na **Zavřít**.

### <a name="perform-the-initial-backup-offline"></a>Proveďte počáteční zálohu do offline režimu

Počáteční můžete automaticky spustit zálohování, přes síť nebo offline. Offline seeding pro prvotní záloha je užitečné, pokud máte velké množství dat, který bude vyžadovat velkou šířku pásma sítě pro přenos. Proveďte offline převod následujícím způsobem:

1. Zapisovat data záloh do pracovního umístění.
2. Pomocí nástroje AzureOfflineBackupDiskPrep ke zkopírování dat z pracovního umístění na jeden nebo více disků SATA.
3. Nástroj vytvoří úlohu služby Azure Import. [Další informace](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) o Azure import a export.
4. Odešlete disky SATA do datacentra Azure.
5. V datovém centru diskových dat zkopírována do účtu služby Azure storage.
6. Azure Backup kopíruje data z účtu úložiště do trezoru a přírůstkové zálohování.

[Další informace](backup-azure-backup-import-export.md) o offline předvyplnění.

### <a name="enable-network-throttling"></a>Povolení omezení využití sítě

Můžete řídit využití šířky pásma sítě pomocí agenta MARS povolením, omezení šířky pásma sítě. Omezení šířky pásma je užitečné, pokud potřebujete zálohovat data během pracovní doby, ale chcete mít pod kontrolou, jaký poměr šířky pásma se používá pro zálohování a obnovení aktivity.

- Omezení použití Azure Backup sítě [Quality of Service (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) místního operačního systému.
- Omezení pro zálohování sítě je k dispozici ve Windows serveru 2008 R2 a novější a Windows 7 a vyšší. Operační systémy by měly běžet nejnovější aktualizace service Pack.

Povolte omezení sítě následujícím způsobem:

1. V agenta MARS, klikněte na tlačítko **změnit vlastnosti**.
2. Na **omezování** kartě **Povolit omezování šířky pásma Internetu u operací zálohování**.

    ![Omezení využití sítě](./media/backup-configure-vault/throttling-dialog.png)
3. Určení povolených šířky pásma během práce a mimo pracovní dobu. Šířka pásma hodnoty pokračovali 512 kB/s a přejít až 1,023 MB/s. Pak klikněte na **OK**.

## <a name="run-an-ad-hoc-backup"></a>Spustit zálohování ad hoc

1. V agenta MARS, klikněte na tlačítko **zálohovat nyní**. To zahajuje počáteční replikaci přes síť.

    ![Zálohovat nyní ve Windows Serveru](./media/backup-configure-vault/backup-now.png)

2. V **potvrzení**, zkontrolujte nastavení a klikněte na tlačítko **zálohování**.
3. Průvodce zavřete kliknutím na **Zavřít**. Pokud to uděláte před dokončením zálohování, Průvodce zůstane spuštěný na pozadí.

Po dokončení prvotní zálohy se v konzole Zálohování zobrazí stav **Úloha byla dokončena**.

## <a name="next-steps"></a>Další postup

[Zjistěte, jak](backup-azure-restore-windows-server.md) obnovit soubory.
