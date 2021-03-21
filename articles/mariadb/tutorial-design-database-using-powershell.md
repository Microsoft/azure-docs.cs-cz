---
title: 'Kurz: návrh serveru-Azure PowerShell-Azure Database for MariaDB'
description: V tomto kurzu se dozvíte, jak vytvořit a spravovat Azure Database for MariaDB Server a databázi pomocí prostředí PowerShell.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 05/26/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 9b4500df459e4d4ef67f97dc4fa923988f30401b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664497"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-powershell"></a>Kurz: návrh Azure Database for MariaDB pomocí prostředí PowerShell

Azure Database for MariaDB je služba relačních databází v cloudu Microsoftu založená na databázovém stroji MariaDB Community Edition. V tomto kurzu se naučíte, jak pomocí PowerShellu a dalších nástrojů:

> [!div class="checklist"]
> - Vytvoření databáze Azure Database for MariaDB
> - Konfigurace brány firewall serveru
> - Vytvoření databáze pomocí [Nástroje pro příkazový řádek MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)
> - Načtení ukázkových dat
> - Dotazování dat
> - Aktualizace dat
> - Obnovení dat

## <a name="prerequisites"></a>Předpoklady

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

Následující příklad vytvoří server MariaDB v oblasti **západní USA** s názvem **mydemoserver** ve skupině prostředků **myresourcegroup** s přihlašovacím jménem správce serveru **myadmin**. Je to Server Gen 5 v cenové úrovni pro obecné účely s povolenými 2 virtuální jádray a geograficky redundantními zálohováními. Heslo pro účet správce serveru MariaDB můžete zdokumentovat v prvním řádku příkladu, protože se jedná o heslo.

> [!TIP]
> Název serveru se mapuje na název DNS a v rámci Azure musí být globálně jedinečný.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Hodnota parametru **SKU** dodržuje cenové úrovně **\_ \_ virtuální jádra COMPUTE-Generation** , jak je znázorněno v následujících příkladech.

- `-Sku B_Gen5_1` mapuje se na Basic, Gen 5 a 1 vCore. Tato možnost je k dispozici nejmenší SKU.
- `-Sku GP_Gen5_32` se mapuje na úroveň pro obecné účely 5. generace se 32 virtuálními jádry.
- `-Sku MO_Gen5_2` se mapuje na úroveň optimalizovanou pro paměť 5. generace se dvěma virtuálními jádry.

Informace o platných hodnotách **SKU** podle oblasti a pro vrstvy najdete v tématu [Azure Database for MariaDB cenové úrovně](./concepts-pricing-tiers.md).

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

Připojte se k serveru pomocí `mysql` nástroje příkazového řádku. Pokud si chcete stáhnout a nainstalovat nástroj příkazového řádku, přečtěte si téma [stažení komunity MariaDB](https://dev.mysql.com/downloads/shell/). Můžete také získat přístup k předem nainstalované verzi `mysql` nástroje příkazového řádku v Azure Cloud Shell tak, že v tomto článku vyberete tlačítko **vyzkoušet** v ukázce kódu. Dalším způsobem, jak získat přístup k Azure Cloud Shell, je vybrat tlačítko **>_** na pravém horním panelu nástrojů v Azure Portal nebo [Shell.Azure.com](https://shell.azure.com/).

```azurepowershell-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-database"></a>Vytvoření databáze

Po připojení k serveru vytvořte prázdnou databázi.

```sql
mysql> CREATE DATABASE mysampledb;
```

Na příkazovém řádku spusťte následující příkaz pro přepnutí připojení na tuto nově vytvořenou databázi:

```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Vytvoření tabulek v databázi

Teď, když víte, jak se připojit k databázi Azure Database for MariaDB, dokončete některé základní úlohy.

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

K obnovení serveru použijte `Restore-AzMariaDbServer` rutinu PowerShellu.

### <a name="run-the-restore-command"></a>Spuštění příkazu RESTORE

Pokud chcete server obnovit, spusťte z PowerShellu následující příklad.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Když server obnovíte do dřívějšího bodu v čase, vytvoří se nový server. Původní server a jeho databáze ze zadaného časového okamžiku se zkopírují na nový server.

Hodnoty umístění a cenové úrovně obnoveného serveru zůstanou stejné jako u původního serveru.

Po dokončení procesu obnovení Najděte nový server a ověřte, že se data obnovila podle očekávání. Nový server má stejné přihlašovací jméno a heslo správce serveru, které bylo platné pro existující server v době spuštění obnovení. Heslo lze změnit na stránce **Přehled** nového serveru.

Nový server vytvořený během obnovy nemá koncové body služby virtuální sítě, které existovaly na původním serveru. Tato pravidla musí být nastavená samostatně pro nový server. Obnoví se pravidla brány firewall z původního serveru.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postup zálohování a obnovení serveru Azure Database for MariaDB pomocí prostředí PowerShell](howto-restore-server-powershell.md)