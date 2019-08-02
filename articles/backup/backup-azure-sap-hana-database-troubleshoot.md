---
title: Řešení chyb při zálohování SAP HANA databází pomocí Azure Backup | Microsoft Docs
description: Popisuje, jak řešit běžné chyby, ke kterým může dojít při použití Azure Backup k zálohování databází SAP HANA.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: dacurwin
ms.openlocfilehash: a2711339f5e952747adeeb6217b283770cb6cc6b
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689045"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Řešení potíží se zálohováním databází SAP HANA v Azure

Tento článek poskytuje informace o řešení potíží při zálohování SAP HANA databází na virtuálních počítačích Azure.

## <a name="prerequisites"></a>Požadavky

V rámci [požadavků](backup-azure-sap-hana-database.md#prerequisites)se ujistěte, že je na virtuálním počítači, kde je nainstalovaný Hana, spuštěný skript pro předregistraci.

### <a name="setting-up-permissions"></a>Nastavení oprávnění

K čemu používá skript pro předregistraci:

1. Vytvoří v systému HANA AZUREWLBACKUPHANAUSER a přidá tyto požadované role a oprávnění:
    - Správce databáze: pro vytvoření nového databáze při obnovení.
    - ČTENÍ katalogu: pro čtení katalogu záloh.
    - SAP_INTERNAL_HANA_SUPPORT: pro přístup k několika soukromým tabulkám.
2. Přidá klíč do Hdbuserstore pro modul plug-in HANA pro zpracování všech operací (databázové dotazy, operace obnovení, konfigurace a spuštění zálohování).
   
   Pro potvrzení vytvoření klíče spusťte na počítači HANA příkaz HDBSQL s přihlašovacími údaji SIDADM:

    ``` hdbsql
    hdbuserstore list
    ```
    
    Výstup příkazu by měl zobrazovat klíč {SID} {DBNAME} s uživatelem zobrazeným jako AZUREWLBACKUPHANAUSER.

> [!NOTE]
> Ujistěte se, že máte v **/usr/SAP/{SID}/Home/.hdb/** jedinečnou sadu souborů SSFS. V této cestě by měla být jenom jedna složka.

### <a name="setting-up-backint-parameters"></a>Nastavení parametrů BackInt

Po výběru databáze pro zálohování nakonfiguruje služba Azure Backup parametry backInt na úrovni databáze:

- [catalog_backup_using_backint: true]
- [enable_accumulated_catalog_backup: false]
- [parallel_data_backup_backint_channels: 1]
- [log_backup_timeout_s: 900)]
- [backint_response_timeout:7200]

> [!NOTE]
> Ujistěte se, že tyto parametry nejsou k *dispozici na* úrovni hostitele. Parametry na úrovni hostitele přepíšou tyto parametry a můžou způsobit neočekávané chování.

## <a name="common-user-errors"></a>Běžné chyby uživatelů

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

data| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Nepovedlo se připojit k systému HANA. Ověřte, že je váš systém v provozu.| Služba Azure Backup se nemůže připojit k HANA, protože databáze HANA je mimo provoz. Nebo HANA je spuštěný, ale neumožňuje službě Azure Backup připojit. | Ověřte, zda nefunguje databáze HANA nebo služba. Pokud je databáze nebo služba HANA v provozu, ověřte, jestli [jsou nastavená všechna oprávnění](#setting-up-permissions). Pokud klíč chybí, spusťte znovu registrační skript a vytvořte nový klíč. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Chybová zpráva | Možné příčiny | Doporučená akce |
|---|---|---|
| Zjistila se neplatná konfigurace Backint. Zastavte ochranu a znovu nakonfigurujte databázi.| Parametry backInt jsou pro Azure Backup nesprávně zadány. | Ověřte, zda [jsou parametry nastaveny](#setting-up-backint-parameters). Pokud jsou na hostiteli přítomné parametry založené na backInt, odeberte je. Pokud parametry nejsou k dispozici na úrovni hostitele, ale byly ručně upraveny na úrovni databáze, vraťte je na příslušné hodnoty, jak je popsáno výše. Případně můžete spustit možnost **Zastavit ochranu a zachovat data zálohy** z Azure Portal a pak vybrat **obnovit zálohování**.|
