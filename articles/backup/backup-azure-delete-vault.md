---
title: Odstranění trezoru Microsoft Azure Recovery Services
description: V tomto článku se dozvíte, jak odebrat závislosti a jak odstranit Azure Backup trezoru Recovery Services.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: fd941db933d243b83c1c19c7ae0fdfc2d7869b8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293063"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Odstranění trezoru Azure Backup Recovery Services

Tento článek popisuje, jak odstranit [Azure Backup](backup-overview.md) trezoru Recovery Services. Obsahuje pokyny pro odebrání závislostí a odstranění trezoru.

## <a name="before-you-start"></a>Než začnete

Nemůžete odstranit trezor služby Recovery Services s některou z následujících závislostí:

- Nemůžete odstranit trezor, který obsahuje chráněné zdroje dat (například virtuální počítače s IaaS, databáze SQL, sdílené složky Azure).
- Trezor, který obsahuje data zálohy, nelze odstranit. Zálohovaná data po odstranění přejdou do stavu obnovitelného odstranění.
- Trezor, který obsahuje zálohovaná data, nelze odstranit ve stavu tichého odstranění.
- Trezor, který má registrované účty úložiště, se nedá odstranit.

Pokud se pokusíte odstranit trezor bez odebrání závislostí, dojde k jedné z následujících chybových zpráv:

- Trezor není možné odstranit, protože stále obsahuje prostředky. Zajistěte, aby neexistovaly žádné zálohované položky, chráněné servery nebo servery pro správu zálohování přidružené k tomuto trezoru. Než budete pokračovat v odstraňování, zrušte registraci následujících kontejnerů přidružených k tomuto trezoru.

- Trezor služby Recovery Services nejde odstranit, protože obsahuje zálohované položky ve stavu obnovitelného odstranění. Měkké odstraněné položky se po 14 dnech operace odstranění trvale odstraní. Zkuste prosím odstranit trezor, až se zálohované položky trvale odstraní a v trezoru zůstane v tichém odstraněném stavu žádná položka. Další informace najdete v tématu [obnovitelné odstranění pro Azure Backup](./backup-azure-security-feature-cloud.md).

## <a name="proper-way-to-delete-a-vault"></a>Správný způsob odstranění trezoru

>[!WARNING]
>Následující operace je destruktivní a nelze ji vrátit zpět. Všechna zálohovaná data a zálohované položky přidružené k chráněnému serveru budou trvale odstraněny. Postupujte však opatrně.

Chcete-li odstranit trezor správně, je nutné postupovat podle kroků v tomto pořadí:

- **Krok 1**: zakažte funkci obnovitelného odstranění. Postup zakázání obnovitelného odstranění [najdete tady](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) .

- **Krok 2**: po zakázání obnovitelného odstranění ověřte, jestli ve stavu tichého odstranění nejsou žádné položky, které by dřív zbyly. Pokud jsou položky v tichém odstraněném stavu, je nutné je *undelete* znovu odstranit a *Odstranit* . [Pomocí těchto kroků](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items) můžete najít obnovitelné položky odstranění a trvale je odstranit.

- **Krok 3**: Chcete-li ověřit, zda jsou k dispozici chráněné položky, je nutné zkontrolovat na následujících třech místech:

  - **Položky chráněné cloudem**: přejděte do nabídky řídicího panelu trezoru > **zálohované položky**. Všechny položky, které jsou zde uvedeny, je nutné odebrat pomocí nástroje **Zastavit zálohování** nebo **Odstranit zálohovaná data** společně s jejich zálohovanými daty.  [Pomocí těchto kroků tyto](#delete-protected-items-in-the-cloud) položky odeberte.
  - **SQL Server instance**: přejděte do nabídky řídicího panelu trezoru > **Backup Infrastructure**  >  **servery chráněné**infrastruktury zálohování. V části chráněné servery vyberte server, jehož registraci chcete zrušit. Pokud chcete trezor odstranit, musíte zrušit registraci všech serverů. Klikněte pravým tlačítkem na chráněný Server a vyberte zrušit **registraci**.
  - **Servery chráněné Mars**: přejděte do nabídky řídicího panelu trezoru > **Backup Infrastructure**  >  **servery chráněné**infrastruktury zálohování. Pokud máte chráněné servery MARS, musí se všechny uvedené položky odstranit spolu s jejich zálohovanými daty. [Pomocí těchto kroků](#delete-protected-items-on-premises) odstraňte servery chráněné serverem Mars.
  - **MABS nebo servery pro správu DPM**: přejděte do nabídky řídicího panelu trezoru > **Backup Infrastructure**  >  **servery pro správu zálohování**infrastruktury zálohování. Pokud máte aplikaci DPM nebo Azure Backup Server (MABS), musí se všechny uvedené položky odstranit nebo zrušit jejich registraci spolu s jejich zálohovanými daty. [Pomocí těchto kroků](#delete-protected-items-on-premises) odstraníte servery pro správu.

- **Krok 4**: je potřeba zajistit, aby byly odstraněny všechny registrované účty úložiště. Přejděte do nabídky řídicího panelu trezoru > **Backup Infrastructure**  >  **účty úložiště**infrastruktury zálohování. Pokud zde jsou uvedené účty úložiště, musíte je zrušit. Informace o zrušení registrace účtu najdete v tématu zrušení [Registrace účtu úložiště](manage-afs-backup.md#unregister-a-storage-account).

Po dokončení těchto kroků můžete trezor i nadále [Odstranit](#delete-the-recovery-services-vault).

Pokud nemáte místní nebo cloudové chráněné položky, ale stále dochází k chybě odstranění trezoru, proveďte kroky v části [odstranění Recovery Servicesového trezoru pomocí Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager)

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

3. Podívejte se na ikonu **oznámení** : ![ ikona oznámení.](./media/backup-azure-delete-vault/messages.png) Po dokončení procesu se ve službě zobrazí následující zpráva: *zastavte zálohování a odstraňte zálohovaná data pro "* zálohovanou položku *"*. *Operace se úspěšně dokončila*.
4. V nabídce **zálohované položky** vyberte **aktualizovat** a ujistěte se, že se zálohovaná položka odstranila.

      ![Stránka Odstranit zálohované položky](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Odstranění chráněných položek místně

Nejprve si přečtěte část **[než začnete](#before-you-start)** , abyste porozuměli závislostem a procesu odstranění trezoru.

1. V nabídce řídicího panelu trezoru vyberte **infrastruktura zálohování**.
2. V závislosti na vašem místním scénáři vyberte jednu z následujících možností:

      - V případě MARS vyberte **chráněné servery** a pak  **Azure Backup agenta**. Pak vyberte server, který chcete odstranit.

        ![V případě MARS vyberte svůj trezor a otevřete jeho řídicí panel.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Pro MABS nebo DPM vyberte **záložní servery pro správu**. Pak vyberte server, který chcete odstranit.

          ![V případě MABS nebo DPM vyberte svůj trezor a otevřete jeho řídicí panel.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Otevře se podokno **Odstranit** se zprávou upozornění.

     ![Podokno odstranění.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Přečtěte si zprávu s upozorněním a pokyny v tématu týkajícím se souhlasu.
    > [!NOTE]
    >
    >- Pokud je chráněný Server synchronizovaný se službami Azure a existují zálohované položky, zobrazí se v políčku pro souhlas počet závislých zálohových položek a odkaz na zobrazení zálohovaných položek.
    >- Pokud není chráněný Server synchronizovaný se službami Azure a existují zálohované položky, zobrazí se v políčku pro souhlas jenom počet zálohovaných položek.
    >- Pokud nejsou k dispozici žádné zálohované položky, vyzve se zaškrtávací políčko pro odstranění souhlasu.

4. Zaškrtněte políčko souhlasu a pak vyberte **Odstranit**.

5. Podívejte se na ikonu **oznámení** ![ Odstranit zálohovaná data ](./media/backup-azure-delete-vault/messages.png) . Po dokončení operace se ve službě zobrazí zpráva: *zastavení zálohování a odstranění zálohovaných dat pro "zálohovanou položku".* *Operace se úspěšně dokončila*.
6. V nabídce **zálohované položky** vyberte **aktualizovat** a ujistěte se, že se zálohovaná položka odstranila.

>[!NOTE]
>Pokud odstraníte místní chráněnou položku z portálu, který obsahuje závislosti, zobrazí se upozornění, že odstranění registrace serveru je destruktivní operace a nelze ji vrátit zpět. Všechna zálohovaná data (body obnovení vyžadované k obnovení dat) a zálohované položky přidružené k chráněnému serveru se trvale odstraní.

Po dokončení tohoto procesu můžete odstranit zálohované položky z konzoly pro správu:

- [Odstranit zálohované položky z konzoly pro správu MARS](#delete-backup-items-from-the-mars-management-console)
- [Odstranění zálohových položek z konzoly pro správu MABS nebo DPM](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Odstranit zálohované položky z konzoly pro správu MARS

>[!NOTE]
>Pokud jste zdrojový počítač odstranili nebo ztratili bez zastavení zálohování, další naplánované zálohování se nezdaří. Původní bod obnovení vyprší podle zásad, ale poslední jeden bod obnovení se vždy zachová, dokud neukončíte zálohování a neodstraníte data. To můžete provést podle kroků v [této části](#delete-protected-items-on-premises).

1. Otevřete konzolu pro správu MARS, klikněte na podokno **Akce** a vyberte **naplánovat zálohování**.
2. Na stránce **změnit nebo zastavit naplánované zálohování** vyberte možnost **ukončit používání tohoto plánu zálohování a odstraňte všechny uložené zálohy**. Pak vyberte **Další**.

    ![Upravte nebo zastavte naplánované zálohování.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na stránce **zastavit naplánované zálohování** vyberte **Dokončit**.

    ![Zastavte naplánované zálohování.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Zobrazí se výzva k zadání bezpečnostního kódu PIN (osobní identifikační číslo), které musíte vygenerovat ručně. Abyste to mohli udělat, nejdřív se přihlaste k Azure Portal.
5. Přejít na vlastnosti nastavení **trezoru Recovery Services**  >  **Settings**  >  **Properties**.
6. V části **bezpečnostní kód PIN**vyberte **Generovat**. Zkopírujte tento kód PIN. PIN kód je platný jenom po dobu pěti minut.
7. V konzole pro správu vložte kód PIN a pak vyberte **OK**.

    ![Vygenerujte bezpečnostní kód PIN.](./media/backup-azure-delete-vault/security-pin.png)

8. Na stránce **Upravit průběh zálohování** se zobrazí následující zpráva: *Odstraněná data zálohy se uchovávají 14 dní. Po uplynutí této doby se zálohovaná data trvale odstraní.*  

    ![Odstraňte infrastrukturu zálohování.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Po odstranění místních zálohových položek postupujte podle dalších kroků na portálu.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>Odstranění zálohových položek z konzoly pro správu MABS nebo DPM

>[!NOTE]
>Pokud jste zdrojový počítač odstranili nebo ztratili bez zastavení zálohování, další naplánované zálohování se nezdaří. Původní bod obnovení vyprší podle zásad, ale poslední jeden bod obnovení se vždy zachová, dokud neukončíte zálohování a neodstraníte data. To můžete provést podle kroků v [této části](#delete-protected-items-on-premises).

Existují dvě metody, které můžete použít k odstranění zálohovaných položek z konzoly pro správu aplikace MABS nebo DPM.

#### <a name="method-1"></a>Metoda 1

Chcete-li zastavit ochranu a odstranit data zálohy, proveďte následující kroky:

1. Otevřete Konzola správce aplikace DPM a pak na navigačním panelu vyberte **ochrana** .
2. V podokně zobrazení vyberte člena skupiny ochrany, kterého chcete odebrat. Kliknutím pravým tlačítkem vyberte možnost **Zastavit ochranu členů skupiny** .
3. V dialogovém okně **Zastavit ochranu** vyberte možnost **Odstranit chráněná data**a potom zaškrtněte políčko **Odstranit úložiště online** . Pak vyberte **Zastavit ochranu**.

    ![V podokně zastavit ochranu vyberte Odstranit chráněná data.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Stav chráněných členů se změní na *neaktivní repliku, která je k dispozici*.

4. Klikněte pravým tlačítkem na neaktivní skupinu ochrany a vyberte možnost **Odebrat neaktivní ochranu**.

    ![Odebrat neaktivní ochranu.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. V okně **Odstranit neaktivní ochranu** zaškrtněte políčko **Odstranit online úložiště** a pak vyberte **OK**.

    ![Odstraňte online úložiště.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Metoda 2

Otevřete konzolu pro správu **MABS** nebo **DPM** . V části **Vybrat způsob ochrany dat**zrušte zaškrtnutí políčka  **Chci online ochranu** .

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

  [Přečtěte si další informace](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) o tom, jak zakázat ochranu pro položku chráněnou Azure Backup.

- Zastavte ochranu a odstraňte data pro všechny položky chráněné zálohováním v cloudu (například: IaaS VM, sdílení souborů Azure atd.):

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

    [Další informace](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)   o zakáže ochranu pro položku chráněnou zálohováním.

- Pro místní soubory a složky chráněné pomocí agenta Azure Backup, který se zálohuje do Azure, pomocí následujícího příkazu PowerShellu odstraňte zálohovaná data z každého modulu MARS PowerShellu:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Následně se zobrazí následující výzva:

    *Microsoft Azure Backup jste si jisti, že chcete odebrat tyto zásady zálohování? Data odstraněných záloh se uchovávají po dobu 14 dnů. Po uplynutí této doby se zálohovaná data trvale odstraní. <br/> [Y] Ano [A] Ano všem [N] ne [L] No všem [S] pozastavit [?] Help (výchozí je "Y"):*

- U místních počítačů chráněných pomocí MABS (Microsoft Azure Backup serveru) nebo DPM (System Center Data Protection Manager) do Azure použijte následující příkaz k odstranění zálohovaných dat v Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Následně se zobrazí následující výzva:

   *Microsoft Azure Backup* Opravdu chcete odebrat tyto zásady zálohování? Data odstraněných záloh se uchovávají po dobu 14 dnů. Po uplynutí této doby se data záloh trvale odstraní. <br/>
   [Y] Ano [A] Ano všem [N] ne [L] No všem [S] pozastavit [?] Help (výchozí je "Y"):*

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

    [Přečtěte si další informace](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) o zrušení registrace Windows serveru nebo jiného kontejneru z trezoru.

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

    [Přečtěte si další informace](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) o zrušení registrace kontejneru správy zálohování z trezoru.

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

[Přečtěte si další informace](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) o odstranění trezoru Recovery Services.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Odstranění Recovery Servicesho trezoru pomocí rozhraní příkazového řádku

Nejprve si přečtěte část **[než začnete](#before-you-start)** , abyste porozuměli závislostem a procesu odstranění trezoru.

> [!NOTE]
> V současné době Azure Backup CLI podporuje správu jenom záloh virtuálních počítačů Azure, takže následující příkaz k odstranění trezoru funguje jenom v případě, že trezor obsahuje zálohy virtuálních počítačů Azure. Trezor nelze odstranit pomocí Azure Backup CLI, pokud trezor obsahuje libovolnou zálohovanou položku jiného typu než virtuální počítače Azure.

Pokud chcete odstranit existující trezor Recovery Services, proveďte následující kroky:

- Zastavení ochrany a odstranění zálohovaných dat

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

    Další informace najdete v tomto [článku](/cli/azure/backup/protection#az-backup-protection-disable).

- Odstranit existující trezor Recovery Services:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Další informace najdete v tomto [článku](/cli/azure/backup/vault) .

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Odstranění trezoru Recovery Services pomocí Azure Resource Manager

Tato možnost odstranění trezoru Recovery Services se doporučuje jenom v případě, že se odeberou všechny závislosti a pořád dojde k *chybě odstranění trezoru*. Zkuste použít některé z následujících tipů:

- V podokně **základy** v nabídce trezoru ověřte, že v seznamu nejsou žádné zálohované položky, servery pro správu zálohování nebo replikované položky. Pokud jsou k dispozici zálohované položky, přečtěte si informace v části [než začnete](#before-you-start) .
- Zkuste [trezor odstranit znovu z portálu](#delete-the-recovery-services-vault) .
- Pokud se všechny závislosti odeberou a pořád dojde k *chybě odstranění trezoru*, použijte nástroj ARMClient k provedení následujících kroků (po tom, kde se nachází Poznámka).

1. Pokud si chcete stáhnout a nainstalovat čokoláda, navštivte [Chocolatey.org](https://chocolatey.org/) . Pak nainstalujte ARMClient spuštěním následujícího příkazu:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Přihlaste se ke svému účtu Azure a pak spusťte následující příkaz:

    `ARMClient.exe login [environment name]`

3. V Azure Portal Shromážděte ID předplatného a název skupiny prostředků pro trezor, který chcete odstranit.

Další informace o příkazu ARMClient najdete v tématu [soubor Readme pro ARMClient](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Použití klienta Azure Resource Manager k odstranění trezoru Recovery Services

1. Spusťte následující příkaz pomocí ID vašeho předplatného, názvu skupiny prostředků a názvu trezoru. Pokud nemáte žádné závislosti, odstraní se trezor, když spustíte následující příkaz:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. Pokud trezor není prázdný, zobrazí se tato chybová zpráva: *trezor se nedá odstranit, protože v tomto trezoru existují nějaké prostředky.* Chcete-li odebrat chráněnou položku nebo kontejner v rámci trezoru, spusťte následující příkaz:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. V Azure Portal se ujistěte, že je trezor odstraněný.

## <a name="next-steps"></a>Další kroky

[Další informace o trezorech Recovery Services](backup-azure-recovery-services-vault-overview.md) 
 [Seznamte se s monitorováním a správou trezorů Recovery Services](backup-azure-manage-windows-server.md) .
