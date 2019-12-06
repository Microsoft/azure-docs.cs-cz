---
title: Pravidla brány firewall protokolu IP
description: Nakonfigurujte pravidla brány firewall protokolu IP na úrovni serveru pro databázi SQL nebo bránu SQL Data Warehouse firewall. Spravujte přístup a nakonfigurujte pravidla brány firewall protokolu IP na úrovni databáze pro jednu nebo sdruženou databázi.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 5c1a146a12fd8881982826e0a87868a6eaf05cb1
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851810"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL Database a Azure SQL Data Warehouse pravidla brány firewall protokolu IP

> [!NOTE]
> Tento článek se týká serverů SQL Azure a databází Azure SQL Database i Azure SQL Data Warehouse na serveru Azure SQL. Pro zjednodušení *SQL Database* se používá pro odkazování na SQL Database a SQL Data Warehouse.

> [!IMPORTANT]
> Tento článek se *nevztahuje na* *Azure SQL Database spravovanou instanci*. Informace o konfiguraci sítě najdete v tématu [připojení aplikace k Azure SQL Database Managed instance](sql-database-managed-instance-connect-app.md).

Když vytvoříte nový server SQL Azure s názvem *MySQLServer*, brána firewall SQL Database blokuje veškerý přístup k veřejnému koncovému bodu pro server (který je dostupný na *MySQLServer.Database.Windows.NET*).

> [!IMPORTANT]
> SQL Data Warehouse podporuje jenom pravidla brány firewall protokolu IP na úrovni serveru. Nepodporuje pravidla brány firewall protokolu IP na úrovni databáze.

## <a name="how-the-firewall-works"></a>Jak funguje brána firewall
Pokusy o připojení z Internetu a Azure musí projít přes bránu firewall, aby se dostaly na SQL Server nebo SQL Database, jak ukazuje následující diagram.

   ![Diagram konfigurace brány firewall][1]

### <a name="server-level-ip-firewall-rules"></a>Pravidla firewallu protokolu IP na úrovni serveru

  Tato pravidla umožňují klientům přístup k celému serveru SQL Azure, to znamená všem databázím v rámci stejného serveru SQL Database. Pravidla jsou uložena v *Hlavní* databázi. Pro SQL Server Azure můžete mít maximálně 128 pravidel brány firewall protokolu IP na úrovni serveru.
  
  Pravidla brány firewall protokolu IP na úrovni serveru můžete nakonfigurovat pomocí příkazů Azure Portal, PowerShellu nebo jazyka Transact-SQL.
  - Pokud chcete používat portál nebo PowerShell, musíte být vlastníkem předplatného nebo přispěvatelem předplatného.
  - Chcete-li použít jazyk Transact-SQL, je nutné se připojit k instanci SQL Database jako přihlašovací jméno hlavního objektu na úrovni serveru nebo jako správce Azure Active Directory. (Pravidlo brány firewall protokolu IP na úrovni serveru musí být nejdřív vytvořené uživatelem, který má oprávnění na úrovni Azure.)

### <a name="database-level-ip-firewall-rules"></a>Pravidla brány firewall protokolu IP na úrovni databáze

  Tato pravidla umožňují klientům přístup k určitým (zabezpečeným) databázím v rámci stejného serveru SQL Database. Pravidla můžete vytvořit pro každou databázi (včetně *Hlavní* databáze) a ukládají se do jednotlivých databází.
  
  Pravidla brány firewall na úrovni databáze pro hlavní a uživatelské databáze můžete vytvářet a spravovat pouze pomocí příkazů jazyka Transact-SQL a až po nakonfigurování první brány firewall na úrovni serveru.
  
  Pokud zadáte rozsah IP adres do pravidla brány firewall protokolu IP na úrovni databáze, které je mimo rozsah v pravidle brány firewall protokolu IP na úrovni serveru, budou mít přístup k databázi jenom klienti, kteří mají IP adresy v rozsahu na úrovni databáze.
  
  Pro databázi můžete mít maximálně 128 pravidel brány firewall protokolu IP na úrovni databáze. Další informace o konfiguraci pravidel brány firewall na úrovni databáze najdete v příkladu dále v tomto článku a v tématu [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Doporučení pro nastavení pravidel brány firewall

Pokud je to možné, doporučujeme použít pravidla brány firewall na úrovni databáze. Tento postup zvyšuje zabezpečení a zajišťuje větší přenositelnost databáze. Pro správce použijte pravidla brány firewall IP na úrovni serveru. Můžete je také použít, pokud máte mnoho databází se stejnými požadavky na přístup a nechcete konfigurovat jednotlivé databáze zvlášť.

> [!NOTE]
> Informace o přenosných databázích v kontextu kontinuity podnikových procesů najdete v tématu [Požadavky na ověřování pro zotavení po havárii](sql-database-geo-replication-security-config.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Pravidla brány firewall protokolu IP na úrovni serveru i databáze

*Mají být uživatelé jedné databáze plně izolované z jiné databáze?*

Pokud *Ano*, použijte pravidla brány firewall IP na úrovni databáze pro udělení přístupu. Tato metoda zabraňuje použití pravidel brány firewall na úrovni serveru, která umožňují přístup přes bránu firewall do všech databází. To by snížilo hloubku obrany.

*Potřebují uživatelé na IP adresách přístup ke všem databázím?*

Pokud *Ano*, použijte pravidla brány firewall na úrovni serveru k omezení počtu pokusů o konfiguraci pravidel brány firewall protokolu IP.

*Má osoba nebo tým, který konfiguruje pravidla brány firewall protokolu IP, přístup jenom prostřednictvím Azure Portal, PowerShellu nebo REST API?*

Pokud ano, musíte použít pravidla brány firewall IP na úrovni serveru. Pravidla brány firewall protokolu IP na úrovni databáze lze konfigurovat pouze pomocí jazyka Transact-SQL.  

*Má osoba nebo tým, který konfiguruje pravidla brány firewall protokolu IP, zakázaná mít oprávnění vysoké úrovně na úrovni databáze?*

Pokud ano, použijte pravidla brány firewall IP na úrovni serveru. Abyste mohli konfigurovat pravidla brány firewall protokolu IP na úrovni databáze prostřednictvím jazyka Transact-SQL, potřebujete aspoň oprávnění *řídicí databáze* na úrovni databáze.  

*Spravuje osoba nebo tým, který konfiguruje nebo Audituje pravidla brány firewall protokolu IP, centrálně spravují pravidla brány firewall protokolu IP pro mnoho (možná stovky) databází?*

V tomto scénáři jsou osvědčené postupy určeny podle vašich potřeb a prostředí. Pravidla brány firewall protokolu IP na úrovni serveru můžou být snáze nakonfigurovaná, ale skriptování může konfigurovat pravidla na úrovni databáze. A i když používáte pravidla brány firewall na úrovni serveru, možná budete muset auditovat pravidla brány firewall protokolu IP na úrovni databáze a zjistit, jestli uživatelé s oprávněním k *řízení* databáze vytvořit pravidla brány firewall IP na úrovni databáze.

*Můžu používat kombinaci pravidel brány firewall na úrovni serveru i databáze?*

Ano. Někteří uživatelé, třeba správci, můžou potřebovat pravidla brány firewall IP na úrovni serveru. Jiní uživatelé, například uživatelé databázové aplikace, můžou potřebovat pravidla brány firewall IP na úrovni databáze.

### <a name="connections-from-the-internet"></a>Připojení z Internetu

Když se počítač pokusí připojit k databázovému serveru z Internetu, brána firewall nejprve zkontroluje původní IP adresu žádosti o pravidla brány firewall protokolu IP na úrovni databáze pro databázi, kterou připojení požaduje.

- Pokud se adresa nachází v rozsahu zadaném v pravidlech brány firewall na úrovni databáze, připojení se udělí službě SQL Database, která pravidlo obsahuje.
- Pokud adresa není v rozsahu v pravidlech brány firewall protokolu IP na úrovni databáze, brána firewall zkontroluje pravidla brány firewall protokolu IP na úrovni serveru. Pokud je adresa v rozsahu, ve kterém jsou pravidla brány firewall IP na úrovni serveru, připojení se udělí. Pravidla brány firewall protokolu IP na úrovni serveru se vztahují na všechny databáze SQL na serveru SQL Azure.  
- Pokud adresa není v rozsahu, který je v žádném z pravidel brány firewall na úrovni databáze nebo na úrovni serveru, požadavek na připojení se nezdařil.

> [!NOTE]
> Pokud chcete získat přístup k SQL Database z místního počítače, zajistěte, aby brána firewall v síti a místní počítač povolovala odchozí komunikaci na portu TCP 1433.

### <a name="connections-from-inside-azure"></a>Připojení z Azure

Pokud chcete aplikacím hostovaným v Azure povolit připojení k vašemu SQL serveru, musí být povolená připojení Azure. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, jestli jsou povolená připojení Azure. Nastavení brány firewall, které má počáteční a koncovou IP adresu rovnou hodnotě *0.0.0.0* , znamená, že jsou povolená připojení Azure. Pokud se připojení nepovoluje, požadavek nebude mít přístup k serveru SQL Database.

> [!IMPORTANT]
> Tato možnost nakonfiguruje bránu firewall tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných jiných zákazníků. Pokud vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup jenom na autorizované uživatele.

## <a name="create-and-manage-ip-firewall-rules"></a>Vytvoření a Správa pravidel brány firewall protokolu IP

První nastavení brány firewall na úrovni serveru můžete vytvořit pomocí [Azure Portal](https://portal.azure.com/) nebo programově pomocí [Azure POWERSHELL](https://docs.microsoft.com/powershell/module/az.sql), [Azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)nebo Azure [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Můžete vytvářet a spravovat další pravidla brány firewall protokolu IP na úrovni serveru pomocí těchto metod nebo jazyka Transact-SQL.

> [!IMPORTANT]
> Pravidla brány firewall protokolu IP na úrovni databáze lze vytvořit a spravovat pouze pomocí jazyka Transact-SQL.

Pro zvýšení výkonu se pravidla brány firewall IP na úrovni serveru dočasně ukládají do mezipaměti na úrovni databáze. Pokud chcete mezipaměť aktualizovat, podívejte se na příkaz [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Pomocí [auditování SQL Database](sql-database-auditing.md) můžete auditovat změny brány firewall na úrovni serveru a databáze.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Použití Azure Portal ke správě pravidel brány firewall protokolu IP na úrovni serveru

Pokud chcete v Azure Portal nastavit pravidlo brány firewall na úrovni serveru, přejdete na stránku Přehled pro vaši databázi SQL Azure nebo server SQL Database.

> [!TIP]
> Kurz najdete v tématu [Vytvoření databáze pomocí Azure Portal](sql-database-single-database-get-started.md).

#### <a name="from-the-database-overview-page"></a>Na stránce Přehled databáze

1. Pokud chcete na stránce Přehled databáze nastavit pravidlo brány firewall protokolu IP na úrovni serveru, vyberte na panelu nástrojů možnost **nastavit bránu firewall serveru** , jak ukazuje následující obrázek. 

    ![Pravidlo brány firewall protokolu IP serveru](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database.

2. Na panelu nástrojů vyberte **Přidat IP adresu klienta** , abyste mohli přidat IP adresu počítače, který používáte, a pak vyberte **Uložit**. Vytvoří se pravidlo brány firewall protokolu IP na úrovni serveru pro vaši aktuální IP adresu.

    ![Nastavení pravidla brány firewall protokolu IP na úrovni serveru](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Na stránce Přehled serveru

Otevře se stránka s přehledem pro váš server. Zobrazuje plně kvalifikovaný název serveru (například *mynewserver20170403.Database.Windows.NET*) a nabízí možnosti pro další konfiguraci.

1. Chcete-li nastavit pravidlo na úrovni serveru z této stránky, vyberte možnost **Brána firewall** v nabídce **Nastavení** na levé straně.

2. Na panelu nástrojů vyberte **Přidat IP adresu klienta** , abyste mohli přidat IP adresu počítače, který používáte, a pak vyberte **Uložit**. Vytvoří se pravidlo brány firewall protokolu IP na úrovni serveru pro vaši aktuální IP adresu.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Správa pravidel brány firewall protokolu IP pomocí jazyka Transact-SQL

| Zobrazení katalogu nebo uložená procedura | Úroveň | Popis |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Zobrazí aktuální pravidla brány firewall protokolu IP na úrovni serveru. |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Vytvoří nebo aktualizuje pravidla brány firewall protokolu IP na úrovni serveru. |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Odebere pravidla brány firewall protokolu IP na úrovni serveru. |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Databáze |Zobrazí aktuální pravidla brány firewall protokolu IP na úrovni databáze. |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Databáze |Vytvoří nebo aktualizuje pravidla brány firewall protokolu IP na úrovni databáze. |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databáze |Odebere pravidla brány firewall protokolu IP na úrovni databáze. |

Následující příklad zkontroluje stávající pravidla, povolí rozsah IP adres na serveru *Contoso*a odstraní pravidlo brány firewall protokolu IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Pak přidejte pravidlo brány firewall protokolu IP na úrovni serveru.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Pokud chcete odstranit pravidlo brány firewall protokolu IP na úrovni serveru, spusťte uloženou proceduru *sp_delete_firewall_rule* . Následující příklad odstraní pravidlo *ContosoFirewallRule*:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Správa pravidel brány firewall protokolu IP na úrovni serveru pomocí PowerShellu 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale pro modul AZ. SQL je teď k dispozici veškerý vývoj. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty příkazů v modulech AZ a AzureRm jsou v podstatě identické.

| Rutina | Úroveň | Popis |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Vrátí aktuální pravidla brány firewall na úrovni serveru. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Vytvoří nové pravidlo brány firewall na úrovni serveru |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Aktualizuje vlastnosti existujícího pravidla brány firewall na úrovni serveru. |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Odebere pravidla brány firewall na úrovni serveru. |

Následující příklad používá PowerShell k nastavení pravidla brány firewall protokolu IP na úrovni serveru:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> Pro $servername zadejte název serveru, a ne plně kvalifikovaný název DNS, třeba zadat **mysqldbserver** místo **mysqldbserver.Database.Windows.NET** .

> [!TIP]
> Příklady prostředí PowerShell v souvislosti s rychlým startem najdete v tématu [Vytvoření databáze – PowerShell](sql-database-powershell-samples.md) a [Vytvoření izolované databáze a konfigurace pravidla brány firewall IP SQL Database na úrovni serveru pomocí PowerShellu](scripts/sql-database-create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Použití rozhraní příkazového řádku ke správě pravidel brány firewall protokolu IP na úrovni serveru

| Rutina | Úroveň | Popis |
| --- | --- | --- |
|[AZ SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Vytvoří pravidlo brány firewall protokolu IP serveru.|
|[AZ SQL Server Firewall-Rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Vypíše pravidla brány firewall protokolu IP na serveru.|
|[AZ SQL Server Firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Zobrazuje podrobnosti pravidla brány firewall protokolu IP.|
|[AZ SQL Server Firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Aktualizuje pravidlo brány firewall protokolu IP.|
|[AZ SQL Server Firewall-Rule DELETE](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Odstraní pravidlo brány firewall protokolu IP.|

V následujícím příkladu se k nastavení pravidla brány firewall protokolu IP na úrovni serveru používá rozhraní příkazového řádku:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> Pro $servername zadejte název serveru, a ne plně kvalifikovaný název DNS, třeba zadat **mysqldbserver** místo **mysqldbserver.Database.Windows.NET** .

> [!TIP]
> Příklad rozhraní příkazového řádku v souvislosti s rychlým startem najdete v tématu [Vytvoření databáze – Azure CLI](sql-database-cli-samples.md) a [Vytvoření izolované databáze a konfigurace pravidla brány firewall SQL Database IP pomocí Azure CLI](scripts/sql-database-create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Použití REST API ke správě pravidel brány firewall protokolu IP na úrovni serveru

| API | Úroveň | Popis |
| --- | --- | --- |
| [Vypsat pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Server |Zobrazí aktuální pravidla brány firewall protokolu IP na úrovni serveru. |
| [Vytvořit nebo aktualizovat pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Server |Vytvoří nebo aktualizuje pravidla brány firewall protokolu IP na úrovni serveru. |
| [Odstranit pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Server |Odebere pravidla brány firewall protokolu IP na úrovni serveru. |
| [Získat pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Server | Načte pravidla brány firewall IP na úrovni serveru. |

## <a name="troubleshoot-the-database-firewall"></a>Řešení potíží s bránou firewall databáze

Pokud se přístup ke službě SQL Database nechová podle očekávání, vezměte v úvahu následující body.

- **Konfigurace místní brány firewall:**

  Předtím, než počítač bude moci získat přístup k SQL Database, může být nutné vytvořit v počítači výjimku brány firewall pro port TCP 1433. Aby bylo možné připojit se k hranici cloudu Azure, možná budete muset otevřít další porty. Další informace najdete v části "SQL Database: mimo rámec vs Inside" v tématu [porty nad 1433 pro ADO.NET 4,5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Překlad síťových adres:**

  Kvůli překladu adres (NAT) se IP adresa, kterou váš počítač používá pro připojení k SQL Database, může lišit od IP adresy v nastavení konfigurace protokolu IP počítače. Zobrazení IP adresy, kterou váš počítač používá pro připojení k Azure:
    1. Přihlaste se k portálu.
    1. Na serveru, který je hostitelem vaší databáze, navštivte kartu **Konfigurace** .
    1. V části **povolené IP adresy** se zobrazí **aktuální IP adresa klienta** . Pokud chcete povolit tomuto počítači přístup k serveru, vyberte **Přidat** pro **povolené IP adresy** .

- **Změny v seznamu povolených se zatím neprojevily:**

  Může trvat až pět minut, než se změny konfigurace SQL Database brány firewall projeví.

- **Přihlašovací jméno není autorizováno nebo bylo použito nesprávné heslo:**

  Pokud přihlášení nemá oprávnění k serveru SQL Database nebo není správné heslo, připojení k serveru je odepřené. Vytvořením nastavení brány *firewall budou klienti* moct zkusit se připojit k vašemu serveru. Klient musí stále zadat potřebná zabezpečovací pověření. Další informace o přípravě přihlášení najdete v tématu [řízení a udělení přístupu k databázi SQL Database a SQL Data Warehouse](sql-database-manage-logins.md).

- **Dynamická IP adresa:**

  Pokud máte připojení k Internetu, které používá dynamické přidělování IP adres a máte potíže s připojením přes bránu firewall, zkuste použít jedno z následujících řešení:
  
  - Zeptejte se poskytovatele internetových služeb na rozsah IP adres, který je přiřazený k klientským počítačům, které přistupují k serveru SQL Database. Přidejte tento rozsah IP adres jako pravidlo brány firewall protokolu IP.
  - Místo toho Získejte místo klientských počítačů přidělování statických IP adres. Přidejte IP adresy jako pravidla brány firewall protokolu IP.

## <a name="next-steps"></a>Další kroky

- Ověřte, že vaše podnikové síťové prostředí umožňuje příchozí komunikaci z rozsahů IP adres služby COMPUTE (včetně rozsahů SQL) používaných datacentry Azure. Tyto IP adresy možná budete muset přidat do seznamu povolených adres. Viz [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653).  
- Rychlý Start o vytvoření pravidla brány firewall IP na úrovni serveru najdete v tématu [Vytvoření databáze SQL Azure](sql-database-single-database-get-started.md).
- Nápovědu k připojení k databázi SQL Azure z open source aplikací nebo aplikací třetích stran najdete v tématu [ukázky kódu pro rychlý Start klienta SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Informace o dalších portech, které možná budete muset otevřít, najdete v části "SQL Database: mimo rámec vs Inside" na [portech, které přesahují 1433 pro ADO.NET 4,5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- Přehled zabezpečení Azure SQL Database najdete v tématu [zabezpečení databáze](sql-database-security-overview.md).

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
