---
title: Odstranění trezoru služby Microsoft Azure Recovery Services
description: V tomto článku se dozvíte, jak odebrat závislosti a potom odstranit trezor Mars (Microsoft Azure Backup Recovery Services).
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: f33f52048729b50015ba86db71118b9a21e1a2fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500403"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Odstranění trezoru služby Obnovení zálohování Azure

Tento článek popisuje, jak odstranit trezor služby Mars (Microsoft [Azure Backup](backup-overview.md) Recovery Services). Obsahuje pokyny pro odebrání závislostí a následné odstranění trezoru.

## <a name="before-you-start"></a>Než začnete

Trezor služby Recovery Services, který má přidružené závislosti, například chráněné servery nebo servery pro správu zálohování, nelze odstranit.

- Trezory, které obsahují záložní data, nelze odstranit (i když jste ochranu zastavili, ale zachováte záložní data).

- Pokud odstraníte trezor, který obsahuje závislosti, zobrazí se následující zpráva:

  ![Odstraňte chybu úschovny.](./media/backup-azure-delete-vault/error.png)

- Pokud odstraníte místní chráněnou položku z portálu, který obsahuje závislosti, zobrazí se varovná zpráva:

  ![Odstraňte chybu chráněného serveru.](./media/backup-azure-delete-vault/error-message.jpg)

- Pokud jsou položky zálohování v obnovitelném odstraněném stavu, zobrazí se varovná zpráva a budete muset počkat, dokud nebudou trvale odstraněny. Další informace naleznete v tomto [článku](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud).

   ![Odstraňte chybu úschovny.](./media/backup-azure-delete-vault/error-message-soft-delete.png)

- Trezory, které mají registrované účty úložiště, nelze odstranit. Informace o tom, jak zrušit registraci účtu, najdete v [tématu Zrušení registrace účtu úložiště](manage-afs-backup.md#unregister-a-storage-account).
  
Chcete-li trezor odstranit, zvolte scénář, který odpovídá vašemu nastavení, a postupujte podle doporučených kroků:

Scénář | Postup odebrání závislostí k odstranění trezoru |
-- | --
Mám místní soubory a složky chráněné pomocí agenta Azure Backup, zálohování do Azure | Provedení kroků v části [Odstranění záložních položek z konzoly pro správu MARS](#delete-backup-items-from-the-mars-management-console)
Mám místní počítače, které jsou chráněné pomocí MABS (Microsoft Azure Backup Server) nebo DPM (System Center Data Protection Manager) do Azure | Provedení kroků v části [Odstranění zálohovacích položek z konzoly pro správu MABS](#delete-backup-items-from-the-mabs-management-console)
Mám chráněné položky v cloudu (například laaS virtuální počítač nebo sdílené složky Azure Files)  | Provedení kroků v části [Odstranění chráněných položek v cloudu](#delete-protected-items-in-the-cloud)
Mám chráněné položky jak v místním prostředí, tak v cloudu | Kroky proveďte ve všech následujících částech v následujícím pořadí: <br> 1. [Odstranění chráněných položek v cloudu](#delete-protected-items-in-the-cloud)<br> 2. [Odstranění položek zálohování z konzoly pro správu MARS](#delete-backup-items-from-the-mars-management-console) <br> 3. [Odstranění zálohovacích položek z konzoly MABS Management](#delete-backup-items-from-the-mabs-management-console)
Nemám žádné chráněné položky v místním prostředí nebo cloudu; Stále se mi však zobrazuje chyba odstranění úložiště | Provedení kroků v [části Odstranění trezoru služby Recovery Services pomocí Správce prostředků Azure](#delete-the-recovery-services-vault-by-using-azure-resource-manager) <br><br> Ujistěte se, že v trezoru nejsou registrovány žádné účty úložiště. Informace o tom, jak zrušit registraci účtu, najdete v [tématu Zrušení registrace účtu úložiště](manage-afs-backup.md#unregister-a-storage-account).

## <a name="delete-protected-items-in-the-cloud"></a>Odstranění chráněných položek v cloudu

Nejprve si přečtěte část **[Před spuštěním,](#before-you-start)** abyste pochopili proces odstranění závislostí a trezoru.

Chcete-li ochranu zastavit a odstranit záložní data, proveďte následující kroky:

1. Na portálu přejděte do **trezoru služby Recovery Services**a potom přejděte na **položky zálohování**. Pak zvolte chráněné položky v cloudu (například Virtuální počítače Azure, Azure Storage [služba Azure Files] nebo SQL Server na virtuálních počítačích Azure).

    ![Vyberte typ zálohy.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Klepnutím pravým tlačítkem myši vyberte položku zálohy. V závislosti na tom, zda je položka zálohování chráněna či nikoli, se v nabídce zobrazí podokno **Zastavit zálohování** nebo Odstranit **data zálohování.**

    - Pokud se zobrazí podokno **Zastavit zálohování,** vrozené nabídce vyberte **Odstranit záložní data.** Zadejte název zálohovací položky (toto pole rozlišuje malá a velká písmena) a v rozevírací nabídce vyberte důvod. Zadejte své připomínky, pokud máte nějaké. Potom vyberte **Zastavit zálohování**.

        ![Podokno Zastavit zálohování.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Pokud se zobrazí podokno **Odstranit záložní data,** zadejte název položky zálohy (toto pole rozlišuje malá a velká písmena) a v rozevírací nabídce vyberte důvod. Zadejte své připomínky, pokud máte nějaké. Potom vyberte **Odstranit**.

         ![Podokno Odstranit záložní data.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. Zaškrtněte ikonu ![ **Oznámení:** Ikona Oznámení.](./media/backup-azure-delete-vault/messages.png) Po dokončení procesu se zobrazí následující zpráva: *Zastavení zálohování a odstranění záložních dat pro položku*zálohování *.* *Operace byla úspěšně dokončena*.
4. V yberte **Aktualizovat** v nabídce **Položky zálohování,** abyste se ujistili, že byla položka zálohy odstraněna.

      ![Stránka Odstranit položky zálohování.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Odstranění chráněných položek v místním prostředí

Nejprve si přečtěte část **[Před spuštěním,](#before-you-start)** abyste pochopili proces odstranění závislostí a trezoru.

1. V nabídce řídicího panelu úschovny vyberte **možnost Zálohovat infrastrukturu**.
2. V závislosti na místním scénáři zvolte jednu z následujících možností:

      - V případě MARS vyberte **chráněné servery** a potom **agenta zálohování Azure**. Potom vyberte server, který chcete odstranit.

        ![V případě zařízení MARS vyberte trezor a otevřete řídicí panel.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - V případě MABS nebo DPM vyberte **možnost Servery pro správu zálohování**. Potom vyberte server, který chcete odstranit.

          ![V případě MABS vyberte trezor a otevřete řídicí panel.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Zobrazí se podokno **Odstranit** s varovnou zprávou.

     ![Podokno odstranění.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Zkontrolujte varovnou zprávu a pokyny v zaškrtávacím políčku souhlas.
    > [!NOTE]
    >- Pokud je chráněný server synchronizován se službami Azure a existují položky zálohování, zaškrtávací políčko souhlas zobrazí počet závislých položek zálohování a odkaz pro zobrazení položek zálohování.
    >- Pokud chráněný server není synchronizován se službami Azure a existují položky zálohování, zaškrtávací políčko souhlas zobrazí pouze počet položek zálohování.
    >- Pokud neexistují žádné položky zálohování, zaškrtávací políčko souhlas požádá o odstranění.

4. Zaškrtněte políčko Souhlas a pak vyberte **Odstranit**.

5. Zkontrolujte **Notification** ikonu ![Oznámení](./media/backup-azure-delete-vault/messages.png)odstranit záložní data . Po dokončení operace se zobrazí zpráva: *Zastavení zálohování a odstranění záložních dat pro položku zálohování.* *Operace byla úspěšně dokončena*.
6. V yberte **Aktualizovat** v nabídce **Položky zálohování,** abyste se ujistili, že je položka zálohy odstraněna.

Po dokončení tohoto procesu můžete odstranit položky zálohování z konzoly pro správu:

- [Odstranění položek záloh ze konzole pro správu MARS](#delete-backup-items-from-the-mars-management-console)
- [Odstranění položek záloh ze konzole pro správu MABS](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Odstranění položek záloh ze konzole pro správu MARS

1. Otevřete konzolu pro správu MARS, přejděte do podokna **Akce** a vyberte **Naplánovat zálohování**.
2. Na stránce **Změnit nebo Zastavit naplánované zálohování** vyberte Zastavit používání tohoto plánu zálohování a **odstraňte všechny uložené zálohy**. Potom vyberte **Další**.

    ![Upravte nebo zastavte naplánované zálohování.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na stránce **Zastavit naplánované zálohování** vyberte **Dokončit**.

    ![Zastavte naplánované zálohování.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Budete vyzváni k zadání bezpečnostního kódu PIN (osobního identifikačního čísla), který musíte vygenerovat ručně. Chcete-li to provést, nejprve se přihlaste k portálu Azure.
5. Přejděte na**vlastnosti****nastavení** >  **úložiště** > služby Recovery Services .
6. V části **Bezpečnostní KÓD PIN**vyberte **Generovat**. Zkopírujte tento KÓD PIN. PIN je platný pouze pět minut.
7. Do konzoly pro správu vložte kód PIN a pak vyberte **OK**.

    ![Vygenerujte bezpečnostní KÓD PIN.](./media/backup-azure-delete-vault/security-pin.png)

8. Na stránce **Změnit průběh zálohování** se zobrazí následující zpráva: *Odstraněná záložní data budou zachována po dobu 14 dnů. Po uplynutí této doby budou data zálohování trvale odstraněna.*  

    ![Odstraňte infrastrukturu zálohování.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Po odstranění položek zálohování v místním prostředí postupujte podle dalších kroků z portálu.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Odstranění položek záloh ze konzole pro správu MABS

Existují dvě metody, které můžete použít k odstranění položek zálohování z konzoly pro správu MABS.

#### <a name="method-1"></a>Metoda 1

Chcete-li ochranu zastavit a odstranit záložní data, postupujte takto:

1. Otevřete konzolu dpm administrator console a na navigačním panelu vyberte **Možnost Ochrana.**
2. V podokně zobrazení vyberte člena skupiny ochrany, který chcete odebrat. Klepnutím pravým tlačítkem myši vyberte možnost **Zastavit ochranu členů skupiny.**
3. V dialogovém okně **Zastavit ochranu** vyberte **Odstranit chráněná data**a zaškrtněte políčko Odstranit úložiště **online.** Potom vyberte **Zastavit ochranu**.

    ![V podokně Zastavit ochranu vyberte Odstranit chráněná data.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Stav chráněného člena se změní na *neaktivní repliku, která je k dispozici*.

4. Klepněte pravým tlačítkem myši na neaktivní skupinu ochrany a vyberte příkaz **Odebrat neaktivní ochranu**.

    ![Odstraňte neaktivní ochranu.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. V okně **Odstranit neaktivní ochranu** zaškrtněte políčko **Odstranit online úložiště** a pak vyberte **OK**.

    ![Odstraňte online úložiště.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Metoda 2

Otevřete konzolu **pro správu MABS.** V **části Vybrat metodu ochrany dat**zrušte zaškrtnutí políčka **Požadovaná ochrana online.**

  ![Vyberte metodu ochrany dat.](./media/backup-azure-delete-vault/data-protection-method.png)

Po odstranění položek zálohování v místním prostředí postupujte podle dalších kroků z portálu.

## <a name="delete-the-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

1. Po odebrání všech závislostí přejděte v nabídce trezoru do podokna **Essentials.**
2. Ověřte, zda nejsou uvedeny žádné položky zálohování, servery pro správu zálohování nebo replikované položky. Pokud se položky v úschovně stále zobrazují, podívejte se do části [Před spuštěním.](#before-you-start)

3. Pokud v úschovně nejsou žádné další položky, **vyberte** odstranit na řídicím panelu úschovny.

    ![Na řídicím panelu trezoru vyberte Odstranit.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Výběrem **možnosti Ano** ověřte, zda chcete trezor odstranit. Trezor je odstraněn. Portál se vrátí do nabídky **Nová** služba.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Odstranění trezoru služby Recovery Services pomocí prostředí PowerShell

Nejprve si přečtěte část **[Před spuštěním,](#before-you-start)** abyste pochopili proces odstranění závislostí a trezoru.

Ukončení ochrany a odstranění záložních dat:

- Pokud používáte sql v azure virtuálních počítačích zálohování a povolené automatické ochrany pro instance SQL, nejprve zakázat automatickou ochranu.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  [Další informace](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) o tom, jak zakázat ochranu položky chráněné službou Azure Backup.

- Zastavte ochranu a odstraňte data pro všechny položky chráněné zálohováním v cloudu (např. laaS VM, Azure File Share atd.):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [Další informace](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) o zakázání ochrany položky chráněné zálohováním.

- Pro místní soubory a složky chráněné pomocí azure backup agenta (MARS) zálohování do Azure, použijte následující příkaz PowerShell odstranit zálohovaná data z každého modulu MARS PowerShell:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Zaúčtovat, kde se zobrazí následující výzva:

    *Zálohování Microsoft Azure Opravdu chcete tuto zásadu zálohování odebrat? Odstraněná záložní data budou uchovávána po dobu 14 dnů. Po uplynutí této doby budou data zálohování trvale odstraněna. <br/> [Y] Ano [A] Ano všem [N] Ne [L] Ne všem [S] Pozastavit [?] Nápověda (výchozí hodnota je "Y"):*

- Pro místní počítače chráněné pomocí MABS (Microsoft Azure Backup Server) nebo DPM do Azure (Správce ochrany dat system center) použijte následující příkaz k odstranění zálohovaných dat v Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Zaúčtovat, kde se zobrazí následující výzva:

   *Zálohování Microsoft Azure* Opravdu chcete odebrat tuto zásadu zálohování? Odstraněná záložní data budou uchovávána po dobu 14 dnů. Po uplynutí této doby budou data zálohování trvale odstraněna. <br/>
   [Y] Ano [A] Ano všem [N] Ne [L] Ne všem [S] Pozastavit [?] Nápověda (výchozí hodnota je "Y"):*

Po odstranění zálohovaných dat odevníte všechny místní kontejnery a servery pro správu.

- Pro místní soubory a složky chráněné pomocí Azure Backup Agent (MARS) zálohování do Azure:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    [Přečtěte si další informace](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) o zrušení registrace systému Windows Server nebo jiného kontejneru z úložiště.

- Pro místní počítače chráněné pomocí MABS (Microsoft Azure Backup Server) nebo DPM do Azure (Správa ochrany dat system center:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [Přečtěte si další informace](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) o zrušení registrace kontejneru pro správu zálohování z trezoru.

Po trvalém odstranění zálohovaných dat a zrušení registrace všech kontejnerů pokračujte odstraněním úschovny.

Odstranění trezoru služby Recovery Services:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Přečtěte si další informace](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) o odstranění trezoru služeb pro obnovení.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Odstranění trezoru služby Recovery Services pomocí příkazového příkazu k řilišti

Nejprve si přečtěte část **[Před spuštěním,](#before-you-start)** abyste pochopili proces odstranění závislostí a trezoru.

> [!NOTE]
> V současné době azure backup CLI podporuje správu pouze zálohy virtuálních počítačových společností Azure, takže následující příkaz k odstranění trezoru funguje jenom v případě, že trezor obsahuje zálohy virtuálních počítačových společností Azure. Trezor nelze odstranit pomocí azure backup CLI, pokud trezor obsahuje všechny položky zálohování typu než virtuální počítače Azure.

Chcete-li odstranit existující trezor služeb obnovení, proveďte následující:

- Ukončení ochrany a odstranění záložních dat

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Další informace naleznete v tomto [článku](/cli/azure/backup/protection#az-backup-protection-disable).

- Odstranění existujícího trezoru služeb obnovení:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Další informace naleznete v tomto [článku](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Odstranění trezoru služby Recovery Services pomocí Správce prostředků Azure

Tato možnost odstranění trezoru služby Recovery Services se doporučuje pouze v případě, že jsou odebrány všechny závislosti a stále dochází k *chybě odstranění úložiště*. Vyzkoušejte některé nebo všechny následující tipy:

- V podokně **Essentials** v nabídce úschovny ověřte, zda nejsou uvedeny žádné položky zálohování, servery pro správu zálohování nebo replikované položky. Pokud existují položky zálohování, podívejte se do části [Před spuštěním.](#before-you-start)
- Zkuste trezor z portálu znovu [smažit.](#delete-the-recovery-services-vault)
- Pokud jsou odebrány všechny závislosti a stále se zobrazuje *chyba odstranění úložiště*, použijte nástroj ARMClient k provedení následujících kroků (za poznámkou).

1. Přejděte na [chocolatey.org](https://chocolatey.org/) a stáhněte a nainstalujte Chocolatey. Potom nainstalujte ARMClient spuštěním následujícího příkazu:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Přihlaste se ke svému účtu Azure a spusťte následující příkaz:

    `ARMClient.exe login [environment name]`

3. Na webu Azure Portal shromážděte ID předplatného a název skupiny prostředků pro trezor, který chcete odstranit.

Další informace o příkazu ARMClient naleznete v tématu [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services pomocí klienta Správce prostředků Azure

1. Spusťte následující příkaz pomocí ID předplatného, názvu skupiny prostředků a názvu úložiště. Pokud nemáte žádné závislosti, trezor se při spuštění následujícího příkazu odstraní:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. Pokud úschovna není prázdná, zobrazí se následující chybová zpráva: *Trezor nelze odstranit, protože v tomto trezoru existují existující prostředky.* Chcete-li odebrat chráněnou položku nebo kontejner v rámci úschovny, spusťte následující příkaz:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Na webu Azure Portal zkontrolujte, jestli je trezor odstraněn.

## <a name="next-steps"></a>Další kroky

[Další informace o trezorech služby Recovery Services](backup-azure-recovery-services-vault-overview.md)<br/>
[Informace o monitorování a správě trezorů služby Recovery Services](backup-azure-manage-windows-server.md)
