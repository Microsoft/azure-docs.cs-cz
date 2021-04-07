---
title: 'Azure AD Connect: Jak obnovit z LocalDB 10 GB – problém limitu | Microsoft Docs'
description: Toto téma popisuje, jak obnovit službu Azure AD Connect Synchronization Service v případě, že dojde k potížím s limitem LocalDB 10GB.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e10aa5d96722b414d7384ceb81f393575d57e2a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688769"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Jak provést obnovení při dosažení 10GB limitu pro LocalDB
Azure AD Connect vyžaduje k ukládání dat identity databázi SQL Serveru. Můžete použít buď výchozí databázi SQL Server 2012 Express LocalDB nainstalovanou se službou Azure AD Connect, nebo vlastní plnou verzi SQL. SQL Server Express má omezení velikosti 10 GB. Pokud při použití LocalDB dosáhnete tohoto limitu, synchronizační služba Azure AD Connect se už nemůže spustit ani správně synchronizovat. Tento článek popisuje kroky obnovení.

## <a name="symptoms"></a>Příznaky
Existují dva běžné příznaky:

* Služba synchronizace Azure AD Connect **je spuštěná** , ale neproběhne synchronizace s chybou *"zastaveno-Database-disk-Full"* .

* Službu synchronizace Azure AD Connect **nelze spustit**. Při pokusu o spuštění služby dojde k chybě s událostí 6323 a chybovou zprávou *"Server narazil na chybu, protože SQL Server nemá dostatek místa na disku."*

## <a name="short-term-recovery-steps"></a>Kroky krátkodobého obnovení
V této části najdete postup uvolnění místa v databázi, které vyžaduje služba Azure AD Connect Synchronization Service pro obnovení operace. Postup je následující:
1. [Určení stavu synchronizační služby](#determine-the-synchronization-service-status)
2. [Zmenšit databázi](#shrink-the-database)
3. [Odstranit data historie spuštění](#delete-run-history-data)
4. [Zkrátit dobu uchování dat historie spuštění](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Určení stavu synchronizační služby
Nejdřív Zjistěte, jestli je synchronizační služba pořád spuštěná, nebo ne:

1. Přihlaste se k serveru Azure AD Connect jako správce.

2. Přejít na **správce řízení služeb**.

3. Ověřte stav **Microsoft Azure AD synchronizace**.


4. Pokud je služba spuštěná, neprovádějte ani nerestartuje službu. Přeskočte [zmenšit krok databáze](#shrink-the-database) a přejděte na krok [Odstranit data historie spuštění](#delete-run-history-data) .

5. Pokud není spuštěný, zkuste službu spustit. Pokud se služba spustí úspěšně, přeskočte krok [databáze zmenšete](#shrink-the-database) a přejděte na krok [Odstranit data historie spuštění](#delete-run-history-data) . V opačném případě pokračujte v kroku [Zmenšení databáze](#shrink-the-database) .

### <a name="shrink-the-database"></a>Zmenšit databázi
Pomocí operace zmenšení uvolněte dostatek místa v databázi pro spuštění synchronizační služby. Uvolní místo pro databáze odebráním prázdných znaků v databázi. Tento krok je nejlepší úsilí, protože není zaručeno, že můžete kdykoli obnovit místo. Pokud se chcete dozvědět víc o operaci zmenšení, přečtěte si článek [Zmenšení databáze](/sql/relational-databases/databases/shrink-a-database).

> [!IMPORTANT]
> Tento krok přeskočte, pokud můžete získat synchronizační službu, která se má spustit. Nedoporučuje se zmenšit databázi SQL, protože to může vést k špatnému výkonu kvůli zvýšené fragmentaci.

Název databáze vytvořené pro Azure AD Connect je **AdSync**. Chcete-li provést operaci zmenšení, je nutné se přihlásit buď jako správce systému, nebo jako DBO databáze. Během instalace Azure AD Connect jsou následující účty udělena oprávnění správce systému:
* Místní správci
* Uživatelský účet, který byl použit ke spuštění instalace Azure AD Connect.
* Účet synchronizační služby, který se používá jako operační kontext synchronizační služby Azure AD Connect.
* Místní skupina ADSyncAdmins, která byla vytvořena během instalace.

1. Zálohujte databázi tak, že zkopírujete soubory **AdSync. mdf** a **ADSync_log. ldf** nacházející `%ProgramFiles%\Microsoft Azure AD Sync\Data` se v umístění do bezpečného umístění.

2. Spusťte novou relaci PowerShellu.

3. Přejděte do složky `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn` .

4. Spusťte nástroj **Sqlcmd** spuštěním příkazu s `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>` použitím přihlašovacích údajů správce systému nebo databáze dbo.

5. Chcete-li zmenšit databázi, zadejte do příkazového řádku Sqlcmd ( `1>` ) `DBCC Shrinkdatabase(ADSync,1);` text následovaný na `GO` dalším řádku.

6. Pokud je operace úspěšná, zkuste znovu spustit synchronizační službu. Pokud můžete spustit synchronizační službu, otevřete krok [Odstranit data historie spuštění](#delete-run-history-data) . Pokud ne, obraťte se na podporu.

### <a name="delete-run-history-data"></a>Odstranit data historie spuštění
Ve výchozím nastavení Azure AD Connect zachovává data historie spuštění až sedm dní. V tomto kroku odstraníme data historie spuštění pro uvolnění prostoru databáze, aby služba Azure AD Connect synchronizace mohla znovu spustit synchronizaci.

1. Spusťte **Synchronization Service Manager** spuštěním synchronizační služby →.

2. Přejít na kartu **operace** .

3. V části **Akce** vyberte možnost **Vymazat běhy**...

4. Můžete buď zvolit možnost **Vymazat všechna spuštění** nebo **zrušit zaškrtnutí políček před. \<date> ..** . Doporučujeme začít tím, že vymažete data historie spuštění, která jsou starší než dva dny. Pokud budete pokračovat v běhu do problému velikosti databáze, zvolte možnost **Vymazat všechna spuštění** .

### <a name="shorten-retention-period-for-run-history-data"></a>Zkrátit dobu uchování dat historie spuštění
Tento krok umožňuje snížit pravděpodobnost spuštění limitu 10 GB po několika synchronizačních cyklech.

1. Otevřete novou relaci PowerShellu.

2. Spusťte `Get-ADSyncScheduler` a poznamenejte si vlastnost PurgeRunHistoryInterval, která určuje aktuální dobu uchování.

3. Spusťte `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` , chcete-li nastavit dobu uchování na dva dny. Podle potřeby upravte dobu uchování.

## <a name="long-term-solution--migrate-to-full-sql"></a>Dlouhodobé řešení – migrace na úplný SQL
Obecně je problém informativní, že velikost databáze o velikosti 10 GB již není dostatečná, aby mohla Azure AD Connect synchronizovat místní službu Active Directory se službou Azure AD. Doporučuje se přepnout na používání plné verze systému SQL Server. Nemůžete LocalDB existujícího nasazení Azure AD Connect přímo nahradit databází plné verze SQL. Místo toho je nutné nasadit nový server Azure AD Connect s plnou verzí SQL. Doporučuje se provést postupnou migraci, kdy se nový server Azure AD Connect (s databází SQL) nasadí jako pracovní server vedle existujícího serveru Azure AD Connect (s LocalDB). 
* Pokyny ke konfiguraci vzdáleného SQL se službou Azure AD Connect najdete v článku s popisem [vlastní instalace Azure AD Connect](./how-to-connect-install-custom.md).
* Pokyny k postupné migraci kvůli upgradu Azure AD Connect najdete v článku [Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi](./how-to-upgrade-previous-version.md#swing-migration).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
