---
title: Funkce zabezpečení, které chrání hybridní zálohy
description: Přečtěte si, jak pomocí funkcí zabezpečení v Azure Backupu zabezpečit zálohování.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 7213f26493a118c2cb32f8f9935b4954176b99a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586389"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Funkce zabezpečení pomáhají chránit hybridní zálohy, které používají Azure Backup

Obavy z bezpečnostních problémů, jako je malware, ransomware a vniknutí, se zvyšují. Tyto bezpečnostní otázky mohou být nákladné, a to jak z hlediska peněz, tak z hlediska dat. Služba Azure Backup nyní poskytuje funkce zabezpečení, které pomáhají chránit hybridní zálohy, aby se před takovými útoky chránily. Tento článek popisuje, jak povolit a používat tyto funkce pomocí agenta služby Azure Recovery Services a Azure Backup Server. Mezi tyto funkce patří:

- **Prevence**. Další vrstva ověřování je přidána vždy, když je provedena kritická operace, jako je změna přístupového hesla. Toto ověření je zajistit, že tyto operace lze provádět pouze uživatelé, kteří mají platné přihlašovací údaje Azure.
- **Upozorňování**. E-mailové oznámení se odesílá správci předplatného vždy, když se provádí kritická operace, jako je odstranění záložních dat. Tento e-mail zajišťuje, že uživatel je o těchto akcích rychle informován.
- **Obnova**. Odstraněná data zálohování jsou uchovávána dalších 14 dní od data odstranění. Tím je zajištěna obnovitelnost dat v daném časovém období, takže nedojde ke ztrátě dat i v případě, že dojde k útoku. Také větší počet minimální body obnovení jsou udržovány na ochranu proti poškozeným datům.

> [!NOTE]
> Funkce zabezpečení by neměly být povoleny, pokud používáte infrastrukturu jako zálohu virtuálního počítače služby (IaaS). Tyto funkce ještě nejsou k dispozici pro zálohování virtuálních počítače IaaS, takže jejich povolení nebude mít žádný vliv. Funkce zabezpečení by měly být povoleny pouze v případě, že používáte: <br/>
>  * **Agent zálohování Azure**. Minimální verze agenta 2.0.9052. Po povolení těchto funkcí byste měli upgradovat na tuto verzi agenta a provádět důležité operace. <br/>
>  * **Azure Backup Server**. Minimální azure backup agent verze 2.0.9052 s aktualizací serveru Zálohování Azure 1. <br/>
>  * **Správce ochrany dat system center**. Minimální verze agenta Azure Backup verze 2.0.9052 s Správcem ochrany dat 2012 R2 UR12 nebo Správcem ochrany dat 2016 UR2. <br/>


> [!NOTE]
> Tyto funkce jsou k dispozici pouze pro trezor služby Recovery Services. Všechny nově vytvořené trezory služby Recovery Services mají tyto funkce ve výchozím nastavení povolené. U existujících trezorů služby Recovery Services uživatelé povolují tyto funkce pomocí kroků uvedených v následující části. Po povolení funkcí platí pro všechny počítače agenta služby Recovery Services, instance Serveru zálohování Azure a servery Správce ochrany dat registrované v úložišti. Povolení tohoto nastavení je jednorázová akce a po jejich povolení nelze tyto funkce zakázat.
>

## <a name="enable-security-features"></a>Povolení funkcí zabezpečení

Pokud vytváříte trezor služby Recovery Services, můžete použít všechny funkce zabezpečení. Pokud pracujete s existujícím trezorem, povolte funkce zabezpečení takto:

1. Přihlaste se k portálu Azure pomocí přihlašovacích údajů Azure.
2. Vyberte **Procházet**a zadejte **Služby obnovení**.

    ![Snímek obrazovky s možností Procházet na portálu Azure](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Objeví se seznam trezorů Recovery Services. V tomto seznamu vyberte úschovnu. Otevře se řídicí panel vybraného trezoru.
3. V seznamu položek, které se zobrazí pod trezorem, klikněte v části **Nastavení**na **položku Vlastnosti**.

    ![Snímek obrazovky s možnostmi trezoru služby Recovery Services](./media/backup-azure-security-feature/vault-list-properties.png)
4. V části **Nastavení zabezpečení**klepněte na tlačítko **Aktualizovat**.

    ![Snímek obrazovky s vlastnostmi úložiště služby Recovery Services](./media/backup-azure-security-feature/security-settings-update.png)

    Odkaz aktualizace otevře okno **Nastavení zabezpečení,** které poskytuje souhrn funkcí a umožňuje jejich povolení.
5. V rozevíracím seznamu **Nakonfigurovali jste azure vícefaktorové ověřování?**, vyberte hodnotu, která se potvrdí, pokud jste povolili [azure multifaktorové ověřování](../active-directory/authentication/multi-factor-authentication.md). Pokud je povolená, budete vyzváni k ověření z jiného zařízení (například mobilního telefonu) při přihlášení k portálu Azure.

   Když provádíte kritické operace v programu Zálohování, musíte zadat pin zabezpečení, který je k dispozici na webu Azure Portal. Povolení azure multi-factor authentication přidá vrstvu zabezpečení. K portálu Azure mají přístup jenom oprávnění uživatelé s platnými přihlašovacími údaji Azure a ověření z druhého zařízení.
6. Chcete-li uložit nastavení zabezpečení, vyberte **možnost Povolit** a klepněte na tlačítko **Uložit**. Můžete vybrat **Povolit** pouze po výběru hodnoty ze seznamu **Jste nakonfigurovali Azure Multi-Factor Authentication?** v předchozím kroku.

    ![Snímek obrazovky s nastavením zabezpečení](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Obnovení odstraněných záložních dat

Zálohování uchovává odstraněná data zálohování dalších 14 dní a neodstraní je okamžitě, pokud je provedena **operace Zastavit zálohování s odstraněním záložních dat.** Chcete-li tato data obnovit během 14denního období, postupujte podle následujících kroků v závislosti na tom, co používáte:

Pro uživatele **agenta služby Azure Recovery Services:**

1. Pokud je počítač, ve kterém se stále prováděly zálohy, stále k dispozici, znovu chraňte odstraněné zdroje dat a použijte [obnovit data do stejného počítače](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) ve službě Azure Recovery Services, abyste se zotavili ze všech starých bodů obnovení.
2. Pokud tento počítač není k dispozici, použijte [Recover do alternativního počítače](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) k použití jiného počítače Služby azure recovery services k získání těchto dat.

Pro uživatele **zálohovacího serveru Azure:**

1. Pokud server, kde se zálohy stále k dispozici, je stále k dispozici, znovu chránit odstraněné zdroje dat a použít **obnovit data** funkce obnovit ze všech starých bodů obnovení.
2. Pokud tento server není k dispozici, použijte [recover data z jiného serveru Zálohování Azure](backup-azure-alternate-dpm-server.md) k použití jiné instance Azure Backup Server k získání těchto dat.

Pro uživatele **Správce ochrany dat:**

1. Pokud server, kde se zálohy stále k dispozici, je stále k dispozici, znovu chránit odstraněné zdroje dat a použít **obnovit data** funkce obnovit ze všech starých bodů obnovení.
2. Pokud tento server není k dispozici, použijte k získání těchto dat pomocí [funkce Přidat externí dpm](backup-azure-alternate-dpm-server.md) jiný server Správce ochrany dat.

## <a name="prevent-attacks"></a>Zabránit útokům

Byly přidány kontroly, aby bylo zajištěno, že pouze platní uživatelé mohou provádět různé operace. Mezi ně patří přidání další vrstvy ověřování a udržování minimální rozsah uchování pro účely obnovení.

### <a name="authentication-to-perform-critical-operations"></a>Ověřování pro provádění kritických operací

V rámci přidání další vrstvy ověřování pro kritické operace budete při provádění **příkazu Stop Protection pomocí** operací Odstranění dat a **Změnit přístupové fráze** vyzváni k zadání ověřovacího kódu zabezpečení.

> [!NOTE]
>
> V současné době není bezpečnostní pin podporován pro **stop protection s odstraněním dat** pro DPM a MABS.

Chcete-li získat tento KÓD PIN:

1. Přihlaste se k portálu Azure.
2. Přejděte do oblasti**Vlastnosti****nastavení** >  **úložiště** > služby Recovery Services .
3. V části **Bezpečnostní kód PIN**klepněte na tlačítko **Generovat**. Tím se otevře okno, které obsahuje PIN, který se má zadat do uživatelského rozhraní agenta služby Azure Recovery Services.
    Tento KÓD PIN je platný pouze pět minut a po uplynutí této doby se automaticky vygeneruje.

### <a name="maintain-a-minimum-retention-range"></a>Udržujte minimální rozsah zadržení

Aby bylo zajištěno, že je vždy k dispozici platný počet bodů obnovení, byly přidány následující kontroly:

- Pro denní uchovávání by mělo být provedeno minimálně **sedm** dní uchovávání.
- Pro týdenní uchovávání by měly být provedeny minimálně **čtyři** týdny uchovávání.
- Pro měsíční uchovávání by měly být provedeny minimálně **tři** měsíce uchovávání.
- Pro roční uchovávání by mělo být provedeno nejméně **jeden** rok zadržení.

## <a name="notifications-for-critical-operations"></a>Oznámení pro kritické operace

Obvykle při provádění kritické operace, admin předplatného je odesláno e-mailové oznámení s podrobnostmi o operaci. Další příjemce e-mailu pro tato oznámení můžete nakonfigurovat pomocí portálu Azure.

Funkce zabezpečení uvedené v tomto článku poskytují obranné mechanismy proti cíleným útokům. Ještě důležitější je, že pokud dojde k útoku, tyto funkce vám dávají možnost obnovit data.

## <a name="troubleshooting-errors"></a>Řešení chyb

| Operace | Podrobnosti o chybě | Řešení |
| --- | --- | --- |
| Změna zásad |Zásady zálohování nelze změnit. Chyba: Aktuální operace se nezdařila z důvodu vnitřní chyby služby [0x29834]. Opakujte operaci po nějaké mši. Pokud problém přetrvá, obraťte se na podporu Microsoftu. |**Způsobit:**<br/>Tato chyba nastává, když jsou povolena nastavení zabezpečení, pokusíte se snížit rozsah uchování pod minimální hodnoty uvedené výše a jste na nepodporované verzi (podporované verze jsou uvedeny v první poznámce k tomuto článku). <br/>**Doporučená akce:**<br/> V takovém případě byste měli nastavit dobu uchovávání nad minimální dobu uchovávání (sedm dní denně, čtyři týdny týdně, tři týdny pro měsíčně nebo jeden rok pro rok) a pokračovat v aktualizacích souvisejících se zásadami. Volitelně by upřednostňovaným přístupem bylo aktualizovat agenta zálohování, Azure Backup Server a/Nebo DPM UR využít všechny aktualizace zabezpečení. |
| Změnit přístupové heslo |Zadaný bezpečnostní kód PIN je nesprávný. (ID: 100130) Zadejte správný bezpečnostní kód PIN k dokončení této operace. |**Způsobit:**<br/> Tato chyba nastává, když zadáte neplatný bezpečnostní kód PIN nebo kód Bezpečnostní kód vypršela při provádění kritické operace (například změna přístupové fráze). <br/>**Doporučená akce:**<br/> Chcete-li operaci dokončit, musíte zadat platný bezpečnostní kód PIN. Pokud chcete pin získat, přihlaste se na portál Azure portal a přejděte do trezoru služby Recovery Services > Nastavení > vlastnosti > generovat pin y zabezpečení. Pomocí tohoto kódu PIN můžete změnit přístupové heslo. |
| Změnit přístupové heslo |Operace se nezdařila. ID: 120002 |**Způsobit:**<br/>Tato chyba nastává, když jsou povolena nastavení zabezpečení, pokusíte se změnit přístupové heslo a jste na nepodporované verzi (platné verze uvedené v první poznámce tohoto článku).<br/>**Doporučená akce:**<br/> Chcete-li změnit přístupové heslo, musíte nejprve aktualizovat agenta zálohování na minimální verzi 2.0.9052, server Azure Backup na minimální aktualizaci 1 a/nebo DPM na minimální DPM 2012 R2 UR12 nebo DPM 2016 UR2 (odkazy ke stažení níže) a pak zadejte platný bezpečnostní kód PIN. Pokud chcete pin získat, přihlaste se na portál Azure portal a přejděte do trezoru služby Recovery Services > Nastavení > vlastnosti > generovat pin y zabezpečení. Pomocí tohoto kódu PIN můžete změnit přístupové heslo. |

## <a name="next-steps"></a>Další kroky

- [Můžete začít s trezorem Služby Azure recovery services,](backup-azure-vms-first-look-arm.md) abyste mohli tyto funkce povolit.
- [Stáhněte si nejnovějšího agenta Služby azure recovery services,](https://aka.ms/azurebackup_agent) který vám pomůže chránit počítače se systémem Windows a chránit záložní data před útoky.
- [Stáhněte si nejnovější Server zálohování Azure,](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) který vám pomůže chránit úlohy a chránit záložní data před útoky.
- [Stáhněte si UR12 pro Správce ochrany dat System Center 2012 R2](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) nebo [si stáhněte UR2 pro Správce ochrany dat System Center 2016,](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) abyste ochránili úlohy a ochránili vaše záložní data před útoky.
