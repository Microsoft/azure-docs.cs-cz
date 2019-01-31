---
title: Azure SQL Database a datovým skladem vykazovaných pravidla brány firewall | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat pravidla brány firewall na úrovni serveru pro správu přístupu a konfigurace pravidel brány firewall na úrovni databáze SQL Database SQL database a SQL Data Warehouse brány firewall.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 0579746bc4dc554fd7e082f6258f2c13ce22f69b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477671"
---
# <a name="azure-sql-database-and-sql-data-warehouse-firewall-rules"></a>Azure SQL Database a SQL Data Warehouse pravidla brány firewall

Microsoft Azure [SQL Database](sql-database-technical-overview.md) a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) poskytuje relační databázová služba pro Azure a další internetové aplikace. Brána firewall pomáhá chránit vaše data tím, že brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače k tomu mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.

> [!NOTE]
> Tento článek se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořeny na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.
> [!IMPORTANT]
> Tento článek provádí *není* platí pro **Azure SQL Database Managed Instance**. Podrobnosti najdete v následujícím článku na [připojení k Managed Instance](sql-database-managed-instance-connect-app.md) Další informace o konfiguraci sítě potřeba.

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Pravidla virtuální sítě jako alternativy k pravidla protokolu IP

Kromě IP pravidel brány firewall také spravuje *pravidel virtuální sítě*. Pravidla virtuální sítě jsou založené na koncových bodech služby virtuální sítě. Pravidla virtuální sítě může být vhodnější než pravidla protokolu IP v některých případech. Další informace najdete v tématu [koncové body služeb virtuální sítě a pravidel pro službu Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Přehled

Zpočátku je veškerý přístup Transact-SQL k vašemu serveru SQL Azure blokován branou firewall. Pokud chcete začít používat Azure SQL server, je nutné zadat jedno nebo více pravidel brány firewall na úrovni serveru, které umožňují přístup k vašemu serveru Azure SQL. Pomocí pravidel brány firewall určete, které rozsahy IP adres z internetu jsou povolené a zda se mohou aplikace Azure pokoušet připojovat k vašemu serveru SQL Azure.

Pokud chcete selektivně udělit přístup pouze k jedné z databází na vašem serveru SQL Azure, musíte pro příslušnou databázi vytvořit pravidlo na úrovni databáze. Zadejte pro pravidlo brány firewall databáze rozsah IP adres, který je mimo rozsah IP adres zadaný v pravidlu brány firewall na úrovni serveru, a ujistěte se, že IP adresa klienta spadá do rozsahu zadaného v pravidlu na úrovni databáze.

> [!IMPORTANT]
> SQL Data Warehouse pouze podporuje pravidla brány firewall na úrovni serveru a pravidla brány firewall na úrovni databáze nepodporuje.

Pokusy o připojení z internetu a z Azure musí nejdříve projít přes bránu firewall a až potom mají přístup k vašemu serveru SQL Azure nebo službě SQL Database. Znázorňuje to následující diagram:

   ![Diagram popisující konfiguraci brány firewall.][1]

- **Pravidla brány firewall na úrovni serveru:**

  Tato pravidla umožňují klientům přístup k celému serveru Azure SQL, to znamená, že všem databázím na stejném serveru SQL Database. Tato pravidla se ukládají v **hlavní** databázi. Pravidla brány firewall na úrovni serveru můžete konfigurovat pomocí portálu nebo pomocí příkazů jazyka Transact-SQL. Pokud chcete vytvořit pravidla brány firewall na úrovni serveru pomocí portálu Azure Portal nebo PowerShellu, musíte být vlastníkem nebo přispěvatelem předplatného. Pokud chcete vytvořit pravidlo brány firewall na úrovni serveru pomocí příkazu Transact-SQL, musíte se připojit k instanci služby SQL Database jako přihlášení objektu zabezpečení nebo správce Azure Active Directory (to znamená, že musí být pravidlo brány firewall na úrovni serveru nejdřív vytvořené uživatelem s oprávněními na úrovni Azure).

- **Pravidla brány firewall na úrovni databáze:**

  Tato pravidla umožňují klientům přístup k určitým (zabezpečeným) databázím na stejném serveru SQL Database. Můžete vytvořit tato pravidla pro každou databázi (včetně **hlavní** databáze) a jsou uloženy v jednotlivých databázích. Pravidla brány firewall na úrovni databáze pro hlavní a uživatelské databáze může pouze vytvořit a spravovat pomocí příkazů jazyka Transact-SQL a pouze po nakonfigurování první brány firewall na úrovni serveru. Pokud v pravidlu brány firewall na úrovni databáze zadáte rozsah IP adres, který je mimo rozsah zadaný v pravidlu brány firewall na úrovni serveru, budou mít k dané databázi přístup pouze klienti, jejichž IP adresa je v rozsahu na úrovni databáze. Pro jednu databázi můžete mít maximálně 128 pravidel brány firewall na úrovni databáze. Další informace o konfiguraci pravidel brány firewall na úrovni databáze, podívejte se na příklad dále v tomto článku a v tématu [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Doporučení

Microsoft doporučuje používat pravidla brány firewall na úrovni databáze kdykoli je to možné, pro zvýšení zabezpečení a přenositelnosti databáze. Použijte pravidla brány firewall na úrovni serveru pro správce a pokud máte mnoho databází se stejnými požadavky na přístup a nechcete ztrácet čas konfigurací jednotlivých databází.

> [!IMPORTANT]
> Windows Azure SQL Database podporuje maximálně 128 pravidel brány firewall.
> [!NOTE]
> Informace o přenosných databázích v kontextu kontinuity podnikových procesů najdete v tématu [Požadavky na ověřování pro zotavení po havárii](sql-database-geo-replication-security-config.md).

### <a name="connecting-from-the-internet"></a>Připojení z Internetu

Když se počítač pokusí z internetu připojit k databázovému serveru, brána firewall nejprve u databáze, kterou připojení požaduje, pomocí pravidel brány firewall na úrovni databáze zkontroluje zdrojovou IP adresu požadavku:

- Pokud je IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni databáze, připojení ke službě SQL Database, která pravidlo obsahuje, je povoleno.
- Pokud IP adresa požadavku není v žádném z rozsahů určených v pravidlech brány firewall na úrovni databáze, zkontrolují se pravidla brány firewall na úrovni serveru. Pokud je IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni serveru, připojení je povoleno. Pravidla brány firewall na úrovni serveru se vztahují na všechny databáze SQL na serveru Azure SQL.  
- Pokud IP adresa požadavku není v rámci žádném rozsahů určených v pravidlech brány firewall na úrovni databáze nebo serveru, požadavek na připojení selže.

> [!NOTE]
> Pro přístup ke službě Azure SQL Database z místního počítače se ujistěte, že brána firewall na vaší síti i místní počítač umožňují odchozí komunikaci na portu TCP 1433.

### <a name="connecting-from-azure"></a>Připojení z Azure

Pokud chcete umožnit aplikacím z Azure připojení k vašemu serveru SQL Azure, musí být povolená připojení Azure. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Pokud pokus o připojení není povolený, požadavek se k serveru služby Azure SQL Database vůbec nedostane.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.

## <a name="creating-and-managing-firewall-rules"></a>Vytváření a Správa pravidel brány firewall

První nastavení brány firewall na úrovni serveru můžete vytvořit pomocí [webu Azure portal](https://portal.azure.com/) nebo programově pomocí [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql), [rozhraní příkazového řádku Azure](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create), nebo [ Rozhraní REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Další pravidla brány firewall na úrovni serveru můžete vytvářet a spravovat těmito způsoby nebo prostřednictvím jazyka Transact-SQL.

> [!IMPORTANT]
> Pravidla brány firewall na úrovni databáze lze vytvořit pouze a spravují s použitím příkazů jazyka Transact-SQL.

Pro zvýšení výkonu se pravidla brány firewall na úrovni serveru dočasně ukládají do mezipaměti na úrovni databáze. Pokud chcete mezipaměť aktualizovat, podívejte se na příkaz [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Můžete použít [auditování služby SQL Database](sql-database-auditing.md) auditovat změny brány firewall na úrovni serveru a databáze.

## <a name="manage-firewall-rules-using-the-azure-portal"></a>Správa pravidel brány firewall pomocí webu Azure portal

Pokud chcete nastavit pravidlo brány firewall na úrovni serveru na webu Azure Portal, můžete buď přejít na stránku přehled pro vaši databázi Azure SQL nebo na stránce Přehled pro váš server SQL Database.

> [!TIP]
> Podívejte se kurz [vytvoření databáze pomocí webu Azure portal](sql-database-get-started-portal.md).

### <a name="from-database-overview-page"></a>Na stránce Přehled databáze

1. Chcete-li nastavit pravidlo brány firewall na úrovni serveru na stránce Přehled databáze, klikněte na tlačítko **nastavit bránu firewall serveru** na panelu nástrojů, jak je znázorněno na následujícím obrázku: Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database.

      ![pravidlo brány firewall serveru](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Klikněte na tlačítko **přidat IP adresu klienta** na panelu nástrojů a přidejte IP adresu počítače právě používáte a potom klikněte na **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu.

      ![nastavení pravidla brány firewall serveru](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>Na stránce Přehled serveru

Otevře se stránka s přehledem pro váš server, zobrazí plně kvalifikovaný název serveru (například **mynewserver20170403.database.windows.net**) a poskytne vám možnosti další konfigurace.

1. Na stránce Přehled serveru nastavit serverové pravidlo, klikněte na tlačítko **brány Firewall** v nabídce vlevo v části nastavení:

2. Klikněte na tlačítko **přidat IP adresu klienta** na panelu nástrojů a přidejte IP adresu počítače právě používáte a potom klikněte na **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu.

## <a name="manage-firewall-rules-using-transact-sql"></a>Správa pravidel brány firewall pomocí příkazů jazyka Transact-SQL

| Zobrazení katalogu nebo uložená procedura | Úroveň | Popis |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Zobrazí aktuální pravidla brány firewall na úrovni serveru. |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Vytvoří nebo aktualizuje pravidla brány firewall na úrovni serveru. |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Odebere pravidla brány firewall na úrovni serveru. |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Databáze |Zobrazí aktuální pravidla brány firewall na úrovni databáze. |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Databáze |Vytvoří nebo aktualizuje pravidla brány firewall na úrovni databáze. |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databáze |Odebere pravidla brány firewall na úrovni databáze. |

Následující příklady zkontrolujte existující pravidla povolit rozsah IP adres na serveru Contoso a odstraní pravidlo brány firewall:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Potom přidejte pravidlo brány firewall.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Pokud chcete odstranit pravidlo brány firewall na úrovni serveru, spusťte uloženou proceduru sp_delete_firewall_rule. Následující příklad odstraní pravidlo s názvem ContosoFirewallRule.

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-firewall-rules-using-azure-powershell"></a>Správa pravidel brány firewall pomocí Azure Powershellu

| Rutina | Úroveň | Popis |
| --- | --- | --- |
| [Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule) |Server |Vrátí aktuální pravidla brány firewall na úrovni serveru. |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) |Server |Vytvoří nové pravidlo brány firewall na úrovni serveru |
| [Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule) |Server |Aktualizuje vlastnosti existujícího pravidla brány firewall na úrovni serveru. |
| [Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule) |Server |Odebere pravidla brány firewall na úrovni serveru. |

Následující příklad nastaví pravidlo brány firewall na úrovni serveru pomocí prostředí PowerShell:

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Příklady prostředí PowerShell v rámci rychlý start, najdete v článku [vytvoření databáze – PowerShell](sql-database-powershell-samples.md) a [vytvoření izolované databáze a konfigurace pravidla brány firewall SQL Database pomocí Powershellu](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-firewall-rules-using-azure-cli"></a>Správa pravidel brány firewall pomocí Azure CLI

| Rutina | Úroveň | Popis |
| --- | --- | --- |
|[Vytvoření az sql server firewall-rule](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Vytvoří pravidlo brány firewall serveru|
|[AZ sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Obsahuje seznam pravidel brány firewall na serveru|
|[AZ sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Zobrazí podrobnosti pravidla brány firewall|
|[AZ sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Aktualizuje pravidlo brány firewall|
|[AZ sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Odstraní pravidlo brány firewall|

Následující příklad nastaví pravidlo brány firewall na úrovni serveru pomocí Azure CLI:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Příklad rozhraní příkazového řádku Azure v rámci rychlý start, naleznete v tématu [vytvoření databáze – Azure CLI](sql-database-cli-samples.md) a [vytvoření izolované databáze a konfigurace pravidla brány firewall SQL Database pomocí rozhraní příkazového řádku Azure](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-firewall-rules-using-rest-api"></a>Správa pravidel brány firewall pomocí rozhraní REST API

| Rozhraní API | Úroveň | Popis |
| --- | --- | --- |
| [Výpis pravidel brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Server |Zobrazí aktuální pravidla brány firewall na úrovni serveru. |
| [Vytvoření nebo aktualizace pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Server |Vytvoří nebo aktualizuje pravidla brány firewall na úrovni serveru. |
| [Odstranění pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Server |Odebere pravidla brány firewall na úrovni serveru. |
| [Získání pravidla brány Firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Server | Získá pravidla brány firewall na úrovni serveru |

## <a name="server-level-firewall-rule-versus-a-database-level-firewall-rule"></a>Pravidlo brány firewall na úrovni serveru a pravidla brány firewall na úrovni databáze

Otázka: By měl být uživatelé jednu databázi plně izolované od jiné databáze?
Pokud ano, udělení přístupu pomocí pravidel brány firewall na úrovni databáze. Tím předejdete pomocí pravidel brány firewall na úrovni serveru, které povolují přístup přes bránu firewall, aby všechny databáze, zmenšete hloubku vaši obranu.

Otázka: Potřebují uživatelé na IP adrese přístup ke všem databázím?
Pravidla brány firewall na úrovni serveru můžete snížit počet, kolikrát musí nakonfigurovat pravidla brány firewall.

Otázka: Osobu nebo tým konfiguraci pravidel brány firewall pouze má přístup prostřednictvím webu Azure portal, Powershellu nebo rozhraní REST API?
Je nutné použít pravidla brány firewall na úrovni serveru. Pravidla brány firewall na úrovni databáze lze konfigurovat pouze pomocí příkazů jazyka Transact-SQL.  

Otázka: Je osobu nebo tým konfigurace pravidla brány firewall zpřístupníme s vysoké úrovně oprávnění na úrovni databáze?
Pomocí pravidel brány firewall na úrovni serveru. Konfigurace pravidel brány firewall na úrovni databáze pomocí příkazů jazyka Transact-SQL, vyžaduje alespoň `CONTROL DATABASE` oprávnění na úrovni databáze.  

Otázka: Je osobu nebo tým konfigurace ani audit, pravidla brány firewall, centrálně spravovat pravidla brány firewall pro mnoho (třeba 100s) z databáze?
Tento výběr, závisí na konkrétních potřeb a prostředí. Může být jednodušší konfigurovat pravidla brány firewall na úrovni serveru, ale skriptování můžete nakonfigurovat pravidla na úrovni databáze. A i když používáte pravidla brány firewall na úrovni serveru, může být potřeba auditovat pravidla firewallu databáze, a zjistěte, jestli uživatelé s `CONTROL` oprávnění v databázi vytvořili pravidla brány firewall na úrovni databáze.

Otázka: Můžete použít kombinaci obou pravidla brány firewall na úrovni serveru a databáze?
Ano. Někteří uživatelé, třeba o správce může být nutné pravidla brány firewall na úrovni serveru. Ostatní uživatelé, jako jsou uživatelé databázovou aplikaci, může být nutné pravidla brány firewall na úrovni databáze.

## <a name="troubleshooting-the-database-firewall"></a>Řešení potíží s branou firewall databáze

Pokud se přístup ke službě Microsoft Azure SQL Database nechová podle očekávání, zvažte následující:

- **Konfigurace místní brány firewall:**

  Než počítač přístup k Azure SQL Database, budete muset vytvořit výjimku brány firewall v počítači pro TCP port 1433. Pokud provádíte připojení v rámci cloudu Azure, možná bude nutné otevřít další porty. Další informace najdete v tématu **SQL Database: Vnější vs. vnitřní** část [porty nad 1433 pro ADO.NET 4.5 a službu SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Překlad síťových adres (NAT):**

  Kvůli překladu adres IP adresu, kterou váš počítač používá pro připojení ke službě Azure SQL Database může být jiný než IP adresou uvedenou v nastavení konfigurace IP počítače. Pokud chcete zobrazit IP adresu, kterou váš počítač používá pro připojení k Azure, přihlaste se na portál a přejděte na kartu **Konfigurovat** na serveru, který je hostitelem vaší databáze. V části **Povolené IP adresy** se zobrazí **Aktuální IP adresa klienta**. Kliknutím na **Přidat** mezi **Povolené IP adresy** umožníte tomuto počítači přístup k serveru.

- **Změny do seznamu povolených ještě nevstoupilo v platnost:**

  Může být co nejvíce pět minut, než se změny konfigurace brány firewall Azure SQL Database projeví.

- **Přihlášení není autorizováno nebo bylo nesprávné heslo:**

  Pokud přihlášení nemá oprávnění na serveru Azure SQL Database nebo použít heslo není správné, připojení k serveru Azure SQL Database je odepřen. Vytvoření nastavení brány firewall klientům pouze poskytuje možnost pokusit se o připojení k vašemu serveru – každý klient musí dodat potřebné zabezpečené přihlašovací údaje. Další informace o přípravě přihlášení najdete v tématu [Správa databází, přihlášení a uživatelů ve službě Azure SQL Database](sql-database-manage-logins.md).

- **Dynamická IP adresa:**

  Pokud máte připojení k Internetu dynamické přidělování IP adres a máte problémy dostat se přes bránu firewall, můžete zkusit jedno z následujících řešení:
  
  - Požádejte svého poskytovatele internetových služeb (ISP) o rozsah IP adres přidělený vašim klientským počítačům, které přistupují k serveru služby Azure SQL Database, a následně přidejte tento rozsah IP adres jako pravidlo brány firewall.
  - Získejte pro své klientské počítače statické přidělování IP adres a následně přidejte tyto IP adresy jako pravidla brány firewall.

## <a name="next-steps"></a>Další postup

- Rychlý start k vytvoření databáze a pravidla brány firewall na úrovni serveru, naleznete v tématu [vytvoření Azure SQL database](sql-database-get-started-portal.md).
- S připojováním k databázi SQL Azure z open source aplikací nebo aplikací třetích stran vám pomůžou [Ukázky kódu pro rychlý start klientů se službou SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Informace o další porty, které budete muset otevřít, najdete v článku **SQL Database: Vnější vs. vnitřní** část [porty nad 1433 pro ADO.NET 4.5 a službu SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- Přehled zabezpečení služby Azure SQL Database najdete v tématu [zabezpečení databáze](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
