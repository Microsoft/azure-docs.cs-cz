---
title: Pravidla brány firewall IP
description: Konfigurace pravidel brány firewall IP na úrovni serveru pro databázi SQL nebo bránu firewall datového skladu SQL. Spravujte pravidla brány firewall IP na úrovni databáze a nakonfigurujte je pro jednu nebo sdruženou databázi.
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
ms.date: 03/18/2019
ms.openlocfilehash: 12280e8a5b90c6712703fefc60ec1bfb12ba8573
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606097"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Pravidla brány firewall IP azure SQL Database a Azure SQL Data Warehouse

> [!NOTE]
> Tento článek se vztahuje na servery Azure SQL a pro databáze Azure SQL Database a Azure SQL Data Warehouse na serveru Azure SQL. Pro jednoduchost *sql databáze* se používá k odkazovat na sql databáze a SQL datový sklad.

> [!IMPORTANT]
> Tento článek se *nevztahuje* na *azure SQL database spravované instance*. Informace o konfiguraci sítě najdete v [tématu Připojení aplikace ke spravované instanci Azure SQL Database .](sql-database-managed-instance-connect-app.md)

Když vytvoříte nový server Azure SQL s názvem *mysqlserver*, například brána firewall databáze SQL blokuje veškerý přístup k veřejnému koncovému bodu pro server (který je přístupný na *mysqlserver.database.windows.net*).

> [!IMPORTANT]
> Datový sklad SQL podporuje pouze pravidla brány firewall IP na úrovni serveru. Nepodporuje pravidla brány firewall IP na úrovni databáze.

## <a name="how-the-firewall-works"></a>Jak brána firewall funguje
Pokusy o připojení z Internetu a Azure musí projít bránou firewall, než se dostanou k serveru SQL nebo databázi SQL, jak ukazuje následující diagram.

   ![Konfigurační diagram brány firewall][1]

### <a name="server-level-ip-firewall-rules"></a>Pravidla firewallu protokolu IP na úrovni serveru

  Tato pravidla umožňují klientům přístup k celému serveru Azure SQL, to znamená, že všechny databáze v rámci stejného serveru SQL Database. Pravidla jsou uložena v *hlavní* databázi. Pro Azure SQL Server můžete mít maximálně 128 pravidel brány firewall IP na úrovni serveru. Pokud máte povoleno **povolit služby Azure a prostředky pro přístup k tomuto** nastavení serveru, počítá se to jako jediné pravidlo brány firewall pro Azure SQL Server.
  
  Pravidla brány IP na úrovni serveru můžete nakonfigurovat pomocí příkazů Azure Portal, PowerShell nebo Transact-SQL.
  - Chcete-li použít portál nebo PowerShell, musíte být vlastníkem předplatného nebo přispěvatelem předplatného.
  - Chcete-li použít Transact-SQL, musíte se připojit k instanci DATABÁZE SQL jako hlavní přihlášení na úrovni serveru nebo jako správce služby Azure Active Directory. (Pravidlo brány firewall IP na úrovni serveru musí nejprve vytvořit uživatel, který má oprávnění na úrovni Azure.)

### <a name="database-level-ip-firewall-rules"></a>Pravidla brány firewall IP na úrovni databáze

  Tato pravidla umožňují klientům přístup k určité (zabezpečené) databáze v rámci stejného serveru SQL Database. Vytvoříte pravidla pro každou databázi (včetně *hlavní* databáze) a jsou uložena v jednotlivých databázích.
  
  Pravidla brány firewall IP na úrovni databáze lze vytvářet a spravovat pouze pro hlavní a uživatelské databáze pomocí příkazů Transact-SQL a pouze po konfiguraci první brány firewall na úrovni serveru.
  
  Pokud zadáte rozsah IP adres v pravidle brány firewall IP na úrovni databáze, které je mimo rozsah v pravidle brány firewall IP na úrovni serveru, budou mít k databázi přístup pouze klienti, kteří mají adresy IP v rozsahu na úrovni databáze.
  
  Pro databázi můžete mít maximálně 128 pravidel brány firewall IP na úrovni databáze. Další informace o konfiguraci pravidel brány firewall IP na úrovni databáze naleznete v příkladu dále v tomto článku a [najdete sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Doporučení pro nastavení pravidel brány firewall

Doporučujeme používat pravidla brány firewall IP na úrovni databáze, kdykoli je to možné. Tento postup zvyšuje zabezpečení a umožňuje databázi přenosnější. Pro správce použijte pravidla brány firewall IP na úrovni serveru. Používejte je také v případě, že máte mnoho databází, které mají stejné požadavky na přístup a nechcete konfigurovat každou databázi jednotlivě.

> [!NOTE]
> Informace o přenosných databázích v kontextu kontinuity podnikových procesů najdete v tématu [Požadavky na ověřování pro zotavení po havárii](sql-database-geo-replication-security-config.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Pravidla brány firewall IP na úrovni serveru versus databáze

*Měli by být uživatelé jedné databáze plně izolováni od jiné databáze?*

Pokud *ano*, použijte k udělení přístupu pravidla brány firewall IP na úrovni databáze. Tato metoda zabraňuje použití pravidel brány firewall IP na úrovni serveru, která umožňují přístup přes bránu firewall do všech databází. To by snížilo hloubku vaší obrany.

*Potřebují uživatelé na IP adresách přístup ke všem databázím?*

Pokud *ano*, použijte pravidla brány firewall IP na úrovni serveru ke snížení počtu, kolikrát je třeba nakonfigurovat pravidla brány firewall IP.

*Má osoba nebo tým, který konfiguruje pravidla brány firewall IP, přístup jenom prostřednictvím portálu Azure, Prostředí PowerShell nebo rozhraní REST API?*

Pokud ano, musíte použít pravidla brány firewall IP na úrovni serveru. Pravidla brány firewall IP na úrovni databáze lze konfigurovat pouze prostřednictvím transact-SQL.  

*Je osoba nebo tým, který konfiguruje pravidla brány firewall IP, zakázáno mít oprávnění vysoké úrovně na úrovni databáze?*

Pokud ano, použijte pravidla brány firewall IP na úrovni serveru. Ke *konfiguraci* pravidel brány firewall IP na úrovni databáze prostřednictvím aplikace Transact-SQL potřebujete alespoň oprávnění databáze databáze.  

*Má osoba nebo tým, který konfiguruje nebo audituje pravidla brány firewall IP centrálně spravovat pravidla brány firewall IP pro mnoho (možná stovky) databází?*

V tomto scénáři osvědčené postupy jsou určeny vaše potřeby a prostředí. Konfigurace pravidel brány firewall IP na úrovni serveru může být jednodušší, ale skriptování může nakonfigurovat pravidla na úrovni databáze. A i když používáte pravidla brány firewall IP na úrovni serveru, možná budete muset auditovat pravidla brány firewall IP na úrovni databáze, abyste zjistili, zda uživatelé s oprávněním *CONTROL* v databázi vytvářejí pravidla brány firewall IP na úrovni databáze.

*Mohu použít kombinaci pravidel brány firewall IP na úrovni serveru a databáze?*

Ano. Někteří uživatelé, například správci, mohou potřebovat pravidla brány firewall IP na úrovni serveru. Ostatní uživatelé, například uživatelé databázové aplikace, mohou potřebovat pravidla brány firewall IP na úrovni databáze.

### <a name="connections-from-the-internet"></a>Připojení z internetu

Pokud se počítač pokusí připojit k databázovému serveru z Internetu, brána firewall nejprve zkontroluje původní adresu IP požadavku podle pravidel brány firewall IP na úrovni databáze pro databázi, kterou připojení požaduje.

- Pokud je adresa v rozsahu, který je zadán v pravidlech brány firewall IP na úrovni databáze, je připojení uděleno databázi SQL, která obsahuje pravidlo.
- Pokud adresa není v rámci pravidel brány firewall IP na úrovni databáze v rozsahu, brána firewall zkontroluje pravidla brány firewall IP na úrovni serveru. Pokud je adresa v rozsahu, který je v pravidlech brány firewall IP na úrovni serveru, je připojení uděleno. Pravidla brány firewall IP na úrovni serveru platí pro všechny databáze SQL na serveru Azure SQL.  
- Pokud adresa není v rozsahu, který je v žádném z pravidel brány firewall ip na úrovni databáze nebo serveru, požadavek na připojení se nezdaří.

> [!NOTE]
> Chcete-li získat přístup k databázi SQL z místního počítače, ujistěte se, že brána firewall v síti a místní počítač umožňují odchozí komunikaci na portu TCP 1433.

### <a name="connections-from-inside-azure"></a>Připojení zevnitř Azure

Aby se aplikace hostované v Azure mohly připojit k serveru SQL, musí být povolená připojení Azure. Když se aplikace z Azure pokusí připojit k databázovému serveru, brána firewall ověří, že jsou povolena připojení Azure. To lze zapnout přímo z okna Portálu Azure nastavením pravidel brány firewall, stejně jako přepnutím **povolit služby Azure a prostředky pro přístup k tomuto serveru** **na ON** v nastavení brány firewall a **virtuální sítě.** Pokud připojení není povoleno, požadavek nedosáhne serveru SQL Database.

> [!IMPORTANT]
> Tato možnost nakonfiguruje bránu firewall tak, aby umožňovala všechna připojení z Azure, včetně připojení z předplatných jiných zákazníků. Pokud vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze oprávněným uživatelům.

## <a name="create-and-manage-ip-firewall-rules"></a>Vytvoření a správa pravidel brány firewall IP

První nastavení brány firewall na úrovni serveru vytvoříte pomocí [portálu Azure](https://portal.azure.com/) nebo programově pomocí [Azure PowerShellu](https://docs.microsoft.com/powershell/module/az.sql), [rozhraní API Azure](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)nebo rozhraní API Azure [REST](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Pomocí těchto metod nebo Transact-SQL vytvoříte a spravujete další pravidla brány firewall IP na úrovni serveru.

> [!IMPORTANT]
> Pravidla brány firewall IP na úrovni databáze lze vytvářet a spravovat pouze pomocí transact-SQL.

Pro zvýšení výkonu jsou pravidla brány firewall IP na úrovni serveru dočasně uložena do mezipaměti na úrovni databáze. Pokud chcete mezipaměť aktualizovat, podívejte se na příkaz [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> [Auditování databáze SQL](sql-database-auditing.md) můžete použít k auditování změn brány firewall na úrovni serveru a databáze.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Správa pravidel brány firewall IP na úrovni serveru pomocí portálu Azure Portal

Pokud chcete nastavit pravidlo brány FIREWALL na úrovni serveru na webu Azure Portal, přejděte na stránku s přehledem pro databázi Azure SQL nebo server SQL Database.

> [!TIP]
> Kurz najdete v [tématu Vytvoření databáze pomocí portálu Azure](sql-database-single-database-get-started.md).

#### <a name="from-the-database-overview-page"></a>Na stránce přehledu databáze

1. Chcete-li nastavit pravidlo brány firewall IP na úrovni serveru na stránce přehled databáze, vyberte na panelu nástrojů možnost **Nastavit bránu firewall serveru,** jak ukazuje následující obrázek. 

    ![Pravidlo brány firewall IP serveru](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database.

2. Vyberte **Přidat IP adresu klienta** na panelu nástrojů, chcete-li přidat IP adresu počítače, který používáte, a pak vyberte **Uložit**. Pro aktuální adresu IP je vytvořeno pravidlo brány firewall IP na úrovni serveru.

    ![Nastavení pravidla brány firewall IP na úrovni serveru](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Na stránce přehled serveru

Otevře se stránka s přehledem serveru. Zobrazuje plně kvalifikovaný název serveru (například *mynewserver20170403.database.windows.net*) a poskytuje možnosti pro další konfiguraci.

1. Chcete-li nastavit pravidlo na úrovni serveru z této stránky, vyberte **brána firewall** z nabídky **Nastavení** na levé straně.

2. Vyberte **Přidat IP adresu klienta** na panelu nástrojů, chcete-li přidat IP adresu počítače, který používáte, a pak vyberte **Uložit**. Pro aktuální adresu IP je vytvořeno pravidlo brány firewall IP na úrovni serveru.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Správa pravidel brány firewall IP pomocí transact-SQL

| Zobrazení katalogu nebo uložená procedura | Úroveň | Popis |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Zobrazí aktuální pravidla brány firewall IP na úrovni serveru. |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Vytvoří nebo aktualizuje pravidla brány firewall IP na úrovni serveru. |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Odebere pravidla brány firewall IP na úrovni serveru. |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |databáze |Zobrazí aktuální pravidla brány firewall IP na úrovni databáze. |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |databáze |Vytvoří nebo aktualizuje pravidla brány firewall IP na úrovni databáze. |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databáze |Odebere pravidla brány firewall IP na úrovni databáze. |

Následující příklad zkontroluje existující pravidla, povolí rozsah ADRES IP na serveru *Contoso*a odstraní pravidlo brány firewall IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Dále přidejte pravidlo brány firewall IP na úrovni serveru.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Chcete-li odstranit pravidlo brány firewall IP na úrovni serveru, proveďte *sp_delete_firewall_rule* uloženou proceduru. Následující příklad odstraní pravidlo *ContosoFirewallRule*:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Správa pravidel brány firewall IP na úrovni serveru pomocí prostředí PowerShell 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý vývoj je teď pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech Az a AzureRm jsou v podstatě identické.

| Rutina | Úroveň | Popis |
| --- | --- | --- |
| [Pravidlo brány Get-AzSqlServerFirewallFirewall](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Vrátí aktuální pravidla brány firewall na úrovni serveru. |
| [Nové pravidlo azsqlserverfirewallfirewall](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Vytvoří nové pravidlo brány firewall na úrovni serveru |
| [Pravidlo programu Set-AzSqlServerFirewallFirewall](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Aktualizuje vlastnosti existujícího pravidla brány firewall na úrovni serveru. |
| [Odebrat-AzSqlServerFirewallFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Odebere pravidla brány firewall na úrovni serveru. |

Následující příklad používá prostředí PowerShell k nastavení pravidla brány firewall IP na úrovni serveru:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> Pro $servername zadejte název serveru a ne plně kvalifikovaný název DNS, například zadejte **mysqldbserver** namísto **mysqldbserver.database.windows.net**

> [!TIP]
> Příklady prostředí PowerShell v kontextu rychlého startu najdete v tématu [Vytvoření DB - PowerShell](sql-database-powershell-samples.md) a [Vytvoření jedné databáze a konfigurace pravidla brány firewall na úrovni serveru SQL Database server pomocí prostředí PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Použití cli ke správě pravidel brány firewall IP na úrovni serveru

| Rutina | Úroveň | Popis |
| --- | --- | --- |
|[Vytvoření pravidla brány firewall serveru az SQL Server](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Vytvoří pravidlo brány firewall IP serveru.|
|[seznam pravidel brány firewall serveru az SQL Server](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Zobrazí seznam pravidel brány firewall protokolu IP na serveru.|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Zobrazuje podrobnosti pravidla brány firewall IP.|
|[Aktualizace pravidla brány firewall serveru az SQL Server](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Aktualizuje pravidlo brány firewall IP.|
|[az sql server firewall-rule odstranit](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Odstraní pravidlo brány firewall PROTOKOLU IP.|

Následující příklad používá příkazcli k nastavení pravidla brány firewall IP na úrovni serveru:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> Pro $servername zadejte název serveru a ne plně kvalifikovaný název DNS, například zadejte **mysqldbserver** namísto **mysqldbserver.database.windows.net**

> [!TIP]
> Příklad rozhraní příkazového řádku v kontextu rychlého startu najdete v tématu [Vytvoření DB – Azure CLI](sql-database-cli-samples.md) a Vytvoření jedné databáze a konfigurace pravidla brány firewall IP databáze SQL pomocí rozhraní [příkazového řádku Azure](scripts/sql-database-create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Správa pravidel brány firewall IP na úrovni serveru

| rozhraní API | Úroveň | Popis |
| --- | --- | --- |
| [Seznam pravidel brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Server |Zobrazí aktuální pravidla brány firewall IP na úrovni serveru. |
| [Vytvoření nebo aktualizace pravidel brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Server |Vytvoří nebo aktualizuje pravidla brány firewall IP na úrovni serveru. |
| [Odstranit pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Server |Odebere pravidla brány firewall IP na úrovni serveru. |
| [Získání pravidel brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Server | Získá pravidla brány firewall IP na úrovni serveru. |

## <a name="troubleshoot-the-database-firewall"></a>Poradce při potížích s bránou firewall databáze

Zvažte následující body při přístupu ke službě SQL Database se nechová tak, jak očekáváte.

- **Konfigurace místní brány firewall:**

  Před přístupem k databázi SQL bude pravděpodobně nutné vytvořit v počítači výjimku brány firewall pro port TCP 1433. Chcete-li vytvořit připojení uvnitř hranice cloudu Azure, budete muset otevřít další porty. Další informace naleznete v části "SQL Database: Outside vs inside" [porty nad 1433 pro ADO.NET 4.5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Překlad síťových adres:**

  Z důvodu překladu síťových adres (NAT) se ip adresa používaná počítačem pro připojení k databázi SQL může lišit od adresy IP v nastavení konfigurace IP počítače. Zobrazení IP adresy, kterou váš počítač používá pro připojení k Azure:
    1. Přihlaste se k portálu.
    1. Přejděte na kartu **Konfigurovat** na serveru, který je hostitelem databáze.
    1. **Aktuální adresa IP klienta** se zobrazí v části Povolené adresy **IP.** Chcete-li tomuto počítači povolit přístup k serveru, vyberte **možnost Přidat** **pro povolené adresy IP.**

- **Změny seznamu povolených položek se dosud neprojevily:**

  Může být až pět minut zpoždění pro změny konfigurace brány firewall databáze SQL se projeví.

- **Přihlášení není autorizováno nebo bylo použito nesprávné heslo:**

  Pokud přihlášení nemá oprávnění na serveru SQL Database nebo heslo je nesprávné, připojení k serveru je odepřeno. Vytvoření nastavení brány firewall pouze umožňuje *klientům* pokusit se připojit k serveru. Klient musí stále poskytnout potřebná pověření zabezpečení. Další informace o přípravě přihlášení naleznete v [tématu Řízení a udělení přístupu k databázi sql database a SQL Data Warehouse](sql-database-manage-logins.md).

- **Dynamická IP adresa:**

  Pokud máte připojení k internetu, které používá dynamické adresování IP adres a máte potíže dostat se přes bránu firewall, vyzkoušejte jedno z následujících řešení:
  
  - Požádejte svého poskytovatele služeb Internetu o rozsah IP adres přiřazený klientským počítačům, které přistupují k databázovému serveru SQL. Přidejte tento rozsah IP adres jako pravidlo brány firewall IP.
  - Místo toho získejte statické adresování IP pro klientské počítače. Přidejte adresy IP jako pravidla brány firewall IP.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte, zda vaše prostředí podnikové sítě umožňuje příchozí komunikaci z rozsahu výpočetních IP adres (včetně oblastí SQL), které používají datová centra Azure. Je možné, že budete muset tyto adresy IP přidat do seznamu povolených adres. Podívejte se na [rozsahy IP adres datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).  
- Úvodní příručka o vytvoření pravidla brány firewall IP na úrovni serveru najdete [v tématu Vytvoření databáze Azure SQL](sql-database-single-database-get-started.md).
- Nápovědu k připojení k databázi Azure SQL z aplikací s otevřeným zdrojovým kódem nebo aplikací jiných výrobců najdete v [tématu Ukázky kódu rychlého spuštění klienta do databáze SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Informace o dalších portech, které budete muset otevřít, naleznete v části "SQL Database: Outside vs inside" [porty nad 1433 pro ADO.NET 4.5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- Přehled zabezpečení Azure SQL Database najdete v tématu [Zabezpečení databáze](sql-database-security-overview.md).

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
