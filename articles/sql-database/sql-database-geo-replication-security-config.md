---
title: Konfigurace zabezpečení databáze SQL Azure pro zotavení po havárii | Microsoft Docs
description: Další důležité informace o zabezpečení pro konfiguraci a správu zabezpečení po obnovení databáze nebo převzetí služeb při selhání na sekundární server.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/01/2018
ms.author: sashan
ms.openlocfilehash: 6b4b599fee9a8ff5b4674cf26ca19b22230d6364
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurovat a spravovat zabezpečení Azure SQL Database geografické obnovení nebo převzetí služeb při selhání 

Toto téma popisuje požadavky na ověřování, konfigurovat a řídit [aktivní geografickou replikaci](sql-database-geo-replication-overview.md) a kroky potřebné k nastavení přístup uživatelů k sekundární databázi. Také popisuje, jak chcete umožnit přístup k obnovené databáze po použití [geografické obnovení](sql-database-recovery-using-backups.md#geo-restore). Další informace o možnostech obnovení najdete v tématu [obchodní kontinuity přehled](sql-database-business-continuity.md).

> [!NOTE]
> [Aktivní geografickou replikací](sql-database-geo-replication-overview.md) je nyní k dispozici pro všechny databáze v všechny úrovně služeb.
>  

## <a name="disaster-recovery-with-contained-users"></a>Zotavení po havárii s omezením uživateli
Na rozdíl od tradičních uživatele, které musí být namapován na přihlášení v hlavní databázi, je uživatele zcela spravuje samotná databáze. To má dvě výhody. Ve scénáři zotavení po havárii uživatelé můžete pokračovat pro připojení k nové primární databáze nebo databáze obnovena pomocí geografické obnovení bez jakékoli dodatečné konfigurace, protože databáze spravuje uživatele. Existují také potenciální škálovatelnost a výkon výhody z této konfigurace z hlediska přihlášení. Další informace najdete v tématu [Uživatelé databáze s omezením – zajištění přenositelnosti databáze](https://msdn.microsoft.com/library/ff929188.aspx). 

Hlavní kompromis je, že Správa procesu obnovení po havárii ve velkém měřítku je další náročné. Pokud máte více databází, které používají stejné přihlašovací údaje, může zachování přihlašovací údaje ve více databází pomocí uživatelů s omezením negate výhod uživatelů s omezením. Například otočení zásady hesel vyžaduje, aby změny konzistentně ve více databází místo Změna hesla pro přihlášení jednou v hlavní databázi. Z tohoto důvodu, pokud máte více databází, které používají stejné uživatelské jméno a heslo, uživatelů s omezením se nedoporučuje používat. 

## <a name="how-to-configure-logins-and-users"></a>Postup konfigurace přihlášení a uživatele
Pokud používáte přihlášení a uživatele (místo uživatelů s omezením), je nutné provést další kroky zajistit, že v hlavní databázi existují stejné přihlášení. Následující oddíly popisují kroky související se situací a další důležité informace.

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Nastavit přístup uživatelů k sekundární nebo obnovené databáze
V pořadí pro sekundární databázi možné používat jako sekundární databáze jen pro čtení a k zajištění řádného přístup k nové primární databáze nebo databáze obnovit pomocí geografické obnovení hlavní databáze cílového serveru musí mít příslušná bezpečnostní konfigurace na místě před obnovení.

Konkrétní oprávnění pro jednotlivé kroky jsou popsané dále v tomto tématu.

Příprava přístup uživatelů k sekundární geografická replikace je třeba provést v rámci konfigurace geografická replikace. Příprava uživatelský přístup k databázím geografické obnovení provést kdykoli po online původního serveru (např. jako součást postupu zotavení po Havárii).

> [!NOTE]
> Pokud jste převzetí služeb při selhání nebo geografické obnovení na server, který nemá správně nakonfigurovanou přihlášení, přístup k němu bude omezena na účet správce serveru.
> 
> 

Nastavení přihlášení na cílovém serveru zahrnuje tři kroky uvedené níže:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Určení přihlášení s přístupem k primární databázi:
Prvním krokem procesu je určit, které přihlášení musí být duplicitní na cílovém serveru. To je provedeno pomocí pár příkazů SELECT, jeden v logické hlavní databázi na zdrojovém serveru a jeden v primární databázi, sám sebe.

Pouze správce serveru nebo člen **LoginManager** role serveru můžete určit přihlášení na zdrojovém serveru s následující příkaz SELECT. 

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

Pouze členové databázové role db_owner, dbo uživatele nebo správce serveru, můžete určit všechny objekty uživatele databáze v primární databázi.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Zjistit SID pro přihlášení identifikovaného v kroku 1:
Porovnání výstup dotazy z předchozí části a odpovídající identifikátory SID, můžete namapovat server přihlášení uživatele databáze. Přihlášení, která mají uživatelé databáze s odpovídajícím SID mít uživatel přístup k databázi jako tento uživatel databáze hlavní. 

Následující dotaz slouží k zobrazení všech objektů uživatele a jejich identifikátory SID v databázi. Tento dotaz můžete spustit jenom člen správce databáze db_owner role nebo serveru.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> **INFORMATION_SCHEMA** a **sys** uživatelů, kteří mají *NULL* identifikátory SID a **hosta** SID je **0x00**. **Dbo** SID může začínat *0x01060000000001648000000000048454*, pokud byla databáze Tvůrce správce serveru místo členem **DbManager**.
> 
> 

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Vytvoření přihlášení na cílovém serveru:
Posledním krokem je můžete přejít na cílový server nebo servery a generovat přihlášení s odpovídající identifikátory SID. Základní syntaxe je následující.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Pokud chcete udělit přístup uživatelům sekundární, ale není primární, můžete to udělat změnou přihlášení uživatele na primárním serveru pomocí následující syntaxe.
> 
> PŘÍKAZ ALTER LOGIN <login name> ZAKÁZAT
> 
> ZAKÁZAT nezmění heslo, takže můžete vždy ji povolit v případě potřeby.
> 
> 

## <a name="next-steps"></a>Další postup
* Další informace o správě přístupu k databázi a přihlášení najdete v tématu [zabezpečení SQL Database: správu přístupu a přihlašovací zabezpečení databáze](sql-database-manage-logins.md).
* Další informace o uživatele databáze s omezením naleznete v tématu [obsažené databáze uživatelé – provádění vaše databáze přenosné](https://msdn.microsoft.com/library/ff929188.aspx).
* Informace o používání a konfiguraci aktivní geografickou replikaci, najdete v článku [aktivní geografickou replikaci](sql-database-geo-replication-overview.md)
* Informace o použití geografické obnovení najdete v tématu [geografické obnovení](sql-database-recovery-using-backups.md#geo-restore)

