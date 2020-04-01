---
title: Instalace agenta služby Microsoft Azure Recovery Services (MARS)
description: Zjistěte, jak nainstalovat agenta služby Microsoft Azure Recovery Services (MARS) pro zálohování počítačů s Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: d3932b66dbc41ff2631e2cccbe716c0877a509d3
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422930"
---
# <a name="install-the-azure-backup-mars-agent"></a>Instalace agenta Azure Backup MARS

Tento článek vysvětluje, jak nainstalovat agenta služby Microsoft Azure Recovery Services (MARS). MARS se také označuje jako agent azure backup.

## <a name="about-the-mars-agent"></a>O agentovi MARS

Azure Backup používá agenta MARS k zálohování souborů, složek a stavu systému z místních počítačů a virtuálních počítačů Azure. Tyto zálohy jsou uloženy v trezoru služby Recovery Services v Azure. Agenta můžete spustit:

* Přímo na místních počítačích s Windows. Tyto počítače můžou zálohovat přímo do trezoru služby Recovery Services v Azure.
* Na virtuálních počítačích Azure, které běží windows vedle sebe s rozšířením zálohování virtuálních virtuálních počítače Azure. Agent zálohovat konkrétní soubory a složky na virtuálním počítači.
* Na instanci Microsoft Azure Backup Server (MABS) nebo na serveru System Center Data Protection Manager (DPM). V tomto scénáři počítače a úlohy zálohovat MABS nebo Správce ochrany dat. Pak MABS nebo Správce ochrany dat používá agenta MARS k zálohování do trezoru v Azure.

Data, která jsou k dispozici pro zálohování závisí na tom, kde je agent nainstalován.

> [!NOTE]
> Obecně zálohujete virtuální počítač Azure pomocí rozšíření Azure Backup na virtuálním počítači. Tato metoda zálohuje celý virtuální hod. Pokud chcete zálohovat konkrétní soubory a složky na virtuálním počítači, nainstalujte a použijte agenta MARS vedle rozšíření. Další informace najdete [v tématu Architektura integrované zálohy virtuálních počítačích Azure](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Kroky procesu zálohování](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Než začnete

* Zjistěte, jak [Azure Backup používá agenta MARS k zálohování počítačů s Windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Informace o [záložní architektuře,](backup-architecture.md#architecture-back-up-to-dpmmabs) která spouští agenta MARS na sekundárním serveru MABS nebo Data Protection Manager.
* [Zkontrolujte, co je podporováno a co můžete zálohovat](backup-support-matrix-mars-agent.md) agentem MARS.
* Ujistěte se, že máte účet Azure, pokud potřebujete zálohovat server nebo klienta do Azure. Pokud nemáte účet, můžete si vytvořit [bezplatný](https://azure.microsoft.com/free/) během několika minut.
* Ověřte přístup k internetu na počítačích, které chcete zálohovat.
* Ujistěte se, že uživatel provádějící instalaci a konfiguraci agenta MARS má oprávnění místního správce na serveru, který má být chráněn.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Úprava replikace úložiště

Ve výchozím nastavení používají úložiště [geograficky redundantní úložiště (GRS).](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)

* Pokud je trezor primárním mechanismem zálohování, doporučujeme použít grs.
* Místně [redundantní úložiště (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) můžete použít ke snížení nákladů na úložiště Azure.

Změna typu replikace úložiště:

1. V novém trezoru vyberte **Vlastnosti** v části **Nastavení.**

1. Na stránce **Vlastnosti** vyberte v části **Konfigurace zálohování** **možnost Aktualizovat**.

1. Vyberte typ replikace úložiště a vyberte **Uložit**.

    ![Aktualizovat konfiguraci zálohování](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Po nastavení úložiště a obsahujícím položky zálohy nelze změnit typ replikace úložiště. Chcete-li to provést, je třeba úložiště znovu vytvořit.
>

### <a name="verify-internet-access"></a>Ověření přístupu k internetu

Pokud má váš počítač omezený přístup k internetu, ujistěte se, že nastavení brány firewall v počítači nebo proxy serveru povoluje následující adresy URL a ADRESY IP:

* URL – adresy
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* IP adresy
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Použití Azure ExpressRoute

Svá data můžete zálohovat přes Azure ExpressRoute pomocí veřejného partnerského vztahu (k dispozici pro staré okruhy) a partnerského vztahu Microsoftu. Zálohování přes soukromý partnerský vztah není podporováno.

Chcete-li použít veřejný partnerský vztah, nejprve zajistěte přístup k následujícím doménám a adresám:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Chcete-li používat partnerský vztah Microsoftu, vyberte následující služby, oblasti a příslušné hodnoty komunity:

* Služba Azure Active Directory (12076:5060)
* Oblast Azure podle umístění trezoru služby Recovery Services
* Azure Storage podle umístění trezoru služby Recovery Services

Další informace naleznete v tématu [Požadavky na směrování ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

> [!NOTE]
> Veřejný partnerský vztah je pro nové okruhy zastaralé.

Všechny předchozí adresy URL a adresy IP používají protokol HTTPS na portu 443.

### <a name="private-endpoints"></a>Soukromé koncové body

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Stáhněte si agenta MARS

Stáhněte si agenta MARS, abyste ho mohli nainstalovat do počítačů, které chcete zálohovat.

Pokud jste agenta již nainstalovali do všech počítačů, ujistěte se, že používáte nejnovější verzi agenta. Najděte nejnovější verzi na portálu nebo přejděte přímo ke [stažení](https://aka.ms/azurebackup_agent).

1. V trezoru včásti **Začínáme**vyberte **Zálohovat**.

    ![Otevření cíle zálohování](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. V části **Kde běží vaše úloha?**, vyberte **Místní**. Tuto možnost vyberte i v případě, že chcete nainstalovat agenta MARS na virtuální počítač Azure.
1. V části **Co chcete zálohovat?**, vyberte **Soubory a složky**. Můžete také vybrat **stav systému**. Mnoho dalších možností je k dispozici, ale tyto možnosti jsou podporovány pouze v případě, že používáte sekundární záložní server. Vyberte **možnost Připravit infrastrukturu**.

    ![Konfigurace souborů a složek](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. V **části Install** **Recovery Services**si stáhněte agenta MARS .

    ![Příprava infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. V nabídce stáhnout vyberte **Uložit**. Ve výchozím nastavení se soubor *MARSagentinstaller.exe* uloží do složky Stažené soubory.

1. Vyberte **Možnost Již stáhnout nebo použít nejnovějšího agenta služby Recovery Services**a potom stáhněte přihlašovací údaje trezoru.

    ![Stažení přihlašovacích údajů trezoru](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Vyberte **Uložit**. Soubor se stáhne do složky Soubory ke stažení. Soubor přihlašovacích údajů trezoru nelze otevřít.

## <a name="install-and-register-the-agent"></a>Instalace a registrace agenta

1. Spusťte soubor *MARSagentinstaller.exe* na počítačích, které chcete zálohovat.
1. V Průvodci instalací agenta MARS vyberte **nastavení instalace**. Zde zvolte, kam chcete agenta nainstalovat, a zvolte umístění mezipaměti. Pak vyberte **Další**.
   * Azure Backup používá mezipaměť k ukládání datových snímků před jejich odesláním do Azure.
   * Umístění mezipaměti by mělo mít volné místo rovnající se alespoň 5 procentům velikosti dat, která budete zálohovat.

    ![Zvolte nastavení instalace v Průvodci instalací agenta MARS.](./media/backup-configure-vault/mars1.png)

1. V **části Konfigurace serveru proxy**určete, jak se agent spuštěný v počítači se systémem Windows připojí k Internetu. Pak vyberte **Další**.

   * Pokud používáte vlastní proxy server, zadejte všechna potřebná nastavení proxy serveru a pověření.
   * Nezapomeňte, že agent potřebuje přístup ke [konkrétním adresám URL](#before-you-start).

    ![Nastavení přístupu k internetu v průvodci MARS](./media/backup-configure-vault/mars2.png)

1. V **části Instalace**zkontrolujte požadavky a vyberte možnost **Instalovat**.
1. Po instalaci agenta vyberte **možnost Přejít k registraci**.
1. V části Registrace identifikace **trezoru průvodce** > **serverem**vyhledejte a vyberte soubor pověření, který jste stáhli. Pak vyberte **Další**.

    ![Přidání přihlašovacích údajů trezoru pomocí Průvodce registrovat server](./media/backup-configure-vault/register1.png)

1. Na stránce **Nastavení šifrování** zadejte přístupové heslo, které bude použito k šifrování a dešifrování záloh pro daný počítač.

    * Uložte přístupové heslo na bezpečném místě. Potřebujete ji k obnovení zálohy.
    * Pokud heslo ztratíte nebo zapomenete, společnost Microsoft vám nemůže pomoci obnovit záložní data.

1. Vyberte **Finish** (Dokončit). Agent je nyní nainstalován a váš počítač je zaregistrován v trezoru. Jste připraveni nakonfigurovat a naplánovat zálohování.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [zálohovat počítače s Windows pomocí agenta Azure Backup MARS](backup-windows-with-mars-agent.md)
