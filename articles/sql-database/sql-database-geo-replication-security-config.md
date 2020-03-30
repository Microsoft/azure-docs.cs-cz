---
title: Konfigurace zabezpečení pro zotavení po havárii
description: Seznamte se s aspekty zabezpečení pro konfiguraci a správu zabezpečení po obnovení databáze nebo převzetí služeb při selhání na sekundárním serveru.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 9d628583168883276e67d9e2f2fcafdce292769e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73807496"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurace a správa zabezpečení Azure SQL Database pro geografické obnovení nebo převzetí služeb při selhání

Tento článek popisuje požadavky na ověřování pro konfiguraci a řízení aktivních skupin [geografické replikace](sql-database-active-geo-replication.md) a [automatického převzetí služeb při selhání](sql-database-auto-failover-group.md). Poskytuje také kroky potřebné k nastavení přístupu uživatelů k sekundární databázi. Nakonec také popisuje, jak povolit přístup k obnovené databázi po použití [geografické obnovení](sql-database-recovery-using-backups.md#geo-restore). Další informace o možnostech obnovení naleznete v tématu [Přehled kontinuity provozu](sql-database-business-continuity.md).

## <a name="disaster-recovery-with-contained-users"></a>Zotavení po havárii s uzavřenými uživateli

Na rozdíl od tradičních uživatelů, které musí být mapovány na přihlášení v hlavní databázi, je uzavřený uživatel zcela spravován samotnou databází. To má dvě výhody. Ve scénáři zotavení po havárii se uživatelé mohou nadále připojovat k nové primární databázi nebo k databázi obnovené pomocí geografického obnovení bez jakékoli další konfigurace, protože databáze spravuje uživatele. Existují také potenciální škálovatelnost a výhody výkonu z této konfigurace z hlediska přihlášení. Další informace najdete v tématu [Uživatelé databáze s omezením – zajištění přenositelnosti databáze](https://msdn.microsoft.com/library/ff929188.aspx).

Hlavním kompromisem je, že řízení procesu obnovy po havárii ve velkém měřítku je náročnější. Pokud máte více databází, které používají stejné přihlášení, udržování přihlašovacích údajů pomocí obsažených uživatelů ve více databázích může negovat výhody obsažených uživatelů. Zásady střídání hesel například vyžadují, aby změny byly prováděny konzistentně ve více databázích, nikoli změnou hesla pro přihlášení jednou v hlavní databázi. Z tohoto důvodu pokud máte více databází, které používají stejné uživatelské jméno a heslo, použití obsažených uživatelů se nedoporučuje.

## <a name="how-to-configure-logins-and-users"></a>Jak konfigurovat přihlášení a uživatele

Pokud používáte přihlášení a uživatele (spíše než obsažené uživatele), je nutné provést další kroky k zajištění, že stejné přihlášení existují v hlavní databázi. V následujících částech jsou popsány související kroky a další důležité informace.

  >[!NOTE]
  > Ke správě databází je také možné použít přihlášení služby Azure Active Directory (AAD). Další informace najdete v tématu [Přihlášení Azure SQL a uživatelé](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Nastavení přístupu uživatelů k sekundární nebo obnovené databázi

Aby byla sekundární databáze použitelná jako sekundární databáze jen pro čtení a aby byl zajištěn správný přístup k nové primární databázi nebo databázi obnovené pomocí geografického obnovení, musí mít hlavní databáze cílového serveru odpovídající zabezpečení. konfiguraci na místě před obnovením.

Konkrétní oprávnění pro každý krok jsou popsány dále v tomto tématu.

Příprava přístupu uživatele k sekundární geografické replikaci by měla být provedena jako součást konfigurace geografické replikace. Příprava přístupu uživatelů k geograficky obnoveným databázím by měla být provedena kdykoli, když je původní server online (např. jako součást cvičení zotavení po havárii).

> [!NOTE]
> Pokud převezmete služby při selhání nebo geograficky obnovíte server, který nemá správně nakonfigurovaná přihlášení, bude přístup k němu omezen na účet správce serveru.

Nastavení přihlášení na cílovém serveru zahrnuje tři kroky popsané níže:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Určení přihlášení s přístupem k primární databázi

Prvním krokem procesu je určit, která přihlášení musí být duplikována na cílovém serveru. Toho je dosaženo pomocí dvojice příkazů SELECT, jeden v logické hlavní databázi na zdrojovém serveru a jeden v samotné primární databázi.

Pouze správce serveru nebo člen role serveru **LoginManager** může určit přihlášení na zdrojovém serveru pomocí následujícího příkazu SELECT.

    SELECT [name], [sid]
    FROM [sys].[sql_logins]
    WHERE [type_desc] = 'SQL_Login'

Pouze člen db_owner databázové role, uživatel dbo nebo správce serveru, může určit všechny objekty uživatelů databáze v primární databázi.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Najděte SID pro přihlášení identifikovaná v kroku 1

Porovnáním výstup dotazů z předchozí části a odpovídající SID, můžete mapovat přihlášení serveru k databázi uživatele. Přihlášení, které mají uživatele databáze s odpovídající SID mají přístup uživatele k této databázi jako objekt uživatele databáze.

Následující dotaz lze zobrazit všechny objekty uživatelů a jejich SID v databázi. Tento dotaz může spustit pouze člen db_owner databázové role nebo správce serveru.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> Uživatelé **INFORMATION_SCHEMA** a **sys** mají *hodnoty SID null* a **sid hosta** je **0x00**. **DBO** SID může začínat **DbManager** *0x0106000000016480000000000484544544444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444444*

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Vytvořte přihlášení na cílovém serveru

Posledním krokem je přejít na cílový server nebo servery a generovat přihlášení s příslušnými sidy. Základní syntaxe je následující.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Pokud chcete udělit uživateli přístup k sekundární, ale ne k primární, můžete to udělat změnou přihlášení uživatele na primárním serveru pomocí následující syntaxe.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> DISABLE nezmění heslo, takže ho můžete v případě potřeby vždy povolit.

## <a name="next-steps"></a>Další kroky

* Další informace o správě přístupu k databázi a přihlášení naleznete v tématu [Zabezpečení databáze SQL: Správa přístupu k databázi a zabezpečení přihlášení](sql-database-manage-logins.md).
* Další informace o obsažených uživatelích databáze naleznete [v tématu Contained Database Users - Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx).
* Informace o aktivní geografické replikaci naleznete [v tématu Aktivní geografická replikace](sql-database-active-geo-replication.md).
* Další informace o skupinách s automatickým převzetím služeb při selhání naleznete [v tématu Skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md).
* Informace o použití geografického obnovení naleznete [v tématu geo-restore](sql-database-recovery-using-backups.md#geo-restore)
