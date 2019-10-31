---
title: Správa & monitorování Microsoft Azure Recovery Services zálohování agenta
description: Naučte se spravovat a monitorovat zálohy agenta Microsoft Azure Recovery Services pomocí služby Azure Backup.
ms.reviewer: srinathv
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: dacurwin
ms.openlocfilehash: 68c991396c92e6f288d1cc17d6b7dcf1ec79343f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165482"
---
# <a name="manage-microsoft-azure-recovery-services-agent-backups-by-using-the-azure-backup-service"></a>Správa záloh agenta Microsoft Azure Recovery Services pomocí služby Azure Backup

Tento článek popisuje, jak spravovat soubory a složky, které se zálohují pomocí agenta Microsoft Azure Recovery Services.

## <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování určují, kdy se mají pořizovat snímky dat, aby se vytvořily body obnovení a jak dlouho se mají zachovat body obnovení. Zásady zálohování můžete nakonfigurovat pomocí agenta MARS.

Vytvořte zásadu následujícím způsobem:

1. Po stažení a registraci agenta MARS spusťte konzolu agenta. Najdete ho vyhledáním **Microsoft Azure Backup** ve svém počítači.  
2. V nabídce **Akce**klikněte na **naplánovat zálohování**.

    ![Naplánování zálohování Windows Serveru](./media/backup-configure-vault/schedule-first-backup.png)
3. V Průvodci plánováním zálohování > **Začínáme**klikněte na **Další**.
4. V nabídce **Vyberte položky, které chcete zálohovat**klikněte na **Přidat položky**.

    ![Vyberte položky, které chcete zálohovat.](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. V nabídce **Vybrat položky**vyberte, co chcete zálohovat, a klikněte na **OK**.

    ![Vybrané položky k zálohování](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. Na stránce **Vyberte položky, které chcete zálohovat** klikněte na **Další**.
7. Na stránce **zadat plán zálohování** zadejte, kdy chcete provést denní nebo týdenní zálohování. Pak klikněte na tlačítko **Další**.

    - Bod obnovení se vytvoří při vytvoření zálohy.
    - Počet bodů obnovení vytvořených ve vašem prostředí závisí na plánu zálohování.

8. Můžete naplánovat každodenní zálohování, maximálně třikrát denně. Například snímek obrazovky ukazuje dva denní zálohy, jednu o půlnoc a jednu v 6:00./odp.

    ![Denní plán](./media/backup-configure-vault/day-schedule.png)

9. Můžete spustit i týdenní zálohování. Snímek obrazovky například ukazuje, že se v každé alternativní neděli & středa v 9:30 AM a 1:00 AMy zálohy.

    ![Týdenní plán](./media/backup-configure-vault/week-schedule.png)

10. Na stránce **Vybrat zásady uchovávání informací** určete způsob ukládání historických kopií vašich dat. Pak klikněte na tlačítko **Další**.

    - Nastavení uchovávání určují, které body obnovení by se měly ukládat a jak dlouho se mají ukládat.
    - Když například nastavíte denní nastavení uchovávání, označíte si, že v době určené pro denní uchovávání se bude pro zadaný počet dnů uchovávat nejnovější bod obnovení. Nebo jako jiný příklad můžete zadat měsíční zásady uchovávání informací, které označují, že bod obnovení vytvořený na 30. den v měsíci by měl být uložený na 12 měsíců.
    - Denní a týdenní uchování bodu obnovení se obvykle shoduje s plánem zálohování. To znamená, že když se zálohování spustí podle plánu, bude se bod obnovení vytvořený zálohováním ukládat po dobu určenou v zásadách denního nebo týdenního uchovávání.
    - Například na následujícím snímku obrazovky: denní zálohy s půlnocí a 6:00 PM se uchovávají po dobu sedmi dnů.
            – Zálohy provedené v sobotu s půlnocí a 6:00 PM se uchovávají po dobu čtyř týdnů.
            -Zálohy provedené v sobotu v posledním týdnu v měsíci s půlnocí a 6:00 PM se uchovávají po dobu 12 měsíců.
            -Zálohy provedené na sobotu v minulém týdnu v březnu se uchovávají po dobu 10 let.

           ![Retention example](./media/backup-configure-vault/retention-example.png)

11. V části **zvolit typ prvotní zálohy** rozhodněte, jestli chcete provést prvotní zálohování přes síť, nebo použít offline zálohování (Další informace o offline zálohování najdete v tomto [článku](backup-azure-backup-import-export.md)). Chcete-li provést prvotní zálohování sítě, vyberte možnost **automaticky přes síť** a klikněte na tlačítko **Další**.

    ![Typ prvotní zálohy](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

12. V části **potvrzení**zkontrolujte informace a pak klikněte na **Dokončit**.
    ![potvrdit typ zálohování](./media/backup-azure-manage-mars/confirm-backup-type.png)

13. Až průvodce dokončí vytváření plánu zálohování, klikněte na **Zavřít**.
  ![potvrdit úpravu procesu zálohování](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Je nutné vytvořit zásadu na každém počítači, kde je nainstalován agent.

## <a name="modify-a-backup-policy"></a>Úprava zásady zálohování

Když upravíte zásady zálohování, můžete přidat nové položky, odebrat existující položky ze zálohy nebo vyloučit soubory z zálohování pomocí nastavení vyloučení.

- **Přidat položky** tuto možnost použijte pouze v případě, že chcete přidat nové položky k zálohování. Chcete-li odebrat existující položky, použijte možnost **Odebrat položky** nebo **Nastavení vyloučení** .  
- **Odebráním položek** pomocí této možnosti odeberete položky, které se mají zálohovat.
  - Použijte **Nastavení vyloučení** pro odebrání všech položek ve svazku namísto **Odebrání položek**.
  - Vymazání všech výběrů ve svazku způsobí, že se staré zálohy položek uchovávají jako nastavení uchovávání v době poslední zálohy bez rozsahu úprav.
  - Když se tyto položky znovu vyberou, navedou se k prvnímu úplnému zálohování a nové změny zásad se pro staré zálohy neaplikují.
  - Když se vybere celý svazek, zachová se minulá záloha bez jakéhokoli oboru pro úpravu zásad uchovávání informací.
- **Nastavení vyloučení** Tato možnost slouží k vyloučení určitých položek, které se mají zálohovat.
  
### <a name="add-new-items-to-existing-policy"></a>Přidat nové položky do existujících zásad

1. V nabídce **Akce**klikněte na **naplánovat zálohování**.

    ![Naplánování zálohování Windows Serveru](./media/backup-configure-vault/schedule-first-backup.png)

2. Na kartě **Vybrat položku zásady** a vyberte **Upravit plán zálohování pro vaše soubory a složky** a klikněte na **Další**.

    ![Vybrat položky zásad](./media/backup-azure-manage-mars/select-policy-items.png)

3. Na kartě **Upravit nebo zastavit plánování zálohování** vyberte **provést změny zálohových položek nebo časů** a klikněte na **Další**.

    ![Úprava nebo plánování zálohování](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Na kartě **Vybrat položky na zálohování** klikněte na **Přidat položky** a přidejte položky, které chcete zálohovat.

    ![Úprava nebo plánování zálohování přidat položky](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. V okně **Vybrat položky** vyberte možnost letí nebo složky, které chcete přidat, a klikněte na tlačítko **OK**.

    ![Vybrat položky](./media/backup-azure-manage-mars/select-item.png)

6. Dokončete následující kroky a kliknutím na **Dokončit** dokončete operaci.

### <a name="add-exclusion-rules-to-existing-policy"></a>Přidat pravidla vyloučení do existujících zásad

Můžete přidat pravidla vyloučení pro přeskočení souborů a složek, které nechcete zálohovat. To můžete provést během definování nové zásady nebo úpravou existující zásady.

1. V podokně Akce klikněte na **naplánovat zálohování**. Přejděte na **Vybrat položky, které chcete zálohovat** , a klikněte na **Nastavení vyloučení**.

    ![Vybrat položky](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. V **Nastavení vyloučení**klikněte na **Přidat vyloučení**.

    ![Vybrat položky](./media/backup-azure-manage-mars/add-exclusion.png)

3. V části **Vyberte položky, které chcete vyloučit**, vyhledejte soubory a složky a vyberte položky, které chcete vyloučit, a klikněte na **OK**.

    ![Vybrat položky](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Ve výchozím nastavení jsou vyloučeny všechny **podsložky** v rámci vybraných složek. Můžete to změnit výběrem možnosti **Ano** nebo **ne**. Typy souborů, které se mají vyloučit, můžete upravit a určit tak, jak je znázorněno níže:

    ![Vybrat položky](./media/backup-azure-manage-mars/subfolders-type.png)

5. Dokončete následující kroky a kliknutím na **Dokončit** dokončete operaci.

### <a name="remove-items-from-existing-policy"></a>Odebrat položky z existujících zásad

1. V podokně Akce klikněte na **naplánovat zálohování**. Přejít na **Vyberte položky, které chcete zálohovat**. V seznamu vyberte soubory a složky, které chcete z plánu zálohování odebrat, a klikněte na **Odebrat položky**.

    ![Vybrat položky](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> Po úplném odebrání svazku ze zásad postupujte opatrně.  Pokud ho budete potřebovat znovu přidat, bude se považovat za nový svazek. Další naplánované zálohování provede prvotní zálohování (úplné zálohování) místo přírůstkového zálohování. Pokud potřebujete dočasně odebrat a přidat pozdější položky, doporučuje se místo **Odebrání položek** použít **Nastavení vyloučení** , aby bylo zajištěno přírůstkové zálohování místo úplného zálohování.

2. Dokončete následující kroky a kliknutím na **Dokončit** dokončete operaci.

## <a name="stop-protecting-files-and-folder-backup"></a>Zastavení ochrany zálohování souborů a složek

Existují dva způsoby, jak zabránit ochraně zálohování souborů a složek:

- **Zastavte ochranu a zachovejte data záloh**.
  - Tato možnost zastaví všechny budoucí úlohy zálohování z ochrany.
  - Služba Azure Backup zachová body obnovení, které byly zálohovány na základě zásad uchovávání informací.
  - Budete moct obnovit zálohovaná data pro body obnovení, které nevypršely.
  - Pokud se rozhodnete obnovit ochranu, můžete použít možnost *znovu povolit plán zálohování* . Pak by se data zachovala na základě nových zásad uchovávání informací.
- **Zastavte ochranu a odstraňte zálohovaná data**.
  - Tato možnost zastaví všechny budoucí úlohy zálohování z ochrany vašich dat a odstraní všechny body obnovení.
  - Obdržíte e-mail s upozorněním na odstranění zálohovacích dat se zprávou, že *vaše data této zálohované položky se odstranila. Tato data budou dočasně k dispozici po dobu 14 dnů, po jejímž uplynutí bude tato data trvale odstraněna* a doporučená akce znovu *zabezpečí zálohovanou položku během 14 dnů pro obnovení dat.*
  - Chcete-li obnovit ochranu, znovu proveďte ochranu do 14 dnů od operace delete.

### <a name="stop-protection-and-retain-backup-data"></a>Zastavení ochrany a uchování zálohovaných dat

1. Otevřete konzolu pro správu MARS, klikněte na **podokno akce**a **Vyberte naplánovat zálohování**.
    ![upravit nebo zastavit naplánované zálohování.](./media/backup-azure-manage-mars/mars-actions.png)
1. Na stránce **Vybrat položku zásady** vyberte možnost **Upravit plán zálohování pro soubory a složky** klikněte na **Další**.
    ![upravit nebo zastavit naplánované zálohování.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Na stránce **změnit nebo zastavit naplánované zálohování** vyberte možnost **Zastavit používání tohoto plánu zálohování, ale uložte uložené zálohy do doby, kdy bude plán znovu aktivován**. Pak vyberte **Next** (Další).  
    ![upravit nebo zastavit naplánované zálohování.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. V části **pozastavit naplánované zálohování** zkontrolujte informace kliknutím na **Dokončit** ![upravit nebo zastavit naplánované zálohování.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. v nástroji **upravit proces zálohování** ověřte úspěšnost stavu pozastavení zálohování a klikněte na tlačítko **Zavřít** a dokončete akci.

### <a name="stop-protection-and-delete-backup-data"></a>Zastavení ochrany a odstranění zálohovaných dat

1. Otevřete konzolu pro správu MARS, klikněte na podokno **Akce** a vyberte **naplánovat zálohování**.
2. Na stránce **změnit nebo zastavit naplánované zálohování** vyberte možnost **ukončit používání tohoto plánu zálohování a odstraňte všechny uložené zálohy**. Pak vyberte **Next** (Další).

    ![Upravte nebo zastavte naplánované zálohování.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na stránce **zastavit naplánované zálohování** vyberte **Dokončit**.

    ![Zastavte naplánované zálohování.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Zobrazí se výzva k zadání bezpečnostního kódu PIN (osobní identifikační číslo), které musíte vygenerovat ručně. Abyste to mohli udělat, nejdřív se přihlaste k Azure Portal.
5. Přejít na **Recovery Services trezoru** > **nastavení**–**vlastnosti** > .
6. V části **bezpečnostní kód PIN**vyberte **Generovat**. Zkopírujte tento kód PIN. PIN kód je platný jenom po dobu pěti minut.
7. V konzole pro správu vložte kód PIN a pak vyberte **OK**.

    ![Vygenerujte bezpečnostní kód PIN.](./media/backup-azure-delete-vault/security-pin.png)

8. Na stránce **Upravit průběh zálohování** se zobrazí následující zpráva: *Odstraněná data zálohy se uchovávají 14 dní. Po uplynutí této doby se zálohovaná data trvale odstraní.*  

    ![Odstraňte infrastrukturu zálohování.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Po odstranění místních zálohových položek postupujte podle dalších kroků na portálu.

## <a name="re-enable-protection"></a>Opětovné povolení ochrany

Pokud jste zastavili ochranu při zachování dat a rozhodli jste se obnovit ochranu, pak můžete plán zálohování znovu povolit pomocí úpravy zásady zálohování.

1. V **akcích** vyberte **naplánovat zálohování**.
1. Vyberte **znovu povolit plán zálohování. Můžete také upravit Zálohované položky nebo tines** a kliknout na **Další**.
    ![odstranit infrastrukturu zálohování.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. V nabídce **Vybrat položky, které se mají zálohovat**klikněte na **Další**.
    ![odstranit infrastrukturu zálohování.](./media/backup-azure-manage-mars/re-enable-next.png)
1. V části **zadat plán zálohování**zadejte plán zálohování a klikněte na **Další**.
1. V nabídce **Vybrat zásady uchovávání informací**zadejte dobu uchování a klikněte na **Další**.
1. Nakonec na **obrazovce dodržování** zásad zkontrolujte podrobnosti zásady a klikněte na **Dokončit**.

## <a name="next-steps"></a>Další kroky

- Informace o podporovaných scénářích a omezeních najdete v části [support Matrix pro Mars](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- Přečtěte si další informace o [chování uchovávání zásad zálohování ad hoc](https://docs.microsoft.com/azure/backup/backup-configure-vault#ad-hoc-backup-policy-retention-behavior.md).
