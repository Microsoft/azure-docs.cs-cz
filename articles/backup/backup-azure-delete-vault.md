---
title: Odstranění trezoru Microsoft Azure Backup Recovery Services
description: Tento článek popisuje, jak odstranit Microsoft Azure Backup trezoru Recovery Services.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: dacurwin
ms.openlocfilehash: a49449f799696ce6962afea6bdc212f658c660bd
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860365"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Odstranění trezoru Azure Backup Recovery Services

Tento článek popisuje, jak odstranit trezor služby Microsoft [Azure Backup](backup-overview.md) Recovery Services (MARS). Obsahuje pokyny pro odebrání závislostí a odstranění trezoru.


## <a name="before-you-start"></a>Než začnete

Nelze odstranit Recovery Services trezor, který obsahuje závislosti, jako jsou například chráněné servery nebo servery pro správu zálohování, které jsou k němu přidruženy.

- Trezory obsahující zálohovaná data se nedají odstranit (i když jste zastavili ochranu, ale zachovali data ze zálohy).

- Pokud odstraníte trezor, který obsahuje závislosti, zobrazí se následující zpráva:

  ![Odstraňte chybu trezoru.](./media/backup-azure-delete-vault/error.png)

- Pokud odstraníte místní chráněnou položku z portálu, který obsahuje závislosti, zobrazí se varovná zpráva:

  ![Odstraňte chybu chráněného serveru.](./media/backup-azure-delete-vault/error-message.jpg)

  
Pokud chcete trezor odstranit, vyberte scénář, který odpovídá vašemu nastavení, a postupujte podle doporučených kroků:

Scénář | Postup odebrání závislostí pro odstranění trezoru |
-- | --
Mám místní soubory a složky chráněné pomocí agenta Azure Backup, který se zálohuje do Azure | Proveďte kroky v [části odstranění zálohových položek z konzoly pro správu Mars](#delete-backup-items-from-the-mars-management-console) .
Mám místní počítače, které jsou chráněné pomocí MABS (Microsoft Azure Backup serveru) nebo DPM (System Center Data Protection Manager) do Azure | Proveďte kroky v [části odstranění zálohovaných položek v konzole pro správu MABS](#delete-backup-items-from-the-mabs-management-console) .
Mám chráněné položky v cloudu (například virtuální počítač s laaS nebo sdílenou složku Azure Files)  | Provedení kroků v [části odstranění chráněných položek v cloudu](#delete-protected-items-in-the-cloud)
Mám chráněné položky jak místně, tak i v cloudu | Proveďte kroky ve všech následujících částech v tomto pořadí: <br> 1. [Odstranění chráněných položek v cloudu](#delete-protected-items-in-the-cloud)<br> 2. [Odstranit zálohované položky z konzoly pro správu MARS](#delete-backup-items-from-the-mars-management-console) <br> 3. [Odstranit zálohované položky z konzoly pro správu MABS](#delete-backup-items-from-the-mabs-management-console)
Nepoužívám místní nebo cloudové chráněné položky; Pořád se ale stále zobrazuje chyba odstraňování trezoru | Proveďte kroky v části [odstranění Recovery Servicesového trezoru pomocí Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager)


## <a name="delete-protected-items-in-the-cloud"></a>Odstranění chráněných položek v cloudu

Nejprve si přečtěte část **[než začnete](#before-you-start)** , abyste porozuměli závislostem a procesu odstranění trezoru.

Chcete-li zastavit ochranu a odstranit data zálohy, proveďte následující kroky:

1. Na portálu přejdete do **Recovery Services trezoru**a pak přejdete na **zálohované položky**. Pak zvolte chráněné položky v cloudu (například Azure Virtual Machines, Azure Storage [služba soubory Azure] nebo SQL Server na Virtual Machines Azure).

    ![Vyberte typ zálohování.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Kliknutím pravým tlačítkem vyberte zálohovanou položku. V závislosti na tom, jestli je zálohovaná položka chráněná, nebo ne, se v nabídce zobrazí buď podokno **Zastavit zálohování** , nebo **Odstranit data zálohy** .

    - Pokud se zobrazí podokno **Zastavit zálohování** , v rozevírací nabídce vyberte **Odstranit zálohovaná data** . Zadejte název zálohované položky (Toto pole rozlišuje velká a malá písmena) a pak z rozevírací nabídky vyberte důvod. Pokud nějaké máte, zadejte svoje komentáře. Pak vyberte **Zastavit zálohování**.

        ![Podokno zastavit zálohování.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Pokud se zobrazí podokno **Odstranit zálohovaná data** , zadejte název zálohované položky (Toto pole rozlišuje velká a malá písmena) a pak z rozevírací nabídky vyberte důvod. Pokud nějaké máte, zadejte svoje komentáře. Pak vyberte **Odstranit**. 

         ![Podokno odstranit zálohovaná data.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Ověřte ikonu **oznámení** : ![Ikona oznámení](./media/backup-azure-delete-vault/messages.png) Po dokončení procesu se ve službě zobrazí tato zpráva: *Zastavuje se zálohování a odstraňují se záložní data pro* : Zálohovaná*položka.* *Operace se úspěšně dokončila*.
6. V nabídce **zálohované položky** vyberte **aktualizovat** a ujistěte se, že se zálohovaná položka odstranila.

      ![Stránka Odstranit zálohované položky](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Odstranění chráněných položek místně

Nejprve si přečtěte část **[než začnete](#before-you-start)** , abyste porozuměli závislostem a procesu odstranění trezoru.

1. V nabídce řídicího panelu trezoru vyberte **infrastruktura zálohování**.
2. V závislosti na vašem místním scénáři vyberte jednu z následujících možností:

      - V případě MARS vyberte **chráněné servery** a pak **Azure Backup agenta**. Pak vyberte server, který chcete odstranit. 

        ![V případě MARS vyberte svůj trezor a otevřete jeho řídicí panel.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Pro MABS nebo DPM vyberte **záložní servery pro správu**. Pak vyberte server, který chcete odstranit. 


          ![V případě MABS vyberte svůj trezor a otevřete jeho řídicí panel.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Otevře se podokno **Odstranit** se zprávou upozornění.

     ![Podokno odstranění.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Přečtěte si zprávu s upozorněním a pokyny v tématu týkajícím se souhlasu.
    > [!NOTE]
    >- Pokud je chráněný Server synchronizovaný se službami Azure a existují zálohované položky, zobrazí se v políčku pro souhlas počet závislých zálohových položek a odkaz na zobrazení zálohovaných položek.
    >- Pokud není chráněný Server synchronizovaný se službami Azure a existují zálohované položky, zobrazí se v políčku pro souhlas jenom počet zálohovaných položek.
    >- Pokud nejsou k dispozici žádné zálohované položky, vyzve se zaškrtávací políčko pro odstranění souhlasu.

4. Zaškrtněte políčko souhlasu a pak vyberte **Odstranit**.


5. Podívejte se na ikonu ![oznámení odstranit zálohovaná data](./media/backup-azure-delete-vault/messages.png). Po dokončení operace služba zobrazí zprávu: *Zastavuje se zálohování a odstraňují se zálohovaná data pro "zálohovanou položku".* *Operace se úspěšně dokončila*.
6. V nabídce **zálohované položky** vyberte **aktualizovat** a ujistěte se, že se zálohovaná položka odstranila.

Po dokončení tohoto procesu můžete odstranit zálohované položky z konzoly pro správu:
    
   - [Odstranit zálohované položky z konzoly pro správu MARS](#delete-backup-items-from-the-mars-management-console)
    - [Odstranit zálohované položky z konzoly pro správu MABS](#delete-backup-items-from-the-mabs-management-console)


### <a name="delete-backup-items-from-the-mars-management-console"></a>Odstranit zálohované položky z konzoly pro správu MARS

1. Otevřete konzolu pro správu MARS, klikněte na podokno **Akce** a vyberte **naplánovat zálohování**.
2. Na stránce **změnit nebo zastavit naplánované zálohování** vyberte možnost **ukončit používání tohoto plánu zálohování a odstraňte všechny uložené zálohy**. Pak vyberte **Next** (Další).

    ![Upravte nebo zastavte naplánované zálohování.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na stránce **zastavit naplánované zálohování** vyberte **Dokončit**.

    ![Zastavte naplánované zálohování.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Zobrazí se výzva k zadání bezpečnostního kódu PIN (osobní identifikační číslo), které musíte vygenerovat ručně. Abyste to mohli udělat, nejdřív se přihlaste k Azure Portal.
5. Přejít na > **vlastnosti** **Nastavení** >  **trezoru Recovery Services**.
6. V části **bezpečnostní kód PIN**vyberte **Generovat**. Zkopírujte tento kód PIN. PIN kód je platný jenom po dobu pěti minut.
7. V konzole pro správu vložte kód PIN a pak vyberte **OK**.

    ![Vygenerujte bezpečnostní kód PIN.](./media/backup-azure-delete-vault/security-pin.png)

8. Na stránce **Upravit průběh zálohování** se zobrazí následující zpráva: *Data odstraněných záloh se uchovávají po dobu 14 dnů. Po uplynutí této doby se zálohovaná data trvale odstraní.*  

    ![Odstraňte infrastrukturu zálohování.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Po odstranění místních zálohových položek postupujte podle dalších kroků na portálu.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Odstranit zálohované položky z konzoly pro správu MABS

Existují dvě metody, které můžete použít k odstranění zálohovaných položek z konzoly pro správu MABS.

#### <a name="method-1"></a>Metoda 1
Chcete-li zastavit ochranu a odstranit data zálohy, proveďte následující kroky:

1.  Otevřete Konzola správce aplikace DPM a pak na navigačním panelu vyberte **ochrana** .
2.  V podokně zobrazení vyberte člena skupiny ochrany, kterého chcete odebrat. Kliknutím pravým tlačítkem vyberte možnost **Zastavit ochranu členů skupiny** .
3.  V dialogovém okně **Zastavit ochranu** vyberte možnost **Odstranit chráněná data**a potom zaškrtněte políčko **Odstranit úložiště online** . Pak vyberte **Zastavit ochranu**.

    ![V podokně zastavit ochranu vyberte Odstranit chráněná data.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Stav chráněných členů se změní na *neaktivní repliku, která je k dispozici*.

4. Klikněte pravým tlačítkem na neaktivní skupinu ochrany a vyberte možnost **Odebrat neaktivní ochranu**.

    ![Odebrat neaktivní ochranu.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. V okně **Odstranit neaktivní ochranu** zaškrtněte políčko **Odstranit online úložiště** a pak vyberte **OK**.

    ![Odstraňte online úložiště.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Metoda 2
Otevřete konzolu **pro správu MABS** . V části **Vybrat způsob ochrany dat**zrušte zaškrtnutí políčka **Chci online ochranu** .

  ![Vyberte metodu ochrany dat.](./media/backup-azure-delete-vault/data-protection-method.png)

Po odstranění místních zálohových položek postupujte podle dalších kroků na portálu.


## <a name="delete-the-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

1. Po odebrání všech závislostí se posuňte do podokna **základy** v nabídce trezor.
2. Ověřte, že v seznamu nejsou žádné zálohované položky, servery pro správu zálohování ani replikované položky. Pokud se položky v trezoru stále zobrazují, přečtěte si informace v části [než začnete](#before-you-start) .

3. Pokud v trezoru nejsou žádné další položky, vyberte **Odstranit** na řídicím panelu trezoru.

    ![Vyberte Odstranit na řídicím panelu trezoru.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Vyberte **Ano** , pokud chcete ověřit, že chcete trezor odstranit. Trezor se odstraní. Portál se vrátí do nabídky **Nová** služba.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Odstranění trezoru Recovery Services pomocí prostředí PowerShell

Nejprve si přečtěte část **[než začnete](#before-you-start)** , abyste porozuměli závislostem a procesu odstranění trezoru.

Zastavení ochrany a odstranění zálohovaných dat:

- Pokud používáte SQL v zálohování virtuálních počítačů Azure a povolíte automatickou ochranu pro instance SQL, nejdřív vypněte automatickou ochranu.

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

  [Další informace](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) o tom, jak zakázat ochranu pro položku chráněnou Azure Backup 

- Zastavte ochranu a odstraňte data pro všechny položky chráněné zálohováním v cloudu (např. virtuální počítač laaS, sdílení souborů Azure atd.):

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
    [Přečtěte si další](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) informace o zakázání ochrany pro položku chráněnou zálohováním. 

- Pro místní soubory a složky chráněné pomocí agenta Azure Backup, který se zálohuje do Azure, pomocí následujícího příkazu PowerShellu odstraňte zálohovaná data z každého modulu MARS PowerShellu:

    ```
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Příspěvek, který se zobrazí na následující příkazovém řádku:
     
    *Microsoft Azure Backup jste si jisti, že chcete odebrat tyto zásady zálohování? Data odstraněných záloh se uchovávají po dobu 14 dnů. Po uplynutí této doby se zálohovaná data trvale odstraní. <br/> [Y] Ano [A] Ano všem [N] ne [L] No všem [S] pozastavit [?] Help (výchozí je "Y"):*


- U místních počítačů chráněných pomocí MABS (Microsoft Azure Backup serveru) nebo DPM do Azure (System Center Data Protection Manager) pomocí následujícího příkazu odstraňte zálohovaná data v Azure.

    ```
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin> 
    ```

    Příspěvek, který se zobrazí na následující příkazovém řádku: 
         
   *Microsoft Azure Backup jste si jisti, že chcete odebrat tyto zásady zálohování? Data odstraněných záloh se uchovávají po dobu 14 dnů. Po uplynutí této doby se zálohovaná data trvale odstraní. <br/> [Y] Ano [A] Ano všem [N] ne [L] No všem [S] pozastavit [?] Help (výchozí je "Y"):*

Po odstranění zálohovaných dat zrušte registraci místních kontejnerů a serverů pro správu. 

- Pro místní soubory a složky chráněné pomocí agenta Azure Backup (MARS) zálohování do Azure:

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
    [Přečtěte si další informace](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) o zrušení registrace Windows serveru nebo jiného kontejneru z trezoru. 

- Pro místní počítače chráněné pomocí MABS (Microsoft Azure Backup serveru) nebo DPM do Azure (System Center Data Protection Manage:

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

    [Přečtěte si další informace](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) o zrušení registrace kontejneru správy zálohování z trezoru.

Po trvalém odstranění zálohovaných dat a zrušení registrace všech kontejnerů přejděte k odstranění trezoru. 

Odstranění trezoru Recovery Services: 

   ```PowerShell
       Remove-AzRecoveryServicesVault 
      -Vault <ARSVault> 
      [-DefaultProfile <IAzureContextContainer>] 
      [-WhatIf] 
      [-Confirm] 
      [<CommonParameters>]        
   ```

[Přečtěte si další informace](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) o odstranění trezoru služby Recovery Services. 

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Odstranění Recovery Servicesho trezoru pomocí rozhraní příkazového řádku

Nejprve si přečtěte část **[než začnete](#before-you-start)** , abyste porozuměli závislostem a procesu odstranění trezoru.

> [!NOTE]
> V současné době Azure Backup CLI podporuje správu jenom záloh virtuálních počítačů Azure, takže následující příkaz k odstranění trezoru funguje jenom v případě, že trezor obsahuje zálohy virtuálních počítačů Azure. Trezor nelze odstranit pomocí Azure Backup CLI, pokud trezor obsahuje libovolnou zálohovanou položku jiného typu než virtuální počítače Azure. 

Pokud chcete odstranit existující trezor služby Recovery Services, udělejte toto: 

- Zastavení ochrany a odstranění zálohovaných dat 

    ```CLI
    az backup protection disable --container-name 
                             --item-name 
                             [--delete-backup-data {false, true}] 
                             [--ids] 
                             [--resource-group] 
                             [--subscription] 
                             [--vault-name] 
                             [--yes] 
    ```

    Další informace najdete v tomto [článku](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable.). 

- Odstranit existující trezor služby Recovery Services: 

    ```CLI
    az backup vault delete [--force] 
                       [--ids] 
                       [--name] 
                       [--resource-group] 
                       [--subscription] 
                       [--yes] 
    ```

    Další informace najdete v tomto [článku](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest) . 

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Odstranění trezoru Recovery Services pomocí Azure Resource Manager

Tato možnost odstranění trezoru Recovery Services se doporučuje jenom v případě, že se odeberou všechny závislosti a pořád dojde k *chybě odstranění trezoru*. Zkuste použít některé z následujících tipů:

- V podokně **základy** v nabídce trezoru ověřte, že v seznamu nejsou žádné zálohované položky, servery pro správu zálohování nebo replikované položky. Pokud jsou k dispozici zálohované položky, přečtěte si část [před zahájením](#before-you-start) .
- Zkuste [trezor odstranit znovu z portálu](#delete-the-recovery-services-vault) .
- Pokud se všechny závislosti odeberou a pořád dojde k *chybě odstranění trezoru*, použijte nástroj ARMClient k provedení následujících kroků (po tom, kde se nachází Poznámka).

1. Pokud si chcete stáhnout a nainstalovat čokoláda, navštivte [Chocolatey.org](https://chocolatey.org/) . Pak nainstalujte ARMClient spuštěním následujícího příkazu:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Přihlaste se ke svému účtu Azure a pak spusťte následující příkaz:

    `ARMClient.exe login [environment name]`

3. V Azure Portal Shromážděte ID předplatného a název skupiny prostředků pro trezor, který chcete odstranit.

Další informace o příkazu ARMClient najdete v [souboru Readme pro ARMClient](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Použití klienta Azure Resource Manager k odstranění trezoru Recovery Services

1. Spusťte následující příkaz pomocí ID vašeho předplatného, názvu skupiny prostředků a názvu trezoru. Pokud nemáte žádné závislosti, odstraní se trezor, když spustíte následující příkaz:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Pokud trezor není prázdný, zobrazí se následující chybová zpráva: *Trezor se nedá odstranit, protože v tomto trezoru jsou nějaké prostředky.* Chcete-li odebrat chráněnou položku nebo kontejner v rámci trezoru, spusťte následující příkaz:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. V Azure Portal se ujistěte, že je trezor odstraněný.


## <a name="next-steps"></a>Další kroky

[Další informace o trezorech Recovery Services](backup-azure-recovery-services-vault-overview.md)<br/>
[Seznamte se s monitorováním a správou trezorů Recovery Services.](backup-azure-manage-windows-server.md)
