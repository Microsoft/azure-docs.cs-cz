---
title: Řešení potíží s chybami při zálohování databáze SAP HANA pomocí služby Azure Backup | Dokumentace Microsoftu
description: Tato příručka vysvětluje, jak řešit běžné chyby při pokusu o zálohování databáze SAP HANA pomocí Azure Backup.
services: backup
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: pullabhk
ms.openlocfilehash: 33f1b4674aad35d55ab014c45cd73753533931cb
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514181"
---
# <a name="troubleshoot-back-up-of-sap-hana-server-on-azure"></a>Řešení potíží s zálohování serveru SAP HANA v Azure

Tento článek obsahuje informace o odstraňování potíží pro ochranu databáze SAP HANA v Azure Virtual Machines. Než budete pokračovat k řešení potíží, Projdeme si několik klíčových bodů o oprávnění a nastavení.

## <a name="understanding-pre-requisites"></a>Vysvětlení požadavků

Jako součást [předpoklady](backup-azure-sap-hana-database.md#prerequisites), před registrací skript by měl spustit na virtuálním počítači nainstalovanou HANA nastavení správná oprávnění.

### <a name="setting-up-permissions"></a>Nastavení oprávnění

K čemu slouží předběžné registrace skriptu:

1. Vytvoří AZUREWLBACKUPHANAUSER systému HANA a přidá požadované role a oprávnění, jak je uvedeno níže:
    - Správce databáze – k vytvoření nové databáze během obnovení
    - Přečtěte si KATALOG – číst katalogu záloh
    - SAP_INTERNAL_HANA_SUPPORT – přístup k několika privátní tabulky
2. Přidá klíč do Hdbuserstore pro modul plug-in HANA provádět všechny operace (dotaz databáze, konfigurace zálohování, provádí zálohování, tento postup obnovení)
   
   - Pro potvrzení vytvoření klíče, spusťte příkaz HDBSQL v rámci HANA počítače pomocí přihlašovacích údajů SIDADM:

    ``` hdbsql
    hdbuserstore list
    ```
    
    Výstup příkazu by měl zobrazit klíč {SID} {%{DBNAME/} s uživatelem jako "AZUREWLBACKUPHANAUSER".

> [!NOTE]
> Ujistěte se, že budete mít jedinečnou sadu SSFS soubory v cestě "/ usr/sap/{SID}/home/.hdb/". Měla by existovat pouze jedna složka v této cestě.

### <a name="setting-up-backint-parameters"></a>Nastavení parametrů BackInt

Jakmile databáze je vybrán pro zálohování, služba Azure Backup nakonfiguruje backInt parametry na úrovni databáze.

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> Ujistěte se, že tyto parametry nejsou k dispozici na úrovni hostitele. Parametry na úrovni hostitele přepíše tyto parametry a různé chování může způsobit, než se očekávalo.

## <a name="understanding-common-user-errors"></a>Vysvětlení běžných chyb uživatele

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Nepovedlo se připojit k system.check HANA, které váš systém je zprovozněný.| Služba Azure Backup není schopen připojit se k HANA, protože databáze HANA je mimo provoz. Nebo HANA je spuštěná, ale neumožňuje služby Azure Backup pro připojení | Zkontrolujte, jestli HANA DB/služba je mimo provoz. Pokud HANA DB/služba je spuštěná, zkontrolujte, jestli všechna oprávnění jsou nastavené jak už bylo zmíněno [tady](#setting-up-permissions). Pokud je nalezen klíč, spusťte znovu skript před registrací a vytvořte nový klíč. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Zjištěné Backint neplatné konfigurace. Zastavte ochranu a znovu nakonfigurovat databázi.| Azure Backup jsou nesprávně zadány parametry backInt. | Zkontrolujte parametry jsou uvedené [tady](#setting-up-backint-parameters). Pokud parametry backInt založené na HOSTITELI, potom je odeberte. Pokud parametry nebyly k dispozici v HOSTITELI, ale byly ručně upraveny na úrovni databáze, pak vrátit zpět na odpovídající hodnoty jak je uvedeno výše. Nebo "zastavte ochranu při zachování dat" z webu Azure portal a "pokračovat v zálohování" ještě jednou.|
