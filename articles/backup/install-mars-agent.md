---
title: Instalace agenta Microsoft Azure Recovery Services (MARS)
description: Naučte se, jak nainstalovat agenta Microsoft Azure Recovery Services (MARS) pro zálohování počítačů s Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: acf38fdf338fcdd0cd7902d4295f0f03310543a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98986866"
---
# <a name="install-the-azure-backup-mars-agent"></a>Instalace agenta Azure Backup MARS

Tento článek vysvětluje, jak nainstalovat agenta Microsoft Azure Recovery Services (MARS). MARS se také označuje jako agent Azure Backup.

## <a name="about-the-mars-agent"></a>O agentovi MARS

Azure Backup používá agenta MARS k zálohování souborů, složek a stavu systému z místních počítačů a virtuálních počítačů Azure. Tyto zálohy se ukládají v úložišti Recovery Services v Azure. Můžete spustit agenta:

* Přímo na místních počítačích s Windows. Tyto počítače můžou zálohovat přímo do trezoru Recovery Services v Azure.
* Na virtuálních počítačích Azure, které používají Windows souběžně s rozšířením zálohování virtuálních počítačů Azure. Agent zálohuje na virtuálním počítači konkrétní soubory a složky.
* Na instanci serveru Microsoft Azure Backup (MABS) nebo na serveru System Center Data Protection Manager (DPM). V tomto scénáři se počítače a úlohy zálohují do MABS nebo Data Protection Manager. Pak MABS nebo Data Protection Manager používá agenta MARS k zálohování do trezoru v Azure.

Data, která jsou k dispozici pro zálohování, závisí na tom, kde je agent nainstalován.

> [!NOTE]
> Obecně platí, že zálohujete virtuální počítač Azure pomocí rozšíření Azure Backup na virtuálním počítači. Tato metoda zálohuje celý virtuální počítač. Pokud chcete zálohovat konkrétní soubory a složky na virtuálním počítači, nainstalujte a používejte agenta MARS společně s rozšířením. Další informace najdete v tématu [Architektura integrované zálohy virtuálního počítače Azure](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Kroky procesu zálohování](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Než začnete

* Přečtěte si, jak [Azure Backup používá agenta Mars k zálohování počítačů s Windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Seznamte se s [architekturou zálohování](backup-architecture.md#architecture-back-up-to-dpmmabs) , která spouští agenta Mars na sekundárním serveru MABS nebo data Protection Manager.
* Přečtěte si [, co je podporováno a co můžete zálohovat](backup-support-matrix-mars-agent.md) pomocí agenta Mars.
* Pokud potřebujete zálohovat server nebo klienta do Azure, ujistěte se, že máte účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit nějaký [bezplatný](https://azure.microsoft.com/free/) .
* Ověřte přístup k Internetu na počítačích, které chcete zálohovat.
* Ujistěte se, že uživatel, který provádí instalaci a konfiguraci agenta MARS, má oprávnění místního správce na serveru, který chcete chránit.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Úprava replikace úložiště

Ve výchozím nastavení trezory používají [geograficky redundantní úložiště (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage).

* Pokud je trezor vaším primárním zálohovacím mechanismem, doporučujeme použít GRS.
* Pomocí [místně redundantního úložiště (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) můžete snížit náklady na službu Azure Storage.

Postup úpravy typu replikace úložiště:

1. V novém trezoru vyberte v části **Nastavení** možnost **vlastnosti** .

1. Na stránce **vlastnosti** v části **Konfigurace zálohování** vyberte **aktualizovat**.

1. Vyberte typ replikace úložiště a vyberte **Uložit**.

    ![Aktualizovat konfiguraci zálohování](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Po nastavení trezoru a obsahujícího zálohované položky nemůžete upravit typ replikace úložiště. Pokud to chcete provést, je třeba znovu vytvořit trezor.
>

### <a name="verify-internet-access"></a>Ověřit přístup k Internetu

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

### <a name="use-azure-expressroute"></a>Použití Azure ExpressRoute

Data můžete zálohovat přes Azure ExpressRoute pomocí veřejného partnerského vztahu (dostupného pro staré okruhy) a partnerského vztahu Microsoftu. Zálohování přes soukromý partnerský vztah se nepodporuje.

Pokud chcete použít veřejný partnerský vztah, nejdřív zajistěte přístup k následujícím doménám a adresám:

* `http://www.msftncsi.com/ncsi.txt`
* `http://www.msftconnecttest.com/connecttest.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`
* IP adresy
  * 20.190.128.0/18
  * 40.126.0.0/18

Chcete-li použít partnerský vztah Microsoftu, vyberte následující služby, oblasti a příslušné hodnoty komunity:

* Azure Active Directory (12076:5060)
* Oblast Azure, podle umístění vašeho trezoru Recovery Services
* Azure Storage podle umístění vašeho trezoru Recovery Services

Další informace najdete v tématu [požadavky na směrování ExpressRoute](../expressroute/expressroute-routing.md).

> [!NOTE]
> Veřejné partnerské vztahy se pro nové okruhy zastaraly.

Všechny předchozí adresy URL a IP adresy používají protokol HTTPS na portu 443.

### <a name="private-endpoints"></a>Privátní koncové body

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Stáhnout agenta MARS

Stáhněte agenta MARS, abyste ho mohli nainstalovat na počítače, které chcete zálohovat.

Pokud jste už agenta nainstalovali na všechny počítače, ujistěte se, že používáte nejnovější verzi agenta. Na portálu můžete najít nejnovější verzi nebo přejít přímo ke [stažení](https://aka.ms/azurebackup_agent).

1. V trezoru v části **Začínáme** vyberte **zálohovat**.

    ![Otevřít cíl zálohování](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. V části **kde je spuštěná vaše úloha?** vyberte **místní**. Tuto možnost vyberte, i když chcete agenta MARS nainstalovat na virtuální počítač Azure.
1. V části **co chcete zálohovat?** vyberte **soubory a složky**. Můžete také vybrat možnost **stav systému**. K dispozici je řada dalších možností, ale tyto možnosti jsou podporovány pouze v případě, že používáte sekundární záložní server. Vyberte **připravit infrastrukturu**.

    ![Konfigurace souborů a složek](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Pro **připravit infrastrukturu** si v části **instalovat Recovery Services agenta** Stáhněte agenta Mars.

    ![Příprava infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. V nabídce stáhnout vyberte **Uložit**. Ve výchozím nastavení se soubor *MARSagentinstaller.exe* uloží do složky Stažené soubory.

1. Vyberte možnost **už si stáhněte nebo použijte nejnovějšího agenta Recovery Services** a pak stáhněte přihlašovací údaje trezoru.

    ![Stažení přihlašovacích údajů trezoru](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Vyberte **Uložit**. Soubor se stáhne do složky Stažené soubory. Nejde otevřít soubor s přihlašovacími údaji trezoru.

## <a name="install-and-register-the-agent"></a>Instalace a registrace agenta

1. Spusťte *MARSagentinstaller.exe* soubor na počítačích, které chcete zálohovat.
1. V Průvodci instalací agenta MARS vyberte **nastavení instalace**. Vyberte, kam chcete nainstalovat agenta, a vyberte umístění mezipaměti. Pak vyberte **Další**.
   * Azure Backup používá mezipaměť k ukládání snímků dat před jejich odesláním do Azure.
   * Umístění mezipaměti by mělo mít volné místo, které se rovná alespoň 5 procent velikosti dat, která budete zálohovat.

    ![V Průvodci instalací agenta MARS vyberte nastavení instalace.](./media/backup-configure-vault/mars1.png)

1. V případě **konfigurace proxy** určete, jak se agent, který běží na počítači s Windows, připojí k Internetu. Pak vyberte **Další**.

   * Pokud používáte vlastní proxy server, zadejte všechna potřebná nastavení proxy serveru a přihlašovací údaje.
   * Mějte na paměti, že agent potřebuje přístup k [určitým adresám URL](#before-you-start).

    ![Nastavení přístupu k Internetu v průvodci MARS](./media/backup-configure-vault/mars2.png)

1. Pro **instalaci** Zkontrolujte požadavky a vyberte **nainstalovat**.
1. Po instalaci agenta vyberte **pokračovat k registraci**.
1. V části Identifikace trezoru **Průvodce registrací serveru**  >  vyhledejte a vyberte soubor s přihlašovacími údaji, který jste stáhli. Pak vyberte **Další**.

    ![Přidání přihlašovacích údajů trezoru pomocí Průvodce registrací serveru](./media/backup-configure-vault/register1.png)

1. Na stránce **nastavení šifrování** zadejte heslo, které se použije k šifrování a dešifrování záloh pro daný počítač. Další informace o povolených znacích hesla [najdete tady](backup-azure-file-folder-backup-faq.md#what-characters-are-allowed-for-the-passphrase) .

    * Uložte heslo do zabezpečeného umístění. Budete ho potřebovat k obnovení zálohy.
    * Pokud heslo ztratíte nebo zapomenete, Microsoft vám nemůžete obnovit data záloh.

1. Vyberte **Dokončit**. Agent je nyní nainstalován a počítač je zaregistrován do trezoru. Jste připraveni nakonfigurovat a naplánovat zálohování.

## <a name="next-steps"></a>Další kroky

Informace o [zálohování počítačů s Windows pomocí agenta Azure Backup Mars](backup-windows-with-mars-agent.md)
