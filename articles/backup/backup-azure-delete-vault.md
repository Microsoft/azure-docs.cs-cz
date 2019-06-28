---
title: Odstranění trezoru služby Recovery Services v Azure
description: Popisuje, jak odstranit trezor služby Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: raynew
ms.openlocfilehash: 51de1c4ac17360282877f05d52c3ea8fa2c6d712
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310779"
---
# <a name="delete-a-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

Tento článek popisuje, jak odstranit [Azure Backup](backup-overview.md) trezor služby Recovery Services. Obsahuje pokyny k odebrání závislostí a pak odstranění trezoru služby a odstraňuje se trezor vynutí.


## <a name="before-you-start"></a>Než začnete

Než začnete, je důležité pochopit, že nemůžete odstranit trezor služby Recovery Services se servery zaregistrované v ní nebo, který uchovává zálohovaná data.

- Řádně odstranění trezoru, zrušte registraci serverů, které obsahuje, odebrání úložiště dat a pak odstraňte trezor.
- Pokud se pokusíte odstranit trezor, který má stále závislosti, vydává chybovou zprávu a budete muset ručně odebrat závislosti trezoru, včetně:
    - Zálohovaných položek
    - Chráněné servery
    - Zálohování serverů pro správu (Azure Backup Server, aplikace DPM) ![vyberte svůj trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- Pokud nechcete zachovat žádná data v trezoru služby Recovery Services a chcete trezor odstranit, můžete odstranit trezor vynutí.
- Pokud při pokusu o odstranění trezoru, ale nikoli, trezor stále probíhá konfigurace pro příjem dat záloh.


## <a name="delete-a-vault-from-the-azure-portal"></a>Odstranění trezoru z portálu Azure portal

1. Otevření řídicího panelu trezoru.  
2. Na řídicím panelu, klikněte na tlačítko **odstranit**. Ověřte, že chcete odstranit.

    ![Vyberte svůj trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

Pokud obdržíte chybu, odeberte [zálohování položek](#remove-backup-items), [servery infrastruktury](#remove-azure-backup-management-servers), a [body obnovení](#remove-azure-backup-agent-recovery-points)a pak odstraňte trezor.

![odstranění trezoru chyba](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Odstranění trezoru služby Recovery Services pomocí klienta Azure Resource Manageru

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instalace chocolatey z [tady](https://chocolatey.org/) a k instalaci ARMClient spouštění následující příkaz:

   ` choco install armclient --source=https://chocolatey.org/api/v2/ `
2. Přihlaste se ke svému účtu Azure a spusťte tento příkaz:

    ` ARMClient.exe login [environment name] `

3. Na webu Azure Portal shromážděte názvu předplatného ID a zdroj skupiny pro trezor, který chcete odstranit.

Další informace o příkazu ARMClient najdete na tomto [dokumentu](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Pomocí klienta Azure Resource Manageru můžete odstranit trezor služby Recovery Services

1. Spusťte následující příkaz pomocí ID předplatného, název skupiny prostředků a název trezoru. Při spuštění příkazu odstraní trezor, pokud nemáte žádné závislosti.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Pokud je Trezor není prázdná, zobrazí chybová zpráva "Trezor nejde odstranit, protože stávající prostředky v rámci tohoto trezoru". Chcete-li odebrat chráněných položek / kontejner v trezoru, postupujte takto:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Na webu Azure Portal ověřte, že se odstraní.


## <a name="remove-vault-items-and-delete-the-vault"></a>Odebrat položky trezor a odstranění trezoru

Tyto postupy obsahují příklady pro odebrání dat záloh a serverů infrastruktury. Poté, co všechno, co se odebere z trezoru, můžete ho odstranit.

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


### <a name="remove-azure-backup-management-servers"></a>Odeberte servery pro správu Azure Backup

1. V nabídce řídicího panelu trezoru klikněte na tlačítko **infrastruktura zálohování**.
2. Klikněte na tlačítko **servery pro správu zálohování** k zobrazení serverů.

    ![Vyberte trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Klikněte pravým tlačítkem na položku > **odstranit**.
4. Na **odstranit** nabídku, zadejte název serveru a klikněte na tlačítko **odstranit**.

     ![Odstranit data zálohy](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)
5.  Volitelně zadejte důvod, proč odstraňujete data a přidejte komentáře.

> [!NOTE]
> Odebrat položky, v konzole pro správu serveru nebo v konzole MARS na chráněném serveru, ukončete ochranu a odstranit zálohy. Pokud zůstanou zálohované položky, se zobrazí následující chyba při pokusu o odstranění a zrušení registrace serveru:
>
>![nepovedlo se odstranit](./media/backup-azure-delete-vault/deletion-failed.png)

6. Pokud chcete ověřit, že úloha odstranění dokončena, zkontrolujte zprávy Azure ![Odstranit data zálohy](./media/backup-azure-delete-vault/messages.png).
7. Po dokončení úlohy, služba odesílá zprávu: **byl zastaven proces zálohování a zálohování dat byl odstraněn**.
8. Po odstranění položky v seznamu na **infrastruktura zálohování** nabídky, klikněte na tlačítko **aktualizovat** pro zobrazení položek v trezoru.


### <a name="remove-azure-backup-agent-recovery-points"></a>Odebrat body obnovení Azure Backup agent

1. V nabídce řídicího panelu trezoru klikněte na tlačítko **infrastruktura zálohování**.
2. Klikněte na tlačítko **chráněné servery** k zobrazení serverů infrastruktury.

    ![Vyberte svůj trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. V **chráněné servery** klikněte na možnost Azure Backup Agent.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Klikněte na server v seznamu serverů chráněných pomocí agenta Azure Backup.

    ![Vyberte konkrétní chráněný server](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. Řídicí panel vybraného serveru, klikněte na **odstranit**.

    ![Odstranit vybraný server](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Na **odstranit** nabídku, zadejte název serveru a klikněte na tlačítko **odstranit**.

     ![Odstranit data zálohy](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Volitelně zadejte důvod, proč odstraňujete data a přidejte komentáře.

> [!NOTE]
> Před odstraněním těchto server registrace, musí se odstranit zálohované položky přidružené k zálohování serveru pro správu nebo agenta Azure Backup serveru. Odebrat položky zálohování, přejděte na SC DPM, MABS nebo MARS konzoly pro správu na serveru podle potřeby a vyberte příslušné možnosti k zastavení ochrany a odstranění zálohy. Pokud jsou stále spojeny žádné zálohované položky, zobrazí se následující chyba:
>
>
>![nepovedlo se odstranit](./media/backup-azure-delete-vault/deletion-failed.png)

8. Pokud chcete ověřit, že úloha odstranění dokončena, zkontrolujte zprávy Azure ![Odstranit data zálohy](./media/backup-azure-delete-vault/messages.png).
9. Po odstranění položky v seznamu na **infrastruktura zálohování** nabídky, klikněte na tlačítko **aktualizovat** pro zobrazení položek v trezoru.

### <a name="delete-the-vault-after-removing-dependencies"></a>Odstranění trezoru po odebrání závislostí

1. Když byly odebrány všechny závislosti, přejděte **Essentials** podokno v nabídce trezoru.
2. Ověřte, že nejsou k dispozici žádné **zálohování položek**, **zálohování serverů pro správu**, nebo **replikované položky** uvedené. Pokud se stále zobrazí položky v trezoru, odeberte je.

3. Pokud nejsou žádné další položky v trezoru, na řídicím panelu trezoru klikněte na **odstranit**.

    ![Odstranit data zálohy](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Pokud chcete ověřit, že chcete trezor odstranit, klikněte na tlačítko **Ano**. Trezor se odstraní a portál se vrátí **nový** nabídku služby.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Co když mám zastavte sledovací proces zálohování ale zachovat data?

Zastavení procesu zálohování, ale náhodně zachovat data, musíte odstranit zálohovaná data, jak je popsáno v předchozích částech.

## <a name="next-steps"></a>Další postup

[Další informace o](backup-azure-recovery-services-vault-overview.md) trezory služby Recovery Services.
