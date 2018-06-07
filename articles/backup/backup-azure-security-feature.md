---
title: Funkce zabezpečení k ochraně hybridní zálohování, které používají Azure Backup
description: Další informace o použití funkce zabezpečení v Azure Backup zabezpečit zálohy
services: backup
author: JPallavi
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2017
ms.author: pajosh
ms.openlocfilehash: 11483cd87600ef8b10c2c7492e2434b9ab97149e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606201"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Funkce zabezpečení k ochraně hybridní zálohování, které používají Azure Backup
Pochybnostmi problémy se zabezpečením, jako je malware, Ransomware, který a narušení, se zvyšuje. Tyto problémy se zabezpečením může být drahé z hlediska peníze a data. Ochrana před těmito útoky, Azure Backup teď poskytuje funkce zabezpečení k ochraně hybridní zálohy. Tento článek obsahuje informace o povolení a používání těchto funkcí pomocí agenta služeb zotavení Azure a serveru Azure Backup. Tyto funkce patří:

- **Prevence**. Další úroveň ověřování je přidána vždy, když se provádí kritické operace, jako je změna přístupové heslo. Toto ověření je zajistit, že tyto operace může provést pouze uživatelé, kteří mají platné přihlašovací údaje Azure.
- **Výstrahy**. Správce předplatného je odeslána e-mailové oznámení vždy, když se provádí kritické operace jako odstraňují se záložní data. Tento e-mail zajistí, že se uživatel dozví o takové akce rychle.
- **Obnovení**. Odstraněné zálohovaná data se uchovávají pro další 14 dní od data k odstranění. Tím se zajistí obnovitelnost data v daném časovém období, takže není nedošlo ke ztrátě dat, i když se stane útoku. Větší počet bodů obnovení minimální jsou také udržuje pro ochranu proti poškozená data.

> [!NOTE]
> Funkce zabezpečení by nemělo být povoleno, pokud používáte infrastrukturu jako službu (IaaS) zálohování virtuálních počítačů. Tyto funkce ještě nejsou k dispozici pro zálohování virtuálních počítačů IaaS, takže povolením nebude mít žádný vliv. Funkce zabezpečení lze povolit pouze v případě, že používáte: <br/>
>  * **Azure Backup agent**. Verze agenta minimální 2.0.9052. Po povolení těchto funkcí, měli byste upgradovat na tuto verzi agenta k provedení kritické operace. <br/>
>  * **Azure Backup Server**. Minimální Azure Backup agent verze 2.0.9052 s Azure Backup Server aktualizací 1. <br/>
>  * **System Center Data Protection Manager**. Minimální Azure Backup agent verze 2.0.9052 Data Protection Manager 2012 R2 UR12 nebo UR2 Data Protection Manager 2016. <br/> 


> [!NOTE]
> Tyto funkce jsou dostupné pouze pro trezor služeb zotavení. Všechny nově vytvořené trezory služeb zotavení mají tyto funkce ve výchozím nastavení povolené. Pro existující trezory služeb zotavení uživatelé tyto funkce povolit pomocí kroků uvedených v následující části. Po funkcí jsou povolené, se vztahují na všechny služby zotavení počítačů agentů, instance serveru Azure Backup a Data Protection Manager servery registraci do trezoru. Povolením tohoto nastavení je jednorázová akce a tyto funkce nelze zakázat po povolení je.
>

## <a name="enable-security-features"></a>Povolení funkcí zabezpečení
Při vytváření trezoru služeb zotavení, můžete použít všechny funkce zabezpečení. Pokud pracujete s existující trezor, povolte funkce zabezpečení pomocí následujících kroků:

1. Přihlaste se k portálu Azure pomocí přihlašovacích údajů Azure.
2. Vyberte **Procházet**a typ **služeb zotavení**.

    ![Snímek obrazovky Azure portálu zpřístupní možnost procházení](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Objeví se seznam trezorů Recovery Services. Z tohoto seznamu vyberte trezor. Otevře se řídicí panel vybraného trezoru.
3. Ze seznamu položek, který se zobrazí v úložišti, v části **nastavení**, klikněte na tlačítko **vlastnosti**.

    ![Možnosti trezoru služeb zotavení – snímek obrazovky](./media/backup-azure-security-feature/vault-list-properties.png)
4. V části **nastavení zabezpečení**, klikněte na tlačítko **aktualizace**.

    ![Vlastnosti trezoru služeb zotavení – snímek obrazovky](./media/backup-azure-security-feature/security-settings-update.png)

    Aktualizace odkaz otevře **nastavení zabezpečení** okno, které poskytuje přehled funkcí a můžete je povolit.
5. Z rozevíracího seznamu **nakonfigurovali ověřování Azure Multi-Factor Authentication?**, vyberte hodnotu pro potvrzení, pokud jste povolili [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md). Pokud je povolena, zobrazí se výzva k ověření z jiného zařízení (například mobilní telefon), při přihlášení k portálu Azure.

   Když provedete kritické operace zálohování, je nutné zadat zabezpečení PIN kódu, dostupné na portálu Azure. Povolení ověřování Azure Multi-Factor Authentication přidává další vrstvu zabezpečení. Jenom oprávnění uživatelé s platné přihlašovací údaje Azure a ověřit z druhé zařízení, můžete přístup k portálu Azure.
6. Chcete-li uložit nastavení zabezpečení, vyberte **povolit** a klikněte na tlačítko **Uložit**. Můžete vybrat **povolit** pouze po výběru hodnoty z **nakonfigurovali ověřování Azure Multi-Factor Authentication?** seznamu v předchozím kroku.

    ![Snímek obrazovky nastavení zabezpečení](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Zálohovaná data obnovit odstraněnou
Zálohování uchovává odstraněné záložní data pro další 14 dnů a nedojde k jeho odstranění okamžitě, pokud **zastavení zálohování pomocí zálohování dat odstranit** operace. Chcete-li obnovit data v období 14 dnů, proveďte následující kroky, v závislosti na tom, co používáte:

Pro **agenta služeb zotavení Azure** uživatelů:

1. Pokud počítač, kde byly děje zálohy je stále k dispozici, použijte [obnovit data do stejného počítače](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) ve službě Azure Recovery Services obnovit ze všech starých bodů obnovení.
2. Pokud tento počítač není k dispozici, použijte [obnovit na alternativní počítač](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) používat jiný počítač služeb zotavení Azure se tato data získat.

Pro **serveru Azure Backup** uživatelů:

1. Pokud je server, kde byly děje zálohy stále k dispozici, znovu ochranu zdrojů odstraněná data a pomocí **obnovit Data** funkce obnovení ze všech starých bodů obnovení.
2. Pokud tento server není k dispozici, použijte [obnovit data z jiného serveru Azure Backup](backup-azure-alternate-dpm-server.md) používat jiná instance serveru Azure Backup se tato data získat.

Pro **Data Protection Manager** uživatelů:

1. Pokud je server, kde byly děje zálohy stále k dispozici, znovu ochranu zdrojů odstraněná data a pomocí **obnovit Data** funkce obnovení ze všech starých bodů obnovení.
2. Pokud tento server není k dispozici, použijte [přidat externí DPM](backup-azure-alternate-dpm-server.md) získat tato data pomocí jiného serveru Data Protection Manager.

## <a name="prevent-attacks"></a>Zabránit útokům
Kontroly byly přidány do Ujistěte se, že pouze platné uživatelé mohou provádět různé operace. Jedná se o přidáním další úrovně ověřování a udržování minimální uchování pro účely obnovení.

### <a name="authentication-to-perform-critical-operations"></a>Ověřování k provedení kritické operace
Jako součást přidáním další úrovně ověřování pro kritické operace, budete vyzváni k zadání kódu PIN zabezpečení při provádění **zastavit ochranu s odstranit data** a **změnit heslo** operace.

Chcete-li zobrazit tento PIN kód:

1. Přihlaste se k portálu Azure.
2. Přejděte do **trezor služeb zotavení** > **nastavení** > **vlastnosti**.
3. V části **zabezpečení PIN**, klikněte na tlačítko **generování**. Otevře se okno, které obsahuje kód PIN, které je třeba zadat v uživatelském rozhraní agenta služeb zotavení Azure.
    Tento PIN kód je platný pouze pět minut, a získá automaticky generovány po uplynutí této doby.

### <a name="maintain-a-minimum-retention-range"></a>Udržovat minimální uchování
Zajistit, aby byly vždy platný počet bodů obnovení dostupné, byly přidány následující kontroly:

- Pro denní uchování, minimálně **sedm** dní uchovávání by mělo být provedeno.
- Pro týdenní uchování, minimálně **čtyři** týdny uchovávání by mělo být provedeno.
- Pro měsíční uchování, minimálně **tři** měsíců uchovávání by mělo být provedeno.
- Pro roční uchování, minimálně **jeden** rok uchovávání by mělo být provedeno.

## <a name="notifications-for-critical-operations"></a>Oznámení pro kritické operace
Obvykle když kritické operace, správce předplatného je odeslat e-mail s oznámením o podrobnosti o operaci. Další e-mailovou příjemce těchto oznámení můžete nakonfigurovat pomocí portálu Azure.

Funkce zabezpečení, které jsou uvedené v tomto článku poskytují mechanismy obrana proti cílenými útoky. Je důležité Pokud se stane útoku, tyto funkce získáte možnost obnovit data.

## <a name="troubleshooting-errors"></a>Řešení chyb
| Operace | Detaily chyby | Řešení |
| --- | --- | --- |
| Změny zásad |Zásady zálohování se nepodařilo upravit. Chyba: Aktuální operace selhala kvůli vnitřní chybě služby [0x29834]. Zkuste operaci po nějaké době zopakovat. Pokud se problém opakuje, obraťte se prosím na podporu Microsoftu. |**Příčina:**<br/>Tato chyba pochází, když je nastavení zabezpečení povoleno, pokusíte snížit rozsah uchování nižší než minimální výše uvedené hodnoty a na nepodporovanou verzi (podporované verze jsou určené v první poznamenejte si tento článek). <br/>**Doporučená akce:**<br/> V takovém případě by měl nastavit dobu uchování výše zadaná minimální doba uchovávání období (sedm dní denně, čtyři týdny týdně, tři týdny, měsíčně nebo jeden rok pro roční) Chcete-li pokračovat pomocí zásad související aktualizace. Volitelně žádoucí bude aktualizovat agenta zálohování, Azure Backup Server nebo UR aplikace DPM, můžete využít všech aktualizací zabezpečení. |
| Změnit heslo |Zabezpečení zadán kód PIN není správný. (ID: 100130) Zadejte správný kód zabezpečení PIN k dokončení této operace. |**Příčina:**<br/> Tato chyba se dodává při zadání kódu PIN zabezpečení neplatná nebo vypršela její platnost při provádění kritické operace (jako jsou změnit heslo). <br/>**Doporučená akce:**<br/> K dokončení operace, je nutné zadat platný kód PIN zabezpečení. Pokud chcete získat kód PIN, přihlaste se k portálu Azure a přejděte do trezoru služeb zotavení > Nastavení > Vlastnosti > generování kódu PIN zabezpečení. Chcete-li změnit heslo, použijte tento PIN kód. |
| Změnit heslo |Operace se nezdařila. ID: 120002 |**Příčina:**<br/>Tato chyba se dodává při nastavení zabezpečení povoleno, pokusíte změnit heslo a na nepodporovanou verzi (platná verze zadané v první poznamenejte si tento článek).<br/>**Doporučená akce:**<br/> Chcete-li změnit heslo, musíte nejdřív aktualizovat agenta zálohování na minimální verzi minimální 2.0.9052, server Azure Backup ke minimální update 1, nebo aby minimální DPM 2012 R2 UR12 aplikace DPM nebo DPM 2016 UR2 (stažení odkazy níže), zadejte platný kód PIN zabezpečení. Pokud chcete získat kód PIN, přihlaste se k portálu Azure a přejděte do trezoru služeb zotavení > Nastavení > Vlastnosti > generování kódu PIN zabezpečení. Chcete-li změnit heslo, použijte tento PIN kód. |

## <a name="next-steps"></a>Další postup
* [Začínáme s trezor služeb zotavení Azure](backup-azure-vms-first-look-arm.md) pro povolení těchto funkcí.
* [Stáhněte si nejnovější verzi agenta služeb zotavení Azure](http://aka.ms/azurebackup_agent) k ochraně počítače se systémem Windows a ochrana zálohovaných dat před útoky.
* [Stáhněte si nejnovější Server Azure Backup](https://aka.ms/latest_azurebackupserver) k ochraně úloh a ochrana zálohovaných dat před útoky.
* [Stáhněte si UR12 pro System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) nebo [stáhnout UR2 pro System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) k ochraně úloh a ochrana zálohovaných dat před útoky.
