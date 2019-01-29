---
title: 'Azure AD Connect: Jak provést obnovení LocalDB 10 GB limitu problém | Dokumentace Microsoftu'
description: Toto téma popisuje, jak služba Azure AD Connect synchronizaci obnovit, pokud se setká s LocalDB 10GB limit problém.
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
ms.openlocfilehash: 08e1beb199287db1d030d5ad9357cbd20f1eb859
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168631"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Jak provést obnovení při dosažení 10GB limitu pro LocalDB
Azure AD Connect vyžaduje k ukládání dat identity databázi SQL Serveru. Můžete použít buď výchozí databázi SQL Server 2012 Express LocalDB nainstalovanou se službou Azure AD Connect, nebo vlastní plnou verzi SQL. SQL Server Express má omezení velikosti 10 GB. Pokud při použití LocalDB dosáhnete tohoto limitu, synchronizační služba Azure AD Connect se už nemůže spustit ani správně synchronizovat. Tento článek popisuje kroky obnovení.

## <a name="symptoms"></a>Příznaky
Existují dva běžné příznaky:

* Služba Azure AD Connect synchronizaci **běží** , ale selže na synchronizaci s *"zastavena database zaplnění disku"* chyby.

* Služba Azure AD Connect synchronizaci **nelze spustit**. Při pokusu o spuštění služby nezdaří s chybové zprávy a události 6323 *"na serveru došlo k chybě systému SQL Server je nedostatek místa na disku."*

## <a name="short-term-recovery-steps"></a>Postup krátkodobé obnovení
Tato část vysvětluje, jak uvolněním místa v databázi požadované pro službu Azure AD Connect synchronizaci k obnovení. Zahrnuje následující kroky:
1. [Zjistit stav služby synchronizace](#determine-the-synchronization-service-status)
2. [Zmenšit databázi](#shrink-the-database)
3. [Odstranění dat historie spuštění](#delete-run-history-data)
4. [Zkraťte dobu uchování dat historie spuštění](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>Zjistit stav služby synchronizace
Nejprve určete, zda synchronizační služba je stále spuštěný:

1. Přihlaste se k serveru Azure AD Connect jako správce.

2. Přejděte na **správce řízení služeb**.

3. Zkontrolujte stav **Microsoft Azure AD Sync**.


4. Pokud je spuštěná, zastavte nebo restartujte službu. Přeskočit [zmenšit databázi](#shrink-the-database) krok a přejděte na [dat historie spuštění odstranění](#delete-run-history-data) kroku.

5. Pokud není spuštěný, pokuste se spustit službu. Pokud úspěšně spustí službu přeskočit [zmenšit databázi](#shrink-the-database) krok a přejděte na [dat historie spuštění odstranění](#delete-run-history-data) kroku. V opačném případě pokračujte [zmenšit databázi](#shrink-the-database) kroku.

### <a name="shrink-the-database"></a>Zmenšit databázi
Pomocí operace zmenšení můžete uvolnit tak dostatek místa v databázi spustit službu synchronizace. To uvolní místa v databázi tak, že odeberete obsahovat jenom prázdné znaky v databázi. Tento krok je best effort, protože není zaručeno, že vždy můžete obnovit místa. Další informace o operaci zmenšení, přečtěte si tento článek [zmenšit databázi](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Tento krok přeskočte, pokud se zobrazí se synchronizační služba ke spuštění. Není doporučeno zmenšit databázi SQL, protože to může vést ke špatnému výkonu z důvodu zvýšení fragmentace.

Název databáze vytvořené pro Azure AD Connect je **ADSync**. K provedení operace zmenšení, musíte se přihlásit jako správce nebo vlastníka databáze. Během instalace služby Azure AD Connect následující účty jsou udělena práva správce systému:
* Místní správci
* Uživatelský účet, který byl použit ke spuštění instalace služby Azure AD Connect.
* Účet synchronizační služby, který se používá jako provozní kontext služba Azure AD Connect synchronizaci.
* Místní skupině ADSyncAdmins, který byl vytvořen během instalace.

1. Zálohování databáze tak, že zkopírujete **ADSync.mdf** a **ADSync_log.ldf** soubory jsou umístěné v části `%ProgramFiles%\Microsoft Azure AD Sync\Data` na bezpečné místo.

2. Spusťte novou relaci Powershellu.

3. Přejděte do složky `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`.

4. Spustit **sqlcmd** nástroj spuštěním příkazu `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>`, pomocí přihlašovacích údajů správce systému nebo databáze DBO.

5. Zmenšení databáze do příkazového řádku sqlcmd (1 >), zadejte `DBCC Shrinkdatabase(ADSync,1);`následovaný `GO` na dalším řádku.

6. Pokud je operace úspěšná, zkuste spustit synchronizační službu znovu. Pokud můžete spustit synchronizační službu, přejděte na [dat historie spuštění odstranění](#delete-run-history-data) kroku. Pokud ne, obraťte se na podporu.

### <a name="delete-run-history-data"></a>Odstranění dat historie spuštění
Ve výchozím nastavení služby Azure AD Connect zachová až do data historie spuštění za sedm dní. V tomto kroku odstraníme dat historie spouštění a uvolněním místa v databázi tak, aby služba Azure AD Connect synchronizaci můžete provést synchronizaci znovu.

1.  Spustit **Synchronization Service Manager** tak, že přejdete na ZAČÁTKU → synchronizační služba.

2.  Přejděte **operace** kartu.

3.  V části **akce**vyberte **vymazat spuštění**...

4.  Můžete si vybrat **zrušte všechna spuštění** nebo **vymazat spuštěn dříve, než... <date>**  možnost. Doporučujeme začít tím, že zrušíte spustit data historie, která jsou starší než dvou dnů. Pokud narazíte na problém velikost databáze, klikněte na tlačítko **zrušte všechna spuštění** možnost.

### <a name="shorten-retention-period-for-run-history-data"></a>Zkraťte dobu uchování dat historie spuštění
Tento krok je snížit pravděpodobnost, že narazíte na problém 10 GB limitu po několik synchronizačních cyklů.

1. Otevřete novou relaci Powershellu.

2. Spustit `Get-ADSyncScheduler` a poznamenejte si vlastnost PurgeRunHistoryInterval, která určuje aktuální dobu uchovávání.

3. Spustit `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` nastavit dobu uchování na dva dny. Upravte dobu uchovávání informací podle potřeby.

## <a name="long-term-solution--migrate-to-full-sql"></a>Dlouhodobým řešením – migrace na SQL
Problém je většinou naznačuje, že velikost 10 GB databáze již není dostatečné pro Azure AD Connect pro synchronizaci vaší místní služby Active Directory do služby Azure AD. Doporučuje se, že přejdete na používání plnou verzi systému SQL server. Nemůžete LocalDB existujícího nasazení Azure AD Connect přímo nahradit databází plné verze SQL. Místo toho je nutné nasadit nový server Azure AD Connect s plnou verzí SQL. Doporučuje se provést postupnou migraci, kdy se nový server Azure AD Connect (s databází SQL) nasadí jako pracovní server vedle existujícího serveru Azure AD Connect (s LocalDB). 
* Pokyny ke konfiguraci vzdáleného SQL se službou Azure AD Connect najdete v článku s popisem [vlastní instalace Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom).
* Pokyny k postupné migraci kvůli upgradu Azure AD Connect najdete v článku [Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration).

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
