---
title: 'Rychlý Start: vytvoření serveru-Azure PowerShell-Azure Database for PostgreSQL-Single server'
description: Průvodce rychlým startem k vytvoření serveru Azure Database for PostgreSQL-Single pomocí Azure PowerShell.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 7f04c4c52b2c1e2b347699168b3255d2f809278b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91705292"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-powershell"></a>Rychlý Start: vytvoření serveru Azure Database for PostgreSQL-Single pomocí prostředí PowerShell

V tomto rychlém startu se dozvíte, jak pomocí PowerShellu vytvořit Azure Database for PostgreSQL Server ve skupině prostředků Azure. PowerShell můžete použít k interaktivnímu vytváření a správě prostředků Azure nebo ve skriptech.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud se rozhodnete použít prostředí PowerShell místně, Tento článek vyžaduje, abyste nainstalovali modul AZ PowerShell a připojili se k účtu Azure pomocí rutiny [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) . Další informace o instalaci modulu AZ PowerShell najdete v tématu [Install Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

> [!IMPORTANT]
> I když je modul PowerShell AZ. PostgreSql ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Jakmile je modul PowerShellu AZ. PostgreSql všeobecně dostupný, bude součástí budoucna k tomu, aby vydaná vydání modulu PowerShellu a nativně dostupná v rámci Azure Cloud Shell.

Pokud používáte službu Azure Database for PostgreSQL poprvé, je nutné zaregistrovat poskytovatele prostředků **Microsoft. DBforPostgreSQL** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se prostředky mají fakturovat. Pomocí rutiny [set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) vyberte konkrétní ID předplatného.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pomocí rutiny [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina.

Následující příklad vytvoří skupinu prostředků s názvem **myresourcegroup** v oblasti **západní USA** .

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL

Vytvořte Azure Database for PostgreSQL Server pomocí `New-AzPostgreSqlServer` rutiny. Server může spravovat více databází. Obvykle se pro jednotlivé projekty nebo uživatele používají samostatné databáze.

Následující tabulka obsahuje seznam běžně používaných parametrů a ukázkových hodnot pro `New-AzPostgreSqlServer` rutinu.

|        **Nastavení**         | **Ukázková hodnota** |                                                                                                                                                             **Popis**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                       | mydemoserver     | V Azure vyberte globálně jedinečný název, který identifikuje Azure Database for PostgreSQL Server. Název serveru může obsahovat jenom písmena, číslice a znak spojovníku (-). Všechna zadaná velká písmena se během procesu vytváření automaticky převedou na malá písmena. Musí se skládat ze 3 až 63 znaků. |
| ResourceGroupName          | myresourcegroup  | Zadejte název skupiny prostředků Azure.                                                                                                                                                                                                                                                                                            |
| Skladová jednotka (SKU)                        | GP_Gen5_2        | Název skladové položky. Postupuje podle konvence **cenové úrovně \_ COMPUTE – generace \_ virtuální jádra** ve zkráceném znění. Další informace o parametru SKU naleznete v informacích uvedených v této tabulce.                                                                                                                                           |
| BackupRetentionDay         | 7                | Určuje, jak dlouho se mají uchovávat zálohy. Jednotkou jsou dny. Rozsah je 7 až 35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Povoleno          | Určuje, jestli pro tento server mají nebo nemají být povolené geograficky redundantní zálohy. Tato hodnota se nedá povolit pro servery v cenové úrovni Basic a nedá se změnit po vytvoření serveru. Povolené hodnoty: Enabled, Disabled.                                                                                                      |
| Umístění                   | westus           | Oblast Azure pro server.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Povoleno          | Zda má být pro tento server povolen nebo nikoli protokol SSL. Povolené hodnoty: Enabled, Disabled.                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | Kapacita úložiště serveru (jednotkou jsou megabajty). Platný StorageInMb je minimálně 5120 MB a zvyšuje se o 1024 MB. Další informace o limitech velikosti úložiště najdete v tématu [Azure Database for PostgreSQL cenové úrovně](./concepts-pricing-tiers.md).                                                                               |
| Verze                    | 9,6              | Hlavní verze PostgreSQL.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | Uživatelské jméno pro přihlášení správce. Nemůže být ** azure_superuser **, ** admin **, ** administrátor **, ** root **, ** host ** nebo ** public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Heslo správce uživatele ve formě zabezpečeného řetězce. Musí mít 8 až 128 znaků. Heslo musí obsahovat znaky ze tří z těchto kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice a jiné než alfanumerické znaky.                                       |

Hodnota parametru **SKU** dodržuje cenové úrovně ** \_ \_ virtuální jádra COMPUTE-Generation** , jak je znázorněno v následujících příkladech.

- `-Sku B_Gen5_1` mapuje se na Basic, Gen 5 a 1 vCore. Tato možnost je k dispozici nejmenší SKU.
- `-Sku GP_Gen5_32` se mapuje na úroveň pro obecné účely 5. generace se 32 virtuálními jádry.
- `-Sku MO_Gen5_2` se mapuje na úroveň optimalizovanou pro paměť 5. generace se dvěma virtuálními jádry.

Informace o platných hodnotách **SKU** podle oblasti a pro vrstvy najdete v tématu [Azure Database for PostgreSQL cenové úrovně](./concepts-pricing-tiers.md).

Následující příklad vytvoří server PostgreSQL v oblasti **západní USA** s názvem **mydemoserver** ve skupině prostředků **myresourcegroup** s přihlašovacím jménem správce serveru **myadmin**. Je to Server Gen 5 v cenové úrovni pro obecné účely s povolenými 2 virtuální jádray a geograficky redundantními zálohováními. Heslo pro účet správce serveru PostgreSQL můžete zdokumentovat v prvním řádku příkladu, protože se jedná o heslo.

> [!TIP]
> Název serveru se mapuje na název DNS a v rámci Azure musí být globálně jedinečný.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

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
mydemoserver.postgres.database.azure.com       myadmin
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

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Připojení k serveru PostgreSQL pomocí nástroje pgAdmin

pgAdmin je opensourcový nástroj používaný se systémem PostgreSQL. Nástroj pgAdmin můžete nainstalovat z [webu pgAdmin](https://www.pgadmin.org/). Verze nástroje pgAdmin, kterou používáte, se může lišit od verze používané v tomto rychlém startu. Pokud potřebujete další pokyny, přečtěte si dokumentaci nástroje pgAdmin.

1. Na klientském počítači otevřete aplikaci pgAdmin.

1. Na panelu nástrojů přejděte na **Objekt**, najeďte myší na **Vytvořit** a vyberte **Server**.

1. V dialogovém okně **Vytvořit – server** na kartě **Obecné** zadejte jedinečný popisný název serveru, jako například **mydemoserver**.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/9-pgadmin-create-server.png" alt-text="Karta Obecné":::

1. V dialogovém okně **Vytvořit – server** na kartě **Připojení** vyplňte tabulku nastavení.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/10-pgadmin-create-server.png" alt-text="Karta Obecné":::

    Parametr pgAdmin |Hodnota|Popis
    ---|---|---
    Název nebo adresa hostitele | Název serveru | Hodnota názvu serveru, kterou jste použili dříve při vytváření serveru Azure Database for PostgreSQL. Ukázkový server v příkladu je **mydemoserver.postgres.database.azure.com**. Použijte plně kvalifikovaný název domény (** \* . Postgres.Database.Azure.com**), jak je znázorněno v příkladu. Pokud si název vašeho serveru nepamatujete, získejte informace o připojení pomocí postupu v předchozí části.
    Port | 5432 | Port, který se použije pro připojení k serveru Azure Database for PostgreSQL.
    Databáze údržby | *postgres* | Výchozí systémem vygenerovaný název databáze.
    Uživatelské jméno | Přihlašovací jméno správce serveru | Přihlašovací uživatelské jméno správce serveru, které jste zadali dříve při vytváření serveru Azure Database for PostgreSQL. Pokud si uživatelské jméno nepamatujete, získejte informace o připojení pomocí postupu v předchozí části. Formát je *UserName \@ servername*.
    Heslo | Vaše heslo správce | Heslo, které jste si zvolili při vytváření serveru dříve v tomto rychlém startu.
    Role | Ponechte prázdné. | V tuto chvíli není nutné zadávat název role. Ponechte toto pole prázdné.
    Režim SSL | *Vyžadovat* | Režim TLS/SSL můžete nastavit na kartě SSL v pgAdmin. Ve výchozím nastavení se všechny Azure Database for PostgreSQL servery vytvoří se zapnutým vynucováním TLS. Pokud chcete zapnout vynucování TLS, přečtěte si téma [Konfigurace vynucení TLS](./concepts-ssl-connection-security.md#configure-enforcement-of-tls).

1. Vyberte **Uložit**.

1. V levém podokně **Prohlížeč** rozbalte uzel **Servery**. Vyberte server, například **mydemoserver**. Kliknutím se k němu připojte.

1. Rozbalte uzel serveru a pod ním pak rozbalte **Databáze**. V seznamu by měla být vaše stávající databáze *postgres* a případné další databáze, které jste vytvořili. Na jednom serveru se službou Azure Database for PostgreSQL můžete vytvořit víc databází.

1. Klikněte pravým tlačítkem na **Databáze**, zvolte nabídku **Vytvořit** a pak vyberte **Databáze**.

1. Do pole **Databáze** zadejte libovolný název databáze, například **mypgsqldb2**.

1. Ze seznamu vyberte **Vlastníka** databáze. Zvolte přihlašovací jméno správce serveru, jako je **myadmin** v příkladu.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/11-pgadmin-database.png" alt-text="Karta Obecné":::

1. Vyberte **Uložit** a vytvořte novou prázdnou databázi.

1. V podokně **Prohlížeč** se vytvořená databáze zobrazí v seznamu databází pod názvem vašeho serveru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud prostředky vytvořené v tomto rychlém startu nepotřebujete pro další rychlý Start nebo kurz, můžete je odstranit spuštěním následujícího příkladu.

> [!CAUTION]
> Následující příklad odstraní zadanou skupinu prostředků a všechny prostředky, které jsou v ní obsažené.
> Pokud v zadané skupině prostředků existují prostředky mimo rozsah tohoto rychlého startu, budou také odstraněny.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Pokud chcete odstranit jenom server vytvořený v rámci tohoto rychlého startu bez odstranění skupiny prostředků, použijte `Remove-AzPostgreSqlServer` rutinu.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Návrh Azure Database for PostgreSQL pomocí prostředí PowerShell](tutorial-design-database-using-powershell.md)
