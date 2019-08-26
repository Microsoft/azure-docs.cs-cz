---
title: Azure SQL Database a pravidla brány firewall pro IP data datového skladu | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat databázi SQL nebo bránu SQL Data Warehouse firewall pomocí pravidel brány firewall na úrovni serveru pro správu přístupu a nakonfigurovat jednu nebo sdruženou databázi s pravidly brány firewall protokolu IP na úrovni databáze.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 39d2dae28bde8ff35408733a1af886c302ec79bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568189"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL Database a SQL Data Warehouse pravidla brány firewall protokolu IP

Microsoft Azure [SQL Database](sql-database-technical-overview.md) a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) poskytují službu relačních databází pro Azure a další internetové aplikace. Brána firewall pomáhá chránit vaše data tím, že brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače k tomu mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.

> [!NOTE]
> Tento článek se týká Azure SQL serveru a databází SQL Database i SQL Data Warehouse, které jsou vytvořené na Azure SQL serveru. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.
> [!IMPORTANT]
> Tento článek se *nevztahuje na* **Azure SQL Database spravovanou instanci**. Další informace o potřebné konfiguraci sítě najdete v následujícím článku o [připojení ke spravované instanci](sql-database-managed-instance-connect-app.md) .

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Pravidla virtuální sítě jako alternativy k pravidlům IP

Kromě pravidel protokolu IP tato brána firewall také spravuje *pravidla virtuální sítě*. Pravidla virtuální sítě jsou založená na Virtual Networkch koncových bodech služby. Pravidla virtuální sítě mohou být v některých případech vhodnější pro pravidla protokolu IP. Další informace najdete v tématu [Virtual Network koncových bodů a pravidel služby pro Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Přehled

Zpočátku je veškerý přístup k vašemu serveru SQL Azure blokovaný bránou SQL Database firewall. Pokud chcete získat přístup k databázovému serveru, musíte zadat jedno nebo více pravidel brány firewall IP na úrovni serveru, která umožňují přístup k vašemu serveru SQL Azure. Pomocí pravidel brány firewall protokolu IP určete, které rozsahy IP adres z Internetu jsou povolené a jestli se můžou aplikace Azure pokoušet připojit k vašemu serveru SQL Azure.

Pokud chcete selektivně udělit přístup pouze k jedné z databází na vašem serveru SQL Azure, musíte pro příslušnou databázi vytvořit pravidlo na úrovni databáze. Zadejte rozsah IP adres pro pravidlo brány firewall protokolu IP pro databázi, které překračuje rozsah IP adres zadaný v pravidle brány firewall protokolu IP na úrovni serveru, a ujistěte se, že IP adresa klienta spadá do rozsahu zadaného v pravidle na úrovni databáze.

> [!IMPORTANT]
> SQL Data Warehouse podporuje pouze pravidla brány firewall na úrovni serveru a nepodporují pravidla brány firewall na úrovni databáze.

Pokusy o připojení z internetu a z Azure musí nejdříve projít přes bránu firewall a až potom mají přístup k vašemu serveru SQL Azure nebo službě SQL Database. Znázorňuje to následující diagram:

   ![Diagram popisující konfiguraci brány firewall.][1]

- **Pravidla brány firewall protokolu IP na úrovni serveru:**

  Tato pravidla umožňují klientům přístup k celému serveru SQL Azure, to znamená všem databázím v rámci stejného serveru SQL Database. Tato pravidla se ukládají v **hlavní** databázi. Pravidla brány firewall protokolu IP na úrovni serveru se dají nakonfigurovat pomocí portálu nebo pomocí příkazů jazyka Transact-SQL. Pokud chcete vytvořit pravidla brány firewall protokolu IP na úrovni serveru pomocí Azure Portal nebo PowerShellu, musíte být vlastníkem předplatného nebo přispěvatelem předplatného. Chcete-li vytvořit pravidlo brány firewall protokolu IP na úrovni serveru pomocí jazyka Transact-SQL, je nutné se připojit k instanci SQL Database jako přihlašovací jméno objektu zabezpečení na úrovni serveru nebo správce Azure Active Directory (což znamená, že pravidlo brány firewall protokolu IP na úrovni serveru musí být nejprve vytvořeno pomocí uživatel s oprávněními na úrovni Azure.

- **Pravidla brány firewall protokolu IP na úrovni databáze:**

  Tato pravidla umožňují klientům přístup k určitým (zabezpečeným) databázím v rámci stejného serveru SQL Database. Tato pravidla můžete vytvořit pro každou databázi (včetně **Hlavní** databáze) a ukládají se do jednotlivých databází. Pravidla brány firewall protokolu IP na úrovni databáze pro hlavní a uživatelské databáze lze vytvořit a spravovat pouze pomocí příkazů jazyka Transact-SQL a až po nakonfigurování první brány firewall na úrovni serveru. Pokud zadáte rozsah IP adres v pravidle brány firewall IP na úrovni databáze, které je mimo rozsah zadaný v pravidle brány firewall protokolu IP na úrovni serveru, budou mít přístup k databázi jenom klienti, kteří mají IP adresy v rozsahu na úrovni databáze. Pro databázi můžete mít maximálně 128 pravidel brány firewall protokolu IP na úrovni databáze. Další informace o konfiguraci pravidel brány firewall na úrovni databáze najdete v příkladu dále v tomto článku a v tématu [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Doporučení

Microsoft doporučuje používat pravidla brány firewall na úrovni databáze, kdykoli je to možné, zvýšit zabezpečení a zvýšit přenositelnost databáze. Pro správce použijte pravidla brány firewall na úrovni serveru a pokud máte mnoho databází se stejnými požadavky na přístup a nechcete ztrácet čas konfigurací jednotlivých databází zvlášť.

> [!IMPORTANT]
> Windows Azure SQL Database podporuje maximálně 128 pravidel brány firewall protokolu IP.
> [!NOTE]
> Informace o přenosných databázích v kontextu kontinuity podnikových procesů najdete v tématu [Požadavky na ověřování pro zotavení po havárii](sql-database-geo-replication-security-config.md).

### <a name="connecting-from-the-internet"></a>Připojení z Internetu

Když se počítač pokusí připojit k databázovému serveru z Internetu, brána firewall nejprve zkontroluje původní IP adresu požadavku na pravidla brány firewall protokolu IP na úrovni databáze pro databázi, kterou vyžaduje připojení:

- Pokud je IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall protokolu IP na úrovni databáze, připojení se udělí SQL Database, která pravidlo obsahuje.
- Pokud IP adresa požadavku není v jednom z rozsahů určených v pravidle brány firewall protokolu IP na úrovni databáze, zkontrolují se pravidla brány firewall IP na úrovni serveru. Pokud je IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall protokolu IP na úrovni serveru, připojení je uděleno. Pravidla brány firewall protokolu IP na úrovni serveru se vztahují na všechny databáze SQL na serveru SQL Azure.  
- Pokud IP adresa požadavku není v rozsahu zadaném v pravidlech brány firewall na úrovni databáze nebo na úrovni serveru, požadavek na připojení se nezdařil.

> [!NOTE]
> Pro přístup ke službě Azure SQL Database z místního počítače se ujistěte, že brána firewall na vaší síti i místní počítač umožňují odchozí komunikaci na portu TCP 1433.

### <a name="connecting-from-azure"></a>Připojení z Azure

Pokud chcete umožnit aplikacím z Azure připojení k vašemu serveru SQL Azure, musí být povolená připojení Azure. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Nastavení brány firewall s počáteční a koncovou adresou rovnou 0.0.0.0 znamená, že jsou povolená připojení Azure. Pokud pokus o připojení není povolený, požadavek se k serveru služby Azure SQL Database vůbec nedostane.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.

## <a name="creating-and-managing-ip-firewall-rules"></a>Vytváření a Správa pravidel brány firewall protokolu IP

První nastavení brány firewall na úrovni serveru se dá vytvořit pomocí [Azure Portal](https://portal.azure.com/) nebo programově pomocí [Azure POWERSHELL](https://docs.microsoft.com/powershell/module/az.sql), [Azure CLI](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)nebo [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Další pravidla brány firewall na úrovni serveru se dají vytvořit a spravovat pomocí těchto metod a pomocí jazyka Transact-SQL.

> [!IMPORTANT]
> Pravidla brány firewall protokolu IP na úrovni databáze lze vytvořit a spravovat pouze pomocí jazyka Transact-SQL.

Pro zvýšení výkonu se pravidla brány firewall IP na úrovni serveru dočasně ukládají do mezipaměti na úrovni databáze. Pokud chcete mezipaměť aktualizovat, podívejte se na příkaz [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Pomocí [auditování SQL Database](sql-database-auditing.md) můžete auditovat změny brány firewall na úrovni serveru a databáze.

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>Správa pravidel brány firewall protokolu IP na úrovni serveru pomocí Azure Portal

Pokud chcete v Azure Portal nastavit pravidlo brány firewall na úrovni serveru, můžete buď přejít na stránku Přehled pro vaši databázi SQL Azure, nebo na stránku Přehled pro SQL Database Server.

> [!TIP]
> Kurz najdete v tématu [Vytvoření databáze pomocí Azure Portal](sql-database-single-database-get-started.md).

### <a name="from-database-overview-page"></a>Stránka Přehled z databáze

1. Pokud chcete na stránce Přehled databáze nastavit pravidlo brány firewall protokolu IP na úrovni serveru, klikněte na **nastavit bránu firewall serveru** na panelu nástrojů, jak je znázorněno na následujícím obrázku: Otevře se stránka **Nastavení brány firewall** pro server služby SQL Database.

      ![pravidlo brány firewall protokolu IP serveru](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Kliknutím na **Přidat IP adresu klienta** na panelu nástrojů přidejte IP adresu počítače, který právě používáte, a pak klikněte na **Uložit**. Vytvoří se pravidlo brány firewall protokolu IP na úrovni serveru pro vaši aktuální IP adresu.

      ![nastavení pravidla brány firewall protokolu IP na úrovni serveru](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>Stránka Přehled serveru

Otevře se stránka s přehledem pro váš server, na které se zobrazí plně kvalifikovaný název serveru (například **mynewserver20170403.Database.Windows.NET**), který poskytuje možnosti pro další konfiguraci.

1. Pokud chcete nastavit pravidlo na úrovni serveru ze stránky přehled serveru, klikněte na **firewall** v nabídce vlevo v části nastavení:

2. Kliknutím na **Přidat IP adresu klienta** na panelu nástrojů přidejte IP adresu počítače, který právě používáte, a pak klikněte na **Uložit**. Vytvoří se pravidlo brány firewall protokolu IP na úrovni serveru pro vaši aktuální IP adresu.

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>Správa pravidel brány firewall protokolu IP pomocí jazyka Transact-SQL

| Zobrazení katalogu nebo uložená procedura | Level | Popis |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Zobrazí aktuální pravidla brány firewall protokolu IP na úrovni serveru. |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Vytvoří nebo aktualizuje pravidla brány firewall protokolu IP na úrovni serveru. |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Odebere pravidla brány firewall protokolu IP na úrovni serveru. |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Databáze |Zobrazí aktuální pravidla brány firewall protokolu IP na úrovni databáze. |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Databáze |Vytvoří nebo aktualizuje pravidla brány firewall protokolu IP na úrovni databáze. |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databáze |Odebere pravidla brány firewall protokolu IP na úrovni databáze. |

Následující příklady kontrolují existující pravidla, aktivují rozsah IP adres na serveru contoso a odstraní pravidlo brány firewall protokolu IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Pak přidejte pravidlo brány firewall protokolu IP na úrovni serveru.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Pokud chcete odstranit pravidlo brány firewall protokolu IP na úrovni serveru, spusťte uloženou proceduru sp_delete_firewall_rule. Následující příklad odstraní pravidlo s názvem ContosoFirewallRule:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>Správa pravidel brány firewall protokolu IP na úrovni serveru pomocí Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

| Rutiny | Level | Popis |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Vrátí aktuální pravidla brány firewall na úrovni serveru. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Vytvoří nové pravidlo brány firewall na úrovni serveru |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Aktualizuje vlastnosti existujícího pravidla brány firewall na úrovni serveru. |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Odebere pravidla brány firewall na úrovni serveru. |

Následující příklad nastaví pravidlo brány firewall protokolu IP na úrovni serveru pomocí prostředí PowerShell:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Příklady prostředí PowerShell v souvislosti s rychlým startem najdete v tématech [Vytvoření databáze – PowerShell](sql-database-powershell-samples.md) a [Vytvoření izolované databáze a konfigurace pravidla brány firewall IP SQL Database na úrovni serveru pomocí PowerShellu](scripts/sql-database-create-and-configure-database-powershell.md) .

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>Správa pravidel brány firewall protokolu IP na úrovni serveru pomocí Azure CLI

| Rutiny | Level | Popis |
| --- | --- | --- |
|[AZ SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Vytvoří pravidlo brány firewall protokolu IP serveru.|
|[AZ SQL Server Firewall-Rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Vypíše pravidla brány firewall protokolu IP na serveru.|
|[AZ SQL Server Firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Zobrazuje podrobnosti pravidla brány firewall protokolu IP.|
|[AZ SQL Server Firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Aktualizuje pravidlo brány firewall protokolu IP.|
|[AZ SQL Server Firewall-Rule DELETE](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Odstraní pravidlo brány firewall protokolu IP.|

Následující příklad nastaví pravidlo brány firewall protokolu IP na úrovni serveru pomocí rozhraní příkazového řádku Azure:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Příklad rozhraní příkazového řádku Azure v souvislosti s rychlým startem najdete v tématu [Vytvoření databáze – Azure CLI](sql-database-cli-samples.md) a [Vytvoření izolované databáze a konfigurace pravidla brány firewall SQL Database IP pomocí Azure CLI](scripts/sql-database-create-and-configure-database-cli.md) .

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>Správa pravidel brány firewall protokolu IP na úrovni serveru pomocí REST API

| rozhraní API | Level | Popis |
| --- | --- | --- |
| [Výpis pravidel brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Server |Zobrazí aktuální pravidla brány firewall protokolu IP na úrovni serveru. |
| [Vytvoření nebo aktualizace pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Server |Vytvoří nebo aktualizuje pravidla brány firewall protokolu IP na úrovni serveru. |
| [Odstranění pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Server |Odebere pravidla brány firewall protokolu IP na úrovni serveru. |
| [Získat pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Server | Načte pravidla brány firewall IP na úrovni serveru. |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Pravidla brány firewall protokolu IP na úrovni serveru i databáze

Otázka: Mají být uživatelé jedné databáze plně izolované z jiné databáze?
Pokud ano, udělte přístup pomocí pravidel brány firewall protokolu IP na úrovni databáze. Tím se vyhnete použití pravidel brány firewall na úrovni serveru, která umožňují přístup přes bránu firewall ke všem databázím, což snižuje hloubku obrany.

Otázka: Potřebují uživatelé na IP adrese přístup ke všem databázím?
Pomocí pravidel brány firewall na úrovni serveru snižte počet, kolikrát je potřeba nakonfigurovat pravidla brány firewall protokolu IP.

Otázka: Má osoba nebo tým, kteří konfigurují pravidla brány firewall protokolu IP, přístup jenom prostřednictvím Azure Portal, PowerShellu nebo REST API?
Je nutné použít pravidla brány firewall protokolu IP na úrovni serveru. Pravidla brány firewall protokolu IP na úrovni databáze lze konfigurovat pouze pomocí jazyka Transact-SQL.  

Otázka: Má osoba nebo tým nakonfigurovat pravidla brány firewall protokolu IP zakázaná, aby měla na úrovni databáze oprávnění vysoké úrovně?
Použijte pravidla brány firewall protokolu IP na úrovni serveru. Konfigurace pravidel brány firewall protokolu IP na úrovni databáze s použitím jazyka Transact-SQL `CONTROL DATABASE` vyžaduje aspoň oprávnění na úrovni databáze.  

Otázka: Je osoba nebo tým konfigurován nebo auditovat pravidla brány firewall protokolu IP, centrálně spravují pravidla brány firewall protokolu IP pro mnoho (možná stovky) databází?
Tento výběr závisí na vašich potřebách a prostředí. Pravidla brány firewall protokolu IP na úrovni serveru můžou být snáze nakonfigurovaná, ale skriptování může konfigurovat pravidla na úrovni databáze. A i když používáte pravidla brány firewall na úrovni serveru, možná budete muset auditovat pravidla brány firewall protokolu IP na úrovni databáze a zjistit, jestli uživatelé s `CONTROL` oprávněním k databázi vytvořili pravidla brány firewall IP na úrovni databáze.

Otázka: Můžu používat kombinaci pravidel brány firewall na úrovni serveru i databáze?
Ano. Někteří uživatelé, třeba správci, můžou potřebovat pravidla brány firewall IP na úrovni serveru. Jiní uživatelé, například uživatelé databázové aplikace, můžou potřebovat pravidla brány firewall IP na úrovni databáze.

## <a name="troubleshooting-the-database-firewall"></a>Řešení potíží s branou firewall databáze

Pokud se přístup ke službě Microsoft Azure SQL Database nechová podle očekávání, zvažte následující:

- **Konfigurace místní brány firewall:**

  Předtím, než počítač bude moci získat přístup k Azure SQL Database, může být nutné vytvořit v počítači výjimku brány firewall pro port TCP 1433. Pokud provádíte připojení v rámci cloudu Azure, možná bude nutné otevřít další porty. Další informace najdete v **SQL Database: Mimo oddíl vs** zevnitř [portů nad 1433 pro ADO.NET 4,5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Překlad síťových adres (NAT):**

  Z důvodu překladu adres (NAT) se IP adresa, kterou váš počítač používá pro připojení k Azure SQL Database, může lišit od IP adresy uvedené v nastavení konfigurace protokolu IP počítače. Pokud chcete zobrazit IP adresu, kterou váš počítač používá pro připojení k Azure, přihlaste se na portál a přejděte na kartu **Konfigurovat** na serveru, který je hostitelem vaší databáze. V části **Povolené IP adresy** se zobrazí **Aktuální IP adresa klienta**. Kliknutím na **Přidat** mezi **Povolené IP adresy** umožníte tomuto počítači přístup k serveru.

- **Změny v seznamu povolených se zatím neprojevily:**

  Změny konfigurace Azure SQL Database brány firewall se projeví až po dobu pěti minut.

- **Přihlášení není autorizováno nebo bylo použito nesprávné heslo:**

  Pokud přihlášení nemá oprávnění k serveru Azure SQL Database nebo je použité heslo nesprávné, připojení k serveru Azure SQL Database je odepřeno. Vytvoření nastavení brány firewall klientům pouze poskytuje možnost pokusit se o připojení k vašemu serveru – každý klient musí dodat potřebné zabezpečené přihlašovací údaje. Další informace o přípravě přihlášení najdete v tématu [Správa databází, přihlášení a uživatelů v Azure SQL Database](sql-database-manage-logins.md).

- **Dynamická IP adresa:**

  Pokud máte připojení k Internetu s dynamickým IP adresou a máte potíže s připojením přes bránu firewall, můžete vyzkoušet jedno z následujících řešení:
  
  - Zeptejte se poskytovatele internetových služeb (ISP) na rozsah IP adres přiřazený ke klientským počítačům, které přistupují k serveru Azure SQL Database, a pak přidejte rozsah IP adres jako pravidlo brány firewall protokolu IP.
  - Místo toho Získejte pro klientské počítače přidělování statických IP adres a pak přidejte IP adresy jako pravidla brány firewall protokolu IP.

## <a name="next-steps"></a>Další postup

- Potvrďte, že podnikové síťové prostředí umožňuje příchozí komunikaci z rozsahů IP adres služby COMPUTE (včetně rozsahů SQL) používaných datacentry Microsoft Azure. Může být nutné povolit tyto IP adresy, viz [Microsoft Azure rozsahy IP adres datacentra](https://www.microsoft.com/download/details.aspx?id=41653) .  
- Rychlý Start při vytváření pravidla brány firewall IP na úrovni serveru najdete v tématu [Vytvoření databáze SQL Azure](sql-database-single-database-get-started.md).
- S připojováním k databázi Azure SQL z open source aplikací nebo aplikací třetích stran vám pomůžou [Ukázky kódu pro rychlý start klientů se službou SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Informace o dalších portech, které možná budete muset otevřít, najdete **v SQL Database: Mimo oddíl vs** Inside [portů nad 1433 pro ADO.NET 4,5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- Přehled zabezpečení Azure SQL Database najdete v tématu [zabezpečení databáze](sql-database-security-overview.md) .

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
