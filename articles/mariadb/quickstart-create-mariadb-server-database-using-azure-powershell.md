---
title: 'Rychlý Start: vytvoření serveru-Azure PowerShell-Azure Database for MariaDB'
description: V tomto rychlém startu se dozvíte, jak pomocí PowerShellu vytvořit Azure Database for MariaDB Server ve skupině prostředků Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 05/26/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 05082ffa891b72b472ed5433282198c61080f073
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999480"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-using-powershell"></a>Rychlý Start: vytvoření serveru Azure Database for MariaDB pomocí prostředí PowerShell

V tomto rychlém startu se dozvíte, jak pomocí PowerShellu vytvořit Azure Database for MariaDB Server ve skupině prostředků Azure. PowerShell můžete použít k interaktivnímu vytváření a správě prostředků Azure nebo ve skriptech.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud se rozhodnete použít prostředí PowerShell místně, Tento článek vyžaduje, abyste nainstalovali modul AZ PowerShell a připojili se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Další informace o instalaci modulu AZ PowerShell najdete v tématu [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> I když je modul PowerShell AZ. MariaDb ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Jakmile je modul PowerShellu AZ. MariaDb všeobecně dostupný, bude součástí budoucna k tomu, aby vydaná vydání modulu PowerShellu a nativně dostupná v rámci Azure Cloud Shell.

Pokud používáte službu Azure Database for MariaDB poprvé, je nutné zaregistrovat poskytovatele prostředků **Microsoft. DBforMariaDB** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMariaDB
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

## <a name="create-an-azure-database-for-mariadb-server"></a>Vytvoření serveru Azure Database for MariaDB

Vytvořte Azure Database for MariaDB Server pomocí `New-AzMariaDbServer` rutiny. Server může spravovat více databází. Obvykle se pro jednotlivé projekty nebo uživatele používají samostatné databáze.

Následující tabulka obsahuje seznam běžně používaných parametrů a ukázkových hodnot pro `New-AzMariaDbServer` rutinu.

|        **Nastavení**         | **Ukázková hodnota** |                                                                                                                                                             **Popis**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                       | mydemoserver     | V Azure vyberte globálně jedinečný název, který identifikuje Azure Database for MariaDB Server. Název serveru může obsahovat jenom písmena, číslice a znak spojovníku (-). Všechna zadaná velká písmena se během procesu vytváření automaticky převedou na malá písmena. Musí se skládat ze 3 až 63 znaků. |
| ResourceGroupName          | myresourcegroup  | Zadejte název skupiny prostředků Azure.                                                                                                                                                                                                                                                                                            |
| Skladová jednotka (SKU)                        | GP_Gen5_2        | Název skladové položky. Postupuje podle konvence **cenové úrovně \_ COMPUTE – generace \_ virtuální jádra** ve zkráceném znění. Další informace o parametru SKU naleznete v informacích uvedených v této tabulce.                                                                                                                                           |
| BackupRetentionDay         | 7                | Určuje, jak dlouho se mají uchovávat zálohy. Jednotkou jsou dny. Rozsah je 7 až 35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Povoleno          | Určuje, jestli pro tento server mají nebo nemají být povolené geograficky redundantní zálohy. Tato hodnota se nedá povolit pro servery v cenové úrovni Basic a nedá se změnit po vytvoření serveru. Povolené hodnoty: Enabled, Disabled.                                                                                                      |
| Umístění                   | westus           | Oblast Azure pro server.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Povoleno          | Zda má být pro tento server povolen nebo nikoli protokol SSL. Povolené hodnoty: Enabled, Disabled.                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | Kapacita úložiště serveru (jednotkou jsou megabajty). Platný StorageInMb je minimálně 5120 MB a zvyšuje se o 1024 MB. Další informace o limitech velikosti úložiště najdete v tématu [Azure Database for MariaDB cenové úrovně](./concepts-pricing-tiers.md).                                                                               |
| Verze                    | 5.7              | Hlavní verze MariaDB                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | Uživatelské jméno pro přihlášení správce. Nemůže být **azure_superuser**, **admin**, **administrátor**, **root**, **host** nebo **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Heslo správce uživatele ve formě zabezpečeného řetězce. Musí mít 8 až 128 znaků. Heslo musí obsahovat znaky ze tří z těchto kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice a jiné než alfanumerické znaky.                                       |

Hodnota parametru **SKU** dodržuje cenové úrovně **\_ \_ virtuální jádra COMPUTE-Generation** , jak je znázorněno v následujících příkladech.

- `-Sku B_Gen5_1` mapuje se na Basic, Gen 5 a 1 vCore. Tato možnost je k dispozici nejmenší SKU.
- `-Sku GP_Gen5_32` se mapuje na úroveň pro obecné účely 5. generace se 32 virtuálními jádry.
- `-Sku MO_Gen5_2` se mapuje na úroveň optimalizovanou pro paměť 5. generace se dvěma virtuálními jádry.

Informace o platných hodnotách **SKU** podle oblasti a pro vrstvy najdete v tématu [Azure Database for MariaDB cenové úrovně](./concepts-pricing-tiers.md).

Následující příklad vytvoří server MariaDB v oblasti **západní USA** s názvem **mydemoserver** ve skupině prostředků **myresourcegroup** s přihlašovacím jménem správce serveru **myadmin**. Je to Server Gen 5 v cenové úrovni pro obecné účely s povolenými 2 virtuální jádray a geograficky redundantními zálohováními. Heslo pro účet správce serveru MariaDB můžete zdokumentovat v prvním řádku příkladu, protože se jedná o heslo.

> [!TIP]
> Název serveru se mapuje na název DNS a v rámci Azure musí být globálně jedinečný.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Zvažte použití cenové úrovně Basic, pokud je pro vaše zatížení vhodné světlé výpočetní prostředky a vstupně-výstupní operace.

> [!IMPORTANT]
> Servery vytvořené v cenové úrovni Basic nelze později škálovat na obecné účely nebo paměťově optimalizované a nelze je geograficky replikovat.

## <a name="configure-a-firewall-rule"></a>Konfigurace pravidla brány firewall

Vytvořte pravidlo brány firewall na úrovni serveru Azure Database for MariaDB pomocí `New-AzMariaDbFirewallRule` rutiny. Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako je například `mysql` Nástroj příkazového řádku nebo MariaDB Workbench, aby se k vašemu serveru připojila prostřednictvím brány firewall služby Azure Database for MariaDB.

Následující příklad vytvoří pravidlo brány firewall s názvem **AllowMyIP** , které umožňuje připojení z konkrétní IP adresy 192.168.0.1. Nahraďte IP adresu nebo rozsah IP adres, které odpovídají umístění, ze kterého se připojujete.

```azurepowershell-interactive
New-AzMariaDbFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Připojení ke službě Azure Database for MariaDB komunikují přes port 3306. Pokud se pokoušíte připojit z podnikové sítě, odchozí provoz přes port 3306 nemusí být povolený. V tomto scénáři se můžete připojit k serveru jenom v případě, že vaše IT oddělení otevře port 3306.

## <a name="configure-ssl-settings"></a>Konfigurace nastavení SSL

Ve výchozím nastavení se připojení SSL mezi serverem a klientskými aplikacemi vynucuje. Tato výchozí hodnota zajišťuje zabezpečení dat _v pohybu_ pomocí šifrování datového proudu prostřednictvím Internetu. Pro účely tohoto rychlého startu zakažte u svého serveru připojení SSL. Další informace najdete v článku o [konfiguraci připojení SSL v aplikaci pro zabezpečené připojení k Azure Database for MariaDB](./howto-configure-ssl.md).

> [!WARNING]
> Pro provozní servery se zakázání protokolu SSL nedoporučuje.

Následující příklad zakáže protokol SSL na vašem serveru Azure Database for MariaDB.

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

## <a name="get-the-connection-information"></a>Získání informací o připojení

Pokud se chcete připojit k serveru, budete muset zadat informace o hostiteli a přihlašovací údaje pro přístup. Pomocí následujícího příkladu určete informace o připojení. Poznamenejte si hodnoty pro **FullyQualifiedDomainName** a **AdministratorLogin**.

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mariadb.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Připojení k serveru pomocí nástroje příkazového řádku MySQL

Připojte se k serveru pomocí `mysql` nástroje příkazového řádku. Pokud si chcete stáhnout a nainstalovat nástroj příkazového řádku, přečtěte si téma [soubory komunity MySQL ke stažení](https://dev.mysql.com/downloads/shell/). Můžete také získat přístup k předem nainstalované verzi `mysql` nástroje příkazového řádku v Azure Cloud Shell tak, že v tomto článku vyberete tlačítko **vyzkoušet** v ukázce kódu. Dalším způsobem, jak získat přístup k Azure Cloud Shell, je vybrat tlačítko **>_** na pravém horním panelu nástrojů v Azure Portal nebo [Shell.Azure.com](https://shell.azure.com/).

1. Připojte se k serveru pomocí `mysql` nástroje příkazového řádku.

   ```azurepowershell-interactive
   mysql -h <servername>.mariadb.database.azure.com -u myadmin@<servername> -p
   ```

1. Zobrazit stav serveru.

   ```sql
   mysql> status
   ```

   ```Output
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: *************
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 64793
   Server version: 5.6.42.0 MariaDB Server

   Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   /usr/bin/mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper

   Connection id:          64793
   Current database:
   Current user:           myadmin@myipaddress
   SSL:                    Cipher in use is ECDHE-RSA-AES256-GCM-SHA384
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.42.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 17 min 4 sec

   Threads: 19  Questions: 482  Slow queries: 0  Opens: 50  Flush tables: 3  Open tables: 12  Queries per second avg: 0.470
   --------------

   mysql>
   ```

Další příkazy najdete v [Referenční příručce k MySQL 5.7 – v kapitole 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-mariadb-workbench"></a>Připojení k serveru pomocí MariaDB Workbench

1. Na klientském počítači spusťte aplikaci MySQL Workbench. Pokud chcete stáhnout a nainstalovat aplikaci MySQL Workbench, přečtěte si téma [Stažení aplikace MySQL Workbench](https://dev.mysql.com/downloads/workbench/).

1. V dialogovém okně pro **nastavení nového připojení** zadejte na kartě **Parametry** následující informace:

   ![nastavení nového připojení](./media/quickstart-create-mariadb-server-database-using-azure-powershell/setup-new-connection.png)

    |    **Nastavení**    |            **Navrhovaná hodnota**            |                      **Popis**                       |
    | ----------------- | ----------------------------------------- | ---------------------------------------------------------- |
    | Název připojení   | My Connection                             | Zadejte popisek pro toto připojení.                        |
    | Způsob připojení | Standardní (TCP/IP)                         | Pro připojení k Azure Database for MariaDB použijte protokol TCP/IP. |
    | Název hostitele          | `mydemoserver.mariadb.database.azure.com` | Název serveru, který jste si dříve poznamenali                           |
    | Port              | 3306                                      | Výchozí port pro MariaDB                                 |
    | Uživatelské jméno          | myadmin@mydemoserver                      | Přihlašovací jméno správce serveru, které jste si poznamenali dříve                |
    | Heslo          | *************                             | Použijte heslo účtu správce, které jste nakonfigurovali dříve.      |

1. Pokud chcete otestovat, jestli jsou parametry správně nakonfigurované, klikněte na tlačítko **Testovat připojení** .

1. Vyberte připojení pro připojení k serveru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud prostředky vytvořené v tomto rychlém startu nepotřebujete pro další rychlý Start nebo kurz, můžete je odstranit spuštěním následujícího příkladu.

> [!CAUTION]
> Následující příklad odstraní zadanou skupinu prostředků a všechny prostředky, které jsou v ní obsažené.
> Pokud v zadané skupině prostředků existují prostředky mimo rozsah tohoto rychlého startu, budou také odstraněny.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Pokud chcete odstranit jenom server vytvořený v rámci tohoto rychlého startu bez odstranění skupiny prostředků, použijte `Remove-AzMariaDbServer` rutinu.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Návrh Azure Database for MariaDB pomocí prostředí PowerShell](tutorial-design-database-using-powershell.md)