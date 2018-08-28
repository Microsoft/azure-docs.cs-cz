---
title: Zálohování Windows Serveru do Azure
description: Tento kurz podrobně popisuje zálohování místních Windows Serverů do trezoru služby Recovery Services.
services: backup
author: saurabhsensharma
manager: shivamg
keywords: windows server back up; back up windows server; back up and disaster recovery
ms.service: backup
ms.topic: tutorial
ms.date: 8/22/2018
ms.author: saurse
ms.custom: mvc
ms.openlocfilehash: 9bf4c25b416edf86d29c27bcb19901bf43073bb4
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42616185"
---
# <a name="back-up-windows-server-to-azure"></a>Zálohování Windows Serveru do Azure


Pomocí služby Azure Backup můžete chránit svůj Windows Server před poškozením, útoky a haváriemi. Azure Backup poskytuje odlehčený nástroj označovaný jako agent služby Microsoft Azure Recovery Services (MARS). Agent MARS se instaluje na Windows Server za účelem ochrany souborů, složek a informací o konfiguraci serveru prostřednictvím stavu systému Windows Server. Tento kurz vysvětluje, jak pomocí agenta MARS zálohovat Windows Server do Azure. V tomto kurzu se naučíte: 


> [!div class="checklist"]
> * Stažení a nastavení agenta MARS
> * Konfigurace časů zálohování a plánu uchovávání záloh serveru
> * Provedení zálohování ad hoc


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Před zálohováním Windows Serveru musíte vytvořit místo pro uložení záloh neboli bodů obnovení. [Trezor služby Recovery Services](backup-azure-recovery-services-vault-overview.md) je kontejner v Azure, který uchovává zálohy z Windows Serveru. Pomocí následujícího postupu vytvořte na webu Azure Portal trezor služby Recovery Services. 

1. V nabídce vlevo vyberte **Všechny služby** a v seznamu služeb zadejte **Recovery Services**. Klikněte na **Trezory služby Recovery Services**.

   ![otevření trezoru služby Recovery Services](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. V nabídce **Trezory Recovery Services** klikněte na **Přidat**.

   ![zadání informací o trezoru](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. V nabídce **Trezor služby Recovery Services**:

    - Jako **Název** zadejte *myRecoveryServicesVault*.
    - V části **Předplatné** se zobrazí ID aktuálního předplatného.
    - V části **Skupina prostředků** vyberte **Použít existující** a zvolte *myResourceGroup*. Pokud *myResourceGroup* neexistuje, vyberte **Vytvořit novou** a zadejte *myResourceGroup*. 
    - V rozevírací nabídce **Umístění** zvolte *Západní Evropa*.
    - Kliknutím na **Vytvořit** vytvořte svůj trezor služby Recovery Services.
 
Když je trezor vytvořený, zobrazí se v seznamu trezorů Služeb zotavení.

## <a name="download-recovery-services-agent"></a>Stažení agenta služby Recovery Services

Agent služby Microsoft Azure Recovery Services (MARS) vytvoří přidružení mezi Windows Serverem a vaším trezorem služby Recovery Services. Následující postup vysvětluje stažení agenta na server.

1. V seznamu trezorů služby Recovery Services výběrem trezoru **myRecoveryServicesVault** otevřete jeho řídicí panel.

   ![zadání informací o trezoru](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. V nabídce řídicího panelu trezoru klikněte na **Zálohování**.

3. V nabídce **Cíl zálohování**:

   * V části **Kde je spuštěná vaše úloha?** vyberte **Místní prostředí**. 
   * V části **Co chcete zálohovat?** vyberte **Soubory a složky** a **Stav systému**.

   ![zadání informací o trezoru](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. Kliknutím na **Příprava infrastruktury** otevřete nabídku **Příprava infrastruktury**.

5. V nabídce **Příprava infrastruktury** klikněte na **Stáhnout agenta pro Windows Server nebo klienta Windows** a stáhněte soubor *MARSAgentInstaller.exe*. 

    ![Příprava infrastruktury](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    Instalační program otevřete samostatné okno prohlížeče a stáhne soubor **MARSAgentInstaller.exe**.
 
6. Před spuštěním staženého souboru v nabídce Příprava infrastruktury klikněte na **Stáhnout** a uložte soubor s **přihlašovacími údaji trezoru**. Přihlašovací údaje trezoru se vyžadují k propojení agenta MARS s trezorem služby Recovery Services.

    ![Příprava infrastruktury](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)
 
## <a name="install-and-register-the-agent"></a>Instalace a registrace agenta

1. Vyhledejte a dvakrát klikněte na stažený soubor **MARSagentinstaller.exe**.
2. Zobrazí se **Průvodce instalací agenta služby Microsoft Azure Recovery Services**. Při procházení průvodce zadejte po zobrazení výzev následující informace a pak klikněte na **Zaregistrovat**.
    - Umístění instalace a složky mezipaměti.
    - Informace o proxy serveru, pokud pro připojování k internetu používáte proxy server.
    - Vaše uživatelské jméno a heslo, pokud používáte ověřený proxy server.

    ![Příprava infrastruktury](./media/tutorial-backup-windows-server-to-azure/mars-installer.png) 

3. Na konci průvodce klikněte na **Pokračovat k registraci** a zadejte soubor s **přihlašovacími údaji trezoru**, který jste stáhli v předchozím postupu.
 
4. Po zobrazení výzvy zadejte šifrovací heslo pro šifrování záloh z Windows Serveru. Toto heslo uložte na bezpečném místě, protože ho Microsoft v případě ztráty nemůže obnovit.

5. Klikněte na **Dokončit**. 

## <a name="configure-backup-and-retention"></a>Konfigurace zálohování a uchovávání

Pomocí agenta služby Microsoft Azure Recovery Services můžete nakonfigurovat, kdy na Windows Serveru bude docházet k zálohování do Azure. Na serveru, na který jste stáhli agenta, proveďte následující kroky.

1. Otevřete agenta Služeb zotavení Microsoft Azure. Najdete ho vyhledáním **Microsoft Azure Backup** ve svém počítači.

2.  V konzole agenta služby Recovery Services v **podokně Akce** klikněte na **Naplánovat zálohování**.

    ![Příprava infrastruktury](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Kliknutím na **Další** přejděte na stránku **Výběr položek k zálohování**.

4. Klikněte na **Přidat položky** a v dialogovém okně, které se otevře, vyberte **Stav systému** a soubory nebo složky, které chcete zálohovat. Pak klikněte na **OK**.

5. Klikněte na **Další**.

6. Na stránce **Zadání plánu zálohování (Stav systému)** zadejte denní dobu nebo den v týdnu, kdy se má aktivovat zálohování stavu systému, a klikněte na **Další**.

7. Na stránce **Výběr zásady uchovávání informací (Stav systému)** vyberte zásadu uchovávání informací pro záložní kopii stavu systému a klikněte na **Další**.

8. Podobným způsobem vyberte plán zálohování a zásadu uchovávání pro vybrané soubory a složky. 

9. Na stránce **Výběr typu prvotní zálohy** vyberte **Automaticky přes síť** a klikněte na **Další**.

10. Na stránce **Potvrzení** zkontrolujte uvedené informace a klikněte na **Dokončit**.

11. Až průvodce dokončí vytváření plánu zálohování, klikněte na **Zavřít**.

## <a name="perform-an-ad-hoc-back-up"></a>Provedení zálohování ad hoc

Vytvořili jste plán spouštění úloh zálohování. Server jste však ještě nezálohovali. Osvědčeným postupem pro zotavení po havárii je spustit zálohování na vyžádání, aby se pro server zajistila odolnost dat.

1.  V konzole agenta služby Microsoft Azure Recovery Services klikněte na **Zálohovat nyní**.

    ![Příprava infrastruktury](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2.  V průvodci **Zálohovat nyní** vyberte buď **Soubory nebo složky**, nebo **Stav systému**, podle toho, co chcete zálohovat, a klikněte na **Další**. 
3. Na stránce **Potvrzení** zkontrolujte nastavení, která průvodce **Zálohovat nyní** použije k zálohování vašeho serveru. Poté klikněte na **Zálohovat**.
4.  Průvodce zavřete kliknutím na **Zavřít**. Pokud průvodce zavřete před dokončením procesu zálohování, průvodce zůstane spuštěný na pozadí.
4.  Po dokončení prvotní zálohy se v konzole agenta MARS v podokně **Úlohy** zobrazí stav **Úloha byla dokončena**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí webu Azure Portal provedli následující kroky: 
 
> [!div class="checklist"] 
> * Vytvoření trezoru Služeb zotavení 
> * Stažení agenta služby Microsoft Azure Recovery Services 
> * Instalace agenta 
> * Konfigurace zálohování pro Windows Server 
> * Provedení zálohování na vyžádání 

Přejděte k dalšímu kurz, kde obnovíte soubory z Azure do Windows Serveru.

> [!div class="nextstepaction"] 
> [Obnovení souborů z Azure do Windows Serveru](./tutorial-backup-restore-files-windows-server.md) 

