---
title: Konfigurace transakční replikace mezi spravovanou instancí Azure SQL a SQL Server
description: Kurz, ve kterém se konfiguruje replikace mezi spravovanou instancí vydavatele, distributorem spravované instance a předplatitelem SQL Server na virtuálním počítači Azure, a to spolu s potřebnými síťovými součástmi, jako je například privátní zóna DNS a partnerský vztah virtuální sítě.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/21/2019
ms.openlocfilehash: d2b45f5b51f4656294632aa46f679a7a09c06ed3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94593922"
---
# <a name="tutorial-configure-transactional-replication-between-azure-sql-managed-instance-and-sql-server"></a>Kurz: Konfigurace transakční replikace mezi spravovanou instancí Azure SQL a SQL Server
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Transakční replikace umožňuje replikovat data z jedné databáze do jiné, jejímž hostitelem je SQL Server nebo [Azure SQL Managed instance](sql-managed-instance-paas-overview.md). Spravovaná instance SQL může být Vydavatel, distributor nebo předplatitel v topologii replikace. V tématu [Konfigurace transakční replikace](replication-transactional-overview.md#common-configurations) pro dostupné konfigurace. 

Transakční replikace je aktuálně ve verzi Public Preview pro spravovanou instanci SQL. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Konfigurace spravované instance jako vydavatele replikace.
> - Nakonfigurujte spravovanou instanci jako distributora replikace.
> - Nakonfigurujte SQL Server jako předplatitele.

![Replikace mezi vydavatelem spravované instance, distributorem spravované instance a předplatitelem SQL Server](./media/replication-two-instances-and-sql-server-configure-tutorial/sqlmi-to-sql-replication.png)

Tento kurz je určený pro zkušené publikum a předpokládá, že uživatel je obeznámen s nasazením a připojením ke spravovaným instancím a SQL Server virtuálním počítačům v rámci Azure. 


> [!NOTE]
> Tento článek popisuje použití [transakční replikace](/sql/relational-databases/replication/transactional/transactional-replication) ve spravované instanci Azure SQL. Nesouvisí se [skupinami převzetí služeb při selhání](../database/auto-failover-group-overview.md), což je funkce spravované instance Azure SQL, která umožňuje vytvářet kompletní čitelné repliky jednotlivých instancí. Při konfiguraci [replikace transakcí se skupinami převzetí služeb při selhání](replication-transactional-overview.md#with-failover-groups)jsou k dispozici další předpoklady.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu se ujistěte, že máte následující požadavky:

- [Předplatné Azure](https://azure.microsoft.com/free/)
- Možnosti nasazení dvou spravovaných instancí v rámci stejné virtuální sítě.
- Předplatitel SQL Server, buď místně, nebo na virtuálním počítači Azure. V tomto kurzu se používá virtuální počítač Azure.  
- [SQL Server Management Studio (SSMS) 18,0 nebo vyšší](/sql/ssms/download-sql-server-management-studio-ssms).
- Nejnovější verze [Azure PowerShell](/powershell/azure/install-az-ps).
- Porty 445 a 1433 umožňují provoz SQL v bráně firewall Azure i bráně Windows Firewall.

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Pomocí následujícího fragmentu kódu PowerShellu vytvořte novou skupinu prostředků:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="create-two-managed-instances"></a>Vytvořit dvě spravované instance

Pomocí [Azure Portal](https://portal.azure.com)vytvořte v rámci této nové skupiny prostředků dvě spravované instance.

- Název spravované instance vydavatele by měl být `sql-mi-publisher` (spolu s několika znaky pro náhodnost) a název virtuální sítě by měl být `vnet-sql-mi-publisher` .
- Název spravované instance distributora by měl být `sql-mi-distributor` (spolu s několika znaky pro náhodnost) a měl by být _ve stejné virtuální síti jako spravovaná instance vydavatele_.

   ![Použití virtuální sítě vydavatele pro distributora](./media/replication-two-instances-and-sql-server-configure-tutorial/use-same-vnet-for-distributor.png)

Další informace o vytváření spravované instance najdete v tématu [Vytvoření spravované instance na portálu](instance-create-quickstart.md).

  > [!NOTE]
  > V zájmu jednoduchosti a vzhledem k tomu, že se jedná o nejběžnější konfiguraci, tento kurz navrhuje umístění spravované instance distributora ve stejné virtuální síti jako vydavatel. Distributor je však možné vytvořit v samostatné virtuální síti. Provedete to tak, že budete muset nakonfigurovat partnerský vztah virtuálních sítí mezi virtuálními sítěmi vydavatele a distributora a potom nakonfigurovat partnerský vztah virtuálních sítí mezi virtuálními sítěmi distributora a odběratele.

## <a name="create-a-sql-server-vm"></a>Vytvoření virtuálního počítače s SQL Server

Vytvořte virtuální počítač s SQL Server pomocí [Azure Portal](https://portal.azure.com). Virtuální počítač s SQL Server by měl mít následující vlastnosti:

- Název: `sql-vm-sub`
- Obrázek: SQL Server 2016 nebo vyšší
- Skupina prostředků: stejná jako spravovaná instance
- Virtuální síť: `sql-vm-sub-vnet`

Další informace o nasazení SQL Server virtuálního počítače do Azure najdete v tématu [rychlý Start: Vytvoření virtuálního počítače s SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md).

## <a name="configure-vnet-peering"></a>Konfigurace partnerského vztahu virtuální sítě

Nakonfigurujte partnerský vztah virtuálních sítí tak, aby umožňoval komunikaci mezi virtuální sítí dvou spravovaných instancí a virtuální sítí SQL Server. Provedete to tak, že použijete tento fragment kódu PowerShellu:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VNet peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VNet peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

Po navázání partnerského vztahu virtuálních sítí otestujte připojení spuštěním SQL Server Management Studio (SSMS) na SQL Server a připojením ke spravovaným instancím. Další informace o připojení ke spravované instanci pomocí SSMS najdete v tématu [použití SSMS pro připojení ke spravované instanci SQL](point-to-site-p2s-configure.md#connect-with-ssms).

![Test připojení ke spravovaným instancím](./media/replication-two-instances-and-sql-server-configure-tutorial/test-connectivity-to-mi.png)

## <a name="create-a-private-dns-zone"></a>Vytvoření privátní zóny DNS

Privátní zóna DNS umožňuje směrování DNS mezi spravovanými instancemi a SQL Server.

### <a name="create-a-private-dns-zone"></a>Vytvoření privátní zóny DNS

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Pokud chcete vytvořit nový prostředek Azure, vyberte **vytvořit prostředek** .
1. Hledat `private dns zone` na Azure Marketplace.
1. Zvolte prostředek **zóny privátní DNS** publikovaný Microsoftem a pak vyberte **vytvořit** a vytvořte zónu DNS.
1. V rozevíracím seznamu vyberte předplatné a skupinu prostředků.
1. Zadejte libovolný název zóny DNS, například `repldns.com` .

   ![Vytvořit privátní zónu DNS](./media/replication-two-instances-and-sql-server-configure-tutorial/create-private-dns-zone.png)

1. Vyberte **Zkontrolovat a vytvořit**. Zkontrolujte parametry vaší privátní zóny DNS a pak vyberte **vytvořit** a vytvořte prostředek.

### <a name="create-an-a-record"></a>Vytvoření záznamu A

1. Přejít do nové **zóny privátní DNS** a vyberte **Přehled**.
1. Vyberte **+ Sada záznamů** a vytvořte nový záznam.
1. Zadejte název vašeho virtuálního počítače SQL Server a privátní interní IP adresu.

   ![Konfigurace záznamu A](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-a-record.png)

1. Vyberte **OK** a vytvořte záznam A.

### <a name="link-the-virtual-network"></a>Propojit virtuální síť

1. Přejdete do nové **zóny privátní DNS** a vyberete **odkazy virtuální sítě**.
1. Vyberte **+ Přidat**.
1. Zadejte název odkazu, například `Pub-link` .
1. V rozevíracím seznamu vyberte své předplatné a pak vyberte virtuální síť pro spravovanou instanci vydavatele.
1. Zaškrtněte políčko vedle **Povolit automatickou registraci**.

   ![Vytvořit propojení virtuální sítě](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-vnet-link.png)

1. Vyberte **OK** a propojte svoji virtuální síť.
1. Opakováním těchto kroků přidejte odkaz pro virtuální síť předplatitele s názvem, jako je například `Sub-link` .

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

[Vytvořte účet úložiště Azure](../../storage/common/storage-account-create.md#create-a-storage-account) pro pracovní adresář a pak vytvořte [sdílenou složku](../../storage/files/storage-how-to-create-file-share.md) v rámci účtu úložiště.

Zkopírujte cestu ke sdílené složce ve formátu: `\\storage-account-name.file.core.windows.net\file-share-name`

Příklad: `\\replstorage.file.core.windows.net\replshare`

Zkopírujte připojovací řetězec přístupového klíče k úložišti ve formátu: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Příklad: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).

## <a name="create-a-database"></a>Vytvoření databáze

Vytvoří novou databázi na spravované instanci vydavatele. To můžete provést pomocí těchto kroků:

1. Spustí SQL Server Management Studio na SQL Server.
1. Připojte se ke `sql-mi-publisher` spravované instanci.
1. Otevřete **nové okno dotazu** a spusťte následující dotaz T-SQL k vytvoření databáze.

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
   ID INT NOT NULL PRIMARY KEY,
   c1 VARCHAR(100) NOT NULL,
   dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="configure-distribution"></a>Konfigurovat distribuci

Jakmile se naváže připojení a máte ukázkovou databázi, můžete nakonfigurovat distribuci na `sql-mi-distributor` spravované instanci. To můžete provést pomocí těchto kroků:

1. Spustí SQL Server Management Studio na SQL Server.
1. Připojte se ke `sql-mi-distributor` spravované instanci.
1. Otevřete **nové okno dotazu** a spusťte následující kód Transact-SQL ke konfiguraci distribuce na spravované instanci distributora:

   ```sql
   EXEC sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   
   EXEC sp_adddistributiondb @database = N'distribution'
   
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Nezapomeňte pro parametr použít jenom zpětná lomítka ( `\` ) @working_directory . Použití lomítka ( `/` ) může při připojování ke sdílené složce způsobit chybu.

1. Připojte se ke `sql-mi-publisher` spravované instanci.
1. Otevřete **nové okno dotazu** a spusťte následující kód Transact-SQL k registraci distributora na vydavateli:

   ```sql
   Use MASTER
   EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   ```

## <a name="create-the-publication"></a>Vytvoření publikace

Po nakonfigurování distribuce teď můžete vytvořit publikaci. To můžete provést pomocí těchto kroků:

1. Spustí SQL Server Management Studio na SQL Server.
1. Připojte se ke `sql-mi-publisher` spravované instanci.
1. V **Průzkumník objektů** rozbalte uzel **replikace** a klikněte pravým tlačítkem myši na složku **místní publikace** . Vybrat **novou publikaci...**.
1. Kliknutím na tlačítko **Další** přejdete za úvodní stránku.
1. Na stránce **databáze publikace** vyberte `ReplTutorial` databázi, kterou jste předtím vytvořili. Vyberte **Další**.
1. Na stránce **Typ publikace** vyberte **transakční publikace**. Vyberte **Další**.
1. Na stránce **články** zaškrtněte políčko vedle pole **tabulky**. Vyberte **Další**.
1. Na stránce **Filtrovat řádky tabulky** vyberte **Další** bez přidání jakýchkoli filtrů.
1. Na stránce **Agent snímku** zaškrtněte políčko vedle **vytvořit snímek hned a nechte snímek dostupný pro inicializaci předplatných**. Vyberte **Další**.
1. Na stránce **zabezpečení agenta** vyberte **nastavení zabezpečení...**. Zadejte SQL Server přihlašovací přihlašovací údaje pro použití pro agenta snímku a připojení k vydavateli. Výběrem **OK** zavřete stránku **zabezpečení agenta snímků** . Vyberte **Další**.

   ![Konfigurace zabezpečení agenta snímků](./media/replication-two-instances-and-sql-server-configure-tutorial/snapshot-agent-security.png)

1. Na stránce **Akce průvodce** vyberte možnost **vytvořit publikaci** a (volitelně) zvolit, že se má **vygenerovat soubor skriptu s postupem vytvoření publikace** , pokud chcete tento skript Uložit pro pozdější verzi.
1. Na stránce **dokončení průvodce zadejte** název publikace `ReplTest` a vyberte **Další** pro vytvoření publikace.
1. Po vytvoření publikace aktualizujte **replikační** uzel v **Průzkumník objektů** a rozbalte **místní publikace** . tím se zobrazí nová publikace.

## <a name="create-the-subscription"></a>Vytvoření předplatného

Po vytvoření publikace můžete vytvořit předplatné. To můžete provést pomocí těchto kroků:

1. Spustí SQL Server Management Studio na SQL Server.
1. Připojte se ke `sql-mi-publisher` spravované instanci.
1. Otevřete **nové okno dotazu** a spusťte následující kód Transact-SQL pro přidání agenta pro odběr a distribuci. Jako součást názvu předplatitele použijte DNS.

```sql
use [ReplTutorial]
exec sp_addsubscription
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub',
@subscription_type = N'Push',
@sync_type = N'automatic',
@article = N'all',
@update_mode = N'read only',
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub',
@job_login = N'azureuser',
@job_password = '<Complex Password>',
@subscriber_security_mode = 0,
@subscriber_login = N'azureuser',
@subscriber_password = '<Complex Password>',
@dts_package_location = N'Distributor'
GO
```

## <a name="test-replication"></a>Test replikace

Po dokončení konfigurace replikace ji můžete otestovat vložením nových položek na vydavatele a sledováním změn, které se šíří do odběratele.

Spusťte následující fragment T-SQL pro zobrazení řádků v předplatiteli:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Spusťte následující fragment T-SQL pro vložení dalších řádků na vydavateli a potom znovu zkontrolujte řádky předplatitele.

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. Přejděte do skupiny prostředků v [Azure Portal](https://portal.azure.com).
1. Vyberte spravované instance a pak vyberte **Odstranit**. `yes`Do textového pole zadejte a potvrďte, že chcete odstranit prostředek, a pak vyberte **Odstranit**. Dokončení tohoto procesu může nějakou dobu trvat na pozadí a až do dokončení, nebudete moci odstranit *virtuální cluster* ani žádné jiné závislé prostředky. Sledujte odstranění na kartě **aktivita** a potvrďte, že se vaše spravovaná instance odstranila.
1. Po odstranění spravované instance odstraňte *virtuální cluster* tak, že ho vyberete ve vaší skupině prostředků, a pak zvolíte **Odstranit**. `yes`Do textového pole zadejte a potvrďte, že chcete odstranit prostředek, a pak vyberte **Odstranit**.
1. Odstraňte všechny zbývající prostředky. `yes`Do textového pole zadejte a potvrďte, že chcete odstranit prostředek, a pak vyberte **Odstranit**.
1. Odstraňte skupinu prostředků výběrem možnosti **Odstranit skupinu prostředků**, zadáním názvu skupiny prostředků `myResourceGroup` a pak výběrem možnosti **Odstranit**.

## <a name="known-errors"></a>Známé chyby

### <a name="windows-logins-are-not-supported"></a>Přihlášení Windows nejsou podporovaná.

`Exception Message: Windows logins are not supported in this version of SQL Server.`

Agent byl nakonfigurován s přihlášením systému Windows a musí místo něj použít SQL Server přihlášení. Pomocí stránky **zabezpečení agenta** ve **vlastnostech publikace** změňte přihlašovací údaje na SQL Server přihlašovací jméno.

### <a name="failed-to-connect-to-azure-storage"></a>Selhání připojení ke službě Azure Storage

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 získal Azure Storage připojovací řetězec pro replstorage 2019-11-19 02:21:05.07 připojení ke službě Azure File Storage ' \\ replstorage. File. Core. Windows. net\replshare ' 2019-11-19 02:21:31.21 se nepodařilo připojit k Azure Storage ' ' s chybou operačního systému: 53.

Pravděpodobnou příčinou je to, že port 445 je uzavřený buď v bráně firewall Azure, bráně Windows Firewall, nebo v obou.

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

Tato chyba může způsobit použití lomítka místo zpětného lomítka v cestě k souboru pro sdílenou složku.
  
  - To je v pořádku: `\\replstorage.file.core.windows.net\replshare`
  - Příčinou může být Chyba operačního systému 55: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Nepovedlo se připojit k předplatiteli.

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Možná řešení:

- Ujistěte se, že je port 1433 otevřený.
- Zajistěte, aby byl na odběrateli povolen protokol TCP/IP.
- Potvrďte, že se název DNS použil při vytváření odběratele.
- Ověřte, že jsou vaše virtuální sítě správně propojené v privátní zóně DNS.
- Ověřte, zda je záznam A správně nakonfigurován.
- Ověřte, že je správně nakonfigurovaný partnerský vztah virtuálních sítí.

### <a name="no-publications-to-which-you-can-subscribe"></a>Žádné publikace, ke kterým se můžete přihlásit k odběru

Pokud přidáváte nové předplatné pomocí průvodce **novým odběrem** , můžete na stránce **publikace** zjistit, že v seznamu dostupné možnosti nejsou žádné databáze a publikace, a může se zobrazit následující chybová zpráva:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`

I když je možné, že tato chybová zpráva je přesná a na vydavateli, ke kterému jste se připojili, nejsou k dispozici publikace, nebo nemáte dostatečná oprávnění, může být tato chyba také způsobena starší verzí SQL Server Management Studio. Pokuste se upgradovat na SQL Server Management Studio 18,0 nebo vyšší, abyste toto pravidlo vyzkoušeli jako hlavní příčinu.

## <a name="next-steps"></a>Další kroky

### <a name="enable-security-features"></a>Povolit funkce zabezpečení

Úplný seznam způsobů zabezpečení databáze najdete v článku [co je Azure SQL Managed instance?](sql-managed-instance-paas-overview.md#advanced-security-and-compliance) . Projednávají se následující funkce zabezpečení:

- [Auditování spravované instance SQL](auditing-configure.md)
- [Funkce Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detekce hrozeb](threat-detection-configure.md)
- [Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking)
- [Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security)
- [Transparentní šifrování dat (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>Možnosti spravované instance SQL

Úplný přehled funkcí spravované instance najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Možnosti spravované instance SQL](sql-managed-instance-paas-overview.md)