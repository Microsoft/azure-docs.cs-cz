---
title: Udělení přístupu k Azure SQL Database | Dokumentace Microsoftu
description: Přečtěte si, jak udělit přístup ke službě Microsoft Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 1844abbff660b175ea78d09bd2256aa5ba83ed83
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="azure-sql-database-access-control"></a>Řízení přístupu ke službě Azure SQL Database
Služba SQL Database zajišťuje zabezpečení řízením přístupu pomocí pravidel brány firewall omezujících připojení podle IP adresy, ověřovacích mechanismů vyžadujících po uživatelích prokázání identity a autorizačních mechanismů omezujících uživatelům přístup ke konkrétním akcím a datům. 

> [!IMPORTANT]
> Přehled funkcí zabezpečení služby SQL Database najdete v [přehledu zabezpečení SQL](sql-database-security-overview.md). Podívejte se kurz [zabezpečení vaší databázi SQL Azure](sql-database-security-tutorial.md).

## <a name="firewall-and-firewall-rules"></a>Brána firewall a pravidla brány firewall
Microsoft Azure SQL Database poskytuje relační databázovou službu pro aplikace Azure a další internetové aplikace. Brána firewall pomáhá chránit vaše data tím, že brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače k tomu mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku. Další informace najdete v tématu [Přehled pravidel brány firewall služby Azure SQL Database](sql-database-firewall-configure.md).

Služba Azure SQL Database je dostupná jenom prostřednictvím portu TCP 1433. Pokud chcete z počítače získat přístup ke službě SQL Database, ověřte, že brána firewall vašeho klientského počítače umožňuje odchozí komunikaci TCP přes port TCP 1433. Pokud ho jiné aplikace nepotřebují, zablokujte na portu TCP 1433 příchozí připojení. 

V rámci připojovacího procesu budou připojení virtuálních počítačů Azure přesměrována na jinou IP adresu a port, které jsou pro každou roli pracovního procesu jedinečné. Číslo portu je v rozsahu 11000 až 11999. Další informace o portech TCP naleznete v tématu [porty nad rámec 1433 pro technologii ADO.NET 4.5 a SQL Database2](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Authentication

SQL Database podporuje dva typy ověřování:

* **Ověřování SQL,** které používá uživatelské jméno a heslo. Když jste vytvářeli logický server databáze, zadali jste uživatelské jméno a heslo účtu „server admin“. Tyto přihlašovací údaje můžete použít k ověření a přihlášení k libovolné databázi na daném serveru jako vlastník databáze neboli „dbo“. 
* **Ověřování pomocí Azure Active Directory,** které používá identity spravované v Azure Active Directory a je podporované u spravovaných a integrovaných domén. [Kdykoliv to půjde](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), použijte ověřování pomocí Active Directory (integrované zabezpečení). Pokud chcete k ověřování použít Azure Active Directory, musíte vytvořit jiného správce serveru, který se jmenuje „Azure AD admin“ a který smí spravovat uživatele a skupiny služby Azure AD. Tento správce také smí provádět všechny operace jako běžný správce serveru. Postup vytvoření účtu správce služby Azure AD, který umožňuje ověřování Azure Active Directory, najdete v tématu [Připojení ke službě SQL Database s ověřením přes Azure Active Directory](sql-database-aad-authentication.md).

Databázový stroj zavře připojení, které jsou neaktivní déle než 30 minut. Připojení se musí znovu přihlásit, aby bylo možné ho použít. Trvale aktivní připojení ke službě SQL Database vyžadují opakovanou autorizaci (prováděnou databázovým strojem) nejméně každých 10 hodin. Databázový stroj se pokusí při opakované autorizaci použít původně zadané heslo, aby nebyl nutný zásah uživatele. Z důvodů výkonu při vytvoření nového hesla v databázi SQL, připojení není k novému ověření, i v případě, že připojení je ukončeno z důvodu sdružování připojení. Toto chování se liší od místního SQL Serveru. Pokud se heslo od původního ověření připojení změnilo, musí se připojení ukončit a vytvořit nové připojení pomocí nového hesla. Uživatel s oprávněním `KILL DATABASE CONNECTION` může připojení ke službě SQL Database výslovně ukončit příkazem [KILL](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql).

Uživatelské účty je možné vytvořit v hlavní databázi a udělit jim oprávnění ve všech databázích na serveru, nebo je lze vytvořit v samotné databázi (takové účty se nazývají uživatelé s omezením). Informace o vytváření a správě přihlašování najdete v tématu [Správa přihlašování](sql-database-manage-logins.md). Pomocí databáze s omezením zlepšují přenositelnost a škálovatelnost. Další informace o uživatelích s omezením najdete v tématech [Uživatelé databáze s omezením – vytvoření přenosné databáze](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), [CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) a [Databáze s omezením](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases).

Doporučený postup je, aby aplikace k ověření používala vyhrazený účet – omezíte tak počet oprávnění udělených aplikaci a snížíte riziko škodlivých aktivit v případě ohrožení kódu aplikace útoky v podobě injektáže SQL. Doporučený postup je vytvořit [uživatele databáze s omezením](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), který umožňuje ověřit aplikaci přímo v databázi. 

## <a name="authorization"></a>Autorizace

Autorizace určuje, co může uživatel provádět ve službě Azure SQL Database. Tyto možnosti jsou dané [členstvím v databázových rolích](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) a [oprávněními k databázi na úrovni objektů](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) vašeho účtu. Doporučený postup je udělit uživatelům co nejmenší možná oprávnění. Účet správce serveru, který používáte k připojení, je členem skupiny db_owner. Tato skupina může s databází provádět všechny operace. Tento účet uložte kvůli nasazení upgradovaných schémat a dalším možnostem správy. Použijte účet „ApplicationUser“, který má omezenější oprávnění a umožňuje připojit se z aplikace k databázi s nejnižšími oprávněními, jaké aplikace potřebuje. Další informace najdete v tématu [Správa přihlašování](sql-database-manage-logins.md).

K databázi `master` většinou potřebují mít přístup jenom správci. Pro rutinní přístup ke každé uživatelské databázi můžete použít databázové uživatele s omezením, kteří nejsou správci, ale jsou v každé databázi vytvoření. Když použijete tyto databázové uživatele s omezením, nepotřebujete v databázi `master` vytvářet přihlášení. Další informace najdete v tématu [Uživatelé databáze s omezením – zajištění přenositelnosti databáze](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable).

Měli byste se seznámit s následujícími funkcemi, které jde použít k omezení nebo zvýšení oprávnění:   
* K dočasnému bezpečnému zvýšení oprávnění můžete použít [zosobnění](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) a [přihlašování k modulům](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server).
* K omezení řádků, ke kterým má uživatel přístup, můžete použít [zabezpečení na úrovni řádku](https://docs.microsoft.com/sql/relational-databases/security/row-level-security).
* K omezení rizika ohrožení citlivých dat můžete použít [maskování dat](sql-database-dynamic-data-masking-get-started.md).
* K omezení akcí, které je možné s databází provádět, můžete použít [uložené procedury](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine).

## <a name="next-steps"></a>Další postup

- Přehled funkcí zabezpečení služby SQL Database najdete v [přehledu zabezpečení SQL](sql-database-security-overview.md).
- Další informace o pravidlech brány firewall najdete v tématu [pravidla brány Firewall](sql-database-firewall-configure.md).
- Informace o uživateli a přihlašování najdete v tématu [Správa přihlašování](sql-database-manage-logins.md). 
- Informace Proaktivní monitorování, naleznete v [auditování databáze](sql-database-auditing.md) a [detekce hrozeb databáze SQL](sql-database-threat-detection.md).
- Podívejte se kurz [zabezpečení vaší databázi SQL Azure](sql-database-security-tutorial.md).
