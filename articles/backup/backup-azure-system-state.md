---
title: Zálohování stavu systému Windows do Azure
description: Naučte se zálohovat stav systému počítačů s Windows serverem do Azure.
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 10d37bbafab6aa96986a5d7af88a509d4c8eac25
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89376486"
---
# <a name="back-up-windows-system-state-to-azure"></a>Zálohování stavu systému Windows do Azure

Tento článek vysvětluje, jak zálohovat stav systému Windows Server do Azure. Slouží k tomu, aby vás provedl základy.

Chcete-li se dozvědět více o Azure Backup, přečtěte si tento [přehled](backup-overview.md).

Pokud předplatné Azure nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/), který vám umožní přístup ke službám Azure.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy-for-the-vault"></a>Nastavení redundance úložiště pro trezor

Při vytváření trezoru služby Recovery Services se ujistěte, že je redundance úložiště nakonfigurována požadovaným způsobem.

1. V podokně **Recovery Services trezory** vyberte nový trezor.

    ![Výběr nového trezoru ze seznamu trezorů služby Recovery Services](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Po výběru trezoru se podokno **Recovery Services trezoru** zúží a podokno nastavení (*s názvem trezoru v horní části*) a otevřené podokno Podrobnosti trezoru.

    ![Zobrazení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. V podokně nastavení nového trezoru pomocí svislého snímku přejděte dolů k části Správa a vyberte **infrastruktura zálohování**.
    Otevře se podokno infrastruktura zálohování.
3. V podokně infrastruktura zálohování vyberte **Konfigurace zálohování** a otevřete podokno **Konfigurace zálohování** .

    ![Nastavení konfigurace úložiště pro nový trezor](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Zvolte vhodnou možnost replikace pro svůj trezor.

    ![Volby konfigurace úložiště](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště. Pokud používáte Azure jako primární koncový bod úložiště záloh, pokračujte v používání **geograficky redundantního** úložiště. Pokud Azure nepoužíváte jako primární koncový bod úložiště záloh, vyberte **Místně redundantní** – snížíte tím náklady na úložiště Azure. Další informace o možnostech [geograficky redundantního](../storage/common/storage-redundancy.md) a [místně redundantního](../storage/common/storage-redundancy.md) úložiště najdete v tomto [přehledu redundance úložiště](../storage/common/storage-redundancy.md).

Teď, když jste vytvořili trezor, nakonfigurujte ho pro zálohování stavu systému Windows.

## <a name="configure-the-vault"></a>Konfigurace trezoru

1. V podokně Recovery Services trezoru (pro trezor, který jste právě vytvořili), v části Začínáme vyberte **zálohování**a pak v podokně **Začínáme s zálohováním** vyberte **cíl zálohování**.

    ![Otevřít nastavení zálohování](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Otevře se podokno **cíl zálohování** .

    ![Otevřít podokno cíle zálohování](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. V rozevírací nabídce **Kde běží vaše úlohy?** vyberte **Místní**.

    Zvolíte možnost **místní** , protože počítač se systémem Windows Server nebo Windows je fyzický počítač, který není v Azure.

3. V nabídce **co chcete zálohovat?** vyberte možnost **stav systému**a vyberte **OK**.

    ![Konfigurace souborů a složek](./media/backup-azure-system-state/backup-goal-system-state.png)

    Po výběru **OK**se vedle možnosti **cíl zálohování**zobrazí značka zaškrtnutí a otevře se podokno **Příprava infrastruktury** .

    ![Cíl zálohování je nakonfigurovaný, teď se připraví infrastruktura](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. V podokně **připravit infrastrukturu** vyberte **Stáhnout agenta pro Windows Server nebo klienta Windows**.

    ![Příprava infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Pokud používáte Windows Server Essential, zvolte stažení agenta pro Windows Server Essential. Místní nabídka zobrazí výzvu ke spuštění nebo uložení MARSAgentInstaller.exe.

    ![Dialogové okno MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. V místní nabídce stáhnout vyberte **Uložit**.

    Ve výchozím nastavení se soubor **MARSagentinstaller.exe** uloží do složky Stažené soubory. Po dokončení instalačního programu se zobrazí automaticky otevírané okno s dotazem, jestli chcete spustit instalační program nebo otevřít složku.

    ![Instalační program MARS je dokončený.](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Agenta ještě nemusíte instalovat. Agenta můžete nainstalovat po stažení přihlašovacích údajů trezoru.

6. V podokně **připravit infrastrukturu** vyberte **Stáhnout**.

    ![stažení přihlašovacích údajů trezoru](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    Přihlašovací údaje trezoru se stáhnou do složky **stažené soubory** . Po dokončení stahování přihlašovacích údajů trezoru se zobrazí automaticky otevírané okno s dotazem, jestli chcete přihlašovací údaje otevřít nebo Uložit. Vyberte **Uložit**. Pokud omylem vyberete **otevřít**, nechte dialogové okno, které se pokusí otevřít přihlašovací údaje trezoru, selže. Nebudete moct otevřít přihlašovací údaje trezoru. Pokračujte k dalšímu kroku. Přihlašovací údaje trezoru jsou ve složce **stažené soubory** .

    ![dokončené stahování přihlašovacích údajů trezoru](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > Přihlašovací údaje trezoru musí být uloženy pouze do umístění, které je místní pro systém Windows Server, na kterém chcete agenta použít.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Instalace a registrace agenta

> [!NOTE]
> Povolení zálohování prostřednictvím Azure Portal není k dispozici. Pro zálohování stavu systému Windows Server použijte agenta Microsoft Azure Recovery Services.
>

1. Ve složce Stažené soubory (nebo ve složce, kterou jste vybrali pro stahování) vyhledejte soubor **MARSagentinstaller.exe** a dvakrát na něj klikněte.

    Instalační program v průběhu extrakce, instalace a registrace agenta Recovery Services zobrazuje řadu zpráv.

    ![spuštění přihlašovacích údajů instalačního programu agenta Recovery Services](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Dokončete Průvodce instalací agenta Služeb zotavení Microsoft Azure. K dokončení průvodce budete muset:

   * Vybrat umístění instalace a složky mezipaměti.
   * Zadat informace o serveru proxy, pokud pro připojování k internetu používáte server proxy.
   * Zadat svoje uživatelské jméno a heslo, pokud používáte ověřený server proxy.
   * Zadat stažené přihlašovací údaje trezoru.
   * Uložit šifrovací heslo na bezpečné místo.

     > [!NOTE]
     > Pokud heslo ztratíte nebo zapomenete, Microsoft nemůže získat zálohu zálohovaných dat. Uložte soubor na bezpečné místo. Je nutné obnovit zálohu.
     >
     >

Agent je nyní nainstalovaný a váš počítač je registrovaný k trezoru. Jste připraveni nakonfigurovat a naplánovat zálohování.

## <a name="back-up-windows-server-system-state"></a>Zálohování stavu systému Windows Server

Počáteční záloha zahrnuje dvě úlohy:

* Naplánování zálohování
* První zálohování stavu systému

K dokončení prvotního zálohování použijte agenta Microsoft Azure Recovery Services.

> [!NOTE]
> Stav systému systému Windows Server 2008 R2 můžete zálohovat pomocí systému Windows Server 2016. Zálohování stavu systému není podporováno u klientských SKU klienta. Stav systému není zobrazen jako možnost pro klienty se systémem Windows nebo pro počítače s Windows Serverem 2008 SP2.
>
>

### <a name="to-schedule-the-backup-job"></a>Naplánování úlohy zálohování

1. Otevřete agenta Služeb zotavení Microsoft Azure. Najdete ho vyhledáním **Microsoft Azure Backup** ve svém počítači.

    ![Spuštění agenta Služeb zotavení Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. V Recovery Services agenta vyberte **naplánovat zálohování**.

    ![Naplánování zálohování Windows Serveru](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Na stránce **Začínáme** v Průvodci plánováním zálohování vyberte **Další**.

4. Na stránce **Vyberte položky, které chcete zálohovat** vyberte možnost **Přidat položky**.

5. Vyberte možnost **stav systému** a pak vyberte **OK**.

6. Vyberte **Další**.

7. Na následujících stránkách vyberte požadovanou četnost zálohování a zásady uchovávání informací pro zálohy stavu systému.

8. Na stránce potvrzení zkontrolujte informace a pak vyberte **Dokončit**.

9. Až průvodce dokončí vytváření plánu zálohování, vyberte **Zavřít**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Postup při prvním zálohování stavu systému Windows Server

1. Ujistěte se, že neexistují žádné nedokončené aktualizace pro Windows Server, které vyžadují restart.

2. V agentovi Recovery Services vyberte **Zálohovat nyní** , abyste mohli dokončit počáteční osazení přes síť.

    ![Zálohování Windows serveru hned teď](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Na obrazovce **Vybrat zálohovanou položku** vyberte **stav systému** , který se zobrazí, a vyberte **Další**.

4. Na stránce Potvrzení zkontrolujte nastavení, které Průvodce Zálohování nyní použije k zálohování počítače. Pak vyberte **zálohovat**.

5. Kliknutím na **Zavřít** zavřete průvodce. Pokud průvodce zavřete před dokončením procesu zálohování, průvodce zůstane spuštěný na pozadí.
    > [!NOTE]
    > Agent MARS se spustí `SFC /verifyonly` jako součást předkontroly před všemi zálohováními stavu systému. K tomu je potřeba zajistit, aby byly soubory zálohované jako součást stavu systému správné verze odpovídající verzi Windows. Další informace o nástroji pro kontrolu systémových souborů (SFC) najdete v [tomto článku](/windows-server/administration/windows-commands/sfc).
    >

Po dokončení prvotní zálohy se v konzole Zálohování zobrazí stav **Úloha byla dokončena**.

  ![Dokončení IR](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Máte otázky?

Pokud máte nějaké dotazy, [pošlete nám svůj názor](https://feedback.azure.com/forums/258995-azure-backup).

## <a name="next-steps"></a>Další kroky

* Zdroj dalších informací o [zálohování počítačů se systémem Windows](backup-windows-with-mars-agent.md).
* Teď, když jste zálohovali stav systému Windows Server, můžete [Spravovat trezory a servery](backup-azure-manage-windows-server.md).
* Potřebujete-li obnovit zálohu, použijte tento článek k [obnovení souborů na počítač se systémem Windows](backup-azure-restore-windows-server.md).
