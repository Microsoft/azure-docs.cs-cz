---
title: Pravidla brány firewall ve službě Azure SQL Database | Microsoft Docs
description: Zjistěte, jak nakonfigurovat bránu firewall databáze SQL s pravidly brány firewall na úrovni serveru a databáze pro správu přístupu.
keywords: brána firewall databáze
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: d68ae802e37b5d0b8421099894da43a0559f2fc2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-sql-database-server-level-and-database-level-firewall-rules"></a>Azure pravidla brány firewall serveru úroveň a databáze SQL Database 

Microsoft Azure SQL Database poskytuje relační databázovou službu pro aplikace Azure a další internetové aplikace. Brána firewall pomáhá chránit vaše data tím, že brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače k tomu mají oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.

#### <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Pravidla pro virtuální sítě jako alternativy IP pravidla

Kromě IP pravidel brány firewall také spravuje *pravidla virtuální sítě*. Pravidla pro virtuální sítě jsou založené na koncových bodů služby virtuální sítě. Pravidla pro virtuální sítě může být vhodnější než pravidla IP v některých případech. Další informace najdete v tématu [koncové body služby virtuální sítě a pravidla pro Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Přehled

Zpočátku je veškerý přístup Transact-SQL k vašemu serveru SQL Azure blokován branou firewall. Chcete-li začít používat server Azure SQL, musíte zadat jedno nebo více pravidel brány firewall na úrovni serveru, které umožňují přístup k serveru Azure SQL. Pomocí pravidel brány firewall určete, které rozsahy IP adres z internetu jsou povolené a zda se mohou aplikace Azure pokoušet připojovat k vašemu serveru SQL Azure.

Pokud chcete selektivně udělit přístup pouze k jedné z databází na vašem serveru SQL Azure, musíte pro příslušnou databázi vytvořit pravidlo na úrovni databáze. Zadejte pro pravidlo brány firewall databáze rozsah IP adres, který je mimo rozsah IP adres zadaný v pravidlu brány firewall na úrovni serveru, a ujistěte se, že IP adresa klienta spadá do rozsahu zadaného v pravidlu na úrovni databáze.

Pokusy o připojení z internetu a z Azure musí nejdříve projít přes bránu firewall a až potom mají přístup k vašemu serveru SQL Azure nebo službě SQL Database. Znázorňuje to následující diagram:

   ![Diagram popisující konfiguraci brány firewall.][1]

* **Pravidla brány firewall na úrovni serveru:** Tato pravidla umožňují klientům přístup k celému serveru SQL Azure, jinými slovy ke všem databázím na stejném logickém serveru. Tato pravidla se ukládají v **hlavní** databázi. Pravidla brány firewall na úrovni serveru můžete konfigurovat pomocí portálu nebo pomocí příkazů jazyka Transact-SQL. Pokud chcete vytvořit pravidla brány firewall na úrovni serveru pomocí portálu Azure Portal nebo PowerShellu, musíte být vlastníkem nebo přispěvatelem předplatného. Pokud chcete vytvořit pravidlo brány firewall na úrovni serveru pomocí příkazu Transact-SQL, musíte se připojit k instanci služby SQL Database jako přihlášení objektu zabezpečení nebo správce Azure Active Directory (to znamená, že musí být pravidlo brány firewall na úrovni serveru nejdřív vytvořené uživatelem s oprávněními na úrovni Azure).
* **Pravidla brány firewall na úrovni databáze:** tato pravidla povolit klientům přístup k určité (zabezpečení) databází v rámci stejného logického serveru. Můžete vytvořit tato pravidla pro každou databázi (včetně **hlavní** databáze) a jsou uložené v jednotlivých databázích. Pravidla brány firewall na úrovni databáze pro hlavní a uživatele databáze lze pouze vytvořit a spravovat pomocí příkazů Transact-SQL a až po nakonfigurování první brány firewall na úrovni serveru. Pokud v pravidlu brány firewall na úrovni databáze zadáte rozsah IP adres, který je mimo rozsah zadaný v pravidlu brány firewall na úrovni serveru, budou mít k dané databázi přístup pouze klienti, jejichž IP adresa je v rozsahu na úrovni databáze. Pro jednu databázi můžete mít maximálně 128 pravidel brány firewall na úrovni databáze. Další informace o konfiguraci pravidla brány firewall na úrovni databáze, podívejte se na příklad později v tomto článku a v tématu [sp_set_database_firewall_rule (databáze SQL Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

**Doporučení:** Společnost Microsoft doporučuje pro zvýšení zabezpečení a přenositelnosti databází používat pravidla brány firewall na úrovni databáze kdykoli je to možné. Použijte pravidla brány firewall na úrovni serveru pro správce a pokud máte mnoho databází se stejnými požadavky na přístup a nechcete ztrácet čas konfigurací jednotlivých databází.

> [!Important]
> Windows Azure SQL Database podporuje maximálně 128 pravidel brány firewall.
>

> [!Note]
> Informace o přenosných databázích v kontextu kontinuity podnikových procesů najdete v tématu [Požadavky na ověřování pro zotavení po havárii](sql-database-geo-replication-security-config.md).
>

### <a name="connecting-from-the-internet"></a>Připojení z Internetu

Když se počítač pokusí z internetu připojit k databázovému serveru, brána firewall nejprve u databáze, kterou připojení požaduje, pomocí pravidel brány firewall na úrovni databáze zkontroluje zdrojovou IP adresu požadavku:

* Pokud je IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni databáze, připojení ke službě SQL Database, která pravidlo obsahuje, je povoleno.
* Pokud IP adresa požadavku není v žádném z rozsahů určených v pravidlech brány firewall na úrovni databáze, zkontrolují se pravidla brány firewall na úrovni serveru. Pokud je IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni serveru, připojení je povoleno. Pravidla brány firewall na úrovni serveru se vztahují na všechny databáze SQL na serveru Azure SQL.  
* Pokud IP adresa požadavku není v rámci rozsahů určených v žádné z pravidel brány firewall na úrovni databáze nebo úrovni serveru, požadavek na připojení se nezdaří.

> [!NOTE]
> Pro přístup ke službě Azure SQL Database z místního počítače se ujistěte, že brána firewall na vaší síti i místní počítač umožňují odchozí komunikaci na portu TCP 1433.
> 

### <a name="connecting-from-azure"></a>Připojení z Azure
Pokud chcete umožnit aplikacím z Azure připojení k vašemu serveru SQL Azure, musí být povolená připojení Azure. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Pokud pokus o připojení není povolený, požadavek se k serveru služby Azure SQL Database vůbec nedostane.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

## <a name="creating-and-managing-firewall-rules"></a>Vytváření a správu pravidel brány firewall
První nastavení brány firewall na úrovni serveru můžete vytvořit pomocí [portál Azure](https://portal.azure.com/) nebo programově pomocí [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql), [rozhraní příkazového řádku Azure](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create), nebo [ Rozhraní REST API](https://docs.microsoft.com/rest/api/sql/firewallrules). Další pravidla brány firewall na úrovni serveru můžete vytvářet a spravovat těmito způsoby nebo prostřednictvím jazyka Transact-SQL. 

> [!IMPORTANT]
> Pravidla brány firewall na úrovni databáze mohou být vytvořeny pouze a spravovat pomocí jazyka Transact-SQL. 
>

Pro zvýšení výkonu se pravidla brány firewall na úrovni serveru dočasně ukládají do mezipaměti na úrovni databáze. Pokud chcete mezipaměť aktualizovat, podívejte se na příkaz [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). 

> [!TIP]
> Můžete použít [auditování databáze SQL](sql-database-auditing.md) auditování změn brány firewall úrovni serveru a na úrovni databáze.
>

## <a name="manage-firewall-rules-using-the-azure-portal"></a>Spravovat pravidla brány firewall pomocí portálu Azure

Pokud chcete nastavit na portálu Azure pravidlo brány firewall na úrovni serveru, můžete buď přejdete na stránku přehled pro Azure SQL database nebo stránce Přehled logického serveru Azure databáze.

> [!TIP]
> Podívejte se kurz [vytvořit pomocí portálu Azure DB](sql-database-get-started-portal.md).
>

**Na stránce Přehled databáze**

1. Chcete-li nastavit pravidlo brány firewall na úrovni serveru na stránce Přehled databáze, klikněte na tlačítko **nastavení brány firewall serveru** na panelu nástrojů, jak je znázorněno na následujícím obrázku: **nastavení brány Firewall** stránky pro server databáze SQL Otevře se.

      ![pravidlo brány firewall serveru](./media/sql-database-get-started-portal/server-firewall-rule.png) 

2. Klikněte na tlačítko **přidat IP adresu klienta** na panelu nástrojů přidat IP adresu počítače jsou aktuálně používá a potom klikněte na **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu.

      ![nastavení pravidla brány firewall serveru](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

**Na stránce Přehled serveru**

Otevře se stránka Přehled pro váš server, ukazuje název plně kvalifikovaný serveru (například **mynewserver20170403.database.windows.net**) a poskytuje možnosti pro další konfiguraci.

1. Chcete-li nastavit pravidlo, úrovni serveru ze stránky přehled serveru, klikněte na tlačítko **brány Firewall** v levé nabídce v části nastavení: 

2. Klikněte na tlačítko **přidat IP adresu klienta** na panelu nástrojů přidat IP adresu počítače jsou aktuálně používá a potom klikněte na **Uložit**. Vytvoří se pravidlo brány firewall na úrovni serveru pro vaši aktuální IP adresu.

## <a name="manage-firewall-rules-using-transact-sql"></a>Spravovat pravidla brány firewall pomocí jazyka Transact-SQL
| Zobrazení katalogu nebo uložená procedura | Úroveň | Popis |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Zobrazí aktuální pravidla brány firewall na úrovni serveru. |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Vytvoří nebo aktualizuje pravidla brány firewall na úrovni serveru. |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Odebere pravidla brány firewall na úrovni serveru. |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Databáze |Zobrazí aktuální pravidla brány firewall na úrovni databáze. |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Databáze |Vytvoří nebo aktualizuje pravidla brány firewall na úrovni databáze. |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Databáze |Odebere pravidla brány firewall na úrovni databáze. |


Následující příklady zkontrolujte existující pravidla, povolte rozsah IP adres na serveru Contoso a odstraní pravidlo brány firewall:
   
```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```
  
Potom přidejte pravidlo brány firewall.
   
```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Pokud chcete odstranit pravidlo brány firewall na úrovni serveru, spusťte uloženou proceduru sp_delete_firewall_rule. Následující příklad odstraní pravidlo s názvem ContosoFirewallRule:
   
```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```   

## <a name="manage-firewall-rules-using-azure-powershell"></a>Spravovat pravidla brány firewall pomocí Azure PowerShell
| Rutina | Úroveň | Popis |
| --- | --- | --- |
| [Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule) |Server |Vrátí aktuální pravidla brány firewall na úrovni serveru. |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) |Server |Vytvoří nové pravidlo brány firewall na úrovni serveru |
| [Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule) |Server |Aktualizuje vlastnosti existujícího pravidla brány firewall na úrovni serveru. |
| [Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule) |Server |Odebere pravidla brány firewall na úrovni serveru. |


Následující příklad ilustruje pravidlo brány firewall na úrovni serveru pomocí prostředí PowerShell:

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Příklady prostředí PowerShell v kontextu systému rychle začít, najdete v části [vytvořit DB - PowerShell](sql-database-get-started-powershell.md) a [vytvářet izolované databáze a nakonfigurujte pravidlo brány firewall pomocí prostředí PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)
>

## <a name="manage-firewall-rules-using-azure-cli"></a>Spravovat pravidla brány firewall pomocí rozhraní příkazového řádku Azure
| Rutina | Úroveň | Popis |
| --- | --- | --- |
|[Vytvoření brány firewall pravidlo az sql serveru](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Server|Vytvoří pravidlo brány firewall serveru|
|[seznam az sql serverů pravidlo brány firewall](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Server|Zobrazí seznam pravidel brány firewall na serveru|
|[Zobrazit pravidlo brány firewall serveru sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Server|Zobrazí podrobnosti pravidla brány firewall|
|[aktualizace pravidla brány firewall az sql server](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Server|Aktualizace pravidla brány firewall|
|[Odstranit pravidlo brány firewall serveru sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Server|Odstraní pravidlo brány firewall|

Následující příklad ilustruje pravidlo brány firewall na úrovni serveru pomocí rozhraní příkazového řádku Azure: 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Příklad rozhraní příkazového řádku Azure v kontextu rychle začít, najdete v části [vytvořit odpis - rozhraní příkazového řádku Azure](sql-database-get-started-cli.md) a [vytvářet izolované databáze a nakonfigurujte pravidlo brány firewall pomocí rozhraní příkazového řádku Azure](scripts/sql-database-create-and-configure-database-cli.md)
>

## <a name="manage-firewall-rules-using-rest-api"></a>Spravovat pravidla brány firewall pomocí rozhraní REST API
| Rozhraní API | Úroveň | Popis |
| --- | --- | --- |
| [Výpis pravidel brány firewall](https://docs.microsoft.com/rest/api/sql/FirewallRules/ListByServer) |Server |Zobrazí aktuální pravidla brány firewall na úrovni serveru. |
| [Vytvoření nebo aktualizace pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/FirewallRules/CreateOrUpdate) |Server |Vytvoří nebo aktualizuje pravidla brány firewall na úrovni serveru. |
| [Odstranění pravidla brány firewall](https://docs.microsoft.com/rest/api/sql/FirewallRules/Delete) |Server |Odebere pravidla brány firewall na úrovni serveru. |

## <a name="server-level-firewall-rule-versus-a-database-level-firewall-rule"></a>Pravidlo brány firewall na úrovni serveru a pravidla brány firewall na úrovni databáze
OTÁZKY. Musí být uživatelé jednu databázi plně izolovaném z jiné databáze?   
  Pokud ano, udělte přístup pomocí pravidel brány firewall na úrovni databáze. Tím je zabráněno pomocí pravidla brány firewall na úrovni serveru, která umožňují přístup přes bránu firewall, aby všechny databáze, snižuje hloubka svoji ochranu.   
 
OTÁZKY. Mají uživatelé na IP adrese přístupu pro všechny databáze?   
  Pomocí pravidel brány firewall na úrovni serveru můžete snížit počet, kolikrát je nutné nakonfigurovat pravidla brány firewall.   

OTÁZKY. Osobu nebo tým konfigurace pravidel brány firewall pouze má přístup prostřednictvím portálu Azure, PowerShell nebo rozhraní REST API?   
  Je nutné použít pravidla brány firewall na úrovni serveru. Pravidla brány firewall na úrovni databáze se dá nakonfigurovat jenom pomocí jazyka Transact-SQL.  

OTÁZKY. Je osobě nebo týmu konfigurace pravidla brány firewall zakázáno s vysoké úrovně oprávnění na úrovni databáze?   
  Pomocí pravidel brány firewall na úrovni serveru. Konfigurace pravidel brány firewall na úrovni databáze pomocí jazyka Transact-SQL, vyžaduje alespoň `CONTROL DATABASE` oprávnění na úrovni databáze.  

OTÁZKY. Je osobě nebo týmu konfigurace nebo auditování pravidla brány firewall, centrálně spravovat pravidla brány firewall pro mnoho (možná 100s) z databáze?   
  Tento výběr závisí na konkrétních potřeb a prostředí. Pravidla brány firewall na úrovni serveru, může být jednodušší ke konfiguraci, ale skriptování můžete nakonfigurovat pravidla, na úrovni databáze. I když používáte pravidla brány firewall na úrovni serveru, může být nutné auditovat pravidla firewallu databáze, a zjistěte, zda uživatelé s `CONTROL` oprávnění v databázi vytvořili pravidla brány firewall na úrovni databáze.   

OTÁZKY. Můžete použít kombinaci obou pravidla brány firewall úrovni serveru a na úrovni databáze?   
  Ano. Někteří uživatelé, třeba správci, může být nutné pravidla brány firewall na úrovni serveru. Jiných uživatelů, jako jsou uživatelé databázovou aplikaci, může být nutné pravidla brány firewall na úrovni databáze.   

## <a name="troubleshooting-the-database-firewall"></a>Řešení potíží s branou firewall databáze
Pokud se přístup ke službě Microsoft Azure SQL Database nechová podle očekávání, zvažte následující:

* **Konfigurace místní brány firewall:** Než bude váš počítač moci přistupovat ke službě Azure SQL Database, možná bude nutné vytvořit pro váš počítač výjimku brány firewall pro port TCP 1433. Pokud provádíte připojení v rámci cloudu Azure, možná bude nutné otevřít další porty. Další informace najdete v tématu **SQL Database: mimo vs uvnitř** části [porty nad rámec 1433 pro technologii ADO.NET 4.5 a SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
* **Překlad adres (NAT):** Je možné, že kvůli překladu adres (NAT) se bude lišit IP adresa, kterou váš počítač používá pro připojení ke službě Azure SQL Database, a IP adresa, která se zobrazuje na vašem počítači v nastavení konfigurace IP adresy. Pokud chcete zobrazit IP adresu, kterou váš počítač používá pro připojení k Azure, přihlaste se na portál a přejděte na kartu **Konfigurovat** na serveru, který je hostitelem vaší databáze. V části **Povolené IP adresy** se zobrazí **Aktuální IP adresa klienta**. Kliknutím na **Přidat** mezi **Povolené IP adresy** umožníte tomuto počítači přístup k serveru.
* **Změny seznamu povolených se ještě neprojevily:** Může trvat až pět minut, než se změny konfigurace brány firewall služby Azure SQL Database projeví.
* **Přihlášení není autorizováno nebo bylo použito nesprávné heslo:** Pokud přihlášení nemá oprávnění k serveru služby Azure SQL Database nebo pokud bylo použito nesprávné heslo, připojení k serveru služby Azure SQL Database je zamítnuto. Vytvoření nastavení brány firewall klientům pouze poskytuje možnost pokusit se o připojení k vašemu serveru – každý klient musí dodat potřebné zabezpečené přihlašovací údaje. Další informace o přípravě přihlášení najdete v tématu Správa databází, přihlášení a uživatelů ve službě Azure SQL Database.
* **Dynamická IP adresa:** Pokud vaše internetové připojení používá dynamické přidělování IP adres a máte problémy dostat se přes bránu firewall, můžete zkusit jedno z následujících řešení:
  
  * Požádejte svého poskytovatele internetových služeb (ISP) o rozsah IP adres přidělený vašim klientským počítačům, které přistupují k serveru služby Azure SQL Database, a následně přidejte tento rozsah IP adres jako pravidlo brány firewall.
  * Získejte pro své klientské počítače statické přidělování IP adres a následně přidejte tyto IP adresy jako pravidla brány firewall.

## <a name="next-steps"></a>Další postup

- Rychlý start na vytváření databáze a pravidlo brány firewall na úrovni serveru, najdete v části [vytvoření Azure SQL database](sql-database-get-started-portal.md).
- S připojováním k databázi SQL Azure z open source aplikací nebo aplikací třetích stran vám pomůžou [Ukázky kódu pro rychlý start klientů se službou SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Informace o další porty, které budete muset otevřít v tématu **SQL Database: mimo vs uvnitř** části [porty nad rámec 1433 pro technologii ADO.NET 4.5 a databáze SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
- Přehled zabezpečení Azure SQL Database, najdete v části [zabezpečení databáze](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
