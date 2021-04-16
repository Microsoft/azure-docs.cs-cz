---
title: Správa a sledování záloh agenta MARS
description: Naučte se spravovat a monitorovat zálohy agenta Microsoft Azure Recovery Services (MARS) pomocí služby Azure Backup.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 4306f01d608542f7453b32b32a1a6894c2379159
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515018"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Správa záloh agenta Microsoft Azure Recovery Services (MARS) pomocí služby Azure Backup

Tento článek popisuje, jak spravovat soubory a složky, které se zálohují pomocí agenta Microsoft Azure Recovery Services.

## <a name="modify-a-backup-policy"></a>Úprava zásady zálohování

Když upravíte zásady zálohování, můžete přidat nové položky, odebrat existující položky ze zálohy nebo vyloučit soubory z zálohování pomocí nastavení vyloučení.

- **Přidat položky** tuto možnost použijte pouze v případě, že chcete přidat nové položky k zálohování. Chcete-li odebrat existující položky, použijte možnost **Odebrat položky** nebo **Nastavení vyloučení** .  
- **Odebráním položek** pomocí této možnosti odeberete položky, které se mají zálohovat.
  - Použijte **Nastavení vyloučení** pro odebrání všech položek ve svazku namísto **Odebrání položek**.
  - Vymazání všech výběrů ve svazku způsobí, že se původní zálohy položek uchovávají v závislosti na nastavení uchování v době poslední zálohy bez rozsahu pro úpravy.
  - Po opětovném výběru těchto položek se navede na první úplné zálohování a nové změny zásad se nepoužijí na staré zálohy.
  - Když se vybere celý svazek, zachová se minulá záloha bez jakéhokoli oboru pro úpravu zásad uchovávání informací.
- **Nastavení vyloučení** Tato možnost slouží k vyloučení určitých položek, které se mají zálohovat.

### <a name="add-new-items-to-existing-policy"></a>Přidat nové položky do existujících zásad

1. V **Možnosti akce** vyberte **naplánovat zálohování**.

    ![Naplánování zálohování Windows Serveru](./media/backup-configure-vault/schedule-first-backup.png)

2. Na kartě **Vybrat položku zásady** a vyberte **Upravit plán zálohování pro vaše soubory a složky** a vyberte **Další**.

    ![Vybrat položky zásad](./media/backup-azure-manage-mars/select-policy-items.png)

3. Na kartě **Upravit nebo zastavit plánování zálohování** vyberte **provést změny zálohových položek nebo časů** a vyberte **Další**.

    ![Úprava nebo plánování zálohování](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Na kartě **Vybrat položky na zálohování** vyberte **Přidat položky** a přidejte položky, které chcete zálohovat.

    ![Úprava nebo plánování zálohování přidat položky](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. V okně **Vybrat položky** vyberte soubory nebo složky, které chcete přidat, a vyberte **OK**.

    ![Vybrat položky](./media/backup-azure-manage-mars/select-item.png)

6. Dokončete následující kroky a vyberte **Dokončit** pro dokončení operace.

### <a name="add-exclusion-rules-to-existing-policy"></a>Přidat pravidla vyloučení do existujících zásad

Můžete přidat pravidla vyloučení pro přeskočení souborů a složek, které nechcete zálohovat. To můžete provést během definování nové zásady nebo úpravou existující zásady.

1. V podokně Akce vyberte **naplánovat zálohování**. Vyberte **položku vybrat položky, které chcete zálohovat** , a vyberte **Nastavení vyloučení**.

    ![Nastavení vyloučení](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. V **Nastavení vyloučení** vyberte **Přidat vyloučení**.

    ![Přidat vyloučení](./media/backup-azure-manage-mars/add-exclusion.png)

3. V části **Vyberte položky, které chcete vyloučit**, vyhledejte soubory a složky a vyberte položky, které chcete vyloučit, a vyberte **OK**.

    ![Vyberte položky, které chcete vyloučit.](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Ve výchozím nastavení jsou vyloučeny všechny **podsložky** v rámci vybraných složek. Můžete to změnit výběrem možnosti **Ano** nebo **ne**. Můžete upravit a zadat typy souborů k vyloučení, jak je uvedeno níže:

    ![Vybrat typy podsložek](./media/backup-azure-manage-mars/subfolders-type.png)

5. Dokončete následující kroky a vyberte **Dokončit** pro dokončení operace.

### <a name="remove-items-from-existing-policy"></a>Odebrat položky z existujících zásad

1. V podokně Akce vyberte **naplánovat zálohování**. Přejít na **Vyberte položky, které chcete zálohovat**. V seznamu vyberte soubory a složky, které chcete odebrat z plánu zálohování, a vyberte **Odebrat položky**.

    ![Vyberte položky, které chcete odebrat.](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Po úplném odebrání svazku ze zásad postupujte opatrně.  Pokud ho budete potřebovat znovu přidat, bude se považovat za nový svazek. Další naplánované zálohování provede prvotní zálohování (úplné zálohování) místo přírůstkového zálohování. Pokud potřebujete dočasně odebrat a přidat pozdější položky, doporučuje se místo **Odebrání položek** použít **Nastavení vyloučení** , aby bylo zajištěno přírůstkové zálohování místo úplného zálohování.

2. Dokončete následující kroky a vyberte **Dokončit** pro dokončení operace.

## <a name="stop-protecting-files-and-folder-backup"></a>Zastavení ochrany zálohování souborů a složek

Existují dva způsoby, jak zabránit ochraně zálohování souborů a složek:

- **Zastavte ochranu a zachovejte data záloh**.
  - Tato možnost zastaví všechny budoucí úlohy zálohování z ochrany.
  - Služba Azure Backup bude nadále uchovávat všechny existující body obnovení.  
  - Budete moct obnovit zálohovaná data pro body obnovení, které nevypršely.
  - Pokud se rozhodnete obnovit ochranu, můžete použít možnost *znovu povolit plán zálohování* . Pak se data uchovávají na základě nových zásad uchovávání informací.
- **Zastavte ochranu a odstraňte zálohovaná data**.
  - Tato možnost zastaví všechny budoucí úlohy zálohování z ochrany vašich dat a odstraní všechny body obnovení.
  - Obdržíte e-mail s upozorněním na odstranění záložních dat se zprávou, že *vaše data této zálohované položky se odstranila. Tato data budou dočasně k dispozici po dobu 14 dnů, po jejímž uplynutí bude tato data trvale odstraněna* a doporučená akce znovu *zabezpečí zálohovanou položku během 14 dnů pro obnovení dat.*
  - Chcete-li obnovit ochranu, znovu proveďte ochranu do 14 dnů od operace delete.

### <a name="stop-protection-and-retain-backup-data"></a>Zastavení ochrany a uchování zálohovaných dat

1. Otevřete konzolu pro správu MARS, klikněte na **podokno akce** a **Vyberte naplánovat zálohování**.

    ![Vybrat plán zálohování](./media/backup-azure-manage-mars/mars-actions.png)
1. Na stránce **Vybrat položku zásady** vyberte možnost **Upravit plán zálohování pro vaše soubory a složky** a vyberte **Další**.

    ![Vybrat položku zásad](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Na stránce **změnit nebo zastavit naplánované zálohování** vyberte možnost **zastavit použití tohoto plánu zálohování, ale uložte uložené zálohy, dokud nebude plán znovu aktivován**. Pak vyberte **Další**.

    ![Zastavte naplánované zálohování.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. V části **pozastavit naplánované zálohování** zkontrolujte informace a vyberte **Dokončit**.

    ![Pozastaví naplánované zálohování.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. V nástroji **Upravit průběh zálohování** ověřte, že je v plánu úspěšné ukončení zálohování, a pak vyberte **Zavřít** .

### <a name="stop-protection-and-delete-backup-data"></a>Zastavení ochrany a odstranění zálohovaných dat

1. Otevřete konzolu pro správu MARS, klikněte na podokno **Akce** a vyberte **naplánovat zálohování**.
2. Na stránce **změnit nebo zastavit naplánované zálohování** vyberte možnost **ukončit používání tohoto plánu zálohování a odstraňte všechny uložené zálohy**. Pak vyberte **Další**.

    ![Upravte nebo zastavte naplánované zálohování.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na stránce **zastavit naplánované zálohování** vyberte **Dokončit**.

    ![Zastavte naplánované zálohování a vyberte Dokončit.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Zobrazí se výzva k zadání bezpečnostního kódu PIN (osobní identifikační číslo), které musíte vygenerovat ručně. Abyste to mohli udělat, nejdřív se přihlaste k Azure Portal.
5. Přejít na vlastnosti nastavení **trezoru Recovery Services**  >    >  .
6. V části **bezpečnostní kód PIN** vyberte **Generovat**. Zkopírujte tento kód PIN. PIN kód je platný jenom po dobu pěti minut.
7. V konzole pro správu vložte kód PIN a pak vyberte **OK**.

    ![Vygenerujte bezpečnostní kód PIN.](./media/backup-azure-delete-vault/security-pin.png)

8. Na stránce **Upravit průběh zálohování** se zobrazí následující zpráva: *Odstraněná data zálohy se uchovávají 14 dní. Po uplynutí této doby se zálohovaná data trvale odstraní.*  

    ![Upravit průběh zálohování](./media/backup-azure-delete-vault/deleted-backup-data.png)

Po odstranění místních zálohových položek postupujte podle dalších kroků na portálu.

## <a name="re-enable-protection"></a>Opětovné povolení ochrany

Pokud jste zastavili ochranu při zachování dat a rozhodli jste se obnovit ochranu, pak můžete plán zálohování znovu povolit pomocí úpravy zásady zálohování.

1. V **akcích** vyberte **naplánovat zálohování**.
1. Vyberte **znovu povolit plán zálohování. Můžete také upravit Zálohované položky nebo časy** a vybrat **Další**.<br>

    ![Znovu povolit plán zálohování](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. V poli **Vybrat položky, které se mají zálohovat** vyberte **Další**.

    ![Vyberte položky, které chcete zálohovat.](./media/backup-azure-manage-mars/re-enable-next.png)
1. V části **zadat plán zálohování** zadejte plán zálohování a vyberte **Další**.
1. V poli **Vybrat zásady uchovávání informací** zadejte dobu uchování a vyberte **Další**.
1. Nakonec na obrazovce **potvrzení** zkontrolujte podrobnosti zásady a vyberte **Dokončit**.

## <a name="re-generate-passphrase"></a>Znovu vygenerovat heslo

K šifrování a dešifrování dat se používá přístupové heslo při zálohování nebo obnovení místního nebo místního počítače pomocí agenta MARS do systému nebo z Azure. Pokud jste heslo ztratili nebo zapomněli, můžete heslo znovu vygenerovat (Pokud je váš počítač pořád zaregistrovaný v trezoru Recovery Services a konfigurace zálohování), postupujte podle následujících kroků:

1. V konzole agenta Mars přejděte do **podokna akce**  >  **změnit vlastnosti** >. Pak přejdete na **kartu šifrování**.<br>
1. Zaškrtněte políčko **změnit heslo** .<br>
1. Zadejte nové heslo nebo vyberte **generovat heslo**.
1. Vyberte **Procházet** a uložte nové heslo.

    ![Vygenerujte heslo.](./media/backup-azure-manage-mars/passphrase.png)

1. Kliknutím na **tlačítko OK** aplikujte změny.  Pokud je [funkce zabezpečení](./backup-azure-security-feature.md#enable-security-features) v Azure Portal pro trezor Recovery Services povolená, zobrazí se výzva k zadání bezpečnostního kódu PIN. Chcete-li získat kód PIN, postupujte podle kroků uvedených v tomto [článku](./backup-azure-security-feature.md#authentication-to-perform-critical-operations).<br>
1. Vložte bezpečnostní kód PIN z portálu a vyberte **OK** , aby se změny projevily.<br>

    ![Vložte bezpečnostní kód PIN.](./media/backup-azure-manage-mars/passphrase2.png)
1. Ujistěte se, že je přístupové heslo bezpečně uložené v alternativním umístění (jiné než zdrojový počítač), nejlépe v Azure Key Vault. Pokud máte více počítačů zálohovaných pomocí agentů MARS, Sledujte všechna hesla.

## <a name="managing-backup-data-for-unavailable-machines"></a>Správa zálohovaných dat pro nedostupné počítače

Tato část popisuje situaci, kdy váš zdrojový počítač chráněný službou MARS už není dostupný, protože byl odstraněný, poškozený, nakažený malwarem/ransomwarem nebo vyřazený z provozu.

Pro tyto počítače služba Azure Backup zajišťuje, že poslední bod obnovení nevyprší (tj. nebude vysušen) podle pravidel uchovávání informací uvedených v zásadách zálohování. Proto můžete počítač bezpečně obnovit.  Vezměte v úvahu následující scénáře, které můžete provádět u zálohovaných dat:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>Scénář 1: zdrojový počítač není k dispozici a už nemusíte uchovávat data záloh.

- Zálohovaná data můžete z Azure Portal odstranit pomocí kroků uvedených v [tomto článku](backup-azure-delete-vault.md#delete-protected-items-on-premises).

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>Scénář 2: zdrojový počítač není k dispozici a je třeba zachovat data záloh.

Správa zásad zálohování pro MARS se provádí prostřednictvím konzoly MARS a nikoli prostřednictvím portálu. Pokud potřebujete pro existující body obnovení před vypršením platnosti zvětšit nastavení uchovávání, pak je potřeba obnovit počítač, nainstalovat konzolu MARS a tuto zásadu napřed.

- Chcete-li obnovit počítač, proveďte následující kroky:
  1. [Obnovení virtuálního počítače do alternativního cílového počítače](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Vytvořte znovu cílový počítač se stejným názvem hostitele, jako má zdrojový počítač.
  1. Nainstalujte agenta a znovu ho zaregistrujte do stejného trezoru a se stejným heslem.
  1. Spusťte klienta MARS a prodlužte dobu uchovávání podle vašich požadavků.
- Váš nově obnovený počítač chráněný pomocí MARS bude i nadále provádět zálohování.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>Konfigurace antivirového programu pro agenta MARS

Pro antivirový software doporučujeme následující konfiguraci, aby nedocházelo ke konfliktům s operací agenta MARS.

1. **Přidat vyloučení cest**: Chcete-li se vyhnout snížení výkonu a možných konfliktů, vylučte následující cesty z monitorování v reálném čase antivirového softwaru:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` a podsložky
    1. **Pomocná složka**: Pokud složka pomocné složky není ve standardním umístění, přidejte ji také k vyloučení.  Postup určení umístění pomocné složky [najdete tady](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-) .
1. **Přidat binární vyloučení**: aby se zabránilo snížení výkonu aktivit zálohování a konzoly, vylučte procesy pro následující binární soubory ze sledování v reálném čase pomocí antivirového softwaru:
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>I když vyloučení těchto cest bude pro většinu antivirového softwaru dostatečné, může se stát, že i nadále nekoliduje s operacemi agenta MARS. Pokud se vám zobrazují neočekávané chyby, dočasně odinstalujte antivirový software a monitorujte, abyste viděli, jestli problém nezmizí. Pokud se problém vyřeší, obraťte se na dodavatele antivirového softwaru a požádejte ho o správnou konfiguraci jejich produktu.

## <a name="next-steps"></a>Další kroky

- Informace o podporovaných scénářích a omezeních najdete v tématu [matice podpory pro agenta Mars](./backup-support-matrix-mars-agent.md).
- Přečtěte si další informace o [chování uchovávání zásad zálohování na vyžádání](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
- Nejčastější dotazy najdete v tématu [Nejčastější dotazy k agentovi Mars](backup-azure-file-folder-backup-faq.yml).
