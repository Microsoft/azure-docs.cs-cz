---
title: Správa a sledování záloh agenta MARS
description: Zjistěte, jak spravovat a monitorovat zálohy agentů Microsoft Azure Recovery Services (MARS) pomocí služby Azure Backup.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: c11d73edd32c197aac2cec58eeb1cc20e5c6a339
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673250"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Správa záloh agentů služby Microsoft Azure Recovery Services (MARS) pomocí služby Azure Backup

Tento článek popisuje, jak spravovat soubory a složky, které jsou zálohovány pomocí agenta služby Microsoft Azure Recovery Services.

## <a name="modify-a-backup-policy"></a>Úprava zásad zálohování

Při úpravě zásad zálohování můžete přidat nové položky, odebrat existující položky ze zálohy nebo vyloučit, že soubory budou zálohovány pomocí nastavení vyloučení.

- **Přidat položky** tuto možnost použijte pouze pro přidání nových položek pro zálohování. Chcete-li odebrat existující položky, použijte možnost **Odebrat položky** nebo **Nastavení vyloučení.**  
- **Odebrat položky** pomocí této možnosti odeberte položky ze zálohovaných položek.
  - Nastavení **vyloučení** použijte k odebrání všech položek v rámci svazku namísto **odebrání položek**.
  - Vymazání všech výběrů ve svazku způsobí, že staré zálohy položek, které mají být zachovány podle nastavení uchovávání informací v době poslední zálohy, bez rozsahu pro změny.
  - Opětovným výběrem těchto položek vede k první úplné zálohě a nové změny zásad nejsou použity pro staré zálohy.
  - Zrušení výběru celého svazku zachová předchozí zálohování bez jakéhokoli rozsahu pro úpravu zásad uchovávání informací.
- **Nastavení vyloučení** tuto možnost použijte k vyloučení určitých položek ze zálohování.

### <a name="add-new-items-to-existing-policy"></a>Přidání nových položek do existujících zásad

1. V **seznamu Akce**klepněte na **položku Naplánovat zálohování**.

    ![Naplánování zálohování Windows Serveru](./media/backup-configure-vault/schedule-first-backup.png)

2. Na **kartě Vybrat položku zásad** a vyberte **Změnit plán zálohování souborů a složek** a klepněte na tlačítko **Další**.

    ![Vybrat položky zásad](./media/backup-azure-manage-mars/select-policy-items.png)

3. V **části Změnit nebo zastavit zálohování plánu** vyberte Provést změny u **zálohovacích položek nebo časů** a klepněte na tlačítko **Další**.

    ![Úprava nebo plánování zálohování](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Na kartě **Vybrat položky k zálohování** klikněte na Přidat **položky** a přidejte položky, které chcete zálohovat.

    ![Úprava nebo plánování zálohování přidat položky](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. V okně **Vybrat položky** vyberte mouchy nebo složky, které chcete přidat, a klepněte na **tlačítko OK**.

    ![Výběr položek](./media/backup-azure-manage-mars/select-item.png)

6. Dokončení následujících kroků a dokončení operace klepnutím na **tlačítko Dokončit.**

### <a name="add-exclusion-rules-to-existing-policy"></a>Přidání pravidel vyloučení k existujícím zásadám

Můžete přidat pravidla vyloučení přeskočit soubory a složky, které nechcete zálohovat. Můžete to provést při definování nové zásady nebo úpravě existující zásady.

1. V podokně Akce klepněte na **tlačítko Naplánovat zálohování**. Přejděte na **Vybrat položky k zálohování** a klepněte na nastavení **vyloučení**.

    ![Výběr položek](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. V **nastavení vyloučení**klepněte na tlačítko Přidat **vyloučení**.

    ![Výběr položek](./media/backup-azure-manage-mars/add-exclusion.png)

3. V **části Vybrat položky k vyloučení**projděte soubory a složky a vyberte položky, které chcete vyloučit, a klepněte na tlačítko **OK**.

    ![Výběr položek](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Ve výchozím nastavení jsou vyloučeny všechny **podsložky** ve vybraných složkách. Tuto možnost můžete změnit výběrem možnosti **Ano** nebo **Ne**. Můžete upravit a určit typy souborů, které chcete vyloučit, jak je znázorněno níže:

    ![Výběr položek](./media/backup-azure-manage-mars/subfolders-type.png)

5. Dokončení následujících kroků a dokončení operace klepnutím na **tlačítko Dokončit.**

### <a name="remove-items-from-existing-policy"></a>Odebrání položek z existujících zásad

1. V podokně Akce klepněte na **tlačítko Naplánovat zálohování**. Přejděte na **Možnost Vybrat položky k zálohování**. Ze seznamu vyberte soubory a složky, které chcete odebrat z plánu zálohování, a klepněte na tlačítko **Odebrat položky**.

    ![Výběr položek](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> Při úplném odebrání svazku ze zásadpostupujte opatrně.  Pokud ji potřebujete znovu přidat, bude považována za nový svazek. Další naplánované zálohování provede počáteční zálohování (úplné zálohování) namísto přírůstkového zálohování. Pokud potřebujete dočasně odebrat a přidat položky později, pak se doporučuje použít **nastavení vyloučení** namísto **odebrat položky** k zajištění přírůstkové zálohování namísto úplné zálohy.

2. Dokončení následujících kroků a dokončení operace klepnutím na **tlačítko Dokončit.**

## <a name="stop-protecting-files-and-folder-backup"></a>Ukončení ochrany zálohování souborů a složek

Existují dva způsoby, jak zastavit zálohování souborů a složek:

- **Zastavte ochranu a uchovávejte záložní data**.
  - Tato možnost zastaví všechny budoucí úlohy zálohování z ochrany.
  - Služba Azure Backup si zachová body obnovení, které byly zálohovány na základě zásad uchovávání informací.
  - Zálohovaná data pro body obnovení, jejichž platnost nevypršela, budou moci obnovit.
  - Pokud se rozhodnete obnovit ochranu, můžete použít možnost *Znovu povolit plán zálohování.* Poté by se údaje uchovaly na základě nových zásad uchovávání informací.
- **Zastavte ochranu a odstraňte záložní data**.
  - Tato možnost zabrání všem budoucím úlohaem zálohování chránit vaše data a odstraní všechny body obnovení.
  - Obdržíte e-mail s upozorněním na odstranění dat zálohování se zprávou *Vaše data pro tuto položku zálohování byla odstraněna. Tato data budou dočasně k dispozici po dobu 14 dnů, po které budou trvale odstraněny* a doporučená akce Znovu *zamknout položku zálohování do 14 dnů obnovit data.*
  - Chcete-li obnovit ochranu, znovu chránit do 14 dnů od odstranění operace.

### <a name="stop-protection-and-retain-backup-data"></a>Zastavení ochrany a zachování záložních dat

1. Otevřete konzolu pro správu MARS, přejděte do **podokna Akce**a **vyberte naplánovat zálohování**.

    ![Upravte nebo zastavte naplánované zálohování.](./media/backup-azure-manage-mars/mars-actions.png)
1. Na stránce **Vybrat položku zásad** vyberte **možnost Změnit plán zálohování souborů a složek** a klepněte na tlačítko **Další**.

    ![Upravte nebo zastavte naplánované zálohování.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Na stránce **Změnit nebo Zastavit naplánované zálohování** vyberte Zastavit používání tohoto plánu **zálohování, ale uchovávejte uložené zálohy, dokud nebude plán znovu aktivován**. Potom vyberte **Další**.

    ![Upravte nebo zastavte naplánované zálohování.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. V **pozastavit naplánované zálohování** zkontrolujte informace a klepněte na tlačítko **Dokončit**.

    ![Upravte nebo zastavte naplánované zálohování.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. V **změnit proces zálohování** zkontrolujte, zda je pozastavení zálohování plánu ve stavu úspěchu a klepněte na tlačítko **Zavřít.**

### <a name="stop-protection-and-delete-backup-data"></a>Ukončení ochrany a odstranění záložních dat

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

## <a name="re-enable-protection"></a>Znovu povolit ochranu

Pokud jste ochranu zastavili při zachování dat a rozhodli jste se obnovit ochranu, můžete znovu povolit plán zálohování pomocí zásad y úpravy zálohování.

1. V **nabídce Akce** vyberte **Naplánovat zálohování**.
1. Vyberte **Znovu povolit plán zálohování. Můžete také upravit položky zálohy nebo časy** a klepněte na tlačítko **Další**.<br>

    ![Odstraňte infrastrukturu zálohování.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. V **seznamu Vybrat položky k zálohování**klepněte na tlačítko **Další**.

    ![Odstraňte infrastrukturu zálohování.](./media/backup-azure-manage-mars/re-enable-next.png)
1. V **části Zadat plán zálohování**zadejte plán zálohování a klepněte na tlačítko **Další**.
1. V **poli Vyberte zásady uchovávání informací**zadejte dobu uchovávání informací a klepněte na tlačítko **Další**.
1. Nakonec na obrazovce **Potvrzení** zkontrolujte podrobnosti o zásadách a klepněte na tlačítko **Dokončit**.

## <a name="re-generate-passphrase"></a>Opětovné generování přístupové fráze

Přístupové heslo se používá k šifrování a dešifrování dat při zálohování nebo obnovení místního nebo místního počítače pomocí agenta MARS do nebo z Azure. Pokud jste přístupové heslo ztratili nebo zapomněli, můžete přístupové heslo znovu vygenerovat (za předpokladu, že je počítač stále zaregistrován v trezoru služby Recovery Services a záloha je nakonfigurována) následujícím postupem:

- Z konzoly agenta MARS přejděte na > > **změnit vlastnosti** **podokna akcí.** Potom přejděte na **kartu Šifrování**.<br>
- Zaškrtněte políčko **Změnit přístupové heslo.**<br>
- Zadejte nové přístupové heslo nebo klepněte na **tlačítko Generovat přístupové heslo**.
- Kliknutím na **Procházet** uložte nové přístupové heslo.

    ![Vygenerujte přístupové heslo.](./media/backup-azure-manage-mars/passphrase.png)
- Chcete-li použít změny, klepněte na tlačítko **OK.**  Pokud je [funkce zabezpečení](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) povolena na webu Azure Portal pro trezor služeb obnovení, budete vyzváni k zadání kódu PIN zabezpečení. Chcete-li získat kód PIN, postupujte podle pokynů uvedených v tomto [článku](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations).<br>
- Vložte bezpečnostní KÓD PIN z portálu a klepnutím na **tlačítko OK** aplikujte změny.<br>

    ![Vygenerujte přístupové heslo.](./media/backup-azure-manage-mars/passphrase2.png)
- Ujistěte se, že přístupové heslo je bezpečně uloženy v alternativním umístění (jiné než zdrojový počítač), nejlépe v trezoru klíčů Azure. Mějte přehled o všech přístupových frází, pokud máte více počítačů zálohovány s agenty MARS.


## <a name="next-steps"></a>Další kroky

- Informace o podporovaných scénářích a omezeních naleznete v [matici podpory pro agenta MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- Další informace o [chování uchovávání zásad zálohování na vyžádání](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
