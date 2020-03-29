---
title: 'Azure AD Connect: Jak se zotavit z problému omezení LocalDB 10GB | Dokumenty společnosti Microsoft'
description: Toto téma popisuje, jak obnovit službu synchronizace připojení Azure AD, když narazí na problém omezení LocalDB 10GB.
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
ms.topic: article
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d420c64c5834f7d3cb11d2f5f59e3ed85a54891
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60386920"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Jak provést obnovení při dosažení 10GB limitu pro LocalDB
Azure AD Connect vyžaduje k ukládání dat identity databázi SQL Serveru. Můžete použít buď výchozí databázi SQL Server 2012 Express LocalDB nainstalovanou se službou Azure AD Connect, nebo vlastní plnou verzi SQL. SQL Server Express má omezení velikosti 10 GB. Pokud při použití LocalDB dosáhnete tohoto limitu, synchronizační služba Azure AD Connect se už nemůže spustit ani správně synchronizovat. Tento článek obsahuje kroky obnovení.

## <a name="symptoms"></a>Příznaky
Existují dva běžné příznaky:

* Služba synchronizace připojení Azure AD **je spuštěná,** ale nedaří se synchronizovat s *chybou "zastaveno-databáze-disk-full".*

* Služba synchronizace připojení Azure AD **nelze spustit**. Při pokusu o spuštění služby se nezdaří s událostí 6323 a chybovou *zprávou "Server zjistil chybu, protože SQL Server není na disku."*

## <a name="short-term-recovery-steps"></a>Krátkodobé kroky obnovy
Tato část obsahuje postup, jak uvolnit místo v DB potřebné pro obnovení provozu služby Azure AD Connect Synchronizační služba. Tyto kroky zahrnují:
1. [Určení stavu služby synchronizace](#determine-the-synchronization-service-status)
2. [Zmenšení databáze](#shrink-the-database)
3. [Odstranit data historie spuštění](#delete-run-history-data)
4. [Zkrácení doby uchování dat historie spuštění](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Určení stavu služby synchronizace
Nejprve určete, zda je služba synchronizace stále spuštěna nebo ne:

1. Přihlaste se k serveru Azure AD Connect jako správce.

2. Přejděte do **správce řízení služeb**.

3. Zkontrolujte stav **Microsoft Azure AD Sync**.


4. Pokud je spuštěna, nezastavujte ani nerestartujte službu. Přeskočit [Zmenšit](#shrink-the-database) krok databáze a přejít na Odstranit krok [dat historie spuštění.](#delete-run-history-data)

5. Pokud není spuštěna, zkuste spustit službu. Pokud se služba úspěšně spustí, [přeskočte zmenšit](#shrink-the-database) krok databáze a přejděte na Odstranit krok [dat historie spuštění.](#delete-run-history-data) V opačném případě [pokračujte zmenšit](#shrink-the-database) krok databáze.

### <a name="shrink-the-database"></a>Zmenšení databáze
Pomocí operace Zmenšit uvolněte dostatek místa v DB pro spuštění synchronizační služby. Uvolní místo db odebráním mezery v databázi. Tento krok je nejlepší úsilí, protože není zaručeno, že můžete vždy obnovit prostor. Další informace o operaci zmenšit, přečtěte si tento článek [Zmenšit databázi](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Tento krok přeskočte, pokud lze spustit synchronizační službu. Nedoporučuje se zmenšit SQL DB, protože může vést ke snížení výkonu z důvodu zvýšené fragmentace.

Název databáze vytvořené pro Azure AD Connect je **ADSync**. Chcete-li provést operaci Shrink, musíte se přihlásit buď jako sysadmin nebo DBO databáze. Během instalace služby Azure AD Connect jsou udělena práva sysadmin následující účty:
* Místní správci
* Uživatelský účet, který byl použit ke spuštění instalace Služby Azure AD Connect.
* Účet služby Synchronizace, který se používá jako provozní kontext služby synchronizace připojení Azure AD.
* Místní skupina ADSyncAdmins, která byla vytvořena během instalace.

1. Zálohujte databázi zkopírováním souborů **ADSync.mdf** a `%ProgramFiles%\Microsoft Azure AD Sync\Data` **ADSync_log.ldf** umístěných pod bezpečným umístěním.

2. Spusťte novou relaci PowerShellu.

3. Přejděte `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`do složky .

4. Spusťte **sqlcmd** nástroj `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>`spuštěním příkazu , pomocí pověření sysadmin nebo databáze DBO.

5. Chcete-li zmenšit databázi, na sqlcmd prompt (1>), zadejte `DBCC Shrinkdatabase(ADSync,1);`, následovaný `GO` v dalším řádku.

6. Pokud je operace úspěšná, zkuste znovu spustit synchronizační službu. Pokud můžete spustit službu synchronizace, přejděte na [krok Odstranit data historie spuštění.](#delete-run-history-data) Pokud ne, obraťte se na podporu.

### <a name="delete-run-history-data"></a>Odstranit data historie spuštění
Ve výchozím nastavení azure ad connect uchovává až sedm dní historie dat spuštění. V tomto kroku odstraníme data historie spuštění, abychom uvolnili místo v DB, aby služba synchronizace Azure AD Connect mohla znovu spustit synchronizaci.

1. **Spusťte Správce synchronizačních služeb** tak, že přejdete na start → Služba synchronizace.

2. Přejděte na kartu **Operace.**

3. V části **Akce**vyberte **Vymazat spuštění**...

4. Můžete buď zvolit **Vymazat všechny spuštění** nebo Vymazat spuštění **před... \<** možnost>datum. Doporučujeme začít vymazáním dat historie spuštění, která jsou starší než dva dny. Pokud budete pokračovat v běhu na problém velikosti DB, pak zvolte **vymazat všechny spuštění** možnost.

### <a name="shorten-retention-period-for-run-history-data"></a>Zkrácení doby uchování dat historie spuštění
Tento krok je snížit pravděpodobnost spuštění do 10 GB limit problém po více cyklech synchronizace.

1. Otevřete novou relaci PowerShellu.

2. Spusťte `Get-ADSyncScheduler` a poznamenejte si vlastnost PurgeRunHistoryInterval, která určuje aktuální dobu uchovávání.

3. Spuštěním `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` nastavte dobu uchovávání na dva dny. Podle potřeby upravte dobu uchovávání.

## <a name="long-term-solution--migrate-to-full-sql"></a>Dlouhodobé řešení – migrace na úplné SQL
Obecně platí, že problém naznačuje, že velikost databáze 10 GB již není dostatečná pro Azure AD Connect k synchronizaci místní služby Active Directory do služby Azure AD. Doporučujeme přepnout na plnou verzi serveru SQL. Nemůžete LocalDB existujícího nasazení Azure AD Connect přímo nahradit databází plné verze SQL. Místo toho je nutné nasadit nový server Azure AD Connect s plnou verzí SQL. Doporučuje se provést postupnou migraci, kdy se nový server Azure AD Connect (s databází SQL) nasadí jako pracovní server vedle existujícího serveru Azure AD Connect (s LocalDB). 
* Pokyny ke konfiguraci vzdáleného SQL se službou Azure AD Connect najdete v článku s popisem [vlastní instalace Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* Pokyny k postupné migraci kvůli upgradu Azure AD Connect najdete v článku [Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
