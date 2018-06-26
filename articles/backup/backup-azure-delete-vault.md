---
title: Odstranit trezor služeb zotavení v Azure.
description: Tento článek vysvětluje, jak odstranit trezor služeb zotavení. Článek obsahuje kroky řešení potíží, když se pokuste odstranit trezor, ale nemůže.
services: service-name
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 6/21/2018
ms.author: markgal
ms.openlocfilehash: d8169eba6790e49a85d69434663faabe7430942e
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937596"
---
# <a name="delete-a-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

Tento článek vysvětluje, jak odebrat všechny položky z trezoru služeb zotavení a poté jej odstraňte. Trezor služeb zotavení nelze odstranit, pokud je zaregistrován k serveru a obsahuje zálohovaná data. Pokud se pokuste odstranit trezor, ale nemůže, úložišti stále nakonfigurováno pro příjem zálohovaná data.

Další informace o odstranění úložiště, najdete v části [odstranění trezoru z portálu Azure](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal). Pokud nechcete, aby k zachování všech dat v trezoru služeb zotavení a chcete trezor odstranit, najdete v části [trezor vynutí odstranit](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Pokud si nejste jisti, co je v trezoru a potřebujete, abyste měli jistotu, že odstraněním trezoru, najdete v části [závislosti odebrat trezoru a delete trezoru](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-azure-portal"></a>Odstranění trezoru z portálu Azure

Pokud již máte otevřete trezor služeb zotavení, pokračujte druhém kroku.

1. Otevřete portál Azure a na řídicím panelu otevřete úložišti, které chcete odstranit.

   Pokud nemáte trezor služeb zotavení připnuli k řídicímu panelu, v nabídce centra klikněte na tlačítko **všechny služby** a v seznamu prostředků zadejte **služeb zotavení**. Seznam se průběžně filtruje podle zadávaného textu. Chcete-li zobrazit seznam trezorů v rámci vašeho předplatného, klikněte na tlačítko **trezory služeb zotavení**.

   ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Zobrazí se seznam trezorů služeb zotavení. 

   ![Vyberte možnost trezoru ze seznamu](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

2. V seznamu vyberte trezor, které chcete odstranit. Když vyberete trezoru, otevře se jeho panelu trezoru.

    ![Vyberte svůj trezor pro otevření jeho řídicí panel](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

3. Chcete-li odstranit úložiště, na řídicím panelu trezoru, klikněte na tlačítko **odstranit**. Budete vyzváni k ověření, že chcete trezor odstranit.

    ![Vyberte svůj trezor pro otevření jeho řídicí panel](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    Pokud **trezoru Chyba při odstraňování** se zobrazí, můžete buď odeberte závislosti z trezoru, nebo prostředí PowerShell můžete použít k odstranění trezoru vynutí. Následující části popisují, jak provést tyto úlohy.

    ![Chyba při odstraňování trezoru](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Odstranit trezor služeb zotavení vynutí

Prostředí PowerShell můžete odstranit trezor služeb zotavení vynutí. Platnost způsobem trezor služeb zotavení a všechny přidružené zálohovaná data, je trvale odstraněn. 

> [!Warning]
> Při použití prostředí PowerShell k odstranění trezoru služeb zotavení, se ujistěte, zda chcete trvale odstranit všechna zálohovaná data v trezoru.
>

Odstranění trezoru služeb zotavení:

1. Přihlaste se k účtu Azure.

   Přihlaste se k předplatnému Azure s `Connect-AzureRmAccount` příkazů a postupujte podle na obrazovce pokynů.

   ```powershell
    Connect-AzureRmAccount
   ```
   Při prvním použití služby Azure Backup musíte ve svém předplatném zaregistrovat poskytovatele služby Azure Recovery Services pomocí rutiny [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

2. Otevřete okno prostředí PowerShell s oprávněními správce.

3. Použití `Set-ExecutionPolicy Unrestricted` odebrat žádné omezení.

4. Spusťte následující příkaz ke stažení balíčku klienta Azure Resource Manager z chocolately.org.

    `iex ((New-Object System.Net.WebClient) DownloadString('https://chocolatey.org/install.ps1))`

5. Použijte následující příkaz k instalaci klienta rozhraní API Správce prostředků Azure.

   `choco.exe install armclient`

6. Na portálu Azure shromážděte ID předplatného a název skupiny přidružených prostředků pro trezor služeb zotavení, který chcete odstranit.

7. V prostředí PowerShell spusťte následující příkaz pomocí vaše ID odběru, název skupiny prostředků a název trezoru služeb zotavení. Když spustíte příkaz, odstraní trezor a všechny závislosti.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
8. Přihlaste se k vašemu předplatnému na portálu Azure a ověřte, zda že úložiště je odstranit.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Odebrání závislostí trezoru a odstranění trezoru

Ručně odebrat závislosti trezoru, odstraňte konfiguraci mezi každou položkou nebo server a trezoru služeb zotavení. Při procházení následující postup použijte **zálohování položek** nabídky (viz obrázek) pro:

* Zálohování Azure Storage (soubory Azure)
* SQL Server v zálohování virtuálního počítače Azure
* Zálohování virtuálních počítačů Azure
* Zálohování agenta služeb zotavení Microsoft Azure

Použití **infrastruktura zálohování** nabídky (viz obrázek) pro:

* Zálohování Azure zálohování serveru
* Zálohování aplikace DPM System Center

    ![Vyberte svůj trezor pro otevření jeho řídicí panel](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. V nabídce řídícího panelu trezoru, přejděte do části chráněné položky a klikněte na tlačítko **zálohování položek**. V této nabídce můžete zastavit a odstranit Azure souborové servery, servery SQL ve virtuálním počítači Azure a virtuálních počítačů Azure. V tomto příkladu odstraníme zálohovaná data ze služby Azure souborového serveru.

    ![Vyberte svůj trezor pro otevření jeho řídicí panel](./media/backup-azure-delete-vault/selected-backup-items.png)

2. Vyberte typ zálohy, chcete-li zobrazit všechny položky daného typu.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. Pro všechny položky v seznamu, klikněte pravým tlačítkem položku a v místní nabídce vyberte **zastavení zálohování**.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/stop-backup-item.png) 

    Otevře se nabídka zastavení zálohování.

4. Na **zastavení zálohování** nabídce z **zvolte možnost** nabídce vyberte možnost **odstranit záložní Data**, zadejte název položky a klikněte na tlačítko **zastavení zálohování**.

    Zadejte název položky, chcete-li ověřit, že chcete odstranit. **Zastavení zálohování** tlačítko aktivuje po ověření položky. Je-li zachovat data, nebudete moci Trezor odstranit.

    ![odstranit záložní data](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Pokud chcete, uveďte důvod, proč při odstraňování dat a přidejte komentáře. Pokud chcete ověřit úloha nebyla dokončena, zkontrolujte zprávy Azure ![odstranit záložní data](./media/backup-azure-delete-vault/messages.png). <br/>
    Po dokončení úlohy služby odešle zprávu: *byla zastavena procesu zálohování a zálohování dat byl odstraněn*.

5. Po odstranění položky v seznamu na **zálohování položek** nabídky, klikněte na tlačítko **aktualizovat** zobrazíte položky v trezoru.

      ![odstranit záložní data](./media/backup-azure-delete-vault/empty-items-list.png)

      Pokud v seznamu nejsou žádné položky, přejděte na **Essentials** podokně v nabídce trezoru služeb zotavení. Nesmí být žádné **zálohování položek**, **zálohování serverů pro správu**, nebo **replikované položky** uvedené. Pokud stále se zobrazí položky v trezoru, vraťte se ke kroku tři a zvolte jiný položky typu seznamu.  

6. Když na panelu nástrojů trezoru nejsou žádné další položky, klikněte na tlačítko **odstranit**.

    ![odstranit záložní data](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

7. Chcete-li ověřit, že chcete trezor odstranit, klikněte na tlačítko **Ano**.

    Odstranění trezoru a vrátí portálu **nový** nabídky služby.

## <a name="removing-azure-backup-server-or-dpm"></a>Odebrání Azure Backup Server nebo aplikace DPM

1. V nabídce řídícího panelu trezoru, přejděte do části Správa a klikněte na tlačítko **infrastruktura zálohování**. 

2. V podnabídce, klikněte na tlačítko **servery pro správu zálohování** k zobrazení serverů zálohování Azure a System Center DPM server. můžete zastavit a odstranit Azure souborové servery, servery SQL ve virtuálním počítači Azure a virtuálních počítačů Azure. 

    ![Vyberte svůj trezor pro otevření jeho řídicí panel](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Klikněte pravým tlačítkem na položku, kterou chcete odstranit, a vyberte z nabídky dílčí **odstranit**.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    Otevře se nabídka zastavení zálohování.

4. Na **zastavení zálohování** nabídce z **zvolte možnost** nabídce vyberte možnost **odstranit záložní Data**, zadejte název položky a klikněte na tlačítko **zastavení zálohování**.

    Chcete-li ověřit, zda že chcete odstranit, zadejte jeho název. **Zastavení zálohování** tlačítko aktivuje po ověření položky. Je-li zachovat data, nelze odstranit úložiště.

    ![odstranit záložní data](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Volitelně můžete zadat důvod, proč se odstranit data a přidání komentářů. Pokud chcete ověřit, zda byla úloha dokončena, zkontrolujte zprávy Azure ![odstranit záložní data](./media/backup-azure-delete-vault/messages.png). <br/>
    Po dokončení úlohy služby odešle zprávu: byla zastavena procesu zálohování a zálohování dat byla odstraněna.

5. Po odstranění položky v seznamu na **zálohování položek** nabídky, klikněte na tlačítko **aktualizovat** zobrazit zbývající položky v trezoru.

      ![odstranit záložní data](./media/backup-azure-delete-vault/empty-items-list.png)

      Pokud v seznamu nejsou žádné položky, přejděte na **Essentials** podokně v nabídce trezoru služeb zotavení. Nesmí být žádné **zálohování položek**, **zálohování serverů pro správu**, nebo **replikované položky** uvedené. Pokud stále se zobrazí položky v trezoru, vraťte se ke kroku tři a zvolte jiný položky typu seznamu.  
6. Pokud nejsou žádné další položky v trezoru, na řídicím panelu trezoru klikněte na **odstranit**.

    ![odstranit záložní data](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

7. Chcete-li ověřit, že chcete trezor odstranit, klikněte na tlačítko **Ano**.

    Odstranění trezoru a vrátí portálu **nový** nabídky služby.


## <a name="removing-azure-backup-agent-recovery-points"></a>Odebrání bodů obnovení agenta Azure Backup

1. V nabídce řídícího panelu trezoru, přejděte do části Správa a klikněte na tlačítko **infrastruktura zálohování**.

2. Klikněte v nabídce dílčí příkaz **chráněné servery** zobrazit seznam chráněných typy serverů, včetně agenta Azure Backup.

    ![Vyberte svůj trezor pro otevření jeho řídicí panel](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. V **chráněné servery** seznamu, klikněte na tlačítko Azure Backup Agent.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    Otevře se seznam serverů chráněných pomocí agenta Azure Backup.

    ![Vyberte konkrétní chráněný server](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

4. V seznamu serverů klikněte na jednu otevřete nabídku.

    ![zobrazení řídicího panelu vybraný server](./media/backup-azure-delete-vault/selected-protected-server.png)

5. V nabídce řídícího panelu vybraný server, klikněte na tlačítko **odstranit**.

    ![Odstranit vybraný server](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Na **odstranit** nabídky, zadejte název položky a klikněte na tlačítko **odstranit**.

    Zadejte název položky, chcete-li ověřit, že chcete odstranit. **Odstranit** tlačítko aktivuje po ověření položky.

    ![odstranit záložní data](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    Volitelně můžete zadat důvod, proč se odstranit data a přidání komentářů. Pokud chcete ověřit, zda byla úloha dokončena, zkontrolujte zprávy Azure ![odstranit záložní data](./media/backup-azure-delete-vault/messages.png). <br/>
    Po dokončení úlohy služby odešle zprávu: byla zastavena procesu zálohování a zálohování dat byla odstraněna.

7. Po odstranění položky v seznamu na **zálohování položek** nabídky, klikněte na tlačítko **aktualizovat** zobrazit zbývající položky v trezoru.

      ![odstranit záložní data](./media/backup-azure-delete-vault/empty-items-list.png)

      Pokud v seznamu nejsou žádné položky, přejděte na **Essentials** podokně v nabídce trezoru služeb zotavení. Nesmí být žádné **zálohování položek**, **zálohování serverů pro správu**, nebo **replikované položky** uvedené. Pokud stále se zobrazí položky v trezoru, vraťte se ke kroku tři a zvolte jiný položky typu seznamu.  
8. Pokud nejsou žádné další položky v trezoru, na řídicím panelu trezoru klikněte na **odstranit**.

    ![odstranit záložní data](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

9. Chcete-li ověřit, že chcete trezor odstranit, klikněte na tlačítko **Ano**.

    Odstranění trezoru a vrátí portálu **nový** nabídky služby.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Co když jsem zastavit proces zálohování ale zachovat data?

Pokud ale omylem zastavit proces zálohování *zachovat* data, je nutné odstranit zálohovaná data před odstraněním trezoru. Odstranit záložní data:

1. Na **položky zálohování** nabídky, klikněte pravým tlačítkem položku a v místní nabídce klikněte na tlačítko **odstranit záložní data**.

    ![odstranit záložní data](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    **Odstranit Data zálohování** otevře se nabídka.
2. Na **odstranit Data zálohování** nabídky, zadejte název položky a klikněte na tlačítko **odstranit**.

    ![odstranit záložní data](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Jakmile jste odstranili data, vraťte se ke kroku 4c a pokračujte v procesu.
