---
title: Kurz – zálohování Windows serveru do Azure
description: Tento kurz podrobně popisuje zálohování místních Windows Serverů do trezoru služby Recovery Services.
ms.topic: tutorial
ms.date: 08/22/2018
ms.custom: mvc
ms.openlocfilehash: d2990b5950cf8812367c3a59c6cace39e4085e2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88261901"
---
# <a name="back-up-windows-server-to-azure"></a>Zálohování Windows Serveru do Azure

Pomocí služby Azure Backup můžete chránit svůj Windows Server před poškozením, útoky a haváriemi. Azure Backup poskytuje odlehčený nástroj označovaný jako agent služby Microsoft Azure Recovery Services (MARS). Agent MARS se instaluje na Windows Server za účelem ochrany souborů, složek a informací o konfiguraci serveru prostřednictvím stavu systému Windows Server. Tento kurz vysvětluje, jak pomocí agenta MARS zálohovat Windows Server do Azure. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
>
> * Stažení a nastavení agenta MARS
> * Konfigurace časů zálohování a plánu uchovávání záloh serveru
> * Provedení zálohování na vyžádání

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com>.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Před zálohováním Windows Serveru musíte vytvořit místo pro uložení záloh neboli bodů obnovení. [Trezor služby Recovery Services](backup-azure-recovery-services-vault-overview.md) je kontejner v Azure, který uchovává zálohy z Windows Serveru. Pomocí následujícího postupu vytvořte na webu Azure Portal trezor služby Recovery Services.

1. V nabídce vlevo vyberte **Všechny služby** a v seznamu služeb zadejte **Recovery Services**. Vyberte **úložiště Recovery Services**.

   ![Otevřít Recovery Services trezor](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. V nabídce **Recovery Services trezory** vyberte **Přidat**.

   ![Zadání informací pro trezor](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. V nabídce **Trezor služby Recovery Services**:

    * Jako **Název** zadejte *myRecoveryServicesVault*.
    * V části **Předplatné** se zobrazí ID aktuálního předplatného.
    * V části **Skupina prostředků** vyberte **Použít existující** a zvolte *myResourceGroup*. Pokud *myResourceGroup* neexistuje, vyberte **Vytvořit novou** a zadejte *myResourceGroup*.
    * V rozevírací nabídce **Umístění** zvolte *Západní Evropa*.
    * Vyberte **vytvořit** a vytvořte tak trezor Recovery Services.

Když je trezor vytvořený, zobrazí se v seznamu trezorů Služeb zotavení.

## <a name="download-recovery-services-agent"></a>Stažení agenta služby Recovery Services

Agent služby Microsoft Azure Recovery Services (MARS) vytvoří přidružení mezi Windows Serverem a vaším trezorem služby Recovery Services. Následující postup vysvětluje stažení agenta na server.

1. V seznamu trezorů služby Recovery Services výběrem trezoru **myRecoveryServicesVault** otevřete jeho řídicí panel.

   ![Vyberte trezor a otevřete tak řídicí panel.](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. V nabídce řídicího panelu trezoru vyberte **zálohovat**.

3. V nabídce **Cíl zálohování**:

   * pro **kde je spuštěná vaše úloha?** vyberte **místní** .
   * V části **Co chcete zálohovat?** vyberte **Soubory a složky** a **Stav systému**.

   ![Nabídka cíl zálohování](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. Vyberte **připravit infrastrukturu** a otevřete nabídku **připravit infrastrukturu** .

5. V nabídce **připravit infrastrukturu** vyberte **Stáhnout agenta pro Windows Server nebo klienta windows** a stáhněte *MARSAgentInstaller.exe*.

    ![Stáhnout agenta pro Windows Server nebo klienta Windows](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    Instalační program otevřete samostatné okno prohlížeče a stáhne soubor **MARSAgentInstaller.exe**.

6. Před spuštěním staženého souboru vyberte v nabídce připravit infrastrukturu možnost **Stáhnout** a uložit soubor s **přihlašovacími údaji trezoru** . Přihlašovací údaje trezoru se vyžadují k propojení agenta MARS s trezorem služby Recovery Services.

    ![Stažení přihlašovacích údajů trezoru](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)

## <a name="install-and-register-the-agent"></a>Instalace a registrace agenta

1. Vyhledejte a dvakrát klikněte na stažený soubor **MARSagentinstaller.exe**.
2. Zobrazí se **Průvodce instalací agenta služby Microsoft Azure Recovery Services**. Při procházení průvodce zadejte následující informace po výzvě a vyberte **Registrovat**.
   * Umístění instalace a složky mezipaměti.
   * Informace o proxy serveru, pokud pro připojování k internetu používáte proxy server.
   * Vaše uživatelské jméno a heslo, pokud používáte ověřený proxy server.

     ![Průvodce instalací agenta Microsoft Azure Recovery Services](./media/tutorial-backup-windows-server-to-azure/mars-installer.png)

3. Na konci průvodce vyberte **pokračovat k registraci** a zadejte soubor s **přihlašovacími údaji trezoru** , který jste stáhli v předchozím postupu.

4. Po zobrazení výzvy zadejte šifrovací heslo pro šifrování záloh z Windows Serveru. Uložte si přístupové heslo v zabezpečeném umístění, protože Microsoft nemůže obnovit heslo, pokud je ztraceno.

5. Vyberte **Dokončit**.

## <a name="configure-backup-and-retention"></a>Konfigurace zálohování a uchovávání

Pomocí agenta služby Microsoft Azure Recovery Services můžete nakonfigurovat, kdy na Windows Serveru bude docházet k zálohování do Azure. Na serveru, na který jste stáhli agenta, proveďte následující kroky.

1. Otevřete agenta Služeb zotavení Microsoft Azure. Najdete ho vyhledáním **Microsoft Azure Backup** ve svém počítači.

2. V konzole Recovery Services agenta vyberte v **podokně akce**možnost **naplánovat zálohování** .

    ![Naplánovat zálohování](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Kliknutím na tlačítko **Další** přejdete na stránku **Vybrat položky, které chcete zálohovat** .

4. Vyberte **Přidat položky** a v dialogovém okně, které se otevře, vyberte **stav systému** a soubory nebo složky, které chcete zálohovat. Pak vyberte **OK**.

5. Vyberte **Další**.

6. Na stránce **zadání plánu zálohování (stav systému)** zadejte čas nebo týden v případě, kdy je třeba spustit zálohování pro stav systému a vyberte možnost **Další**.

7. Na stránce **Vybrat zásady uchovávání informací (stav systému)** vyberte zásady uchovávání informací pro záložní kopii stavu systému a vyberte **Další**.

8. Podobným způsobem vyberte plán zálohování a zásadu uchovávání pro vybrané soubory a složky.

9. Na stránce **Výběr počátečního typu zálohování** vyberte **automaticky přes síť**a vyberte **Další**.

10. Na stránce **potvrzení** zkontrolujte informace a vyberte **Dokončit**.

11. Až průvodce dokončí vytváření plánu zálohování, vyberte **Zavřít**.

## <a name="perform-an-on-demand-backup"></a>Provedení zálohování na vyžádání

Nastavili jste plán, kdy se spouštějí úlohy zálohování. Nezálohovali jste ale server. Osvědčeným postupem při zotavení po havárii je spustit zálohování na vyžádání, aby se zajistila odolnost dat pro váš server.

1. V konzole agenta Microsoft Azure Recovery Services vyberte **Zálohovat nyní**.

    ![Zálohovat hned](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2. V průvodci **zálohováním teď** vyberte jednu ze **souborů a složek** nebo **stav systému** , který chcete zálohovat, a **pak vyberte další** .
3. Na stránce **Potvrzení** zkontrolujte nastavení, která průvodce **Zálohovat nyní** použije k zálohování vašeho serveru. Pak vyberte **zálohovat**.
4. Kliknutím na **Zavřít** zavřete průvodce. Pokud průvodce zavřete před dokončením procesu zálohování, průvodce zůstane spuštěný na pozadí.
5. Po dokončení prvotní zálohy se v konzole agenta MARS v podokně **Úlohy** zobrazí stav **Úloha byla dokončena**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí webu Azure Portal provedli následující kroky:

> [!div class="checklist"]
>
> * Vytvoření trezoru Služeb zotavení
> * Stažení agenta služby Microsoft Azure Recovery Services
> * Instalace agenta
> * Konfigurace zálohování pro Windows Server
> * Provedení zálohování na vyžádání

Přejděte k dalšímu kurz, kde obnovíte soubory z Azure do Windows Serveru.

> [!div class="nextstepaction"]
> [Obnovení souborů z Azure do Windows Serveru](./tutorial-backup-restore-files-windows-server.md)
