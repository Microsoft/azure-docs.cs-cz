---
title: 'Azure AD Connect: Jak obnovit LocalDB 10 GB limit problém | Microsoft Docs'
description: Toto téma popisuje, jak obnovit službu Azure AD Connect synchronizace, pokud se setká s LocalDB 10GB omezit problém.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 3ba491902444c9c05f997f854353206d78f2957d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591268"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Jak obnovit z limitu LocalDB 10 GB
Azure AD Connect vyžaduje k ukládání dat identity databázi SQL Serveru. Můžete použít buď výchozí databázi SQL Server 2012 Express LocalDB nainstalovanou se službou Azure AD Connect, nebo vlastní plnou verzi SQL. SQL Server Express má omezení velikosti 10 GB. Pokud při použití LocalDB dosáhnete tohoto limitu, synchronizační služba Azure AD Connect se už nemůže spustit ani správně synchronizovat. Tento článek obsahuje kroky obnovení.

## <a name="symptoms"></a>Příznaky
Existují dvě běžné příznaky:

* Služba Azure AD Connect synchronizace **běží** ale nepodaří synchronizovat s *"zastavena database-disk-full (úplné)* chyby.

* Služba Azure AD Connect synchronizace **nelze spustit**. Při pokusu o spuštění služby nezdaří s událostí 6323 a chybovou zprávou *"serveru došlo k chybě systému SQL Server je nedostatek místa na disku."*

## <a name="short-term-recovery-steps"></a>Postup pro krátkodobé obnovení
Tato část obsahuje kroky k uvolnění místa DB požadované pro službu Azure AD Connect synchronizace pro operace obnovení. Zahrnuje následující kroky:
1. [Zjistit stav služby synchronizace](#determine-the-synchronization-service-status)
2. [Zmenšení databáze](#shrink-the-database)
3. [Odstranění spustit data historie](#delete-run-history-data)
4. [Zkrátit dobu uchování historie spouštění dat](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Zjistit stav služby synchronizace
Nejdřív zjistěte, zda je stále spuštěna služba synchronizace nebo není:

1. Přihlaste se k Azure AD Connect serveru jako správce.

2. Přejděte na **správce řízení služeb**.

3. Zkontrolujte stav **Microsoft Azure AD Sync**.


4. Pokud je spuštěná, zastavte nebo restartujte službu. Přeskočit [zmenšit databázi](#shrink-the-database) krok a přejděte na [odstranění spustit data o historii](#delete-run-history-data) krok.

5. Pokud není spuštěný, pokuste se spustit službu. Pokud byla služba spuštěna úspěšně, přeskočte [zmenšit databázi](#shrink-the-database) krok a přejděte na [odstranění spustit data o historii](#delete-run-history-data) krok. Jinak, pokračujte [zmenšit databázi](#shrink-the-database) krok.

### <a name="shrink-the-database"></a>Zmenšení databáze
Pomocí operace zmenšení uvolní dostatek místa DB ke spuštění synchronizační služby. Se uvolní místo DB odebráním prázdné znaky v databázi. Tento krok je typu best effort, protože není zaručeno, lze vždy zotavit prostor. Další informace o operaci zmenšení, přečtěte si tento článek [zmenšit databázi](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Tento krok přeskočte, pokud vám synchronizační službu spustit. Není doporučeno zmenšit databázi SQL, protože může vést k nižšímu výkonu z důvodu vyšší fragmentace.

Název databáze vytvořené pro Azure AD Connect je **ADSync**. K provedení operace zmenšení, musíte být přihlášení jako správce nebo vlastníka databáze. Během instalace služby Azure AD Connect následující účty jsou udělena práva správce systému:
* Místní správci
* Uživatelský účet, který byl použit ke spuštění instalace služby Azure AD Connect.
* Účet synchronizační služby, který se používá jako operační kontext služby Azure AD Connect synchronizace.
* Místní skupiny ADSyncAdmins, který byl vytvořen během instalace.

1. Zálohovat databázi tak, že zkopírujete **ADSync.mdf** a **ADSync_log.ldf** soubory umístěné v `%ProgramFiles%\Microsoft Azure AD Sync\Data` do bezpečného umístění.

2. Spusťte novou relaci prostředí PowerShell.

3. Přejděte do složky `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`.

4. Spustit **sqlcmd** nástroj spuštěním příkazu `./SQLCMD.EXE -S “(localdb)\.\ADSync” -U <Username> -P <Password>`, pomocí přihlašovacích údajů správce systému nebo databázi DBO.

5. Zmenšení databáze příkazového řádku sqlcmd (1 >), zadejte `DBCC Shrinkdatabase(ADSync,1);`, za nímž následují `GO` v dalším řádku.

6. Pokud byla operace úspěšná, pokuste se znovu spustit službu synchronizace. Pokud je možné spustit služba synchronizace, přejděte na [odstranění spustit data o historii](#delete-run-history-data) krok. Pokud ne, obraťte se na podporu.

### <a name="delete-run-history-data"></a>Odstranění spustit data historie
Ve výchozím nastavení Azure AD Connect zachová až do historie spouštění data za sedm dní. V tomto kroku jsme odstranit data historie spouštění uvolnění místa DB tak, aby služba Azure AD Connect synchronizaci můžete spustit synchronizaci opakovat.

1.  Spustit **Synchronization Service Manager** přechodem na spuštění → synchronizační služba.

2.  Přejděte na **Operations** kartě.

3.  V části **akce**, vyberte **zrušte spustí**...

4.  Můžete buď zvolit **vymazat všechny spustí** nebo **Clear spuštěná před... <date>**  možnost. Doporučuje se spustíte tak, že smažete spustit historie data, která jsou starší než dvou dnů. Pokud budete pokračovat narazíte na problém velikost databáze, zvolte **vymazat všechny spustí** možnost.

### <a name="shorten-retention-period-for-run-history-data"></a>Zkrátit dobu uchování historie spouštění dat
Tento krok je snížit pravděpodobnost vzniku problém 10 GB limit po několik cyklů synchronizace.

1. Otevřete novou relaci prostředí PowerShell.

2. Spustit `Get-ADSyncScheduler` a poznamenejte si vlastnosti PurgeRunHistoryInterval, která určuje aktuální dobu uchování.

3. Spustit `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` nastavit dobu uchování do dvou dnů. Doba uchovávání informací podle potřeby upravte.

## <a name="long-term-solution--migrate-to-full-sql"></a>Dlouhodobé řešení – migrace do úplné SQL
Problém je obecně naznačuje výslednou, že velikost 10 GB databáze již není dostatečná pro Azure AD Connect pro synchronizaci vaší místní služby Active Directory do Azure AD. Doporučuje se, že přejdete na plnou verzi systému SQL server pomocí. Nemůžete LocalDB existujícího nasazení Azure AD Connect přímo nahradit databází plné verze SQL. Místo toho je nutné nasadit nový server Azure AD Connect s plnou verzí SQL. Doporučuje se provést postupnou migraci, kdy se nový server Azure AD Connect (s databází SQL) nasadí jako pracovní server vedle existujícího serveru Azure AD Connect (s LocalDB). 
* Pokyny ke konfiguraci vzdáleného SQL se službou Azure AD Connect najdete v článku s popisem [vlastní instalace Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* Pokyny k postupné migraci kvůli upgradu Azure AD Connect najdete v článku [Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration).

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
