---
title: Řešení potíží s System Center Data Protection Manager pomocí Azure Backup
description: Řešení problémů v System Center Data Protection Manager.
services: backup
author: adigan
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 11/24/2017
ms.author: adigan
ms.openlocfilehash: d3776df8184523999433059e95bc72e1d3abb1c7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606439"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Řešení potíží se System Center Data Protection Managerem

Tento článek popisuje řešení problémů, které se můžete setkat při používání Data Protection Manager.

Nejnovější poznámky k verzi pro System Center Data Protection Manager, najdete v článku [dokumentaci k produktu System Center](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Další informace o podpoře pro aplikaci Data Protection Manager v [Tato matice](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).


## <a name="error-replica-is-inconsistent"></a>Chyba: Replika je nekonzistentní

Repliky může být nekonzistentní z následujících důvodů:
- Úloha vytvoření repliky se nezdaří.
- Nedochází k potížím s deník změn.
- Bitové mapy filtru úrovně svazek obsahuje chyby.
- Zdrojový počítač neočekávaně vypnut.
- Synchronizace protokolu přesahuje vymezenou plochu.
- Replika je skutečně nekonzistentní.

Chcete-li vyřešit tento problém, proveďte následující akce:
- Odebrat nekonzistentní stav, spusťte kontrolu konzistence ručně, nebo naplánovat každodenní kontrolu konzistence.
- Ujistěte se, že používáte nejnovější verzi serveru Microsoft Azure Backup a Data Protection Manager.
- Ujistěte se, že **automatické konzistence** nastavením.
- Pokuste se restartovat službu z příkazového řádku. Použití `net stop dpmra` příkazu s parametrem `net start dpmra`.
- Ujistěte se, že jste splňuje požadavky na síťové připojení a šířku pásma.
- Zkontrolujte, pokud byl neočekávaně vypnut zdrojového počítače.
- Ujistěte se, zda je disk v pořádku a zda je dostatek místa pro danou repliku.
- Ujistěte se, že neexistují žádné duplicitní úloh zálohování, které běží souběžně.

## <a name="error-online-recovery-point-creation-failed"></a>Chyba: Vytvoření bodu obnovení Online se nezdařilo

Chcete-li vyřešit tento problém, proveďte následující akce:
- Ujistěte se, že používáte nejnovější verzi agenta Azure Backup.
- Pokuste se ručně vytvořit bod obnovení v oblasti úloh ochrana.
- Ujistěte se, že spustíte kontrolu konzistence na zdroji dat.
- Ujistěte se, že jste splňuje požadavky na síťové připojení a šířku pásma.
- Když jsou data repliku v nekonzistentním stavu, vytvořte bod obnovení disku tohoto zdroje dat.
- Zajistěte, aby byl repliky přítomen a není chybí.
- Zajistěte, aby replika dostatek místa k vytvoření deníku (USN number) pořadí aktualizace.

## <a name="error-unable-to-configure-protection"></a>Chyba: Nelze konfigurovat ochranu

K této chybě dojde, pokud serveru Data Protection Manager nemůže kontaktovat na chráněném serveru. 

Chcete-li vyřešit tento problém, proveďte následující akce:
- Ujistěte se, že používáte nejnovější verzi agenta Azure Backup.
- Zkontrolujte, zda je připojení (sítě a brány firewall nebo proxy) mezi serverem Data Protection Manager a na chráněném serveru.
- Pokud chráníte SQL server, ujistěte se, že **vlastnosti přihlášení** > **NT AUTHORITY\SYSTEM** vlastnost ukazuje **sysadmin** povolené nastavení.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Chyba: Serveru není registrován jako zadaný v souboru přihlašovacích údajů trezoru

K této chybě dojde během procesu obnovení pro data serveru Data Protection Manager nebo Azure Backup. Soubor s přihlašovacími údaji trezoru, který se používá v procesu obnovení nepatří do trezoru služeb zotavení serveru Data Protection Manager nebo Azure Backup.

Chcete-li vyřešit tento problém, proveďte tyto kroky:
1. Stáhněte soubor s přihlašovacími údaji trezoru z trezoru služeb zotavení, na které je registrované serveru Data Protection Manager nebo Azure Backup.
2. Došlo k pokusu o registraci serveru s úložištěm pomocí nedávno stažený soubor s přihlašovacími údaji trezoru.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Chyba: Žádné obnovitelná data nebo vybraný server není serverem Data Protection Manager

K této chybě dojde, z následujících důvodů:
- Nejsou zaregistrované žádné jiné servery Data Protection Manager nebo Azure Backup do trezoru služeb zotavení.
- Servery ještě jste zatím neodeslali metadata.
- Vybraný server není serverem Data Protection Manager nebo Azure Backup.

Pokud další Data Protection Manager nebo Azure Backup Server je zaregistrovaný trezor služeb zotavení, proveďte tyto kroky k vyřešení problému:
1. Zkontrolujte, zda je nainstalován nejnovější agent Azure Backup.
2. Po zajištění, že je nainstalován nejnovější agent, počkejte, než jeden den před zahájením procesu obnovení. Noční úlohu zálohování odešle metadata pro všechny chráněné zálohování do cloudu. Zálohovaná data, pak je k dispozici pro obnovení.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Chyba: Zadaný šifrovací přístupové heslo neodpovídá přístupové heslo pro server

Při obnovování dat serveru Data Protection Manager nebo Azure Backup, k této chybě dojde během procesu šifrování. Šifrovací přístupové heslo, který se používá v procesu obnovení neodpovídá serveru šifrovací přístupové heslo. V důsledku toho agenta nelze dešifrovat data a obnovení selže.

> [!IMPORTANT]
> Pokud zapomenete nebo šifrovací přístupové heslo ztratíte, nejsou žádné další metody pro obnovení dat. Jedinou možností je znovu vygenerovat přístupové heslo. Použijte nové přístupové heslo k šifrování budoucí zálohovaná data.
>
> Když obnovujete data, vždy poskytnout stejný šifrovací přístupové heslo přidružené k serveru Data Protection Manager nebo Azure Backup. 
>
