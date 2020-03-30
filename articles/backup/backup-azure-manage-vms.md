---
title: Správa a monitorování záloh virtuálních počítačů Azure
description: Zjistěte, jak spravovat a monitorovat zálohy virtuálních počítačích Azure pomocí služby Azure Backup.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: dd4e9dc199048b3faf3da0cadfdf60bdcb26c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248148"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Správa záloh virtuálních operátorů Azure pomocí služby Azure Backup

Tento článek popisuje, jak spravovat virtuální počítače Azure (VM), které jsou zálohovány pomocí [služby Azure Backup](backup-overview.md). Článek také shrnuje informace o zálohování, které najdete na řídicím panelu úschovny.

Na webu Azure Portal poskytuje řídicí panel trezoru služby Recovery Services přístup k informacím o úschovně, včetně:

* Nejnovější záloha, která je také nejnovějším bodem obnovení.
* Zásady zálohování.
* Celková velikost všech snímků zálohy.
* Počet virtuálních počítače, které jsou povolené pro zálohování.

Zálohy můžete spravovat pomocí řídicího panelu a přechodem na jednotlivé virtuální počítače. Chcete-li zahájit zálohování počítače, otevřete trezor na řídicím panelu.

![Úplné zobrazení řídicího panelu s posuvníkem](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Zobrazení virtuálních počítačů na řídicím panelu

Zobrazení virtuálních počítačů na řídicím panelu trezoru:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. V nabídce Hub vyberte **Procházet**. V seznamu prostředků zadejte **Recovery Services**. Při psaní je seznam filtrován na základě vašeho vstupu. Vyberte **trezory služby Recovery Services**.

    ![Vytvoření trezoru Služeb zotavení](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Pro snadné použití klikněte pravým tlačítkem myši na úschovnu a vyberte **připnout k řídicímu panelu**.
4. Otevřete řídicí panel trezoru.

    ![Otevření řídicího panelu trezoru a podokna Nastavení](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Na dlaždici **Položky zálohování** vyberte **Virtuální počítače Azure**.

    ![Otevření dlaždice Zálohovat položky](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. V podokně **Zálohovat položky** můžete zobrazit seznam chráněných virtuálních počítačích. V tomto příkladu trezor chrání jeden virtuální počítač: demobackup.  

    ![Zobrazení podokna Zálohovat položky](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. Z řídicího panelu položky úschovny upravte zásady zálohování, spusťte zálohování na vyžádání, zastavte nebo obnovte ochranu virtuálních počítačů, odstraňte záložní data, zobrazte body obnovení a spusťte obnovení.

    ![Řídicí panel Zálohovat položky a podokno Nastavení](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Správa zásad zálohování pro virtuální počítače

Správa zásad zálohování:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/). Otevřete řídicí panel trezoru.
2. Na dlaždici **Položky zálohování** vyberte **Virtuální počítače Azure**.

    ![Otevření dlaždice Zálohovat položky](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. V podokně **Položky zálohování** můžete zobrazit seznam chráněných virtuálních počítačů a stav posledního zálohování s nejnovějším časem bodů obnovení.

    ![Zobrazení podokna Zálohovat položky](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Na řídicím panelu položky úschovny můžete vybrat zásadu zálohování.

   * Chcete-li přepnout zásady, vyberte jinou zásadu a pak vyberte **Uložit**. Nové zásady se okamžitě použijí na trezor.

     ![Vybrat zásady zálohování](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Spuštění zálohy na vyžádání

Po nastavení jeho ochrany můžete spustit zálohu na vyžádání virtuálního počítače. Mějte na paměti tyto podrobnosti:

* Pokud počáteční záloha čeká na vyřízení, záloha na vyžádání vytvoří úplnou kopii virtuálního počítače v trezoru služby Recovery Services.
* Pokud je počáteční záloha dokončena, záloha na vyžádání odešle změny pouze z předchozího snímku do trezoru služby Recovery Services. To znamená, že pozdější zálohy jsou vždy přírůstkové.
* Rozsah uchování pro zálohování na vyžádání je hodnota uchovávání, kterou zadáte při aktivaci zálohy.

Spuštění zálohy na vyžádání:

1. Na [řídicím panelu položky úschovny](#view-vms-on-the-dashboard)vyberte v části **Chráněná položka** **položku Záloha .**

    ![Možnost Zálohovat nyní](./media/backup-azure-manage-vms/backup-now-button.png)

2. Z **typu správy zálohování**vyberte Azure Virtual **Machine**. Zobrazí se podokno **Položka zálohování (Virtuální počítač Azure).**
3. Vyberte virtuální počítač a vyberte **Zálohování nyní,** chcete-li vytvořit zálohu na vyžádání. Zobrazí se podokno **Zálohovat.**
4. V poli **Zachovat dobu zálohování** zadejte datum, kdy má být záloha zachována.

    ![Kalendář Zálohování](./media/backup-azure-manage-vms/backup-now-check.png)

5. Chcete-li spustit úlohu zálohování, vyberte **možnost OK.**

Chcete-li sledovat průběh úlohy, vyberte na řídicím panelu úschovny dlaždici **Zálohování úloh.**

## <a name="stop-protecting-a-vm"></a>Ukončení ochrany virtuálního virtuálního mísa

Existují dva způsoby, jak zastavit ochranu virtuálního mísa:

* **Zastavte ochranu a uchovávejte záložní data**. Tato možnost zabrání všem budoucím úlohaem zálohování v ochraně virtuálního počítače. služba Azure Backup však zachová zálohované body obnovení.  Budete muset zaplatit, aby body obnovení v trezoru (viz [Azure Backup ceny](https://azure.microsoft.com/pricing/details/backup/) podrobnosti). V případě potřeby budete moct virtuální ho obnovit. Pokud se rozhodnete obnovit ochranu virtuálních počítače, můžete použít možnost *Pokračovat v zálohování.*
* **Zastavte ochranu a odstraňte záložní data**. Tato možnost zabrání všem budoucím úlohaem zálohování v ochraně virtuálního počítače a odstraní všechny body obnovení. Nebudete moct obnovit virtuální ho virtuálního počítače ani použít *možnost Obnovení zálohování.*

>[!NOTE]
>Pokud odstraníte zdroj dat bez zastavení zálohování, nové zálohy se nezdaří. Platnost starých bodů obnovení vyprší podle zásad, ale jeden poslední bod obnovení bude vždy zachován, dokud nezastavíte zálohy a neodstraníte data.
>

### <a name="stop-protection-and-retain-backup-data"></a>Zastavení ochrany a zachování záložních dat

Ukončení ochrany a uchování dat virtuálního virtuálního mandu:

1. Na [řídicím panelu položky úschovny](#view-vms-on-the-dashboard)vyberte **Zastavit zálohování**.
2. Zvolte **Zachovat záložní data**a podle potřeby výběr potvrďte. Pokud chcete, přidejte komentář. Pokud si nejste jisti názvem položky, najeďte na vykřičník a zobrazte název.

    ![Zachovat záložní data](./media/backup-azure-manage-vms/retain-backup-data.png)

Oznámení vás dozví, že úlohy zálohování byly zastaveny.

### <a name="stop-protection-and-delete-backup-data"></a>Ukončení ochrany a odstranění záložních dat

Ukončení ochrany a odstranění dat virtuálního virtuálního mandu:

1. Na [řídicím panelu položky úschovny](#view-vms-on-the-dashboard)vyberte **Zastavit zálohování**.
2. Zvolte **Odstranit záložní data**a podle potřeby výběr potvrďte. Zadejte název zálohovací položky a v případě, že chcete, přidejte komentář.

    ![Odstranění zálohovaných dat](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Obnovení ochrany virtuálního počítače

Pokud jste zvolili [možnost Zastavit ochranu a zachovat záložní data](#stop-protection-and-retain-backup-data) během ochrany stop v virtuálním počítače, můžete použít pokračovat v **zálohování**. Tato možnost není k dispozici, pokud zvolíte [možnost Zastavit ochranu a odstranit záložní data](#stop-protection-and-delete-backup-data) nebo Odstranit záložní [data](#delete-backup-data).

Obnovení ochrany virtuálního počítače:

1. Na [řídicím panelu položky úschovny](#view-vms-on-the-dashboard)vyberte **Pokračovat v zálohování**.

2. Postupujte podle kroků v [části Správa zásad zálohování](#manage-backup-policy-for-a-vm) a přiřaďte zásady pro virtuální počítače. Nemusíte zvolit zásady počáteční ochrany virtuálního soudu.
3. Po použití zásad zálohování na virtuální ms se zobrazí následující zpráva:

    ![Zpráva označující úspěšně chráněný virtuální počítač](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Odstranění zálohovaných dat

Existují dva způsoby, jak odstranit záložní data virtuálního počítače:

* Na řídicím panelu položky úschovny vyberte Zastavit zálohování a postupujte podle pokynů pro [možnost Zastavit a odstranit záložní data.](#stop-protection-and-delete-backup-data)

  ![Vybrat možnost Zastavit zálohování.](./media/backup-azure-manage-vms/stop-backup-buttom.png)

* Na řídicím panelu položky úschovny vyberte Odstranit záložní data. Tato možnost je povolena, pokud jste se rozhodli [zastavit ochranu a zachovat](#stop-protection-and-retain-backup-data) možnost zálohování dat během ochrany stop vina

  ![Vybrat Odstranit zálohu](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  * Na [řídicím panelu položky úschovny](#view-vms-on-the-dashboard)vyberte **Odstranit záložní data**.
  * Zadejte název položky zálohy a potvrďte, že chcete odstranit body obnovení.

    ![Odstranění zálohovaných dat](./media/backup-azure-manage-vms/delete-backup-data1.png)

  * Chcete-li odstranit záložní data pro položku, vyberte **odstranit**. Oznámení vás dozví, že záložní data byla odstraněna.

K ochraně dat azure backup obsahuje funkci obnovitelného odstranění. Při odstranění pomocí obnovitelného odstranění i po odstranění zálohy (všechny body obnovení) virtuálního počítače se záložní data uchovají dalších 14 dní. Další informace naleznete [v dokumentaci k odstranění měkkých dat](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud).

  > [!NOTE]
  > Když odstraníte záložní data, odstraníte všechny přidružené body obnovení. Nelze vybrat konkrétní body obnovení, které chcete odstranit.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Položka zálohování, u které primární zdroj dat již neexistuje

* Pokud virtuální počítače Azure nakonfigurované pro zálohování Azure jsou odstraněny nebo přesunuty bez zastavení ochrany, pak plánované úlohy zálohování a na vyžádání (ad-hoc) úlohy zálohování se nezdaří s chybou UserErrorVmNotFoundV2. Předběžná kontrola zálohování se zobrazí jako kritická pouze pro neúspěšné úlohy zálohování na vyžádání (neúspěšné naplánované úlohy nejsou zobrazeny).
* Tyto položky zálohování zůstávají aktivní v systému, který se řídí zásadami zálohování a uchovávání informací nastavenými uživatelem. Zálohovaná data pro tyto virtuální počítače Azure se zachovají podle zásad uchovávání informací. Body obnovení, jejichž platnost vypršela (s výjimkou posledního bodu obnovení), jsou vyčištěny podle rozsahu uchování nastaveného v zásadách zálohování.
* Uživatelům se doporučuje odstranit položky zálohování, u kterých primární zdroj dat již neexistuje, aby se zabránilo dalším nákladům, pokud se zálohovací položka nebo data pro prostředky odstranění již nevyžadují, protože poslední bod obnovení zůstane navždy zachován a uživatel i nadále bude účtován podle platných cen zálohy.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [zálohovat virtuální počítače Azure z nastavení virtuálního počítače](backup-azure-vms-first-look-arm.md).
* Přečtěte si, jak [obnovit virtuální ms](backup-azure-arm-restore-vms.md).
* Přečtěte si, jak [monitorovat zálohy virtuálních počítačích Azure](backup-azure-monitor-vms.md).
