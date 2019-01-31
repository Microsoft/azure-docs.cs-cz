---
title: Řešení potíží s nástroji System Center Data Protection Manager pomocí služby Azure Backup
description: Řešení potíží s v nástroji System Center Data Protection Manager.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 4108616e3ae41e2c88b74bb08d5f846c0035101f
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55293991"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Řešení potíží se System Center Data Protection Managerem

Tento článek popisuje řešení problémů, které se můžete setkat při používání aplikace Data Protection Manager.

Nejnovější poznámky k verzi pro System Center Data Protection Manager, najdete v článku [dokumentace pro System Center](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Další informace o podpoře pro aplikaci Data Protection Manager v [Tato matice](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).


## <a name="error-replica-is-inconsistent"></a>Chyba: Replika je nekonzistentní.

Repliky může být nekonzistentní z následujících důvodů:
- Úloha vytvoření repliky nezdaří.
- Dochází k problémům s deníku změn.
- Rastrový obrázek filtr na úrovni svazku obsahuje chyby.
- Zdrojový počítač neočekávaně ukončí.
- Protokol synchronizace přetečení.
- Replika je skutečně nekonzistentní.

Chcete-li vyřešit tento problém, proveďte následující akce:
- Chcete-li odebrat nekonzistentní stav, spusťte kontrolu konzistence ručně nebo naplánovat každodenní kontrolu konzistence.
- Ujistěte se, že používáte nejnovější verzi Microsoft Azure Backup serveru a Data Protection Manager.
- Ujistěte se, **automatické kontroly konzistence** je povolené nastavení.
- Zkuste restartovat službu z příkazového řádku. Použití `net stop dpmra` příkaz následovaný `net start dpmra`.
- Ujistěte se, že můžete kontrolovat plnění požadavků na síťové připojení a šířky pásma.
- Zaškrtněte, pokud byl neočekávaně vypnut zdrojového počítače.
- Ujistěte se, že disk je v pořádku a zda je dostatek místa pro repliku.
- Ujistěte se, že neexistují žádné duplicitní úloh zálohování, které jsou spuštěny souběžně.

## <a name="error-online-recovery-point-creation-failed"></a>Chyba: Vytvoření bodu obnovení online se nezdařilo

Chcete-li vyřešit tento problém, proveďte následující akce:
- Ujistěte se, že používáte nejnovější verzi agenta Azure Backup.
- Zkuste ručně vytvořit bod obnovení v oblasti úloh ochrana.
- Ujistěte se, že spustíte kontrolu konzistence na zdroji dat.
- Ujistěte se, že můžete kontrolovat plnění požadavků na síťové připojení a šířky pásma.
- Vytvořit bod obnovení disku tohoto zdroje dat, pokud se replika dat je v nekonzistentním stavu.
- Ujistěte se, že replika je k dispozici a ne chybí.
- Zajistěte, aby replika dostatek místa k vytvoření deníku (USN number) pořadí aktualizace.

## <a name="error-unable-to-configure-protection"></a>Chyba: Ochranu nelze nakonfigurovat.

K této chybě dochází, když serveru aplikace Data Protection Manager nemůže kontaktovat na chráněném serveru. 

Chcete-li vyřešit tento problém, proveďte následující akce:
- Ujistěte se, že používáte nejnovější verzi agenta Azure Backup.
- Zkontrolujte, že existuje připojení (sítě a brány firewall nebo proxy) mezi serveru aplikace Data Protection Manager a na chráněném serveru.
- Pokud chráníte SQL server, ujistěte se, že **vlastnosti přihlášení** > **NT AUTHORITY\SYSTEM** vlastnost ukazuje **sysadmin** nastavení povolené.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Chyba: Server není zaregistrovaný jako zadaný v souboru přihlašovacích údajů trezoru

K této chybě dojde během procesu obnovení pro Data Protection Manager nebo Azure Backup serveru data. Soubor přihlašovacích údajů trezoru, který se používá v procesu obnovení nepatří do trezoru služby Recovery Services pro Data Protection Manager nebo Azure Backup server.

Chcete-li vyřešit tento problém, proveďte tyto kroky:
1. Stáhněte si soubor s přihlašovacími údaji trezoru z trezoru služby Recovery Services, ke kterému je Data Protection Manager nebo Azure Backup server zaregistrovaný.
2. Došlo k pokusu o registraci serveru do trezoru nedávno stažený soubor s přihlašovacími údaji trezoru.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Chyba: Žádné obnovitelná data nebo vybraný server není serveru aplikace Data Protection Manager

K této chybě dochází z následujících důvodů:
- Nejsou zaregistrované žádné jiné servery Data Protection Manager nebo Azure Backup do trezoru služby Recovery Services.
- Servery ještě nepřidali metadata.
- Vybraný server není Data Protection Manager nebo Azure Backup server.

Když ostatní servery Data Protection Manager nebo Azure Backup jsou zaregistrované do trezoru služby Recovery Services, proveďte tyto kroky k vyřešení daného problému:
1. Ujistěte se, že je nainstalovaný nejnovější agent Azure Backup.
2. Po zajištění, že je nainstalovaný nejnovější agent, počkejte, než jeden den před zahájením procesu obnovení. Noční úlohu zálohování odešle metadata pro všechny chráněné zálohování do cloudu. Data záloh jsou pak k dispozici pro obnovení.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Chyba: Zadané šifrovací heslo neodpovídá přístupové heslo pro server

Obnovení dat Data Protection Manager nebo Azure Backup serveru k této chybě dojde při proces šifrování. Šifrovací heslo, který se používá v procesu obnovení serveru šifrovací heslo se neshoduje. V důsledku toho agent nemůže data dešifrovat a obnovení není úspěšné.

> [!IMPORTANT]
> Pokud zapomenete nebo šifrovací přístupové heslo ztratíte, nejsou žádné další metody pro obnovení dat. Znovu vygenerovat přístupové heslo je jedinou možností. Nové heslo použijte k šifrování budoucí zálohovaná data.
>
> Když obnovujete data, vždy poskytnout stejný šifrovací heslo, který je spojen s Data Protection Manager nebo Azure Backup server. 
>
