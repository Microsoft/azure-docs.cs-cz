---
title: Konfigurace zabezpečení pro zotavení po havárii
description: Seznamte se s požadavky na zabezpečení při konfiguraci a správě zabezpečení po obnovení databáze nebo převzetí služeb při selhání sekundárním serverem.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 92a0c7fd3733b5e27c34c6fd0fe157bfb466a0fd
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91444898"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurace a Správa zabezpečení Azure SQL Database pro geografické obnovení nebo převzetí služeb při selhání
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek popisuje požadavky na ověřování ke konfiguraci a řízení [aktivní geografické replikace](active-geo-replication-overview.md) a [skupin s automatickým převzetím služeb při selhání](auto-failover-group-overview.md). Poskytuje taky kroky potřebné k nastavení přístupu uživatelů k sekundární databázi. Nakonec také popisuje, jak povolit přístup k obnovené databázi po použití [geografického obnovení](recovery-using-backups.md#geo-restore). Další informace o možnostech obnovení najdete v tématu [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="disaster-recovery-with-contained-users"></a>Zotavení po havárii s uživateli s omezením

Na rozdíl od tradičních uživatelů, které musí být namapovány na přihlašovací údaje v hlavní databázi, je obsažený uživatel zcela spravován samotným databází. To má dvě výhody. Ve scénáři zotavení po havárii se uživatelé mohou nadále připojovat k nové primární databázi nebo databáze obnovena pomocí geografického obnovení bez jakékoli další konfigurace, protože databáze spravuje uživatele. Z perspektivy přihlášení se z této konfigurace taky mohou využít výhody a možnosti škálovatelnosti a výkonu. Další informace najdete v tématu [Uživatelé databáze s omezením – zajištění přenositelnosti databáze](https://msdn.microsoft.com/library/ff929188.aspx).

Hlavním obchodem je, že Správa procesu zotavení po havárii je ve velkém rozsahu náročná. Pokud máte více databází, které používají stejné přihlašovací údaje, zajistěte, aby pověření s použitím obsažených uživatelů v několika databázích nemusely mít na starosti výhody obsažených uživatelů. Zásady rotace hesla například vyžadují, aby se změny v několika databázích udělaly konzistentně, a neměňte heslo pro přihlášení jednou v hlavní databázi. Z tohoto důvodu platí, že pokud máte více databází, které používají stejné uživatelské jméno a heslo, nedoporučuje se použít uživatele s omezením.

## <a name="how-to-configure-logins-and-users"></a>Jak nakonfigurovat přihlášení a uživatele

Pokud používáte přihlašovací jména a uživatele (místo obsažených uživatelů), musíte provést další kroky, abyste zajistili, že v hlavní databázi existují stejná přihlášení. V následujících částech najdete přehled kroků a další okolnosti.

  >[!NOTE]
  > Ke správě databází je taky možné použít přihlášení pomocí Azure Active Directory (AAD). Další informace najdete v tématu [přihlášení a uživatelé Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Nastavení přístupu uživatele k sekundární nebo obnovené databázi

Aby byla sekundární databáze použitelná jako sekundární databáze jen pro čtení a zajistila řádný přístup k nové primární databázi nebo databázi obnovená pomocí geografického obnovení, musí mít hlavní databáze cílového serveru před obnovením správnou konfiguraci zabezpečení.

Konkrétní oprávnění pro jednotlivé kroky jsou popsány dále v tomto tématu.

Příprava přístupu uživatele k sekundární geografické replikaci by měla být provedena jako součást konfigurace geografické replikace. Příprava přístupu uživatelů k geograficky obnoveným databázím by se měla provádět kdykoli, když je původní server online (například jako součást postupu zotavení po havárii).

> [!NOTE]
> Pokud převezmete služby při selhání nebo geografické obnovení na server, který nemá správně nakonfigurovaná přihlášení, bude přístup k tomuto účtu omezený na účet správce serveru.

Nastavení přihlašovacích údajů na cílovém serveru zahrnuje tři kroky popsané níže:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. určení přihlašovacích údajů s přístupem k primární databázi

Prvním krokem procesu je určení, která přihlášení musí být duplikována na cílovém serveru. K tomu je možné využít dvojici příkazů SELECT, jednu v logické hlavní databázi na zdrojovém serveru a jednu v samotné primární databázi.

Přihlášení na zdrojovém serveru můžou určit jenom správce serveru nebo člen role serveru **LoginManager** , a to pomocí následujícího příkazu SELECT.

```sql
SELECT [name], [sid]
FROM [sys].[sql_logins]
WHERE [type_desc] = 'SQL_Login'
```

Pouze člen role databáze db_owner, uživatel dbo nebo správce serveru může určit všechny objekty zabezpečení databáze uživatele v primární databázi.

```sql
SELECT [name], [sid]
FROM [sys].[database_principals]
WHERE [type_desc] = 'SQL_USER'
```

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Vyhledejte SID pro přihlašovací údaje identifikované v kroku 1.

Porovnáním výstupu dotazů z předchozí části a se shodnými identifikátory SID můžete mapovat přihlášení serveru na uživatele databáze. Přihlášení, která mají uživatele databáze se shodným identifikátorem SID, mají přístup uživatele k této databázi jako objekt zabezpečení databáze uživatele.

Následující dotaz se dá použít k zobrazení všech objektů zabezpečení uživatele a jejich identifikátorů SID v databázi. Tento dotaz může spustit pouze člen databázové role db_owner nebo správce serveru.

```sql
SELECT [name], [sid]
FROM [sys].[database_principals]
WHERE [type_desc] = 'SQL_USER'
```

> [!NOTE]
> Uživatelé **INFORMATION_SCHEMA** a **sys** mají *null* identifikátory SID a identifikátor SID **hosta** je **0x00**. Identifikátor SID **dbo** může začínat na *0x01060000000001648000000000048454*, pokud autor databáze byl správcem serveru, ne členem **DbManager**.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. vytvoření přihlašovacích údajů na cílovém serveru

Posledním krokem je přejít na cílový server nebo na servery a vygenerovat přihlašovací údaje s příslušnými identifikátory zabezpečení (SID). Základní syntaxe je následující.

```sql
CREATE LOGIN [<login name>]
WITH PASSWORD = <login password>,
SID = <desired login SID>
```

> [!NOTE]
> Pokud chcete uživateli udělit přístup k sekundárnímu, ale ne k primárnímu, můžete to udělat tak, že změníte přihlašovací údaje uživatele na primárním serveru pomocí následující syntaxe.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> Při zakázání se heslo nemění, takže v případě potřeby ho můžete kdykoli povolit.

## <a name="next-steps"></a>Další kroky

* Další informace o správě přístupu k databázi a přihlášení najdete v tématu [SQL Database Security: Správa přístupu k databázi a zabezpečení přihlášení](logins-create-manage.md).
* Další informace o uživatelích databáze s omezením najdete v tématu databáze [uživatelů s omezením – vytvoření přenosné databáze](https://msdn.microsoft.com/library/ff929188.aspx).
* Informace o aktivní geografické replikaci najdete v tématu [Aktivní geografická replikace](active-geo-replication-overview.md).
* Další informace o skupinách automatického převzetí služeb při selhání najdete v tématu [skupiny automatického převzetí služeb při selhání](auto-failover-group-overview.md).
* Informace o použití geografického obnovení najdete v tématu [geografické obnovení](recovery-using-backups.md#geo-restore) .
