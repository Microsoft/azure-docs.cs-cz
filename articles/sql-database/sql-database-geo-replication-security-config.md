---
title: Konfigurace zabezpečení databáze SQL Azure pro zotavení po havárii | Dokumentace Microsoftu
description: Další informace o zabezpečení pro konfiguraci a správu zabezpečení po obnovení databáze nebo převzetí služeb při selhání na sekundární server.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: ac57f97f45f14c5011782fa0fb8b708bc52bd151
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871179"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurace a Správa zabezpečení služby Azure SQL Database pro geografické obnovení nebo převzetí služeb při selhání 

Toto téma popisuje požadavky na ověřování pro konfiguraci a řídit [aktivní geografickou replikaci](sql-database-geo-replication-overview.md) a kroky potřebné k nastavení přístupu uživatelů k sekundární databázi. Také popisuje, jak povolit přístup k obnovené databázi po použití [geografické obnovení](sql-database-recovery-using-backups.md#geo-restore). Další informace o možnostech obnovení najdete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md).

> [!NOTE]
> [Aktivní geografická replikace](sql-database-geo-replication-overview.md) je nyní k dispozici pro všechny databáze ve všech úrovních služby.
>  

## <a name="disaster-recovery-with-contained-users"></a>Zotavení po havárii pomocí uživatelé s omezením
Na rozdíl od tradičních uživatele, kteří musí být namapován na přihlášení v hlavní databázi, uživatele spravuje úplně samotná databáze. To má dvě výhody. Ve scénáři zotavení po havárii uživatelé můžou dál pro připojení k nové primární databázi nebo databázi obnovit, využitím geografického obnovení bez jakékoli další konfigurace, protože databáze spravuje uživatele. Existují také potenciální škálovatelnost a výkon výhody z této konfigurace z hlediska přihlášení. Další informace najdete v tématu [Uživatelé databáze s omezením – zajištění přenositelnosti databáze](https://msdn.microsoft.com/library/ff929188.aspx). 

Hlavní nutný kompromis je, že Správa procesu pro zotavení po havárii ve velkém měřítku je složitější. Pokud máte více databází, které používají stejné přihlašovací údaje, Správa pověření pomocí uživatelé s omezením do více databází může negate výhody uživatelé s omezením. Například zásady rotace hesla vyžaduje, aby změny konzistentně ve více databázích místo změny hesla pro přihlášení v hlavní databázi jednou. Z tohoto důvodu Pokud máte více databází, které používají stejné uživatelské jméno a heslo, uživatelé s omezením použití nedoporučuje. 

## <a name="how-to-configure-logins-and-users"></a>Konfigurace přihlašovacích údajů a uživatelů
Pokud použijete přihlášení a uživatele (místo uživatelé s omezením), musejí udělat dodatečné kroky k zajištění, že existují stejné přihlášení v hlavní databázi. Následující oddíly popisují kroky zahrnuté a další důležité informace.

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Nastavení přístupu uživatelů k sekundární nebo obnovené databáze
Sekundární databáze má být použitelná jako sekundární databázi jen pro čtení a k zajištění řádného přístup k nové primární databázi nebo databázi, obnovit pomocí geografické obnovení, hlavní databázi z cílového serveru musí mít příslušná bezpečnostní konfigurace na místě před obnovení.

Konkrétní oprávnění pro jednotlivé kroky jsou popsány dále v tomto tématu.

Připravuje se přístup uživatelů k sekundární geografická replikace je třeba provést v rámci konfigurace geografické replikace. Připravuje se přístup uživatelů k databázím geografické obnovení provést kdykoli původní server není online (například jako součást na postup zotavení po Havárii).

> [!NOTE]
> Pokud převzetí služeb při selhání nebo geografické obnovení na server, který nemá správně nakonfigurovanou přihlašovací jména, přístup k němu bude omezená na účet správce serveru.
> 
> 

Nastavení přihlášení na cílový server zahrnuje tři kroky uvedené níže:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Určete přihlašovací údaje s přístupem k primární databáze:
Prvním krokem procesu je určit, jaké přihlašovací údaje musí být duplicitní na cílovém serveru. Toho se dosahuje pomocí dvojice příkazy SELECT, jednu v logickou hlavní databázi na zdrojovém serveru a druhou v samotné databázi primární.

Jenom správce serveru nebo člen **LoginManager** role serveru, můžete zjistit přihlašovací jména na zdrojovém serveru pomocí následujícího příkazu SELECT. 

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

Jediným členem databázové role db_owner, dbo uživatele nebo správce serveru, můžete určit všechny objekty zabezpečení uživatelů databáze v primární databázi.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Zjistit SID pro přihlášení, určenou v kroku 1:
Porovnání výstup dotazy z předchozí části a odpovídající identifikátory SID, můžete namapovat přihlášení serveru na uživatele databáze. Uživatelé, kteří mají uživatele databáze s odpovídajícím identifikátorem SID mít uživatelský přístup k této databázi jako hlavní databáze uživatele. 

Pokud chcete zobrazit všechny objekty zabezpečení uživatelů a jejich identifikátorů SID v databázi je možné následující dotaz. Tento dotaz můžete spustit pouze členem skupiny db_owner databáze roli nebo server správce.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> **INFORMATION_SCHEMA** a **sys** uživatelé mají *NULL* SID a **hosta** SID je **0x00**. **Dbo** může začínat identifikátor SID *0x01060000000001648000000000048454*, pokud byl Tvůrce databází správce serveru místo členem **DbManager**.
> 
> 

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Vytvoření přihlášení na cílovém serveru:
Posledním krokem je přejít na cílový server nebo servery a generovat přihlášení s odpovídající identifikátory SID. Základní syntaxe je následující.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Pokud chcete udělit přístup uživatelů k sekundární, ale ne na primární, můžete to udělat změnou přihlášení uživatele na primárním serveru pomocí následující syntaxe.
> 
> PŘÍKAZ ALTER LOGIN <login name> ZAKÁZAT
> 
> ZAKÁZAT nedojde ke změně hesla, takže můžete vždy povolit ji v případě potřeby.
> 
> 

## <a name="next-steps"></a>Další postup
* Další informace o správě přístupu k databázi a přihlašovacích údajů, naleznete v tématu [zabezpečení služby SQL Database: Správa databáze přístup a zabezpečení přihlašování](sql-database-manage-logins.md).
* Další informace o uživatele databáze s omezením najdete v tématu [uživatelé databáze s omezením – zajištění přenositelnosti databáze](https://msdn.microsoft.com/library/ff929188.aspx).
* Informace o používání a konfigurace aktivní geografické replikace najdete v tématu [aktivní geografické replikace](sql-database-geo-replication-overview.md)
* Informace o použití geografické obnovení, najdete v části [geografické obnovení](sql-database-recovery-using-backups.md#geo-restore)

