---
title: Odstranění trezoru služby Recovery Services v Azure
description: Popisuje, jak odstranit trezor služby Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: 01c20ce84f5c97b3a0ac437fe602861085b5052c
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827879"
---
# <a name="delete-a-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

Tento článek popisuje, jak odstranit [Azure Backup](backup-overview.md) trezor služby Recovery Services. Obsahuje pokyny k odebrání závislostí a její následné odstranění trezoru.


## <a name="before-you-start"></a>Než začnete

Nelze odstranit trezor služby Recovery Services, který má závislosti, jako jsou chráněné servery a servery pro správu zálohování přidružených k trezoru.<br/>
Trezor obsahuje zálohovaná data nelze odstranit, (to znamená, i když máte zastavení ochrany, ale data záloh uchovávají).

Pokud odstraníte trezor, který obsahuje závislosti, tak se zobrazí následující chyba:

![odstranění trezoru chyba](./media/backup-azure-delete-vault/error.png)

Řádně odstranit trezor, postupujte podle pokynů uvedených v následujícím pořadí:
- Zastavení ochrany a odstranění zálohovaných dat
- Odstranit chráněné servery a servery pro správu zálohování
- Odstranění trezoru


## <a name="delete-backup-data-and-backup-items"></a>Odstranit zálohovaná data a zálohované položky

Než budete pokračovat další čtení **[to ](#before-you-start)** oddílu přehled o závislostech a proces odstranění trezoru.

### <a name="for-protected-items-in-cloud"></a>Pro chráněné položky v cloudu

Pokud chcete zastavit ochranu a odstraňte zálohovaná data, proveďte níže:

1. Z portálu > trezor služby Recovery Services > Zálohování položek zvolte chráněné položky v cloudu.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. Pro každou položku, budete muset klikněte pravým tlačítkem a zvolte **Zastavit zálohování**.

    ![Vyberte typ zálohy](./media/backup-azure-delete-vault/stop-backup-item.png)

3. V **Zastavit zálohování** > **zvolte možnost**vyberte **odstranit zálohovaná Data**.
4. Zadejte název položky a klikněte na tlačítko **Zastavit zálohování**.
   - Ověří, že chcete položku odstranit.
   - **Zastavit zálohování** tlačítko aktivuje po ověření.
   - Je-li zachovat a neodstraňujte dat, nebude možné odstranit trezor.

     ![Odstranit data zálohy](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Zkontrolujte, **oznámení** ![odstranit data zálohy](./media/backup-azure-delete-vault/messages.png). Po dokončení se služba zobrazí zprávu: **Zastavuje se zálohování a odstranění zálohování dat pro "*zálohovaná položka*"** . **Operace se úspěšně dokončila**.
6. Klikněte na tlačítko **aktualizovat** na **zálohování položek** nabídky ke kontrole, pokud se zálohovaná položka se odebere.

      ![Odstranit data zálohy](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>Pro agenta MARS

Pokud chcete zastavit ochranu a odstraňte zálohovaná data, proveďte kroky v níže uvedeném pořadí:

- [Krok 1: Odstranění zálohovaných položek z konzoly pro správu MARS](#step-1-delete-backup-items-from-mars-management-console)
- [Krok 2: Z portálu odeberte agenta Azure Backup](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>Krok 1: Odstranění zálohovaných položek z konzoly pro správu MARS

Pokud nejste schopni provést tento krok z důvodu nedostupnosti serveru pak kontaktujte podporu Microsoftu.

- Spusťte konzolu pro správu MARS, přejděte na **akce** podokně a zvolte **naplánovat zálohování**.
- Z **upravit nebo ukončit naplánované zálohování** průvodce, zvolte možnost **přestat používat tento plán zálohování a odstranit všechny uložené zálohy** a klikněte na tlačítko **Další**.

    ![Upravit nebo ukončit naplánovaného zálohování](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- Z **zastavit naplánované zálohy** průvodce, klikněte na tlačítko **Dokončit**.

    ![Zastavit naplánovaného zálohování](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Zobrazí se výzva k zadání bezpečnostního kódu Pin. Pokud chcete generovat kód PIN, proveďte následujících kroků:
  - Přihlaste se k portálu Azure.
  - Přejděte do **trezor služby Recovery Services** > **nastavení** > **vlastnosti**.
  - V části **bezpečnostní kód PIN**, klikněte na tlačítko **generovat**. Zkopírujte tento kód PIN. (Tento PIN kód je platný pouze pět minut)
- V konzole pro správu (klientská aplikace) vložte kód PIN a klepněte na tlačítko **Ok**.

  ![Bezpečnostní kód Pin](./media/backup-azure-delete-vault/security-pin.png)

- V **upravit průběh zálohování** průvodce se zobrazí *data odstraněných záloh se uchovávají 14 dní. Po tomto datu se zálohovaná data se trvale odstraní.*  

    ![Odstranit infrastruktury zálohování](./media/backup-azure-delete-vault/deleted-backup-data.png)

Teď, když odstraníte zálohované položky z místního proveďte další kroky z portálu.

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>Krok 2: Z portálu odeberte agenta Azure Backup

Zajištění [kroku 1](#step-1-delete-backup-items-from-mars-management-console) je dokončit, než budete pokračovat:

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

> [!NOTE]
> Pokud se zobrazují níže chyby a proveďte kroky uvedené v první [odstranění zálohované položky z konzoly pro správu](#step-1-delete-backup-items-from-mars-management-console).
>
>![nepovedlo se odstranit](./media/backup-azure-delete-vault/deletion-failed.png)
>
>Pokud se nemůžete provést kroky, které chcete odstranit zálohy z konzoly pro správu, například z důvodu nedostupnosti serveru pomocí konzoly pro správu, kontaktujte podporu Microsoftu.

7. Zkontrolujte, **oznámení** ![odstranit data zálohy](./media/backup-azure-delete-vault/messages.png). Po dokončení se služba zobrazí zprávu: **Zastavuje se zálohování a odstranění zálohování dat pro "*zálohovaná položka*"** . **Operace se úspěšně dokončila**.
8. Klikněte na tlačítko **aktualizovat** na **zálohování položek** nabídky ke kontrole, pokud se zálohovaná položka se odebere.


### <a name="for-mabs-agent"></a>Pro MABS agenta

Pokud chcete zastavit ochranu a odstraňte zálohovaná data, proveďte kroky v níže uvedeném pořadí:

- [Krok 1: Odstranit z konzoly pro správu MABS zálohované položky](#step-1-delete-backup-items-from-mabs-management-console)
- [Krok 2: Z portálu odeberte servery pro správu Azure Backup](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>Krok 1: Odstranit z konzoly pro správu MABS zálohované položky

Pokud nejste schopni provést tento krok z důvodu nedostupnosti serveru pak kontaktujte podporu Microsoftu.

**Metoda 1** zastavit ochranu a odstranit data zálohy, proveďte následujících kroků:

1.  V konzole pro správu DPM klikněte na tlačítko **ochrany** na navigačním panelu.
2.  V podokně zobrazení vyberte člena skupiny ochrany, kterou chcete odebrat. Klikněte pravým tlačítkem a zvolte **zastavit členy skupiny ochrany** možnost.
3.  Z **zastavit ochranu** dialogu **odstranit chráněná data** > **odstranit úložiště online** zaškrtávací políčko a potom klikněte na tlačítko **zastavit Ochrana**.

    ![Odstranit úložiště online](./media/backup-azure-delete-vault/delete-storage-online.png)

Chráněný člen stav je teď změněná na **k dispozici je neaktivní replika**.

5. Klikněte pravým tlačítkem Neaktivní skupiny ochrany a vyberte **odebrat neaktivní ochranu**.

    ![Odebrat neaktivní ochranu](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. Z **odstranit neaktivní ochranu** okně **odstranit úložiště online** a klikněte na tlačítko **Ok**.

    ![Odstranění repliky na disku a online](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**Metoda 2** spustit **MABS správu** konzoly. V **vybrat způsob ochrany dat** části, zrušte výběr **chci online ochranu**.

  ![Vyberte způsob ochrany dat](./media/backup-azure-delete-vault/data-protection-method.png)

Teď, když odstraníte zálohované položky z místního proveďte další kroky z portálu.

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>Krok 2: Z portálu odeberte servery pro správu Azure Backup

Zajištění [kroku 1](#step-1-delete-backup-items-from-mabs-management-console) je dokončit, než budete pokračovat:

1. V nabídce řídicího panelu trezoru klikněte na tlačítko **infrastruktura zálohování**.
2. Klikněte na tlačítko **servery pro správu zálohování** k zobrazení serverů.

    ![Vyberte trezor otevřete jeho řídicí panel](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Klikněte pravým tlačítkem na položku > **odstranit**.
4. Na **odstranit** nabídku, zadejte název serveru a klikněte na tlačítko **odstranit**.

     ![Odstranit data zálohy](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Pokud se zobrazují níže chyby a proveďte kroky uvedené v první [odstranění zálohované položky z konzoly pro správu](#step-2-from-portal-remove-azure-backup-management-servers).
>
>![nepovedlo se odstranit](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Pokud se nemůžete provést kroky, které chcete odstranit zálohy z konzoly pro správu, například z důvodu nedostupnosti serveru pomocí konzoly pro správu, kontaktujte podporu Microsoftu.

5. Zkontrolujte, **oznámení** ![odstranit data zálohy](./media/backup-azure-delete-vault/messages.png). Po dokončení se služba zobrazí zprávu: **Zastavuje se zálohování a odstranění zálohování dat pro "*zálohovaná položka*"** . **Operace se úspěšně dokončila**.
6. Klikněte na tlačítko **aktualizovat** na **zálohování položek** nabídky ke kontrole, pokud se zálohovaná položka se odebere.


## <a name="delete-the-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

1. Když byly odebrány všechny závislosti, přejděte **Essentials** podokno v nabídce trezoru.
2. Ověřte, že nejsou k dispozici žádné **zálohování položek**, **zálohování serverů pro správu**, nebo **replikované položky** uvedené. Pokud se stále zobrazí položky v trezoru, [neodeberete](#delete-backup-data-and-backup-items).

3. Pokud nejsou žádné další položky v trezoru, na řídicím panelu trezoru klikněte na **odstranit**.

    ![Odstranit data zálohy](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Pokud chcete ověřit, že chcete trezor odstranit, klikněte na tlačítko **Ano**. Trezor se odstraní a portál se vrátí **nový** nabídku služby.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Odstranění trezoru služby Recovery Services pomocí klienta Azure Resource Manageru

Tuto možnost, chcete-li odstranit trezor služby Recovery Services se doporučuje jenom při se odeberou všechny závislosti a stále se zobrazuje *trezoru Chyba při odstraňování*.



- Z **Essentials** podokno v nabídce trezoru, ověřte, že nejsou k dispozici žádné **zálohování položek**, **zálohování serverů pro správu**, nebo **replikované položky** uvedené. Pokud nejsou zálohované položky, postupujte podle pokynů v [odstranit zálohovaná data a zálohované položky](#delete-backup-data-and-backup-items).
- Zkuste [odstranění trezoru z portálu](#delete-the-recovery-services-vault).
- Pokud se odeberou všechny závislosti a stále se zobrazuje *trezoru Chyba při odstraňování* pak pomocí nástroje ARMClient pro provést kroky uvedené níže;

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instalace chocolatey z [tady](https://chocolatey.org/) a k instalaci ARMClient spouštění následující příkaz:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Přihlaste se ke svému účtu Azure a spusťte tento příkaz:

    `ARMClient.exe login [environment name]`

3. Na webu Azure Portal shromážděte názvu předplatného ID a zdroj skupiny pro trezor, který chcete odstranit.

Další informace o příkazu ARMClient najdete na tomto [dokumentu](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Pomocí klienta Azure Resource Manageru můžete odstranit trezor služby Recovery Services

1. Spusťte následující příkaz pomocí ID předplatného, název skupiny prostředků a název trezoru. Při spuštění příkazu odstraní trezor, pokud nemáte žádné závislosti.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Pokud úložiště není prázdná, zobrazí se chyba "Trezor nejde odstranit, protože stávající prostředky v rámci tohoto trezoru". Chcete-li odebrat chráněných položek / kontejner v trezoru, postupujte takto:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Na webu Azure Portal ověřte, že se odstraní.


## <a name="next-steps"></a>Další postup

[Další informace o](backup-azure-recovery-services-vault-overview.md) trezory služby Recovery Services.<br/>
[Další informace o](backup-azure-manage-windows-server.md) monitorování a správa trezorů služby Recovery Services.
