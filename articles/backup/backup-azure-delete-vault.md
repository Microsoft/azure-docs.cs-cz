---
title: Odstranění trezoru Recovery Services v Azure
description: Popisuje odstranění trezoru Recovery Services.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: 9c63170b60a871182042acab8a35e505c603f260
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018875"
---
# <a name="delete-a-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

Tento článek popisuje, jak odstranit [Azure Backup](backup-overview.md) trezoru Recovery Services. Obsahuje pokyny pro odebrání závislostí a odstranění trezoru.


## <a name="before-you-start"></a>Než začnete

Nelze odstranit Recovery Services trezor, který obsahuje závislosti, jako jsou chráněné servery nebo servery pro správu zálohování přidružené k trezoru.

- Trezor obsahující data zálohy nelze odstranit (tj. i když jste zastavili ochranu, ale zachovali data zálohy).

- Pokud odstraníte trezor, který obsahuje závislosti, zobrazí se následující chyba:

  ![Chyba odstranění trezoru](./media/backup-azure-delete-vault/error.png)

- Pokud odstraníte místní chráněnou položku (MARS, MABS nebo DPM do Azure) z portálu obsahujícího závislosti, zobrazí se zpráva s upozorněním:

  ![Chyba při odstraňování chráněného serveru](./media/backup-azure-delete-vault/error-message.jpg)

  
K řádnému odstranění trezoru vyberte scénář, který odpovídá vašemu nastavení, a postupujte podle doporučených kroků:

Scénář | Postup odebrání závislostí pro odstranění trezoru |
-- | --
Mám místní soubory a složky chráněné pomocí Azure Backup agenta (MARS), který se zálohuje do Azure | Proveďte kroky v části odstranění zálohovaných dat a zálohových položek – [pro agenta Mars](#delete-backup-items-from-mars-management-console) .
Mám místní počítače chráněné pomocí MABS (Microsoft Azure Backup serveru) nebo DPM do Azure (System Center Data Protection Manager) | Provedení kroků v části odstranění zálohovaných dat a zálohových položek – [pro agenta MABS](#delete-backup-items-from-mabs-management-console)
Mám chráněné položky v cloudu (např. virtuální počítač laaS, sdílení souborů Azure atd.)  | Provedení kroků v části odstranění zálohovaných dat a zálohované položky – [pro chráněné položky v cloudu](#delete-protected-items-in-cloud)
Mám chráněné položky místní i cloudové | Proveďte kroky v části odstranění zálohovaných dat a zálohované položky v následujícím pořadí: <br> - [Pro chráněné položky v cloudu](#delete-protected-items-in-cloud)<br> - [Pro agenta MARS](#delete-backup-items-from-mars-management-console) <br> - [Pro agenta MABS](#delete-backup-items-from-mabs-management-console)
Nepoužívám místní nebo cloudové chráněné položky; Pořád se ale stále zobrazuje chyba odstraňování trezoru | Provedení kroků v části [odstranění Recovery Servicesového trezoru pomocí klienta Azure Resource Manager](#delete-the-recovery-services-vault-using-azure-resource-manager-client)


## <a name="delete-protected-items-in-cloud"></a>Odstranění chráněných položek v cloudu

Než budete pokračovat v přečtení **[této](#before-you-start)** části, abyste porozuměli závislostem a procesu odstranění trezoru.

Chcete-li zastavit ochranu a odstranit data zálohy, postupujte takto:

1. Z portálu >**zálohované položky** **Recovery Services trezoru** > vyberte chráněné položky v cloudu (například AzureVirtual počítač, Azure Storage (soubory Azure), SQL na virtuálním počítači Azure a tak dále).

    ![Vyberte typ zálohování.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Klikněte pravým tlačítkem na zálohovanou položku. V závislosti na tom, jestli je zálohovaná položka chráněná, nebo ne, se v nabídce zobrazí položka **Zastavit zálohování** nebo **Odstranit zálohovaná data**.

    - V části **Zastavit zálohování**vyberte **Odstranit zálohovaná data** z rozevíracího seznamu. Zadejte **název** zálohované položky (rozlišuje velká a malá písmena), vyberte **důvod**, zadejte **Komentáře**a klikněte na **Zastavit zálohování**.

        ![Vyberte typ zálohování.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - V poli **Odstranit zálohovaná data**zadejte název zálohované položky (rozlišuje velká a malá písmena), vyberte **důvod**, zadejte **Komentáře**a klikněte na **Odstranit**. 

         ![Odstranit data zálohy](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Podívejte se na **oznámení** ![odstranění zálohovaných dat.](./media/backup-azure-delete-vault/messages.png) Po dokončení služba zobrazí zprávu: **Zastavuje se zálohování a odstraňují se zálohovaná data pro "*zálohovanou položku*"** . **Operace se úspěšně dokončila**.
6. V nabídce zálohované **položky** klikněte na **aktualizovat** a ověřte, jestli je zálohovaná položka odebraná.

      ![Odstranit data zálohy](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Místní odstranění chráněných položek

Než budete pokračovat, přečtěte si v **[této](#before-you-start)** části informace o závislostech a procesu odstraňování trezoru.

1. V nabídce řídicího panelu trezoru klikněte na **infrastruktura zálohování**.
2. V závislosti na vašem místním scénáři vyberte níže uvedenou možnost:

      - U **Azure Backup agenta**vyberte **chráněné servery** > **Azure Backup agenta** a vyberte server, který chcete odstranit. 

        ![Vyberte svůj trezor a otevřete jeho řídicí panel.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Pro **Azure Backup Server**/**DPM**vyberte **záložní servery pro správu**. Vyberte server, který chcete odstranit. 


          ![Vyberte trezor a otevřete jeho řídicí panel.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Zobrazí se okno **Odstranit** se zprávou upozornění.

     ![Odstranit data zálohy](./media/backup-azure-delete-vault/delete-protected-server.png)

     Přečtěte si zprávu upozornění a pokyny uvedené v článku o souhlasu.
    
    > [!NOTE]
    >- Pokud je chráněný Server synchronizovaný se službou Azure a existují zálohované položky, zobrazí se v políčku pro souhlas počet závislých zálohových položek a odkaz na zobrazení zálohovaných položek.
    >- Pokud chráněný Server není synchronizovaný se službou Azure a existují zálohované položky, zobrazí se v políčku pro souhlas počet zálohovaných položek.
    >- Pokud zálohované položky neexistují, požádejte o odstranění zaškrtávací políčko souhlasu.

4. Zaškrtněte políčko souhlasu a klikněte na **Odstranit**.




5. Podívejte se na **oznámení** ![odstranění zálohovaných dat.](./media/backup-azure-delete-vault/messages.png) Po dokončení služba zobrazí zprávu: **Zastavuje se zálohování a odstraňují se zálohovaná data pro "*zálohovanou položku*"** . **Operace se úspěšně dokončila**.
6. V nabídce zálohované **položky** klikněte na **aktualizovat** a ověřte, jestli je zálohovaná položka odebraná.

Nyní můžete pokračovat v odstraňování zálohovaných položek z konzoly pro správu:
    
   - [Položky chráněné pomocí MARS](#delete-backup-items-from-mars-management-console)
    - [Položky chráněné pomocí MABS](#delete-backup-items-from-mabs-management-console)


### <a name="delete-backup-items-from-mars-management-console"></a>Odstranit zálohované položky z konzoly pro správu MARS

Odstranění zálohovaných položek z konzoly pro správu MARS

- Spusťte konzolu pro správu MARS, v podokně **Akce** klikněte na možnost **naplánovat zálohování**.
- V průvodci **změnou nebo zastavením naplánovaného zálohování** vyberte možnost **Zastavit používání tohoto plánu zálohování a odstraňte všechny uložené zálohy** a klikněte na **Další**.

    ![Úprava nebo zastavení plánovaného zálohování](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- V průvodci **zastavit naplánované zálohování** klikněte na **Dokončit**.

    ![Zastavit naplánované zálohování](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Zobrazí se výzva k zadání bezpečnostního kódu PIN. K vygenerování kódu PIN proveďte následující kroky:
  - Přihlaste se k portálu Azure.
  - Přejděte na **Recovery Services** > **vlastnosti** **Nastavení** > trezoru.
  - V části **bezpečnostní kód PIN**klikněte na vygenerovat. Zkopírujte tento kód PIN. (Tento kód PIN je platný jenom po dobu pěti minut.)
- V konzole pro správu (klientská aplikace) vložte kód PIN a klikněte na **OK**.

  ![Bezpečnostní kód PIN](./media/backup-azure-delete-vault/security-pin.png)

- V průvodci **úpravou průběhu zálohování** uvidíte *, že odstraněná data zálohy se uchovávají 14 dnů. Po uplynutí této doby se zálohovaná data trvale odstraní.*  

    ![Odstranit infrastrukturu zálohování](./media/backup-azure-delete-vault/deleted-backup-data.png)

Teď, když jste odstranili zálohované položky z místního prostředí, dokončete následující kroky z portálu.

### <a name="delete-backup-items-from-mabs-management-console"></a>Odstranit zálohované položky z konzoly pro správu MABS

Odstranění zálohovaných položek z konzoly pro správu MABS

**Metoda 1** Chcete-li zastavit ochranu a odstranit data zálohy, proveďte následující kroky:

1.  V Konzola správce aplikace DPM klikněte na tlačítko **ochrana** na navigačním panelu.
2.  V podokně zobrazení vyberte člena skupiny ochrany, kterého chcete odebrat. Kliknutím pravým tlačítkem myši vyberte možnost **Zastavit ochranu členů skupiny** .
3.  V dialogovém okně **Zastavit ochranu** zaškrtněte políčko **Odstranit chráněná data** > **Odstranit úložiště online** a pak klikněte na **Zastavit ochranu**.

    ![Odstranit úložiště online](./media/backup-azure-delete-vault/delete-storage-online.png)

Stav chráněných členů je nyní změněn na **neaktivní repliku**.

4. Klikněte pravým tlačítkem na neaktivní skupinu ochrany a vyberte možnost **Odebrat neaktivní ochranu**.

    ![Odebrat neaktivní ochranu](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. V okně **Odstranit neaktivní ochranu** vyberte **Odstranit online úložiště** a klikněte na **OK**.

    ![Odebrat repliky na disku a online](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**Metoda 2** Spusťte konzolu **pro správu MABS** . V části **Vybrat způsob ochrany dat** zrušte výběr, **který chci online ochranu**.

  ![Vyberte způsob ochrany dat](./media/backup-azure-delete-vault/data-protection-method.png)

Teď, když jste odstranili zálohované položky z místního prostředí, dokončete následující kroky z portálu.


## <a name="delete-the-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

1. Po odebrání všech závislostí se posuňte do podokna **základy** v nabídce trezor.
2. Ověřte, že v seznamu nejsou žádné **zálohované položky**, **servery pro správu zálohování**ani **replikované položky** . Pokud se položky v trezoru stále zobrazují, přečtěte si část [před zahájením](#before-you-start) .

3. Pokud v trezoru nejsou žádné další položky, klikněte na řídicím panelu trezoru na **Odstranit**.

    ![Odstranit data zálohy](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Chcete-li ověřit, zda chcete trezor odstranit, klikněte na tlačítko **Ano**. Trezor se odstraní a portál se vrátí do nabídky **Nová** služba.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Odstranění trezoru Recovery Services pomocí klienta Azure Resource Manager

Tato možnost odstraní trezor Recovery Services. doporučuje se jenom v případě, že se odeberou všechny závislosti a pořád dojde k *chybě odstranění trezoru*.

- V podokně **základy** v nabídce trezoru ověřte, že v seznamu nejsou žádné **zálohované položky**, **servery pro správu zálohování**nebo **replikované položky** . Pokud jsou k dispozici zálohované položky, přečtěte si část [před zahájením](#before-you-start) ..
- Zkuste [trezor znovu odstranit z portálu](#delete-the-recovery-services-vault).
- Pokud se všechny závislosti odeberou a pořád se vám zobrazí *Chyba odstranění trezoru*, použijte nástroj ARMClient k provedení níže uvedených kroků.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Z [tohoto místa](https://chocolatey.org/) nainstalujte čokolády a nainstalujte ARMClient spusťte následující příkaz:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Přihlaste se ke svému účtu Azure a spusťte tento příkaz:

    `ARMClient.exe login [environment name]`

3. V Azure Portal Shromážděte ID předplatného a název skupiny prostředků pro trezor, který chcete odstranit.

Další informace o příkazu ARMClient najdete v tomto [dokumentu](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Použití klienta Azure Resource Manager k odstranění trezoru Recovery Services

1. Spusťte následující příkaz s ID předplatného, názvem skupiny prostředků a názvem trezoru. Když příkaz spustíte, odstraní se trezor, pokud nemáte žádné závislosti.

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Pokud trezor není prázdný, zobrazí se chyba "Trezor nelze odstranit, protože v tomto trezoru existují prostředky". K odebrání chráněných položek/kontejneru v rámci trezoru postupujte takto:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. V Azure Portal ověřte odstranění trezoru.


## <a name="next-steps"></a>Další kroky

[Další informace](backup-azure-recovery-services-vault-overview.md) Recovery Services trezory.<br/>
[Seznamte](backup-azure-manage-windows-server.md) se s monitorováním a správou trezorů Recovery Services.
