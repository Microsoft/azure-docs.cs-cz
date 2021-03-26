---
title: 'Kurz: návrh Azure Database for PostgreSQL-Single server – Azure PowerShell'
description: V tomto kurzu se dozvíte, jak vytvořit, nakonfigurovat a dotazovat první Azure Database for PostgreSQL samostatný server pomocí Azure PowerShell.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 84b52ec43dc5fec4671459720af1b218b7f176d1
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605803"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-powershell"></a>Kurz: návrh Azure Database for PostgreSQLho samostatného serveru pomocí prostředí PowerShell

Azure Database for PostgreSQL je služba relačních databází v cloudu Microsoftu založená na databázovém stroji PostgreSQL Community Edition. V tomto kurzu se naučíte, jak pomocí PowerShellu a dalších nástrojů:

> [!div class="checklist"]
> - Vytvoření Azure Database for PostgreSQL
> - Konfigurace brány firewall serveru
> - Vytvoření databáze pomocí nástroje [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html)
> - Načtení ukázkových dat
> - Dotazování dat
> - Aktualizace dat
> - Obnovení dat

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud se rozhodnete použít prostředí PowerShell místně, Tento článek vyžaduje, abyste nainstalovali modul AZ PowerShell a připojili se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Další informace o instalaci modulu AZ PowerShell najdete v tématu [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> I když je modul PowerShell AZ. PostgreSql ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Jakmile je modul PowerShellu AZ. PostgreSql všeobecně dostupný, bude součástí budoucna k tomu, aby vydaná vydání modulu PowerShellu a nativně dostupná v rámci Azure Cloud Shell.

Pokud používáte službu Azure Database for PostgreSQL poprvé, je nutné zaregistrovat poskytovatele prostředků **Microsoft. DBforPostgreSQL** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se prostředky mají fakturovat. Pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) vyberte konkrétní ID předplatného.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pomocí rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina.

Následující příklad vytvoří skupinu prostředků s názvem **myresourcegroup** v oblasti **západní USA** .

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Vytvořte Azure Database for PostgreSQL Server pomocí `New-AzPostgreSqlServer` rutiny. Server může spravovat více databází. Obvykle se pro jednotlivé projekty nebo uživatele používají samostatné databáze.

Následující příklad vytvoří server PostgreSQL v oblasti **západní USA** s názvem **mydemoserver** ve skupině prostředků **myresourcegroup** s přihlašovacím jménem správce serveru **myadmin**. Je to Server Gen 5 v cenové úrovni pro obecné účely s povolenými 2 virtuální jádray a geograficky redundantními zálohováními. Heslo pro účet správce serveru PostgreSQL můžete zdokumentovat v prvním řádku příkladu, protože se jedná o heslo.

> [!TIP]
> Název serveru se mapuje na název DNS a v rámci Azure musí být globálně jedinečný.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Hodnota parametru **SKU** dodržuje cenové úrovně **\_ \_ virtuální jádra COMPUTE-Generation** , jak je znázorněno v následujících příkladech.

- `-Sku B_Gen5_1` mapuje se na Basic, Gen 5 a 1 vCore. Tato možnost je k dispozici nejmenší SKU.
- `-Sku GP_Gen5_32` se mapuje na úroveň pro obecné účely 5. generace se 32 virtuálními jádry.
- `-Sku MO_Gen5_2` se mapuje na úroveň optimalizovanou pro paměť 5. generace se dvěma virtuálními jádry.

Informace o platných hodnotách **SKU** podle oblasti a pro vrstvy najdete v tématu [Azure Database for PostgreSQL cenové úrovně](./concepts-pricing-tiers.md).

Zvažte použití cenové úrovně Basic, pokud je pro vaše zatížení vhodné světlé výpočetní prostředky a vstupně-výstupní operace.

> [!IMPORTANT]
> Servery vytvořené v cenové úrovni Basic nelze později škálovat na obecné účely nebo paměťově optimalizované a nelze je geograficky replikovat.

## <a name="configure-a-firewall-rule"></a>Konfigurace pravidla brány firewall

Vytvořte pravidlo brány firewall na úrovni serveru Azure Database for PostgreSQL pomocí `New-AzPostgreSqlFirewallRule` rutiny. Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako je například `psql` Nástroj příkazového řádku nebo PostgreSQL Workbench, aby se k vašemu serveru připojila prostřednictvím brány firewall služby Azure Database for PostgreSQL.

Následující příklad vytvoří pravidlo brány firewall s názvem **AllowMyIP** , které umožňuje připojení z konkrétní IP adresy 192.168.0.1. Nahraďte IP adresu nebo rozsah IP adres, které odpovídají umístění, ze kterého se připojujete.

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Připojení Azure Database for PostgreSQL komunikovat přes port 5432. Pokud se pokusíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 5432 povolen. V tomto scénáři se můžete připojit k serveru jenom v případě, že vaše IT oddělení otevře port 5432.

## <a name="get-the-connection-information"></a>Získání informací o připojení

Pokud se chcete připojit k serveru, budete muset zadat informace o hostiteli a přihlašovací údaje pro přístup. Pomocí následujícího příkladu určete informace o připojení. Poznamenejte si hodnoty pro **FullyQualifiedDomainName** a **AdministratorLogin**.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgresql.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>Připojení k databázi PostgreSQL pomocí nástroje psql

Pokud má klientský počítač nainstalovaný systém PostgreSQL, můžete se připojit k serveru Azure PostgreSQL pomocí místní instance nástroje [psql](https://www.postgresql.org/docs/current/static/app-psql.html). Můžete také získat přístup k předem nainstalované verzi `psql` nástroje příkazového řádku v Azure Cloud Shell tak, že v tomto článku vyberete tlačítko **vyzkoušet** v ukázce kódu. Dalším způsobem, jak získat přístup k Azure Cloud Shell, je vybrat tlačítko **>_** na pravém horním panelu nástrojů v Azure Portal nebo [Shell.Azure.com](https://shell.azure.com/).

1. Připojte se k serveru Azure PostgreSQL pomocí `psql` nástroje příkazového řádku.

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Například následující příkaz se připojí k výchozí databázi s názvem **Postgres** na serveru PostgreSQL `mydemoserver.postgres.database.azure.com` pomocí přihlašovacích údajů pro přístup. Zadejte heslo `<server_admin_password>`, které jste zvolili při zobrazení výzvy k zadání hesla.

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Pokud upřednostňujete použití cesty URL pro připojení k Postgres, adresa URL kódování @ uživatelského jména pomocí `%40` . Například připojovací řetězec pro psql by byl, `psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres`

1. Po připojení k serveru vytvořte na příkazovém řádku prázdnou databázi.

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. Na příkazovém řádku spusťte následující příkaz, který přepne připojení na nově vytvořenou databázi **mypgsqldb**:

   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Vytvoření tabulek v databázi

Teď, když víte, jak se připojit k databázi Azure Database for PostgreSQL, dokončete některé základní úlohy.

Nejdřív vytvoříte tabulku a načtete do ní data. Pojďme vytvořit tabulku, ve které jsou uložené informace o inventáři.

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Načtení dat do tabulek

Teď máte vytvořenou tabulku a můžete do ní vložit data. V otevřeném okně příkazového řádku spusťte následující dotaz, který vloží několik řádků dat.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Tabulka, kterou jste vytvořili dříve, teď obsahuje dva řádky ukázkových dat.

## <a name="query-and-update-the-data-in-the-tables"></a>Dotazování na data a aktualizace dat v tabulkách

Spuštěním následujícího příkazu načtete informace z databázové tabulky.

```sql
SELECT * FROM inventory;
```

Data v tabulce můžete také aktualizovat.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Při načtení dat se řádek příslušným způsobem aktualizuje.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Obnovení databáze k dřívějšímu bodu v čase

Server můžete obnovit k předchozímu bodu v čase. Obnovená data se zkopírují na nový server a stávající server zůstane beze změny. Například pokud je tabulka omylem vyřazena, můžete ji obnovit v čase pouhým odhozením. Pak můžete načíst chybějící tabulku a data z obnovené kopie serveru.

K obnovení serveru použijte `Restore-AzPostgreSqlServer` rutinu PowerShellu.

### <a name="run-the-restore-command"></a>Spuštění příkazu RESTORE

Pokud chcete server obnovit, spusťte z PowerShellu následující příklad.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Když server obnovíte do dřívějšího bodu v čase, vytvoří se nový server. Původní server a jeho databáze ze zadaného časového okamžiku se zkopírují na nový server.

Hodnoty umístění a cenové úrovně obnoveného serveru zůstanou stejné jako u původního serveru.

Po dokončení procesu obnovení Najděte nový server a ověřte, že se data obnovila podle očekávání. Nový server má stejné přihlašovací jméno a heslo správce serveru, které bylo platné pro existující server v době spuštění obnovení. Heslo lze změnit na stránce **Přehled** nového serveru.

Nový server vytvořený během obnovy nemá koncové body služby virtuální sítě, které existovaly na původním serveru. Tato pravidla musí být nastavená samostatně pro nový server. Obnoví se pravidla brány firewall z původního serveru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině serverů. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu serverů. Stiskněte tlačítko *Odstranit* na stránce *Přehled* pro skupinu serverů. Po zobrazení výzvy na místní stránce potvrďte název skupiny serverů a klikněte na tlačítko poslední *Odstranit* .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postup zálohování a obnovení serveru Azure Database for PostgreSQL pomocí prostředí PowerShell](howto-restore-server-powershell.md)
