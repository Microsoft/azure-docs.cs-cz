---
title: Řešení potíží se System Center Data Protection Managerem
description: V tomto článku zjišťujte řešení problémů, se kterými se můžete setkat při používání Správce ochrany dat system center.
ms.topic: troubleshooting
ms.date: 01/30/2019
ms.openlocfilehash: bcb30fa7eb3e05099761fc751b09a9fb16134e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75664750"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Řešení potíží se System Center Data Protection Managerem

Tento článek popisuje řešení problémů, se kterými se můžete setkat při používání Správce ochrany dat.

Nejnovější poznámky k verzi pro Správce ochrany dat centra systému naleznete v [dokumentaci k centru systému](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Další informace o podpoře správce ochrany dat naleznete v [této matici](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).

## <a name="error-replica-is-inconsistent"></a>Chyba: Replika je nekonzistentní.

Replika může být nekonzistentní z následujících důvodů:

- Úloha vytvoření repliky se nezdaří.
- Existují problémy s deníkem změn.
- Bitmapa filtru na úrovni hlasitosti obsahuje chyby.
- Zdrojový počítač se neočekávaně vypne.
- Protokol synchronizace přeteče.
- Replika je opravdu nekonzistentní.

Chcete-li tento problém vyřešit, proveďte následující akce:

- Chcete-li odebrat nekonzistentní stav, spusťte kontrolu konzistence ručně nebo naplánujte denní kontrolu konzistence.
- Ujistěte se, že používáte nejnovější verzi Microsoft Azure Backup Server a Správce ochrany dat.
- Ujistěte se, že je povoleno nastavení **Automatická konzistence.**
- Pokuste se restartovat služby z příkazového řádku. Použijte `net stop dpmra` příkaz následovaný `net start dpmra`.
- Ujistěte se, že splňujete požadavky na připojení k síti a šířku pásma.
- Zkontrolujte, zda byl zdrojový počítač neočekávaně vypnut.
- Ujistěte se, že disk je v pořádku a že je dostatek místa pro repliku.
- Ujistěte se, že neexistují žádné duplicitní úlohy zálohování, které jsou spuštěny současně.

## <a name="error-online-recovery-point-creation-failed"></a>Chyba: Vytvoření bodu obnovení online se nezdařilo.

Chcete-li tento problém vyřešit, proveďte následující akce:

- Ujistěte se, že používáte nejnovější verzi agenta Azure Backup.
- Pokuste se ručně vytvořit bod obnovení v oblasti úlohy ochrany.
- Ujistěte se, že spustíte kontrolu konzistence zdroje dat.
- Ujistěte se, že splňujete požadavky na připojení k síti a šířku pásma.
- Pokud jsou data repliky v nekonzistentním stavu, vytvořte bod obnovení disku tohoto zdroje dat.
- Ujistěte se, že replika je k dispozici a nechybí.
- Ujistěte se, že replika má dostatek místa k vytvoření deníku pořadové číslo aktualizace (USN).

## <a name="error-unable-to-configure-protection"></a>Chyba: Ochranu nelze nakonfigurovat.

K této chybě dochází, když server Správce ochrany dat nemůže kontaktovat chráněný server.

Chcete-li tento problém vyřešit, proveďte následující akce:

- Ujistěte se, že používáte nejnovější verzi agenta Azure Backup.
- Ujistěte se, že existuje připojení (síť/firewall/proxy server) mezi serverem Data Protection Manager a chráněným serverem.
- Pokud chráníte server SQL, ujistěte se, že vlastnost **Vlastnosti** > přihlášení**NT AUTHORITY\SYSTEM** zobrazuje povolené nastavení **sysadmin.**

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Chyba: Server není registrován tak, jak je určeno v souboru pověření úschovny.

K této chybě dochází během procesu obnovení dat Správce ochrany dat nebo Azure Backup server data. Soubor přihlašovacích údajů trezoru, který se používá v procesu obnovení, nepatří do trezoru služby Recovery Services pro server Správce ochrany dat nebo Azure Backup.

Chcete-li tento problém vyřešit, proveďte následující kroky:

1. Stáhněte si soubor přihlašovacích údajů trezoru z trezoru služby Recovery Services, do kterého je zaregistrován server Správce ochrany dat nebo Azure Backup.
2. Pokuste se zaregistrovat server v úschovně pomocí naposledy staženého souboru přihlašovacích údajů úschovny.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Chyba: Žádná obnovitelná data nebo vybraný server není server správce maskérové ochrany dat.

K této chybě dochází z následujících důvodů:

- Do trezoru služby Recovery Services nejsou registrovány žádné jiné servery Správce ochrany dat nebo Azure Backup.
- Servery dosud nenahrály metadata.
- Vybraný server není server správce mašity dat/Azure Backup.

Když jsou v trezoru služby Recovery Services zaregistrovány jiné servery Správce ochrany dat nebo Služby Azure Pro zálohování, proveďte tento krok k vyřešení problému:

1. Ujistěte se, že je nainstalován nejnovější agent Azure Backup.
2. Po zajištění, že je nainstalován nejnovější agent, počkejte jeden den před zahájením procesu obnovení. Úloha nočního zálohování nahraje metadata pro všechny chráněné zálohy do cloudu. Záložní data jsou pak k dispozici pro obnovení.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Chyba: Za předpokladu, že heslo pro šifrování neodpovídá přístupové frázi pro server.

K této chybě dochází během procesu šifrování při obnovení dat správce ochrany dat nebo Azure Backup server data. Heslo pro šifrování, které se používá v procesu obnovení, neodpovídá přístupové frázi pro šifrování serveru. V důsledku toho agent nemůže dešifrovat data a obnovení se nezdaří.

> [!IMPORTANT]
> Pokud zapomenete nebo ztratíte heslo pro šifrování, neexistují žádné jiné metody pro obnovení dat. Jedinou možností je regenerovat přístupové heslo. Nové přístupové heslo použijte k šifrování budoucích záložních dat.
>
> Při obnovení dat vždy zadejte stejné šifrovací heslo, které je přidruženo k serveru Data Protection Manager/Azure Backup.
>
