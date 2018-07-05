---
title: Funkce zabezpečení k ochraně hybridní zálohy, které používají Azure Backup
description: Další informace o použití funkce zabezpečení ve službě Azure Backup kvůli většímu zabezpečení zálohy
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2017
ms.author: trinadhk
ms.openlocfilehash: 714c8fde28be63e5173f89f92d186445f0990214
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447376"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Funkce zabezpečení k ochraně hybridní zálohy, které používají Azure Backup
Otázky týkající se otázek zabezpečení, jako je malware, před ransomwarem a neoprávněných vniknutí, roste. Tyto problémy se zabezpečením může být náročné, peněz i data. Pro ochranu před těmito útoky, Azure Backup teď poskytuje funkce zabezpečení k ochraně hybridní zálohy. Tento článek popisuje, jak povolit a používat tyto funkce pomocí agenta Azure Recovery Services a služby Azure Backup serveru. Tyto funkce patří:

- **Ochrany před únikem informací**. Přidání další úroveň ověřování pokaždé, když se provádí důležité operace, jako je změna přístupové heslo. Toto ověření je zajistit, že tyto operace můžete provádět jenom uživatelé, kteří mají platné přihlašovací údaje Azure.
- **Výstrahy**. Správce předplatného je odeslána e-mailové oznámení pokaždé, když se provádí důležité operace, jako je odstraňují se záložní data. Tento e-mail zajistí, že se uživatel rychle dozví týkající se těchto akcí.
- **Obnovení**. Odstraněná data záloh uchovávají po dobu dalších 14 dnů od odstranění. Tím se zajistí možnosti obnovení dat v daném časovém období, proto nedochází ke ztrátě dat i v případě útoku dojde. Větší počet bodů obnovení minimální se také udržuje pro ochranu proti poškozená data.

> [!NOTE]
> Pokud používáte infrastruktury jako služby (IaaS) zálohu virtuálního počítače by se neměla povolovat funkce zabezpečení. Tyto funkce ještě nejsou k dispozici pro zálohování virtuálních počítačů IaaS, takže povolovat nebude mít žádný vliv. Zabezpečení funkce povolena, pouze v případě, že používáte: <br/>
>  * **Azure Backup agent**. Minimálně verzi agenta 2.0.9052. Po povolení těchto funkcí, měli byste upgradovat na tuto verzi agenta pro kritické operace provést. <br/>
>  * **Azure Backup Server**. Minimální Azure Backup verze agenta 2.0.9052 s Azure Backup serveru Update 1. <br/>
>  * **System Center Data Protection Manager**. Minimální Azure Backup verzi agenta 2.0.9052 s Data Protection Manager 2012 R2 UR12 nebo aplikace Data Protection Manager 2016 UR2. <br/> 


> [!NOTE]
> Tyto funkce jsou dostupné pouze pro trezor služby Recovery Services. Všechny nově vytvořené trezory služby Recovery Services mají tyto funkce ve výchozím nastavení povolená. Pro existující trezory služby Recovery Services uživatelé tyto funkce povolit pomocí kroků uvedených v následující části. Po funkce jsou povolené, se vztahují na všechny služby Recovery Services agent počítače, instance Azure Backup serveru a aplikace Data Protection Manager servery zaregistrované v úložišti. Povolení tohoto nastavení se o jednorázovou akci, a po povolení je nelze zakázat tyto funkce.
>

## <a name="enable-security-features"></a>Povolení funkcí zabezpečení
Pokud vytvoříte trezor služby Recovery Services, můžete použít všechny funkce zabezpečení. Pokud pracujete s existující trezor, povolení funkcí zabezpečení pomocí následujících kroků:

1. Přihlaste se k webu Azure portal pomocí přihlašovacích údajů Azure.
2. Vyberte **Procházet**a typ **služby Recovery Services**.

    ![Snímek obrazovky Azure portal možnost procházení](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Objeví se seznam trezorů Recovery Services. Z tohoto seznamu vyberte trezor. Otevře se řídicí panel vybraného trezoru.
3. Ze seznamu položek, které se zobrazí v části trezor, v části **nastavení**, klikněte na tlačítko **vlastnosti**.

    ![Snímek obrazovky služby Recovery Services vault možnosti](./media/backup-azure-security-feature/vault-list-properties.png)
4. V části **nastavení zabezpečení**, klikněte na tlačítko **aktualizace**.

    ![Vlastnosti trezoru služby Recovery snímek obrazovky](./media/backup-azure-security-feature/security-settings-update.png)

    Aktualizace odkaz otevře **nastavení zabezpečení** okno, které poskytuje přehled funkcí a umožňuje vám povolit je.
5. Z rozevíracího seznamu **nakonfigurovali ověřování Azure Multi-Factor Authentication?**, vyberte hodnotu pro potvrzení, pokud jste povolili [ověřování Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md). Pokud je povoleno, zobrazí se výzva k ověření z jiného zařízení (například mobilní telefon), při přihlašování k webu Azure portal.

   Když provádíte kritické operace v zálohování, je nutné zadat bezpečnostní kód PIN, k dispozici na na webu Azure portal. Povolení ověřování Azure Multi-Factor Authentication přidává další vrstvu zabezpečení. Pouze ověřeným uživatelům s platné přihlašovací údaje Azure a ověřil z druhé zařízení, můžete přístup k webu Azure portal.
6. Chcete-li uložit nastavení zabezpečení, vyberte **povolit** a klikněte na tlačítko **Uložit**. Můžete vybrat **povolit** až poté, co vyberete hodnotu **nakonfigurovali ověřování Azure Multi-Factor Authentication?** seznamu v předchozím kroku.

    ![Snímek obrazovky nastavení zabezpečení](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Obnovení odstraněného zálohovaných dat
Zálohování odstraněná data záloh uchovávají po dobu dalších 14 dní a nedojde k jeho odstranění okamžitě, pokud **zastavení zálohování s odstranit zálohovaná data** proběhlo. K obnovení těchto dat v období za 14 dní, proveďte následující kroky, podle toho, co používáte:

Pro **agenta služeb zotavení Azure** uživatele:

1. Pokud počítač, ve kterém byly děje zálohy je stále k dispozici, použijte [obnovení dat na stejném počítači](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) ve službě Azure Recovery Services, provést obnovení při všech starých bodů obnovení.
2. Pokud tento počítač není k dispozici, použijte [obnovit na jiný počítač, který](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) použití jiného počítače služby Azure Recovery Services k získání těchto dat.

Pro **Azure Backup serveru** uživatele:

1. Pokud server, ve kterém byly děje zálohy je stále k dispozici, znovu nastavit ochranu zdroje odstraněná data a použít **obnovit Data** funkci provést obnovení při všech starých bodů obnovení.
2. Pokud tento server není k dispozici, použijte [obnovit data z jiného serveru Azure Backup](backup-azure-alternate-dpm-server.md) použít jiná instance Azure Backup serveru k získání těchto dat.

Pro **aplikace Data Protection Manager** uživatele:

1. Pokud server, ve kterém byly děje zálohy je stále k dispozici, znovu nastavit ochranu zdroje odstraněná data a použít **obnovit Data** funkci provést obnovení při všech starých bodů obnovení.
2. Pokud tento server není k dispozici, použijte [přidat externí DPM](backup-azure-alternate-dpm-server.md) k získání těchto dat pomocí jiného serveru aplikace Data Protection Manager.

## <a name="prevent-attacks"></a>Zabránění útokům
Abyste měli jistotu, že pouze platné uživatele můžete provádět různé operace byly přidány kontroly. Patří mezi ně přidáním další úrovně ověřování a správě rozsahu minimální doby uchování pro účely obnovení.

### <a name="authentication-to-perform-critical-operations"></a>Ověřování k provedení kritické operace
Jako součást přidáním další úrovně ověřování pro kritické operace, budete vyzváni, zadejte bezpečnostní kód PIN, když provádíte **zastavit ochranu a odstraňte data** a **změnit heslo** operace.

Chcete-li zobrazit tento PIN kód:

1. Přihlaste se k portálu Azure.
2. Přejděte do **trezor služby Recovery Services** > **nastavení** > **vlastnosti**.
3. V části **bezpečnostní kód PIN**, klikněte na tlačítko **generovat**. Otevře se okno, které obsahuje kód PIN musí být uvedeny v uživatelském rozhraní služby Azure Recovery Services agent.
    Tento PIN kód je platný pouze pět minut, a získá vygenerována automaticky po uplynutí této doby.

### <a name="maintain-a-minimum-retention-range"></a>Udržovat rozsahu minimální doby uchování
Aby se zajistilo, že nejsou vždy platný počet bodů obnovení k dispozici, jsme přidali následující kontroly:

- Pro denní uchovávání, minimálně **sedm** dnů uchovávání informací by mělo být provedeno.
- Týdenní uchovávání, minimálně **čtyři** týdnů uchovávání informací by mělo být provedeno.
- Měsíční uchovávání, minimálně **tři** měsíců uchovávání informací by mělo být provedeno.
- Roční uchovávání, minimálně **jeden** rok uchovávání informací by mělo být provedeno.

## <a name="notifications-for-critical-operations"></a>Oznámení pro kritické operace
Obvykle když se provádí důležité operace, správce předplatného odesílají oznámení o e-mailu s podrobnostmi o operaci. Další e-mailové příjemce těchto oznámení můžete nakonfigurovat pomocí webu Azure portal.

Funkce zabezpečení, které jsou uvedené v tomto článku poskytují mechanismy ochrany před mobilními útoky cílové. Důležitější je pokud k útoku dojde, tyto funkce nabízejí tyto možnosti obnovit data.

## <a name="troubleshooting-errors"></a>Řešení chyb
| Operace | Detaily chyby | Řešení |
| --- | --- | --- |
| Změna zásad |Zásady zálohování se nepodařilo upravit. Chyba: Aktuální operace selhala kvůli vnitřní chybě služby [0x29834]. Zkuste operaci po nějaké době zopakovat. Pokud se problém opakuje, obraťte se prosím na podporu Microsoftu. |**Příčina:**<br/>Tato chyba pochází, když je povolené nastavení zabezpečení, pokusíte se snížit rozsah uchování pod minimální výše uvedené hodnoty a na nepodporovanou verzi (podporované verze jsou určené v prvním Poznámka: část tohoto článku). <br/>**Doporučená akce:**<br/> V takovém případě byste měli nastavit dobu uchování nad minimální zadaná doba uchovávání období (7 dní pro denní, čtyři týdny pro týdenní, tří týdnů pro každý měsíc nebo jeden rok pro roční) a pokračujte zásady týkající se aktualizací. Volitelně můžete oblíbený přístup by aktualizovat agenta zálohování, Azure Backup Server a/nebo DPM UR využívat všechny aktualizace zabezpečení. |
| Změnit heslo |Zadaný bezpečnostní kód PIN je nesprávný. (ID: 100130) Zadejte správný bezpečnostní kód PIN k dokončení této operace. |**Příčina:**<br/> Tato chyba pochází, když zadáte neplatnými nebo prošlými bezpečnostního kódu PIN při provádění kritické operace (například změnit heslo). <br/>**Doporučená akce:**<br/> K dokončení operace, je nutné zadat platný bezpečnostní kód PIN. Pokud chcete získat kód PIN, přihlaste se k webu Azure portal a přejděte do trezoru služby Recovery Services > Nastavení > Vlastnosti > Generovat bezpečnostní kód PIN. Chcete-li změnit heslo, použijte tento PIN kód. |
| Změnit heslo |Operace se nezdařila. ID: 120002 |**Příčina:**<br/>Tato chyba pochází, když je povolené nastavení zabezpečení, zkuste změnit heslo a jsou na nepodporovanou verzi (platná verze zadané v prvním Poznámka: část tohoto článku).<br/>**Doporučená akce:**<br/> Chcete-li změnit heslo, musíte nejprve aktualizovat agenta zálohování na minimální verzi minimální 2.0.9052, Azure Backup serveru k minimální update 1, a/nebo DPM minimální aplikace DPM 2012 R2 UR12 nebo DPM 2016 UR2 (odkazy na stažení níže), zadejte platný bezpečnostní kód PIN. Pokud chcete získat kód PIN, přihlaste se k webu Azure portal a přejděte do trezoru služby Recovery Services > Nastavení > Vlastnosti > Generovat bezpečnostní kód PIN. Chcete-li změnit heslo, použijte tento PIN kód. |

## <a name="next-steps"></a>Další postup
* [Začínáme s Azure Recovery Services vault](backup-azure-vms-first-look-arm.md) a povolení těchto funkcí.
* [Stáhněte si nejnovější verzi agenta Azure Recovery Services](http://aka.ms/azurebackup_agent) k ochraně počítačů s Windows a chránit vaše zálohovaná data před útoky.
* [Stáhněte si nejnovější Server Azure Backup](https://aka.ms/latest_azurebackupserver) k ochraně úloh a chránit vaše zálohovaná data před útoky.
* [Stáhněte si UR12 pro System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) nebo [stáhnout UR2 pro System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) k ochraně úloh a chránit vaše zálohovaná data před útoky.
