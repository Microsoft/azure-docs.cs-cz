---
title: Odstranění trezoru Recovery Services v Azure
description: Popisuje odstranění trezoru Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: 3d3a77b43babb7fa3666dbbc9e4334f61ae612f8
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68232663"
---
# <a name="delete-a-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

Tento článek popisuje, jak odstranit [Azure Backup](backup-overview.md) trezoru Recovery Services. Obsahuje pokyny pro odebrání závislostí a odstranění trezoru.


## <a name="before-you-start"></a>Než začnete

Nelze odstranit Recovery Services trezor, který obsahuje závislosti, jako jsou chráněné servery nebo servery pro správu zálohování přidružené k trezoru.<br/>
Trezor obsahující data zálohy nelze odstranit (tj. i když jste zastavili ochranu, ale zachovali data zálohy).

Pokud odstraníte trezor, který obsahuje závislosti, zobrazí se následující chyba:

![Chyba odstranění trezoru](./media/backup-azure-delete-vault/error.png)

K řádnému odstranění trezoru vyberte scénář, který odpovídá vašemu nastavení, a postupujte podle doporučených kroků:

Scénář | Postup odebrání závislostí pro odstranění trezoru |
-- | --
Mám místní soubory a složky chráněné pomocí Azure Backup agenta (MARS), který se zálohuje do Azure | Proveďte kroky v části odstranění zálohovaných dat a zálohových položek – [pro agenta Mars](#for-mars-agent) .
Mám místní počítače chráněné pomocí MABS (Microsoft Azure Backup serveru) nebo DPM do Azure (System Center Data Protection Manager) | Provedení kroků v části odstranění zálohovaných dat a zálohových položek – [pro agenta MABS](#for-mabs-agent)
Mám chráněné položky v cloudu (např. virtuální počítač laaS, sdílení souborů Azure atd.)  | Provedení kroků v části odstranění zálohovaných dat a zálohované položky – [pro chráněné položky v cloudu](#for-protected-items-in-cloud)
Mám chráněné položky místní i cloudové | Proveďte kroky v části odstranění zálohovaných dat a zálohované položky v následujícím pořadí: <br> - [Pro chráněné položky v cloudu](#for-protected-items-in-cloud)<br> - [Pro agenta MABS](#for-mars-agent) <br> - [Pro agenta MABS](#for-mabs-agent)
Nepoužívám místní nebo cloudové chráněné položky; Pořád se ale stále zobrazuje chyba odstraňování trezoru | Provedení kroků v části [odstranění Recovery Servicesového trezoru pomocí klienta Azure Resource Manager](#delete-the-recovery-services-vault-using-azure-resource-manager-client)
Nemám už původní místní server (ztracený nebo vyřazený z provozu) a chci odstranit Recovery Services trezor. | Obraťte se na podporu Microsoftu.

## <a name="delete-backup-data-and-backup-items"></a>Odstranit zálohovaná data a zálohované položky

Než budete pokračovat, přečtěte si v **[této](#before-you-start)** části informace o závislostech a procesu odstraňování trezoru.

### <a name="for-protected-items-in-cloud"></a>Pro chráněné položky v cloudu

Chcete-li zastavit ochranu a odstranit data zálohy, postupujte takto:

1. Z portálu > Recovery Services trezoru > zálohované položky vyberte chráněné položky v cloudu.

    ![Vyberte typ zálohování.](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. Pro každou položku musíte kliknout pravým tlačítkem a zvolit **Zastavit zálohování**.

    ![Vyberte typ zálohování.](./media/backup-azure-delete-vault/stop-backup-item.png)

3. V možnosti **Zastavit zálohování** > **Vyberte možnost** **Odstranit zálohovaná data**.
4. Zadejte název položky a klikněte na **Zastavit zálohování**.
   - Tím ověříte, že chcete položku odstranit.
   - Po ověření se aktivuje tlačítko **Zastavit zálohování** .
   - Pokud data zachováte a neodstraníte, nebudete moct trezor odstranit.

     ![Odstranit data zálohy](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Podívejte se na **oznámení** ![odstranění zálohovaných dat.](./media/backup-azure-delete-vault/messages.png) Po dokončení služba zobrazí zprávu: **Zastavuje se zálohování a odstraňují se zálohovaná data pro "*zálohovanou položku*"** . **Operace se úspěšně dokončila**.
6. V nabídce zálohované **položky** klikněte na **aktualizovat** a ověřte, jestli je zálohovaná položka odebraná.

      ![Odstranit data zálohy](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>Pro agenta MARS

Chcete-li zastavit ochranu a odstranit data zálohy, proveďte kroky v níže uvedeném pořadí:

- [Krok 1: Odstranit zálohované položky z konzoly pro správu MARS](#step-1-delete-backup-items-from-mars-management-console)
- [Krok 2: Z portálu odebrat agenta Azure Backup](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>Krok 1: Odstranit zálohované položky z konzoly pro správu MARS

Pokud nemůžete tento krok provést z důvodu nedostupnosti serveru, obraťte se na podporu Microsoftu.

- Spusťte konzolu pro správu MARS, v podokně **Akce** klikněte na možnost **naplánovat zálohování**.
- V průvodci **změnou nebo zastavením naplánovaného zálohování** vyberte možnost **Zastavit používání tohoto plánu zálohování a odstraňte všechny uložené zálohy** a klikněte na **Další**.

    ![Úprava nebo zastavení plánovaného zálohování](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- V průvodci **zastavit naplánované zálohování** klikněte na **Dokončit**.

    ![Zastavit naplánované zálohování](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Zobrazí se výzva k zadání bezpečnostního kódu PIN. K vygenerování kódu PIN proveďte následující kroky:
  - Přihlaste se k portálu Azure.
  - Přejděte na **Recovery Services** > **vlastnosti** **Nastavení** > trezoru.
  - V části **bezpečnostní kód PIN**klikněte na vygenerovat. Zkopírujte tento kód PIN. (Tento kód PIN je platný jenom po dobu pěti minut).
- V konzole pro správu (klientská aplikace) vložte kód PIN a klikněte na **OK**.

  ![Bezpečnostní kód PIN](./media/backup-azure-delete-vault/security-pin.png)

- V *průvodci **úpravou průběhu zálohování** se budou uchovávat data Odstraněná po dobu 14 dnů. Po uplynutí této doby se zálohovaná data trvale odstraní.*  

    ![Odstranit infrastrukturu zálohování](./media/backup-azure-delete-vault/deleted-backup-data.png)

Teď, když jste odstranili zálohované položky z místního prostředí, dokončete následující kroky z portálu.

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>Krok 2: Z portálu odebrat agenta Azure Backup

Před dalším pokračováním zajistěte dokončení [kroku 1](#step-1-delete-backup-items-from-mars-management-console) :

1. V nabídce řídicího panelu trezoru klikněte na **infrastruktura zálohování**.
2. Kliknutím na položku **chráněné servery** zobrazíte servery infrastruktury.

    ![Vyberte svůj trezor a otevřete jeho řídicí panel.](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. V seznamu **chráněné servery** klikněte na Azure Backup Agent.

    ![Vyberte typ zálohování.](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. V seznamu serverů, které jsou chráněné pomocí agenta Azure Backup, klikněte na server.

    ![vybrat konkrétní chráněný Server](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. Na vybraném řídicím panelu serveru klikněte na **Odstranit**.

    ![Odstraní vybraný server.](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. V nabídce **Odstranit** zadejte název serveru a klikněte na **Odstranit**.

     ![Odstranit data zálohy](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Pokud se zobrazuje níže uvedená chyba, proveďte nejprve kroky uvedené v části [odstraňování zálohovaných položek z konzoly pro správu](#step-1-delete-backup-items-from-mars-management-console).
>
>![neúspěšné odstranění](./media/backup-azure-delete-vault/deletion-failed.png)
>
>Pokud nemůžete provést kroky k odstranění záloh z konzoly pro správu, například kvůli nedostupnosti serveru s konzolou pro správu, obraťte se na podporu Microsoftu.

7. Podívejte se na **oznámení** ![odstranění zálohovaných dat.](./media/backup-azure-delete-vault/messages.png) Po dokončení služba zobrazí zprávu: **Zastavuje se zálohování a odstraňují se zálohovaná data pro "*zálohovanou položku*"** . **Operace se úspěšně dokončila**.
8. V nabídce zálohované **položky** klikněte na **aktualizovat** a ověřte, jestli je zálohovaná položka odebraná.


### <a name="for-mabs-agent"></a>Pro agenta MABS

Chcete-li zastavit ochranu a odstranit data zálohy, proveďte kroky v níže uvedeném pořadí:

- [Krok 1: Odstranit zálohované položky z konzoly pro správu MABS](#step-1-delete-backup-items-from-mabs-management-console)
- [Krok 2: Odebrání Azure Backup serverů pro správu z portálu](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>Krok 1: Odstranit zálohované položky z konzoly pro správu MABS

Pokud nemůžete tento krok provést z důvodu nedostupnosti serveru, obraťte se na podporu Microsoftu.

**Metoda 1** Chcete-li zastavit ochranu a odstranit data zálohy, proveďte následující kroky:

1.  V Konzola správce aplikace DPM klikněte na tlačítko **ochrana** na navigačním panelu.
2.  V podokně zobrazení vyberte člena skupiny ochrany, kterého chcete odebrat. Kliknutím pravým tlačítkem myši vyberte možnost **Zastavit ochranu členů skupiny** .
3.  V dialogovém okně **Zastavit ochranu** zaškrtněte políčko **Odstranit chráněná data** > **odstranění úložiště online** a pak klikněte na **Zastavit ochranu**.

    ![Odstranit úložiště online](./media/backup-azure-delete-vault/delete-storage-online.png)

Stav chráněných členů je nyní změněn na **neaktivní repliku**.

5. Klikněte pravým tlačítkem na neaktivní skupinu ochrany a vyberte možnost **Odebrat neaktivní ochranu**.

    ![Odebrat neaktivní ochranu](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. V okně **Odstranit neaktivní ochranu** vyberte **Odstranit online úložiště** a klikněte na **OK**.

    ![Odebrat repliky na disku a online](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**Metoda 2** Spusťte konzolu **pro správu MABS** . V části **Vybrat způsob ochrany dat** zrušte výběr, **který chci online ochranu**.

  ![Vyberte způsob ochrany dat](./media/backup-azure-delete-vault/data-protection-method.png)

Teď, když jste odstranili zálohované položky z místního prostředí, dokončete následující kroky z portálu.

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>Krok 2: Odebrání Azure Backup serverů pro správu z portálu

Před dalším pokračováním zajistěte dokončení [kroku 1](#step-1-delete-backup-items-from-mabs-management-console) :

1. V nabídce řídicího panelu trezoru klikněte na **infrastruktura zálohování**.
2. Kliknutím na **servery pro správu zálohování** zobrazíte servery.

    ![Vyberte trezor a otevřete jeho řídicí panel.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Klikněte pravým tlačítkem myši na položku > **Odstranit**.
4. V nabídce **Odstranit** zadejte název serveru a klikněte na **Odstranit**.

     ![Odstranit data zálohy](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Pokud se zobrazuje níže uvedená chyba, proveďte nejprve kroky uvedené v části [odstraňování zálohovaných položek z konzoly pro správu](#step-2-from-portal-remove-azure-backup-management-servers).
>
>![neúspěšné odstranění](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Pokud nemůžete provést kroky k odstranění záloh z konzoly pro správu, například kvůli nedostupnosti serveru s konzolou pro správu, obraťte se na podporu Microsoftu.

5. Podívejte se na **oznámení** ![odstranění zálohovaných dat.](./media/backup-azure-delete-vault/messages.png) Po dokončení služba zobrazí zprávu: **Zastavuje se zálohování a odstraňují se zálohovaná data pro "*zálohovanou položku*"** . **Operace se úspěšně dokončila**.
6. V nabídce zálohované **položky** klikněte na **aktualizovat** a ověřte, jestli je zálohovaná položka odebraná.


## <a name="delete-the-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services

1. Po odebrání všech závislostí se posuňte do podokna **základy** v nabídce trezor.
2. Ověřte, že v seznamu nejsou žádné **zálohované položky**, **servery pro správu zálohování**ani **replikované položky** . Pokud se položky v trezoru stále zobrazují, [odeberte je](#delete-backup-data-and-backup-items).

3. Pokud v trezoru nejsou žádné další položky, klikněte na řídicím panelu trezoru na **Odstranit**.

    ![Odstranit data zálohy](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Chcete-li ověřit, zda chcete trezor odstranit, klikněte na tlačítko **Ano**. Trezor se odstraní a portál se vrátí do nabídky **Nová** služba.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Odstranění trezoru Recovery Services pomocí klienta Azure Resource Manager

Tato možnost odstraní trezor Recovery Services. doporučuje se jenom v případě, že se odeberou všechny závislosti a pořád dojde k *chybě odstranění trezoru*.



- V podokně **základy** v nabídce trezoru ověřte, že v seznamu nejsou žádné **zálohované položky**, **servery pro správu zálohování**nebo **replikované položky** . Pokud jsou k dispozici zálohované položky, proveďte kroky v části [odstranění zálohovaných dat a zálohované položky](#delete-backup-data-and-backup-items).
- Zkuste [trezor znovu odstranit z portálu](#delete-the-recovery-services-vault).
- Pokud se všechny závislosti odeberou a pořád se vám zobrazuje *Chyba odstranění trezoru* , použijte k provedení následujících kroků nástroj ARMClient.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Z [tohoto místa](https://chocolatey.org/) nainstalujte čokolády a nainstalujte ARMClient spusťte následující příkaz:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Přihlaste se ke svému účtu Azure a spusťte tento příkaz:

    `ARMClient.exe login [environment name]`

3. V Azure Portal Shromážděte ID předplatného a název skupiny prostředků pro trezor, který chcete odstranit.

Další informace o příkazu ARMClient najdete v tomto [dokumentu](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Použití klienta Azure Resource Manager k odstranění trezoru Recovery Services

1. Spusťte následující příkaz s ID předplatného, názvem skupiny prostředků a názvem trezoru. Když příkaz spustíte, odstraní se trezor, pokud nemáte žádné závislosti.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Pokud trezor není prázdný, zobrazí se chyba "Trezor nelze odstranit, protože v tomto trezoru existují prostředky". K odebrání chráněných položek/kontejneru v rámci trezoru postupujte takto:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. V Azure Portal ověřte odstranění trezoru.


## <a name="next-steps"></a>Další postup

[Další informace](backup-azure-recovery-services-vault-overview.md) Recovery Services trezory.<br/>
[Seznamte](backup-azure-manage-windows-server.md) se s monitorováním a správou trezorů Recovery Services.
