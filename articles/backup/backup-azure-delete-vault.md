---
title: Odstranění trezoru služby Recovery Services v Azure
description: Popisuje, jak odstranit trezor služby Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: raynew
ms.openlocfilehash: 94d66e28f8edbda6c41dcceaf427d7d7d869c90f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620113"
---
# <a name="delete-a-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

Tento článek popisuje, jak odstranit [Azure Backup](backup-overview.md) trezor služby Recovery Services. Obsahuje pokyny k odebrání závislostí a pak odstranění trezoru služby a odstraňuje se trezor vynutí.


## <a name="before-you-start"></a>Než začnete

Než začnete, je důležité pochopit, že nemůžete odstranit trezor služby Recovery Services se servery zaregistrované v ní nebo, který uchovává zálohovaná data.

- Odstranit trezor služby bez výpadku, zrušení registrace serverů v ní, odebrání úložiště dat a pak odstraňte trezor.
- Pokud se pokusíte odstranit trezor, stále se závislostmi, objeví se chybová zpráva. a budete muset ručně odebrat závislosti trezoru, včetně:
    - Zálohovaných položek
    - Chráněné servery
    - Zálohování serverů pro správu (Azure Backup Server, aplikace DPM) ![vyberte svůj trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- Pokud nechcete zachovat žádná data v trezoru služby Recovery Services a chcete trezor odstranit, můžete odstranit trezor vynutí.
- Pokud při pokusu o odstranění trezoru, ale nikoli, trezor stále probíhá konfigurace pro příjem dat záloh.


## <a name="delete-a-vault-from-the-azure-portal"></a>Odstranění trezoru z portálu Azure portal

1. Otevření řídicího panelu trezoru.  
2. Na řídicím panelu, klikněte na tlačítko **odstranit**. Ověřte, že chcete odstranit.

    ![Vyberte svůj trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

Pokud obdržíte chybu, odeberte [zálohování položek](#remove-backup-items), [servery infrastruktury](#remove-backup-infrastructure-servers), a [body obnovení](#remove-azure-backup-agent-recovery-points)a pak odstraňte trezor.

![odstranění trezoru chyba](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Odstranění trezoru služby Recovery Services vynutí

Můžete odstranit trezor vynutí pomocí Powershellu. Vynucené odstranění znamená, že se trvale odstraní trezor a všechna přidružená data záloh.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


Odstranění trezoru vynutí:

1. Přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` příkaz a postupujte podle na obrazovce pokynů.

   ```powershell
    Connect-AzAccount
   ```
2. Při prvním použití služby Azure Backup, zaregistrujte poskytovatele služeb zotavení Azure ve vašem předplatném pomocí [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider).

   ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

3. Otevřete okno Powershellu s oprávněními správce.
4. Použití `Set-ExecutionPolicy Unrestricted` odebrat nějaká omezení.
5. Spusťte následující příkaz ke stažení balíčku klienta Azure Resource Manageru z chocolately.org.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

6. Použijte následující příkaz k instalaci klienta rozhraní API Azure Resource Manageru.

   `choco.exe install armclient`

7. Na webu Azure Portal shromážděte názvu předplatného ID a zdroj skupiny pro trezor, který chcete odstranit.
8. V prostředí PowerShell spusťte následující příkaz pomocí ID předplatného, název skupiny prostředků a název trezoru. Když spustíte příkaz, odstraní se trezor a všechny závislosti.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
9. Pokud je Trezor není prázdná, zobrazí chybová zpráva "Trezor nejde odstranit, protože stávající prostředky v rámci tohoto trezoru". Chcete-li odebrat obsažené v trezoru, postupujte takto:

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

10. Na webu Azure Portal ověřte, že se odstraní.


## <a name="remove-vault-items-and-delete-the-vault"></a>Odebrat položky trezor a odstranění trezoru

Tato procedura poskytují několik příkladů, pro odebrání dat záloh a serverů infrastruktury. Poté, co všechno, co se odebere z trezoru, můžete ho odstranit.

### <a name="remove-backup-items"></a>Odebrat zálohované položky

Tento postup poskytuje příklad, který ukazuje, jak odstranit zálohovaná data ze souborů Azure.

1. Klikněte na tlačítko **zálohování položek** > **Azure Storage (soubory Azure)**

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. Klikněte pravým tlačítkem na každé položky soubory Azure odebrat, a klikněte na **Zastavit zálohování**.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/stop-backup-item.png)


3. V **Zastavit zálohování** > **zvolte možnost**vyberte **odstranit zálohovaná Data**.
4. Zadejte název položky a klikněte na tlačítko **Zastavit zálohování**. 
   - Ověří, že chcete položku odstranit.
   - **Zastavit zálohování** tlačítko aktivuje po ověření.
   - Je-li zachovat a neodstraňujte dat, nebude možné odstranit trezor.

     ![Odstranit data zálohy](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Volitelně zadejte důvod, proč odstraňujete data a přidejte komentáře.
6. Pokud chcete ověřit, že úloha odstranění dokončena, zkontrolujte zprávy Azure ![Odstranit data zálohy](./media/backup-azure-delete-vault/messages.png).
7. Po dokončení úlohy, služba odesílá zprávu: **byl zastaven proces zálohování a zálohování dat byl odstraněn**.
8. Po odstranění položky v seznamu na **zálohování položek** nabídky, klikněte na tlačítko **aktualizovat** pro zobrazení položek v trezoru.

      ![Odstranit data zálohy](./media/backup-azure-delete-vault/empty-items-list.png)


### <a name="remove-backup-infrastructure-servers"></a>Odeberte servery infrastruktury zálohování

1. V nabídce řídicího panelu trezoru klikněte na tlačítko **infrastruktura zálohování**.
2. Klikněte na tlačítko **servery pro správu zálohování** k zobrazení serverů. 

    ![Vyberte svůj trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

2. Klikněte pravým tlačítkem na položku > **odstranit**.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. . V **Zastavit zálohování** > **zvolte možnost**vyberte **odstranit zálohovaná Data**.
4. Zadejte název položky a klikněte na tlačítko **Zastavit zálohování**. 
   - Ověří, že chcete položku odstranit.
   - **Zastavit zálohování** tlačítko aktivuje po ověření.
   - Je-li zachovat a neodstraňujte dat, nebude možné odstranit trezor.

     ![Odstranit data zálohy](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Volitelně zadejte důvod, proč odstraňujete data a přidejte komentáře.
6. Pokud chcete ověřit, že úloha odstranění dokončena, zkontrolujte zprávy Azure ![Odstranit data zálohy](./media/backup-azure-delete-vault/messages.png).
7. Po dokončení úlohy, služba odesílá zprávu: **byl zastaven proces zálohování a zálohování dat byl odstraněn**.
8. Po odstranění položky v seznamu na **zálohování položek** nabídky, klikněte na tlačítko **aktualizovat** pro zobrazení položek v trezoru.


### <a name="remove-azure-backup-agent-recovery-points"></a>Odebrat body obnovení Azure Backup agent

1. V nabídce řídicího panelu trezoru klikněte na tlačítko **infrastruktura zálohování**.
2. Klikněte na tlačítko **servery pro správu zálohování** k zobrazení serverů infrastruktury.

    ![Vyberte svůj trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. V **chráněné servery** klikněte na možnost Azure Backup Agent.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Klikněte na server v seznamu serverů chráněných pomocí agenta Azure Backup.

    ![Vyberte konkrétní chráněný server](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. Řídicí panel vybraného serveru, klikněte na **odstranit**.

    ![Odstranit vybraný server](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Na **odstranit** nabídku, zadejte název položky a klikněte na tlačítko **odstranit**.
   - Ověří, že chcete položku odstranit.
   - **Zastavit zálohování** tlačítko aktivuje po ověření.
   - Je-li zachovat a neodstraňujte dat, nebude možné odstranit trezor.

     ![Odstranit data zálohy](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Volitelně zadejte důvod, proč odstraňujete data a přidejte komentáře.
8. Pokud chcete ověřit, že úloha odstranění dokončena, zkontrolujte zprávy Azure ![Odstranit data zálohy](./media/backup-azure-delete-vault/messages.png).
7. Po dokončení úlohy, služba odesílá zprávu: **byl zastaven proces zálohování a zálohování dat byl odstraněn**.
8. Po odstranění položky v seznamu na **zálohování položek** nabídky, klikněte na tlačítko **aktualizovat** pro zobrazení položek v trezoru.






### <a name="delete-the-vault-after-removing-dependencies"></a>Odstranění trezoru po odebrání závislostí

1. Když byly odebrány všechny závislosti, přejděte **Essentials** podokno v nabídce trezoru.
2. Ověřte, že nejsou k dispozici žádné **zálohování položek**, **zálohování serverů pro správu**, nebo **replikované položky** uvedené. Pokud se stále zobrazí položky v trezoru, odeberte je.

2. Pokud nejsou žádné další položky v trezoru, na řídicím panelu trezoru klikněte na **odstranit**.

    ![Odstranit data zálohy](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Pokud chcete ověřit, že chcete trezor odstranit, klikněte na tlačítko **Ano**. Trezor se odstraní a portál se vrátí **nový** nabídku služby.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Co když mám zastavte sledovací proces zálohování ale zachovat data?

Zastavení procesu zálohování, ale náhodně zachovat data, musíte odstranit zálohovaná data, jak je popsáno v předchozích částech.

## <a name="next-steps"></a>Další postup

[Další informace o](backup-azure-recovery-services-vault-overview.md) trezory služby Recovery Services.
