---
title: Řešení problémů se zálohováním sdílených složek Azure
description: Tento článek obsahuje informace o řešení potíží, ke kterým dochází při ochraně sdílených složek Azure.
services: backup
ms.service: backup
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 334cea710d185a6774e28ea3459b3ca1ad9f846f
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36959793"
---
# <a name="troubleshoot-problems-backing-up-azure-file-shares"></a>Řešení problémů se zálohováním sdílených složek Azure
K řešení problémů a chyb, ke kterým dochází při používání zálohování sdílených složek Azure, můžete využít informace uvedené v následujících tabulkách.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Omezení zálohování sdílených složek Azure během období Preview
Zálohování sdílených složek Azure je ve verzi Preview. Následující scénáře zálohování se nepodporují u sdílených složek Azure:
- Nemůžete chránit sdílené složky Azure v účtech úložiště s replikací do [geograficky redundantního úložiště jen pro čtení](../storage/common/storage-redundancy-grs.md) (RA-GRS)*.
- Nemůžete chránit sdílené složky Azure v účtech úložiště s povolenými virtuálními sítěmi nebo bránou firewall.
- Pro ochranu souborů Azure pomocí služby Azure Backup není k dispozici PowerShell ani rozhraní příkazového řádku.
- Maximální počet plánovaných záloh je jedna za den.
- Maximální počet záloh na vyžádání jsou čtyři za den.
- Používejte v účtu úložiště [zámky prostředků](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest), abyste zabránili nechtěnému odstranění záloh v trezoru služby Recovery Services.
- Neodstraňujte snímky vytvořené službou Azure Backup. Odstranění snímků může způsobit ztrátu bodů obnovení nebo selhání obnovení.

\*Sdílené složky Azure v účtech úložiště s funkcí replikace do [geograficky redundantního úložiště jen pro čtení](../storage/common/storage-redundancy-grs.md) (RA-GRS) jako GRS a účtované za ceny GRS.

Zálohování pro sdílené složky Azure v účtech úložiště s replikací do [zónově redundantního úložiště](../storage/common/storage-redundancy-zrs.md) (ZRS) je aktuálně k dispozici jenom v oblastech USA – střed (CUS), USA – východ 2 (EUS2), Severní Evropa (NE), Jihovýchodní Asie (SEA) a Západní Evropa (WE).

## <a name="configuring-backup"></a>Konfigurace zálohování
Následující tabulka se týká konfigurace zálohování:

| Konfigurace zálohování | Alternativní řešení nebo tipy k řešení |
| ------------------ | ----------------------------- |
| Nemůžu najít svůj účet úložiště pro konfiguraci zálohování sdílené složky Azure. | <ul><li>Počkejte na dokončení zjišťování. <li>Zkontrolujte, jestli nějaká sdílená složka z daného účtu úložiště již není chráněná pomocí jiného trezoru služby Recovery Services. **Poznámka:** Všechny sdílené složky v účtu úložiště je možné chránit pouze v jednom trezoru služby Recovery Services. <li>Ujistěte se, že se sdílená složka nenachází v žádném z nepodporovaných účtů úložiště.|
| Chyba na portálu hlásí, že zjišťování účtů úložiště selhalo. | Pokud máte partnerské předplatné (s podporou poskytovatele CSP), chybu ignorujte. Pokud vaše předplatné nepodporuje poskytovatele CSP a vaše účty úložiště nejde zjistit, kontaktujte podporu.|
| Nepodařilo se ověřit nebo zaregistrovat vybraný účet úložiště.| Zkuste operaci zopakovat. Pokud problém přetrvává, kontaktujte podporu.|
| Nepodařilo se vypsat nebo najít sdílené složky ve vybraném účtu úložiště. | <ul><li> Ujistěte se, že účet úložiště existuje ve skupině prostředků (a nebyl odstraněn nebo přesunut po posledním ověření nebo registraci v trezoru).<li>Ujistěte se, že sdílená složka, kterou chcete chránit, nebyla odstraněná. <li>Ujistěte se, že účet úložiště podporuje zálohování sdílených složek.<li>Zkontrolujte, jestli daná sdílená složka již není chráněná ve stejném trezoru služby Recovery Services.|
| Konfigurace zálohování sdílené složky (nebo konfigurace zásad ochrany) se nedaří. | <ul><li>Zkuste operaci zopakovat a zjistěte, jestli problém přetrvává. <li> Ujistěte se, že sdílená složka, kterou chcete chránit, nebyla odstraněná. <li> Pokud se pokoušíte chránit více sdílených složek najednou a u některých z nich se to nedaří, zkuste znovu nakonfigurovat zálohování sdílených složek, u kterých došlo k chybě. |
| Po zrušení ochrany sdílené složky nejde odstranit trezor služby Recovery Services. | Na webu Azure Portal otevřete Trezor > **Infrastruktura zálohování** > **Účty úložiště** a kliknutím na **Zrušit registraci** odeberte příslušný účet úložiště z trezoru služby Recovery Services.|


## <a name="error-messages-for-backup-or-restore-job-failures"></a>Chybové zprávy pro selhání úloh zálohování nebo obnovení

| Chybové zprávy | Alternativní řešení nebo tipy k řešení |
| -------------- | ----------------------------- |
| Operace selhala, protože se sdílená složka nenašla. | Ujistěte se, že sdílená složka, kterou chcete chránit, nebyla odstraněná.|
| Účet úložiště se nenašel nebo se nepodporuje. | <ul><li>Ujistěte se, že účet úložiště existuje ve skupině prostředků a nebyl z ní odstraněn nebo odebrán po posledním ověření. <li> Ujistěte se, že účet úložiště podporuje zálohování sdílených složek.|
| Dosáhli jste maximálního počtu snímků pro tuto sdílenou složku, další snímky budete moci pořídit po vypršení platnosti starších snímků. | <ul><li> K této chybě může dojít v případě, že vytvoříte více záloh souboru na vyžádání. <li> Platí omezení 200 snímků na sdílenou složku, a to včetně snímků pořízených službou Azure Backup. Starší naplánované zálohy (nebo snímky) se čistí automaticky. Zálohy (nebo snímky) na vyžádání se musí odstranit, pokud dojde k dosažení maximálního omezení.<li> Odstraňte zálohy na vyžádání (snímky sdílené složky Azure) na portálu Soubory Azure. **Poznámka:** Pokud odstraníte snímky vytvořené službou Azure Backup, přijdete o body obnovení. |
| Zálohování nebo obnovení sdílené složky selhalo kvůli omezování služby úložiště. Pravděpodobnou příčinou je zaneprázdněnost služby úložiště zpracováním jiných požadavků pro daný účet úložiště.| Po nějaké době zkuste operaci zopakovat. |
| Obnovení selhalo s chybou Cílová sdílená složka se nenašla. | <ul><li>Ujistěte se, že vybraný účet úložiště existuje a cílová sdílená složka není odstraněná. <li> Ujistěte se, že účet úložiště podporuje zálohování sdílených složek. |
| Azure Backup se v současné době nepodporuje pro sdílené složky Azure v účtech úložiště s povolenými virtuálními sítěmi. | Zakažte ve svém účtu úložiště virtuální sítě, abyste zajistili úspěšné operace zálohování nebo obnovení. |
| Úlohy zálohování nebo obnovení selhaly kvůli tomu, že je účet úložiště v uzamčeném stavu. | Odeberte zámek účtu úložiště nebo místo zámku pro čtení použijte zámek proti odstranění a zkuste operaci zopakovat. |
| Obnovení selhalo, protože počet souborů, u kterých došlo k chybě, překračuje prahovou hodnotu. | <ul><li> Důvody selhání obnovení jsou uvedené v souboru (cestu najdete v podrobnostech o úloze). Vyřešte chyby a zopakujte operaci obnovení pouze pro soubory, u kterých došlo k chybě. <li> Běžné důvody selhání obnovení souborů: <br/> – Ujistěte se, že se soubory, u kterých došlo k chybě, aktuálně nepoužívají. <br/> – V nadřazeném adresáři existuje adresář se stejným názvem jako soubor, u kterého došlo k chybě. |
| Obnovení selhalo, protože nebylo možné obnovit žádný soubor. | <ul><li> Důvody selhání obnovení jsou uvedené v souboru (cestu najdete v podrobnostech o úloze). Vyřešte chyby a zopakujte operaci obnovení pouze pro soubory, u kterých došlo k chybě. <li> Běžné důvody selhání obnovení souborů: <br/> – Ujistěte se, že se soubory, u kterých došlo k chybě, aktuálně nepoužívají. <br/> – V nadřazeném adresáři existuje adresář se stejným názvem jako soubor, u kterého došlo k chybě. |
| Obnovení selhalo, protože některý ze souborů ve zdroji neexistuje. | <ul><li> Data bodu obnovení neobsahují vybrané položky. Pokud chcete obnovit soubory, zadejte správný seznam souborů. <li> Snímek sdílené složky odpovídající bodu obnovení se ručně odstranil. Vyberte jiný bod obnovení zkuste operaci obnovení zopakovat. |
| Probíhá jiná úloha obnovení do stejného cíle. | <ul><li>Zálohování sdílených složek nepodporuje paralelní obnovení do stejné cílové sdílené složky. <li>Počkejte na dokončení stávajícího obnovení a zkuste to znovu. Pokud v trezoru služby Recovery Services úlohu zálohování nenajdete, zkontrolujte další trezory služby Recovery Services ve stejném předplatném. |
| Operace obnovení selhala, protože cílová sdílená složka je plná. | Navyšte kvótu velikosti cílové sdílené složky, aby byla dostatečná pro data obnovení, a zkuste operaci zopakovat. |
| Operace obnovení selhala, protože došlo k chybě při provádění operací před obnovením s prostředky služby File Sync přidruženými k cílové sdílené složce. | Zkuste operaci zopakovat později, a pokud se problém nevyřeší, kontaktujte podporu Azure. |
| Jeden nebo více souborů nebylo možné úspěšně obnovit. Další informace najdete v seznamu souborů, u kterých došlo k chybě, v cestě uvedené výše. | <ul> <li> Důvody selhání obnovení jsou uvedené v souboru (cestu najdete v podrobnostech o úloze). Vyřešte tyto důvody a zopakujte operaci obnovení pouze pro soubory, u kterých došlo k chybě. <li> Běžné důvody selhání obnovení souborů: <br/> – Ujistěte se, že se soubory, u kterých došlo k chybě, aktuálně nepoužívají. <br/> – V nadřazeném adresáři existuje adresář se stejným názvem jako soubor, u kterého došlo k chybě. |

## <a name="see-also"></a>Viz také
Další informace o zálohování sdílených složek Azure najdete tady:
- [Zálohování sdílených složek Azure](backup-azure-files.md)
- [Nejčastější dotazy k zálohování sdílených složek Azure](backup-azure-files-faq.md)
