---
title: Funkce zabezpečení, které chrání hybridní zálohy
description: Naučte se používat funkce zabezpečení v Azure Backup k zajištění většího zabezpečení záloh.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 7213f26493a118c2cb32f8f9935b4954176b99a2
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586389"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Funkce zabezpečení, které vám pomůžou chránit hybridní zálohy, které používají Azure Backup

Problémy se zabezpečením, jako je malware, ransomwarem a vniknutí, se zvyšují. Tyto problémy se zabezpečením můžou být nákladné, s ohledem na peníze i data. Z důvodu ochrany proti takovým útokům teď Azure Backup poskytuje funkce zabezpečení, které vám pomůžou chránit hybridní zálohy. Tento článek popisuje, jak povolit a používat tyto funkce pomocí agenta Azure Recovery Services a Azure Backup Server. Mezi tyto funkce patří:

- **Prevence**. Další úroveň ověřování se přidá vždy, když se provede kritická operace, jako je změna hesla. Ověření je zajištěno, že tyto operace mohou provádět pouze uživatelé, kteří mají platné přihlašovací údaje Azure.
- **Výstrahy**. Správci předplatného se pošle e-mailové oznámení vždy, když se provede kritická operace, jako je odstraňování zálohovaných dat. Tento e-mail zajistí, že se uživateli na tyto akce rychle pošle oznámení.
- **Obnovení**. Data odstraněných záloh se uchovávají po dobu dalších 14 dní od data odstranění. Tím je zajištěna obnova dat během daného časového období, takže nedochází ke ztrátě dat, a to i v případě, že dojde k útoku. Pro ochranu před poškozenými daty je také udržován větší počet minimálních bodů obnovení.

> [!NOTE]
> Pokud používáte zálohování virtuálních počítačů infrastruktury jako služby (IaaS), neměli byste povolit funkce zabezpečení. Tyto funkce nejsou ještě dostupné pro zálohování virtuálních počítačů IaaS, takže jejich povolení nebude mít žádný vliv. Funkce zabezpečení by měly být povolené jenom v případě, že používáte: <br/>
>  * **Agent Azure Backup**. Minimální verze agenta 2.0.9052. Po povolení těchto funkcí byste měli upgradovat na tuto verzi agenta, aby se prováděly kritické operace. <br/>
>  * **Azure Backup Server**. Minimální verze agenta Azure Backup 2.0.9052 s Azure Backup Server Update 1. <br/>
>  * **Data Protection Manager softwaru System Center**. Minimální verze agenta Azure Backup 2.0.9052 s Data Protection Manager 2012 R2 UR12 nebo Data Protection Manager 2016 UR2. <br/>


> [!NOTE]
> Tyto funkce jsou dostupné jenom pro trezor Recovery Services. Všechny nově vytvořené trezory Recovery Services mají tyto funkce ve výchozím nastavení povolené. Pro existující trezory Recovery Services uživatelé tyto funkce povolili pomocí postupu uvedeného v následující části. Po povolení funkcí se použijí pro všechny počítače Recovery Services agenta, Azure Backup Server instance a servery Data Protection Manager zaregistrované v úložišti. Povolení tohoto nastavení je jednorázová akce a tyto funkce nelze po povolení zakázat.
>

## <a name="enable-security-features"></a>Povolit funkce zabezpečení

Pokud vytváříte Recovery Services trezor, můžete použít všechny funkce zabezpečení. Pokud pracujete se stávajícím trezorem, povolte funkce zabezpečení pomocí následujících kroků:

1. Přihlaste se k Azure Portal pomocí svých přihlašovacích údajů Azure.
2. Vyberte **Procházet**a zadejte **Recovery Services**.

    ![Snímek obrazovky s možností procházení Azure Portal](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Objeví se seznam trezorů Recovery Services. V tomto seznamu vyberte trezor. Otevře se řídicí panel vybraného trezoru.
3. V seznamu položek, které se objeví v trezoru, v části **Nastavení**klikněte na **vlastnosti**.

    ![Snímek obrazovky s možnostmi úložiště Recovery Services](./media/backup-azure-security-feature/vault-list-properties.png)
4. V části **nastavení zabezpečení**klikněte na **aktualizovat**.

    ![Snímek obrazovky s vlastnostmi trezoru Recovery Services](./media/backup-azure-security-feature/security-settings-update.png)

    Odkaz na aktualizaci otevře okno **nastavení zabezpečení** , které poskytuje souhrn funkcí a umožňuje jejich povolení.
5. V rozevíracím seznamu jste **nakonfigurovali azure Multi-Factor Authentication?** vyberte hodnotu a potvrďte, jestli jste povolili [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md). Pokud je tato možnost povolená, budete požádáni o ověření z jiného zařízení (například mobilního telefonu) při přihlašování k Azure Portal.

   Když provádíte kritické operace zálohování, musíte zadat bezpečnostní kód PIN, který je k dispozici na Azure Portal. Povolením Azure Multi-Factor Authentication přidáte vrstvu zabezpečení. K Azure Portal mají přístup jenom autorizovaní uživatelé s platnými přihlašovacími údaji Azure a ověřené z druhého zařízení.
6. Nastavení zabezpečení uložíte tak, že vyberete **Povolit** a kliknete na **Uložit**. Můžete vybrat **Povolit** až po výběru hodnoty z v seznamu, **který jste nakonfigurovali v Azure Multi-Factor Authentication?** v předchozím kroku.

    ![Snímek obrazovky s nastavením zabezpečení](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Obnovení odstraněných zálohovaných dat

Funkce Backup uchovává data odstraněných záloh na dalších 14 dnů a neodstraní je okamžitě, pokud je provedena operace **Zastavit zálohování s odstraněním dat zálohy** . K obnovení těchto dat v období 14 dní proveďte následující kroky v závislosti na tom, co používáte:

Pro uživatele **agenta Azure Recovery Services** :

1. Pokud je počítač, ve kterém došlo k zálohování, stále k dispozici, znovu nastavte ochranu odstraněných zdrojů dat a pomocí [obnovení dat do stejného počítače](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) v Azure Recovery Services proveďte obnovení ze všech starých bodů obnovení.
2. Pokud tento počítač není k dispozici, použijte příkaz [obnovit do alternativního](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) počítače k získání těchto dat pomocí jiného počítače s Recovery Services Azure.

Pro **Azure Backup Server** uživatele:

1. Pokud je server, na kterém proběhlo zálohování, stále k dispozici, znovu nastavte ochranu odstraněných zdrojů dat a pomocí funkce **obnovit data** obnovte ze všech starých bodů obnovení.
2. Pokud tento server není k dispozici, použijte [obnovení dat z jiného Azure Backup Server](backup-azure-alternate-dpm-server.md) k získání těchto dat pomocí jiné instance Azure Backup Server.

Pro **Data Protection Manager** uživatele:

1. Pokud je server, na kterém proběhlo zálohování, stále k dispozici, znovu nastavte ochranu odstraněných zdrojů dat a pomocí funkce **obnovit data** obnovte ze všech starých bodů obnovení.
2. Pokud tento server není k dispozici, použijte k získání těchto dat k použití jiného Data Protection Manager serveru další server [DPM](backup-azure-alternate-dpm-server.md) .

## <a name="prevent-attacks"></a>Zabránit útokům

Byly přidány kontroly, aby bylo zajištěno, že mohou provádět různé operace pouze platní uživatelé. Mezi ně patří přidání dodatečné úrovně ověřování a udržování minimálního rozsahu uchování pro účely obnovení.

### <a name="authentication-to-perform-critical-operations"></a>Ověřování pro provádění kritických operací

V rámci přidání dodatečné úrovně ověřování pro kritické operace budete vyzváni k zadání bezpečnostního kódu PIN, když provádíte **zastavení ochrany pomocí operace odstranit data** a **změnit heslo** .

> [!NOTE]
>
> V současné době není bezpečnostní kód PIN podporován pro **zastavení ochrany s odstraněním dat** pro DPM a MABS.

Chcete-li získat tento kód PIN:

1. Přihlaste se k portálu Azure.
2. Přejděte do **nastavení** > **trezoru Recovery Services** > **vlastnosti**.
3. V části **bezpečnostní kód PIN**klikněte na **vygenerovat**. Otevře se okno, které obsahuje PIN kód, který se má zadat v uživatelském rozhraní agenta Azure Recovery Services.
    Tento kód PIN je platný jenom po dobu pěti minut a po uplynutí této doby se vygeneruje automaticky.

### <a name="maintain-a-minimum-retention-range"></a>Udržovat minimální rozsah uchování

Aby bylo zajištěno, že je k dispozici vždy platný počet bodů obnovení, byly přidány následující kontroly:

- Pro každodenní uchovávání by se mělo provést minimálně **sedm** dnů uchovávání.
- Pro týdenní uchovávání by se mělo provést minimálně **čtyři** týdny uchování.
- V případě měsíčního uchování by se mělo provést minimálně **tři** měsíce uchovávání.
- Pro roční uchování by se mělo provést minimálně **jeden** rok uchovávání.

## <a name="notifications-for-critical-operations"></a>Oznámení pro kritické operace

Při provádění kritické operace obvykle správce předplatného pošle e-mailové oznámení s podrobnostmi o operaci. Další příjemce e-mailu pro tato oznámení můžete nakonfigurovat pomocí Azure Portal.

Funkce zabezpečení, které jsou uvedené v tomto článku, poskytují mechanismy ochrany proti cílovým útokům. Důležitější je, že pokud dojde k útoku, poskytují tyto funkce možnost obnovování vašich dat.

## <a name="troubleshooting-errors"></a>Řešení chyb

| Operace | Podrobnosti o chybě | Řešení |
| --- | --- | --- |
| Změna zásad |Zásady zálohování nešlo změnit. Chyba: aktuální operace selhala kvůli vnitřní chybě služby [0x29834]. Po nějaké době zkuste operaci zopakovat. Pokud se problém opakuje, obraťte se prosím na podporu Microsoftu. |**Příčina:**<br/>K této chybě dochází, pokud jsou povolena nastavení zabezpečení, pokoušíte se zmenšit rozsah uchování pod minimálními výše uvedenými minimálními hodnotami a nepodporovanou verzí (podporované verze jsou uvedeny v prvním poznámce tohoto článku). <br/>**Doporučená akce:**<br/> V takovém případě byste měli nastavit dobu uchovávání nad uvedenou minimální dobu uchování (sedm dní pro každý den, čtyři týdny pro každý týden, tři týdny pro každý měsíc nebo jeden rok), aby bylo možné pokračovat s aktualizacemi souvisejícími s zásadami. Volitelně upřednostňovaný přístup by měl aktualizovat agenta zálohování, Azure Backup Server a/nebo adresu UR aplikace DPM, aby bylo možné využívat všechny aktualizace zabezpečení. |
| Změnit heslo |Zadaný bezpečnostní kód PIN je nesprávný. (ID: 100130) Pro dokončení této operace zadejte správný bezpečnostní kód PIN. |**Příčina:**<br/> K této chybě dojde v případě, že při provádění kritické operace (například heslo pro změnu) zadáte neplatný nebo vypršelý bezpečnostní kód PIN. <br/>**Doporučená akce:**<br/> K dokončení této operace je nutné zadat platný bezpečnostní kód PIN. Pokud chcete získat kód PIN, přihlaste se k Azure Portal a přejděte do Recovery Services trezoru > Nastavení > Vlastnosti > vygenerovat bezpečnostní kód PIN. Pomocí tohoto kódu PIN můžete změnit heslo. |
| Změnit heslo |Operace se nezdařila. ID: 120002 |**Příčina:**<br/>K této chybě dochází, když je povolené nastavení zabezpečení, pokusíte se změnit heslo a budete mít nepodporovanou verzi (platné verze jsou uvedené v prvním poznámce tohoto článku).<br/>**Doporučená akce:**<br/> Pokud chcete změnit přístupové heslo, musíte nejdřív aktualizovat agenta pro zálohování na minimální verzi 2.0.9052, Azure Backup Server na minimální aktualizaci 1 nebo DPM na minimální verzi DPM 2012 R2 UR12 nebo DPM 2016 UR2 (dole si můžete stáhnout) a pak zadat platný bezpečnostní PIN kód. Pokud chcete získat kód PIN, přihlaste se k Azure Portal a přejděte do Recovery Services trezoru > Nastavení > Vlastnosti > vygenerovat bezpečnostní kód PIN. Pomocí tohoto kódu PIN můžete změnit heslo. |

## <a name="next-steps"></a>Další kroky

- Pokud chcete tyto funkce povolit, začněte [používat službu Azure Recovery Services trezor](backup-azure-vms-first-look-arm.md) .
- [Stáhněte si nejnovějšího agenta Azure Recovery Services](https://aka.ms/azurebackup_agent) , který vám umožní chránit počítače s Windows a chránit zálohovaná data proti útokům.
- [Stáhněte si nejnovější Azure Backup Server](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) , které vám pomůžou ochránit úlohy a chránit zálohovaná data proti útokům.
- [Stáhněte si UR12 pro System center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) nebo [si stáhněte UR2 pro system Center 2016 data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) , které vám pomůžou chránit úlohy a chránit zálohovaná data proti útokům.
