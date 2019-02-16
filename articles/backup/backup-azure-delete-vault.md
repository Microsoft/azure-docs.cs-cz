---
title: Odstranění trezoru služby Recovery Services v Azure.
description: Tento článek vysvětluje, jak odstranit trezor služby Recovery Services. Tento článek obsahuje postup řešení potíží při pokusu o odstranění trezoru, ale nikoli.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: d15e3773a9b6e3dceb0799d206070730675c211d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310962"
---
# <a name="delete-a-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

Tento článek popisuje, jak odebrat všechny položky z trezoru služby Recovery Services a poté jej odstraňte. Nejde odstranit trezor služby Recovery Services, pokud je zaregistrován k serveru a obsahuje zálohovaná data. Pokud při pokusu o odstranění trezoru, ale nikoli, trezor stále probíhá konfigurace pro příjem dat záloh.

Postup odstranění trezoru, naleznete v části [odstranění trezoru z portálu Azure portal](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal). Pokud nechcete zachovat žádná data v trezoru služby Recovery Services, a chcete trezor odstranit, naleznete v části [odstranění trezoru vynutí](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Pokud si nejste jisti, co je v trezoru a potřebujete, abyste měli jistotu, že jste odstranili trezoru, naleznete v části [odebrat závislosti trezor a odstranit trezor](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-azure-portal"></a>Odstranění trezoru z portálu Azure portal

Pokud už máte otevřete trezor služby Recovery Services, pokračujte v druhém kroku.

1. Otevřete na webu Azure portal a na řídicím panelu otevřete trezor, který chcete odstranit.

   Pokud nemáte trezoru služby Recovery Services připnuté na řídicí panel, v nabídce centra klikněte na tlačítko **všechny služby** a v seznamu prostředků zadejte **služby Recovery Services**. Seznam se průběžně filtruje podle zadávaného textu. Chcete-li zobrazit seznam trezorů v předplatném, klikněte na tlačítko **trezory služby Recovery Services**.

   ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Zobrazí se seznam trezorů služby Recovery Services.

   ![Vyberte trezor ze seznamu](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

1. V seznamu vyberte trezor, který chcete odstranit. Po výběru trezoru se otevře jeho řídicího panelu trezoru.

    ![Vyberte svůj trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. Odstranění trezoru v řídicím panelu trezoru klikněte na tlačítko **odstranit**. Budete vyzváni k ověření, že chcete trezor odstranit.

    ![Vyberte svůj trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    Pokud **trezoru Chyba při odstraňování** se zobrazí, můžete buď odeberte závislosti z trezoru, nebo můžete použití Powershellu k odstranění trezoru vynutí. Následující části popisují, jak provádět tyto úlohy.

    ![Chyba odstraňování trezoru](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Odstranění trezoru služby Recovery Services vynutí

Použití Powershellu k odstranění trezoru služby Recovery Services vynutí. Platnost prostředky trezoru služby Recovery Services a všechny přidružené zálohy dat, je trvale odstraněn.

> [!Warning]
> Při použití Powershellu k odstranění trezoru služby Recovery Services, se ujistěte, zda chcete trvale odstranit všechna data záloh v trezoru.
>

Pokud chcete odstranit trezor služby Recovery Services:

1. Přihlaste se ke svému účtu Azure.

   Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzureRmAccount` a postupujte podle pokynů na obrazovce.

   ```powershell
    Connect-AzureRmAccount
   ```
   Při prvním použití služby Azure Backup musíte ve svém předplatném zaregistrovat poskytovatele služby Azure Recovery Services pomocí rutiny [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

1. Otevřete okno Powershellu s oprávněními správce.

1. Použití `Set-ExecutionPolicy Unrestricted` odebrat nějaká omezení.

1. Spusťte následující příkaz ke stažení balíčku klienta Azure Resource Manageru z chocolately.org.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

1. Použijte následující příkaz k instalaci klienta rozhraní API Azure Resource Manageru.

   `choco.exe install armclient`

1. Na webu Azure Portal shromážděte ID předplatného a název skupiny prostředků přidružených k trezoru služby Recovery Services, který chcete odstranit.

1. V prostředí PowerShell spusťte následující příkaz pomocí ID předplatného, název skupiny prostředků a název trezoru služby Recovery Services. Když spustíte příkaz, odstraní se trezor a všechny závislosti.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
   Trezor musí být prázdný, než budete moct odstranit. V opačném případě dojde k chybě, zobrazí "Trezor nejde odstranit, protože stávající prostředky v rámci tohoto trezoru". Následující příkaz ukazuje, jak odstranit kontejner v trezoru:

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

1. Přihlaste se k předplatnému na webu Azure Portal a ověřte, že se odstraní.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Odebrat závislosti trezor a odstranění trezoru

Ručně odebrat závislosti trezoru, odstranit konfiguraci mezi všechny položky nebo servery, a trezor služby Recovery Services. Při procházení následující postup, použijte **zálohování položek** nabídky (viz obrázek) pro:

* Zálohování Azure Storage (soubory Azure)
* SQL Server v záloh virtuálních počítačů Azure
* Zálohování virtuálních počítačů Azure

Použití **infrastruktura zálohování** nabídky (viz obrázek) pro:

* Zálohování Azure Backup serveru
* Zálohování nástroje System Center DPM

    ![Vyberte svůj trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. V nabídce řídicího panelu trezoru, posuňte se dolů k části chráněné položky a klikněte na tlačítko **zálohování položek**. V této nabídce můžete zastavit a odstranit Azure souborové servery, servery SQL ve virtuálním počítači Azure a Azure virtual machines. V tomto příkladu Odebereme zálohovaná data ze Azure souborového serveru.

    ![Vyberte svůj trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/selected-backup-items.png)

1. Vyberte typ zálohy, chcete-li zobrazit všechny položky daného typu.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

1. Pro všechny položky v seznamu, klikněte pravým tlačítkem na položku a v místní nabídce vyberte **Zastavit zálohování**.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/stop-backup-item.png)

    Otevře se nabídka Zastavit zálohování.

1. Na **Zastavit zálohování** nabídky, z **zvolte možnost** nabídce vyberte možnost **odstranit zálohovaná Data**, zadejte název položky a klikněte na tlačítko **Zastavit zálohování**.

    Zadejte název položky, chcete-li ověřit, že chcete odstranit. **Zastavit zálohování** tlačítko aktivuje po ověření položky. Je-li zachovat data, nebudete moct odstranit trezor.

    ![Odstranit data zálohy](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Pokud chcete, zadejte důvod, proč odstraňujete data a přidejte komentáře. Pokud chcete ověřit úloha byla dokončena, zkontrolujte zprávy Azure ![odstranit data zálohy](./media/backup-azure-delete-vault/messages.png). <br/>
    Po dokončení úlohy, služba odesílá zprávu: *byl zastaven proces zálohování a zálohování dat byl odstraněn*.

1. Po odstranění položky v seznamu na **zálohování položek** nabídky, klikněte na tlačítko **aktualizovat** pro zobrazení položek v trezoru.

      ![Odstranit data zálohy](./media/backup-azure-delete-vault/empty-items-list.png)

      Když v seznamu nejsou žádné položky, přejděte **Essentials** podokno v nabídce trezoru služby Recovery Services. Nesmí být žádné **zálohování položek**, **zálohování serverů pro správu**, nebo **replikované položky** uvedené. Pokud se stále zobrazí položky v trezoru, vraťte se ke kroku 3 a zvolit jinou položku typu seznam.  

1. Pokud neexistují žádné další položky na panelu nástrojů trezoru, klikněte na tlačítko **odstranit**.

    ![Odstranit data zálohy](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Pokud chcete ověřit, že chcete trezor odstranit, klikněte na tlačítko **Ano**.

    Trezor se odstraní a portál se vrátí **nový** nabídku služby.

## <a name="removing-azure-backup-server-or-dpm"></a>Odebrání mobilního zařízení nebo Azure Backup serveru aplikace DPM

1. V nabídce řídicího panelu trezoru, přejděte dolů do části Správa a klikněte na **infrastruktura zálohování**.

1. V podnabídce, klikněte na tlačítko **servery pro správu zálohování** zobrazíte server Azure Backup Server a System Center DPM. můžete zastavit a odstranit Azure souborové servery, servery SQL ve virtuálním počítači Azure a Azure virtual machines.

    ![Vyberte svůj trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

1. Klikněte pravým tlačítkem na položku, kterou chcete odstranit a z podnabídky, vyberte **odstranit**.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    Otevře se nabídka Zastavit zálohování.

1. Na **Zastavit zálohování** nabídky, z **zvolte možnost** nabídce vyberte možnost **odstranit zálohovaná Data**, zadejte název položky a klikněte na tlačítko **Zastavit zálohování**.

    Chcete-li ověřit, zda že chcete odstranit, zadejte její název. **Zastavit zálohování** tlačítko aktivuje po ověření položky. Je-li zachovat data, nelze odstranit trezor.

    ![Odstranit data zálohy](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Volitelně můžete zadat důvod, proč jsou odstranění dat a přidejte komentáře. Pokud chcete ověřit, že je úloha dokončená, zkontrolujte zprávy Azure ![odstranit data zálohy](./media/backup-azure-delete-vault/messages.png). <br/>
    Po dokončení úlohy služby odešle zprávu: byl zastaven proces zálohování a zálohování dat byl odstraněn.

1. Po odstranění položky v seznamu na **zálohování položek** nabídky, klikněte na tlačítko **aktualizovat** zobrazíte zbývající položky v trezoru.

      ![Odstranit data zálohy](./media/backup-azure-delete-vault/empty-items-list.png)

      Když v seznamu nejsou žádné položky, přejděte **Essentials** podokno v nabídce trezoru služby Recovery Services. Nesmí být žádné **zálohování položek**, **zálohování serverů pro správu**, nebo **replikované položky** uvedené. Pokud se stále zobrazí položky v trezoru, vraťte se ke kroku 3 a zvolit jinou položku typu seznam.  
1. Pokud nejsou žádné další položky v trezoru, na řídicím panelu trezoru klikněte na **odstranit**.

    ![Odstranit data zálohy](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Pokud chcete ověřit, že chcete trezor odstranit, klikněte na tlačítko **Ano**.

    Trezor se odstraní a portál se vrátí **nový** nabídku služby.


## <a name="removing-azure-backup-agent-recovery-points"></a>Odebírá se body obnovení Azure Backup agent

1. V nabídce řídicího panelu trezoru, přejděte dolů do části Správa a klikněte na **infrastruktura zálohování**.

1. V dílčí nabídky, klikněte na tlačítko **chráněné servery** zobrazíte seznam chráněné typy serverů, včetně agenta Azure Backup.

    ![Vyberte svůj trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/identify-protected-servers.png)

1. V **chráněné servery** klikněte na možnost Azure Backup Agent.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    Otevře se seznam serverů chráněných pomocí agenta Azure Backup.

    ![Vyberte konkrétní chráněný server](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

1. V seznamu serverů klikněte na jednu otevřete nabídku.

    ![Zobrazit řídicí panel vybraný server](./media/backup-azure-delete-vault/selected-protected-server.png)

1. V nabídce řídicího panelu vybraného serveru, klikněte na **odstranit**.

    ![Odstranit vybraný server](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

1. Na **odstranit** nabídku, zadejte název položky a klikněte na tlačítko **odstranit**.

    Zadejte název položky, chcete-li ověřit, že chcete odstranit. **Odstranit** tlačítko aktivuje po ověření položky.

    ![Odstranit data zálohy](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    Volitelně můžete zadat důvod, proč jsou odstranění dat a přidejte komentáře. Pokud chcete ověřit, že je úloha dokončená, zkontrolujte zprávy Azure ![odstranit data zálohy](./media/backup-azure-delete-vault/messages.png). <br/>
    Po dokončení úlohy služby odešle zprávu: byl zastaven proces zálohování a zálohování dat byl odstraněn.

1. Po odstranění položky v seznamu na **zálohování položek** nabídky, klikněte na tlačítko **aktualizovat** zobrazíte zbývající položky v trezoru.

      ![Odstranit data zálohy](./media/backup-azure-delete-vault/empty-items-list.png)

      Když v seznamu nejsou žádné položky, přejděte **Essentials** podokno v nabídce trezoru služby Recovery Services. Nesmí být žádné **zálohování položek**, **zálohování serverů pro správu**, nebo **replikované položky** uvedené. Pokud se stále zobrazí položky v trezoru, vraťte se ke kroku 3 a zvolit jinou položku typu seznam.  
1. Pokud nejsou žádné další položky v trezoru, na řídicím panelu trezoru klikněte na **odstranit**.

    ![Odstranit data zálohy](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Pokud chcete ověřit, že chcete trezor odstranit, klikněte na tlačítko **Ano**.

    Trezor se odstraní a portál se vrátí **nový** nabídku služby.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Co když mám zastavte sledovací proces zálohování ale zachovat data?

Pokud ale náhodně zastavte sledovací proces zálohování *zachovat* data, je nutné odstranit zálohovaná data můžete odstranit trezor. Odstranění zálohovaných dat:

1. Na **zálohované položky** nabídku, klikněte pravým tlačítkem na položku a v místní nabídce klikněte na tlačítko **odstranit data zálohy**.

    ![Odstranit data zálohy](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    **Odstranit zálohovaná Data** otevře se nabídka.
1. Na **odstranit zálohovaná Data** nabídku, zadejte název položky a klikněte na tlačítko **odstranit**.

    ![Odstranit data zálohy](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Jakmile odstraníte data, vraťte se ke kroku 4c a pokračujte v procesu.
