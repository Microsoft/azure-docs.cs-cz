---
title: Azure SQL Database a Data Warehouse IP pravidla brány firewall | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat pravidla brány firewall na úrovni serveru IP ke správě přístupu a nakonfigurovat pravidla brány firewall na úrovni databáze IP jeden, nebo součástí fondu databáze SQL database nebo SQL Data Warehouse brány firewall.
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
ms.date: 02/04/2019
ms.openlocfilehash: e4b217ada2aae159680b113b6ddcb41c9d121f24
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753057"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL Database a SQL Data Warehouse IP pravidla brány firewall

Microsoft Azure [SQL Database](sql-database-technical-overview.md) a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) poskytuje relační databázová služba pro Azure a další internetové aplikace. Brána firewall pomáhá chránit vaše data tím, že brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače k tomu mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.

> [!NOTE]
> Tento článek se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořeny na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.
> [!IMPORTANT]
> Tento článek provádí *není* platí pro **Azure SQL Database Managed Instance**. Podrobnosti najdete v následujícím článku na [připojení k Managed Instance](sql-database-managed-instance-connect-app.md) Další informace o konfiguraci sítě potřeba.

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Pravidla virtuální sítě jako alternativy k pravidla protokolu IP

Kromě IP pravidel brány firewall také spravuje *pravidel virtuální sítě*. Pravidla virtuální sítě jsou založené na koncových bodech služby virtuální sítě. Pravidla virtuální sítě může být vhodnější než pravidla protokolu IP v některých případech. Další informace najdete v tématu [koncové body služeb virtuální sítě a pravidel pro službu Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Přehled

Zpočátku je veškerý přístup k vašemu serveru Azure SQL blokován branou firewall databáze SQL. Pro přístup k databázovému serveru, je nutné zadat jeden nebo více pravidla firewallu protokolu IP úrovni serveru, které umožňují přístup k vašemu serveru Azure SQL. Chcete-li určit, které rozsahy IP adres z Internetu jsou povolené a zda mohou aplikace Azure pokoušet pro připojení k serveru Azure SQL pomocí pravidla firewallu protokolu IP.

Pokud chcete selektivně udělit přístup pouze k jedné z databází na vašem serveru SQL Azure, musíte pro příslušnou databázi vytvořit pravidlo na úrovni databáze. Zadejte rozsah IP adres pro pravidlo brány firewall protokolu IP databáze, který je mimo rozsah IP adres zadaný v pravidlu brány firewall na úrovni serveru IP a ujistěte se, že IP adresa klienta spadá do rozsahu zadaný v pravidlu na úrovni databáze.

> [!IMPORTANT]
> SQL Data Warehouse pouze podporuje pravidla brány firewall na úrovni serveru IP a pravidla brány firewall na úrovni databáze IP nepodporuje.

Pokusy o připojení z internetu a z Azure musí nejdříve projít přes bránu firewall a až potom mají přístup k vašemu serveru SQL Azure nebo službě SQL Database. Znázorňuje to následující diagram:

   ![Diagram popisující konfiguraci brány firewall.][1]

- **Pravidla brány firewall na úrovni serveru IP:**

  Tato pravidla umožňují klientům přístup k celému serveru Azure SQL, to znamená, že všem databázím na stejném serveru SQL Database. Tato pravidla se ukládají v **hlavní** databázi. Pravidla brány firewall na úrovni serveru IP dá nakonfigurovat pomocí portálu nebo pomocí příkazů jazyka Transact-SQL. K vytvoření pravidla brány firewall na úrovni serveru IP pomocí webu Azure portal nebo Powershellu, musíte být vlastníkem nebo přispěvatelem předplatného. Pokud chcete vytvořit pravidlo firewallu protokolu IP úrovni serveru pomocí příkazů jazyka Transact-SQL, musíte se připojit k instanci služby SQL Database jako přihlášení objektu zabezpečení na úrovni serveru nebo správce Azure Active Directory (to znamená, že pravidlo brány firewall úrovni serveru IP musí být nejprve vytvořen pomocí Uživatel se oprávnění na úrovni Azure).

- **Pravidla brány firewall na úrovni databáze IP:**

  Tato pravidla umožňují klientům přístup k určitým (zabezpečeným) databázím na stejném serveru SQL Database. Můžete vytvořit tato pravidla pro každou databázi (včetně **hlavní** databáze) a jsou uloženy v jednotlivých databázích. Pravidla firewallu protokolu IP úrovni databáze pro hlavní a uživatelské databáze může pouze vytvořit a spravovat pomocí příkazů jazyka Transact-SQL a pouze po nakonfigurování první brány firewall na úrovni serveru. Pokud zadáte rozsah IP adres v úrovni databáze pravidlo firewallu protokolu IP, která je mimo rozsah zadaný v pravidlu brány firewall na úrovni serveru IP, pouze klienti, kteří mají IP adresy v rozsahu na úrovni databáze můžete přístup k databázi. Může mít maximálně 128 pravidel brány firewall protokolu IP úrovni databáze pro databázi. Další informace o konfiguraci pravidel brány firewall na úrovni databáze IP, podívejte se na příklad později v tomto článku a v tématu [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Doporučení

Microsoft doporučuje používat pravidla firewallu protokolu IP úrovni databáze kdykoli je to možné, pro zvýšení zabezpečení a přenositelnosti databáze. Pokud máte mnoho databází se stejnými požadavky na přístup a nechcete ztrácet čas konfigurací jednotlivých databází používejte pravidla brány firewall na úrovni serveru IP pro správce a.

> [!IMPORTANT]
> Windows Azure SQL Database podporuje maximálně 128 pravidel brány firewall protokolu IP.
> [!NOTE]
> Informace o přenosných databázích v kontextu kontinuity podnikových procesů najdete v tématu [Požadavky na ověřování pro zotavení po havárii](sql-database-geo-replication-security-config.md).

### <a name="connecting-from-the-internet"></a>Připojení z Internetu

Když se počítač pokusí z Internetu připojit k vašemu databázovému serveru, brána firewall nejprve zkontroluje zdrojovou IP adresu požadavku na úrovni databáze pravidla firewallu protokolu IP, pro databázi, kterou připojení požaduje:

- Pokud IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni databáze IP, je připojení k databázi SQL, který obsahuje pravidlo povoleno.
- Pokud IP adresa požadavku není v žádném z rozsahů určených v pravidle IP brány firewall na úrovni databáze, zkontrolují se pravidla firewallu protokolu IP úrovni serveru. Pokud IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni serveru IP, připojení je povoleno. Pravidla brány firewall na úrovni serveru IP platí pro všechny databáze SQL na serveru Azure SQL.  
- Pokud IP adresa požadavku není v rozsahu zadaný v libovolné úrovně databáze nebo pravidla firewallu protokolu IP serveru, požadavek na připojení selže.

> [!NOTE]
> Pro přístup ke službě Azure SQL Database z místního počítače se ujistěte, že brána firewall na vaší síti i místní počítač umožňují odchozí komunikaci na portu TCP 1433.

### <a name="connecting-from-azure"></a>Připojení z Azure

Pokud chcete umožnit aplikacím z Azure připojení k vašemu serveru SQL Azure, musí být povolená připojení Azure. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Brána firewall, nastavení s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou povolená připojení Azure. Pokud pokus o připojení není povolený, požadavek se k serveru služby Azure SQL Database vůbec nedostane.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.

## <a name="creating-and-managing-ip-firewall-rules"></a>Vytváření a Správa pravidel brány firewall protokolu IP

První nastavení brány firewall na úrovni serveru můžete vytvořit pomocí [webu Azure portal](https://portal.azure.com/) nebo programově pomocí [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql), [rozhraní příkazového řádku Azure](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create), nebo [ Rozhraní REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Následující pravidla brány firewall na úrovni serveru IP můžete vytvářet a spravovat těmito způsoby a pomocí příkazů jazyka Transact-SQL.

> [!IMPORTANT]
> Pravidla brány firewall na úrovni databáze IP mohou být vytvořeny pouze a spravují s použitím příkazů jazyka Transact-SQL.

Kvůli zvýšení výkonu se pravidla brány firewall na úrovni serveru IP se dočasně ukládají do mezipaměti na úrovni databáze. Pokud chcete mezipaměť aktualizovat, podívejte se na příkaz [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Můžete použít [auditování služby SQL Database](sql-database-auditing.md) auditovat změny brány firewall na úrovni serveru a databáze.

## <a name="manage-ip-firewall-rules-using-the-azure-portal"></a>Správa pravidel brány firewall protokolu IP pomocí webu Azure portal

Pokud chcete nastavit pravidlo firewallu protokolu IP úrovni serveru na webu Azure Portal, můžete buď přejít na stránku přehled pro vaši databázi Azure SQL nebo na stránce Přehled pro váš server SQL Database.

> [!TIP]
> Podívejte se kurz [vytvoření databáze pomocí webu Azure portal](sql-database-single-database-get-started.md).

### <a name="from-database-overview-page"></a>Na stránce Přehled databáze

1. Chcete-li nastavit pravidlo firewallu protokolu IP úrovni serveru na stránce Přehled databáze, klikněte na tlačítko **nastavit bránu firewall serveru** na panelu nástrojů, jak je znázorněno na následujícím obrázku: Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database.

      ![pravidla brány firewall protokolu IP](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Klikněte na tlačítko **přidat IP adresu klienta** na panelu nástrojů a přidejte IP adresu počítače právě používáte a potom klikněte na **Uložit**. Pravidlo firewallu protokolu IP úrovni serveru se vytvoří pro vaši aktuální IP adresu.

      ![nastavit pravidlo brány firewall na úrovni serveru IP](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>Na stránce Přehled serveru

Otevře se stránka s přehledem pro váš server, zobrazí plně kvalifikovaný název serveru (například **mynewserver20170403.database.windows.net**) a poskytne vám možnosti další konfigurace.

1. Na stránce Přehled serveru nastavit serverové pravidlo, klikněte na tlačítko **brány Firewall** v nabídce vlevo v části nastavení:

2. Klikněte na tlačítko **přidat IP adresu klienta** na panelu nástrojů a přidejte IP adresu počítače právě používáte a potom klikněte na **Uložit**. Pravidlo firewallu protokolu IP úrovni serveru se vytvoří pro vaši aktuální IP adresu.

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>Správa pravidel brány firewall protokolu IP pomocí příkazů jazyka Transact-SQL

| Zobrazení katalogu nebo uložená procedura | Úroveň | Popis |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Zobrazí aktuální pravidla brány firewall na úrovni serveru IP |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Vytvoří nebo aktualizuje pravidla brány firewall na úrovni serveru IP |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Odebere pravidla brány firewall na úrovni serveru IP |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Databáze |Zobrazí aktuální pravidla brány firewall na úrovni databáze IP |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Databáze |Vytvoří nebo aktualizuje pravidla brány firewall na úrovni databáze IP |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databáze |Pravidla brány firewall na úrovni databáze IP odebere |

Následující příklady zkontrolujte existující pravidla povolit rozsah IP adres na serveru Contoso a odstraní pravidlo brány firewall protokolu IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Dále přidejte pravidlo firewallu protokolu IP úrovni serveru.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Pokud chcete odstranit pravidlo firewallu protokolu IP úrovni serveru, spusťte proceduru sp_delete_firewall_rule uložená. Následující příklad odstraní pravidlo s názvem ContosoFirewallRule.

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>Spravovat pravidla brány firewall na úrovni serveru IP pomocí Azure Powershellu

| Rutina | Úroveň | Popis |
| --- | --- | --- |
| [Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule) |Server |Vrátí aktuální pravidla brány firewall úrovni serveru IP |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) |Server |Vytvoří nové pravidlo brány firewall na úrovni serveru IP |
| [Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule) |Server |Aktualizuje vlastnosti existujícího pravidla brány firewall na úrovni serveru IP |
| [Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule) |Server |Odebere pravidla brány firewall na úrovni serveru IP |

Následující příklad nastaví pravidlo firewallu protokolu IP úrovni serveru pomocí prostředí PowerShell:

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Příklady prostředí PowerShell v rámci rychlý start, najdete v článku [vytvoření databáze – PowerShell](sql-database-powershell-samples.md) a [vytvoření izolované databáze a nakonfigurujte pravidlo brány firewall protokolu IP serveru SQL Database pomocí Powershellu](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>Spravovat pravidla brány firewall na úrovni serveru IP pomocí Azure CLI

| Rutina | Úroveň | Popis |
| --- | --- | --- |
|[Vytvoření az sql server firewall-rule](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Vytvoří pravidlo brány firewall serveru IP|
|[AZ sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Obsahuje pravidla firewallu protokolu IP na serveru|
|[AZ sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Zobrazí podrobnosti pravidla brány firewall protokolu IP|
|[AZ sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Aktualizuje pravidlo brány firewall protokolu IP|
|[AZ sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Odstraní pravidlo brány firewall protokolu IP|

Následující příklad nastaví pravidlo firewallu protokolu IP úrovni serveru pomocí Azure CLI:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Příklad rozhraní příkazového řádku Azure v rámci rychlý start, naleznete v tématu [vytvoření databáze – Azure CLI](sql-database-cli-samples.md) a [vytvoření izolované databáze a konfigurace pravidla brány firewall SQL Database IP pomocí rozhraní příkazového řádku Azure](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>Spravovat pravidla brány firewall na úrovni serveru IP pomocí rozhraní REST API

| Rozhraní API | Úroveň | Popis |
| --- | --- | --- |
| [Výpis pravidel brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Server |Zobrazí aktuální pravidla brány firewall na úrovni serveru IP |
| [Vytvoření nebo aktualizace pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Server |Vytvoří nebo aktualizuje pravidla brány firewall na úrovni serveru IP |
| [Odstranění pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Server |Odebere pravidla brány firewall na úrovni serveru IP |
| [Získání pravidla brány Firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Server | Získá pravidla brány firewall na úrovni serveru IP |

## <a name="server-level-ip-firewall-rule-versus-a-database-level-ip-firewall-rule"></a>Pravidlo brány firewall na úrovni serveru IP a pravidlo firewallu protokolu IP na úrovni databáze

Otázka: By měl být uživatelé jednu databázi plně izolované od jiné databáze?
Pokud ano, udělení přístupu pomocí pravidel brány firewall na úrovni databáze IP. Tím předejdete pomocí pravidla firewallu protokolu IP serveru, které povolují přístup přes bránu firewall, aby všechny databáze, zmenšete hloubku vaší ochrany.

Otázka: Potřebují uživatelé na IP adrese přístup ke všem databázím?
Pomocí pravidel brány firewall na úrovni serveru IP snížit počet, kolikrát musí nakonfigurovat pravidla brány firewall protokolu IP.

Otázka: Osobu nebo tým konfigurace pouze pravidla firewallu protokolu IP má přístup prostřednictvím webu Azure portal, Powershellu nebo rozhraní REST API?
Je nutné použít pravidla brány firewall na úrovni serveru IP. Pravidla brány firewall na úrovni databáze IP lze konfigurovat pouze pomocí příkazů jazyka Transact-SQL.  

Otázka: Osobu nebo tým konfiguruje pravidla firewallu protokolu IP, zakázáno s vysoké úrovně oprávnění na úrovni databáze?
Pomocí pravidel brány firewall na úrovni serveru IP. Konfigurace pravidla brány firewall na úrovni databáze IP pomocí příkazů jazyka Transact-SQL, vyžaduje alespoň `CONTROL DATABASE` oprávnění na úrovni databáze.  

Otázka: Je osobu nebo tým konfigurace ani audit, pravidla brány firewall protokolu IP, centrálně spravovat pravidla firewallu protokolu IP pro mnoho (třeba 100s) z databáze?
Tento výběr, závisí na konkrétních potřeb a prostředí. Může být jednodušší konfigurovat pravidla brány firewall na úrovni serveru IP, ale skriptování můžete nakonfigurovat pravidla na úrovni databáze. A i když používáte pravidla brány firewall na úrovni serveru IP, možná budete muset auditování na úrovni databáze pravidla firewallu protokolu IP, jestli uživatelé s `CONTROL` oprávnění v databázi vytvořili pravidla brány firewall na úrovni databáze IP.

Otázka: Můžete použít kombinaci pravidla firewallu protokolu IP úrovni serveru a databáze?
Ano. Někteří uživatelé, třeba o správce může být nutné pravidla brány firewall na úrovni serveru IP. Jinými uživateli, jako jsou uživatelé databázovou aplikaci, může být nutné databázi na úrovni pravidla firewallu protokolu IP.

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
  
  - Požádejte poskytovatele služeb Internetu (ISP) o rozsah IP adres přidělený vašim klientským počítačům, které přistupují k serveru Azure SQL Database a pak přidat rozsah IP adres jako pravidlo brány firewall protokolu IP.
  - Získat statické přidělování IP adres pro klientské počítače a pak přidejte IP adresy jako pravidla firewallu protokolu IP.

## <a name="next-steps"></a>Další postup

- Rychlý start k vytvoření pravidla brány firewall na úrovni serveru IP, naleznete v tématu [vytvoření Azure SQL database](sql-database-single-database-get-started.md).
- S připojováním k databázi SQL Azure z open source aplikací nebo aplikací třetích stran vám pomůžou [Ukázky kódu pro rychlý start klientů se službou SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Informace o další porty, které budete muset otevřít, najdete v článku **SQL Database: Vnější vs. vnitřní** část [porty nad 1433 pro ADO.NET 4.5 a službu SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- Přehled zabezpečení služby Azure SQL Database najdete v tématu [zabezpečení databáze](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
