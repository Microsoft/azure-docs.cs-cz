---
title: Řešení potíží s nástrojem System Center Data Protection Manager s Azure Backup
description: Řešení problémů v nástroji System Center Data Protection Manager.
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: dacurwin
ms.openlocfilehash: 501e61d06b7724147d7224ae51bde5de736279b3
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689038"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Řešení potíží se System Center Data Protection Managerem

Tento článek popisuje řešení problémů, se kterými se můžete setkat při používání Data Protection Manager.

Nejnovější poznámky k verzi pro System Center Data Protection Manager najdete v dokumentaci k produktu [System Center](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Další informace o podpoře pro Data Protection Manager najdete v [této matrici](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).


## <a name="error-replica-is-inconsistent"></a>Chyba: Replika je nekonzistentní.

Replika může být nekonzistentní z následujících důvodů:
- Úloha vytvoření repliky se nezdařila.
- Deník změn obsahuje problémy.
- Bitmapa filtru na úrovni svazku obsahuje chyby.
- Zdrojový počítač se neočekávaně ukončí.
- Přetečení protokolu synchronizace.
- Replika je skutečně nekonzistentní.

Chcete-li vyřešit tento problém, proveďte následující akce:
- Chcete-li odebrat nekonzistentní stav, spusťte kontrolu konzistence ručně nebo naplánujte každodenní kontrolu konzistence.
- Ujistěte se, že používáte nejnovější verzi Microsoft Azure Backup serveru a Data Protection Manager.
- Ujistěte se, že je povolené nastavení **Automatické konzistence** .
- Zkuste restartovat služby z příkazového řádku. `net stop dpmra` Použijte příkaz`net start dpmra`následovaný.
- Ujistěte se, že splňujete síťové připojení a požadavky na šířku pásma.
- Ověřte, zda byl zdrojový počítač neočekávaně vypnut.
- Ujistěte se, že je disk v pořádku a že je k dispozici dostatek místa pro repliku.
- Zajistěte, aby nedocházelo k duplicitním úlohám zálohování spuštěným současně.

## <a name="error-online-recovery-point-creation-failed"></a>Chyba: Vytvoření bodu obnovení online se nezdařilo

Chcete-li vyřešit tento problém, proveďte následující akce:
- Ujistěte se, že používáte nejnovější verzi agenta Azure Backup.
- Zkuste ručně vytvořit bod obnovení v oblasti úloh ochrana.
- Ujistěte se, že jste spustili kontrolu konzistence pro zdroj dat.
- Ujistěte se, že splňujete síťové připojení a požadavky na šířku pásma.
- Pokud jsou data repliky v nekonzistentním stavu, vytvořte bod obnovení disku tohoto zdroje dat.
- Zajistěte, aby byla replika přítomna a nebyla nalezena.
- Ujistěte se, že má replika dostatek místa pro vytvoření deníku USN (Update Sequence Number).

## <a name="error-unable-to-configure-protection"></a>Chyba: Ochranu nelze nakonfigurovat.

K této chybě dojde, když Data Protection Manager Server nemůže kontaktovat chráněný Server. 

Chcete-li vyřešit tento problém, proveďte následující akce:
- Ujistěte se, že používáte nejnovější verzi agenta Azure Backup.
- Ujistěte se, že mezi serverem Data Protection Manager a chráněným serverem je připojení (síť/brána firewall/proxy server).
- Pokud chráníte SQL Server, zajistěte, aby vlastnost **přihlášení** > **NT AUTHORITY\SYSTEM** zobrazila povolené nastavení **sysadmin** .

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Chyba: Server není zaregistrovaný jako zadaný v souboru přihlašovacích údajů trezoru.

K této chybě dojde během procesu obnovení pro data serveru Data Protection Manager/Azure Backup. Soubor s přihlašovacími údaji úložiště, který se používá v procesu obnovení, nepatří do trezoru Recovery Services pro Server Data Protection Manager nebo Azure Backup.

Chcete-li tento problém vyřešit, proveďte tyto kroky:
1. Stáhněte si soubor s přihlašovacími údaji trezoru z Recovery Services trezoru, do kterého je Server Data Protection Manager nebo Azure Backup zaregistrován.
2. Zkuste zaregistrovat server k trezoru pomocí posledního staženého souboru s přihlašovacími údaji trezoru.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Chyba: Žádná obnovitelná data nebo vybraný server není Data Protection Manager serverem.

K této chybě dochází z následujících důvodů:
- Žádné další servery Data Protection Manager/Azure Backup nejsou registrovány do trezoru Recovery Services.
- Servery ještě neodeslaly metadata.
- Vybraný server není Server Data Protection Manager/Azure Backup.

Pokud jsou jiné servery Data Protection Manager/Azure Backup zaregistrovány do trezoru Recovery Services, proveďte tyto kroky a problém vyřešte:
1. Ujistěte se, že je nainstalovaný nejnovější agent Azure Backup.
2. Až zajistěte, aby byl nainstalován nejnovější agent, počkejte jeden den před zahájením procesu obnovení. Úloha nočního zálohování nahrává metadata pro všechna chráněná zálohování do cloudu. Zálohovaná data jsou pak k dispozici pro obnovení.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Chyba: Zadané šifrovací heslo se neshoduje s heslem pro server.

K této chybě dojde během procesu šifrování při obnovování dat serveru Data Protection Manager/Azure Backup. Šifrovací heslo, které se používá v procesu obnovení, se neshoduje s šifrovacím heslem serveru. V důsledku toho Agent nemůže data dešifrovat a obnovení nebude úspěšné.

> [!IMPORTANT]
> Pokud šifrovací heslo zapomenete nebo ztratíte, neexistují žádné další metody pro obnovu dat. Jedinou možností je znovu vygenerovat heslo. Použijte nové heslo k šifrování budoucích zálohovaných dat.
>
> Při obnovování dat vždy poskytněte stejné šifrovací heslo, které je přidružené k serveru Data Protection Manager/Azure Backup. 
>
