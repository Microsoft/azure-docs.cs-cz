---
title: Konfigurace transakční replikace mezi dvěma spravovanými instancemi a SQL Server
description: Kurz, ve kterém se konfiguruje replikace mezi spravovanou instancí vydavatele, distributorem spravované instance a předplatitelem SQL Server na virtuálním počítači Azure spolu s potřebnými síťovými součástmi, jako jsou privátní zóny DNS a partnerské vztahy VPN.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: f61c99db3f2c2569705e3a2df4b7134fdd29065a
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614906"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>Kurz: Konfigurace transakční replikace mezi dvěma spravovanými instancemi a SQL Server


V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Konfigurace spravované instance jako vydavatele replikace. 
> - Nakonfigurujte spravovanou instanci jako distributora replikace. 
> - Nakonfigurujte SQL Server jako předplatitele. 

![Replikace mezi SQL MI Pub, SQL MI DIST a SQL Server sub](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

Tento kurz je určený pro zkušené publikum a předpokládá, že uživatel je obeznámen s nasazením a připojením ke spravovaným instancím a s SQL Servermi virtuálními počítači v rámci Azure. V takovém případě jsou některé kroky v tomto kurzu vyleskované. 

Další informace najdete v článcích [přehled Azure SQL Database spravované instance](sql-database-managed-instance-index.yml), [Možnosti](sql-database-managed-instance.md)a [transakční replikace SQL](sql-database-managed-instance-transactional-replication.md) .

Chcete-li nakonfigurovat replikaci mezi vydavatelem spravované instance a odběratelem spravované instance, přečtěte si téma [Konfigurace transakční replikace mezi dvěma spravovanými instancemi](replication-with-sql-database-managed-instance.md). 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu se ujistěte, že máte následující požadavky:

- [Předplatného Azure](https://azure.microsoft.com/free/). 
- Možnosti nasazení dvou spravovaných instancí v rámci stejné virtuální sítě. 
- SQL Server předplatitelem, ať už místní, nebo virtuální počítač Azure. V tomto kurzu se používá virtuální počítač Azure.  
- [SQL Server Management Studio (SSMS) 18,0 nebo vyšší](/sql/ssms/download-sql-server-management-studio-ssms).
- Nejnovější verze [Azure PowerShellu](/powershell/azure/install-az-ps?view=azps-1.7.0).
- Porty 445 a 1433 umožňují provoz SQL na Azure Firewall i bráně Windows Firewall. 

## <a name="1---create-the-resource-group"></a>1\. Vytvoření skupiny prostředků
Pomocí následujícího fragmentu kódu PowerShellu vytvořte novou skupinu prostředků:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2\. vytvoření dvou spravovaných instancí
Pomocí [Azure Portal](https://portal.azure.com)vytvořte v rámci této nové skupiny prostředků dvě spravované instance. 

- Název spravované instance vydavatele by měl být: `sql-mi-publisher` (spolu s několika znaky pro náhodnost) a název virtuální sítě by měl být `vnet-sql-mi-publisher`.
- Název spravované instance distributora by měl: `sql-mi-distributor` (spolu s několika znaky pro náhodnost) a musí být _ve stejné virtuální síti jako spravovaná instance vydavatele_.

   ![Použití virtuální sítě vydavatele pro distributora](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

Další informace o vytváření spravované instance najdete v tématu [Vytvoření spravované instance na portálu](sql-database-managed-instance-get-started.md) .

  > [!NOTE]
  > V zájmu jednoduchosti a vzhledem k tomu, že se jedná o nejběžnější konfiguraci, tento kurz navrhuje umístění spravované instance distributora ve stejné virtuální síti jako vydavatel. Distributor je však možné vytvořit v samostatné virtuální síti. Provedete to tak, že budete muset nakonfigurovat partnerský vztah VPN mezi virtuálními sítěmi vydavatele a distributora a potom nakonfigurovat partnerský vztah VPN mezi virtuálními sítěmi distributora a odběratele. 

## <a name="3---create-a-sql-server-vm"></a>3\. Vytvoření virtuálního počítače s SQL Server
Vytvořte virtuální počítač s SQL Server pomocí [Azure Portal](https://portal.azure.com). Virtuální počítač s SQL Server by měl mít následující vlastnosti:

- Název: `sql-vm-sub`
- Obrázek: SQL Server 2016 nebo vyšší
- Skupina prostředků: stejná jako spravovaná instance
- Virtuální síť: `sql-vm-sub-vnet` 

Další informace o nasazení SQL Server virtuálního počítače do Azure najdete v tématu [rychlý Start: vytvoření SQL Server virtuálního počítače](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md).

## <a name="4---configure-vpn-peering"></a>4\. konfigurace partnerského vztahu VPN
Nakonfigurujte partnerský vztah VPN tak, aby umožňoval komunikaci mezi virtuální sítí dvou spravovaných instancí a virtuální sítí SQL Server. Provedete to tak, že použijete tento fragment kódu PowerShellu:

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

# Configure VPN peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VPN peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

Po navázání partnerského vztahu VPN, otestujte připojení tak, že na SQL Server spustíte SQL Server Management Studio (SSMS) a připojíte se ke spravovaným instancím. Další informace o připojení ke spravované instanci pomocí SSMS najdete v tématu [použití SSMS pro připojení k mi](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance). 

![Test připojení ke spravovaným instancím](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5 – Vytvoření privátní zóny DNS

Privátní zóna DNS umožňuje směrování DNS mezi spravovanými instancemi a SQL Server. 

### <a name="create-private-dns-zone"></a>Vytvořit privátní zónu DNS
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Pokud chcete vytvořit nový prostředek Azure, vyberte **vytvořit prostředek** . 
1. Vyhledejte `private dns zone` v Azure Marketplace. 
1. Zvolte prostředek **zóny privátní DNS** publikovaný Microsoftem a pak vyberte **vytvořit** a vytvořte zónu DNS. 
1. V rozevíracím seznamu vyberte předplatné a skupinu prostředků. 
1. Zadejte libovolný název zóny DNS, například `repldns.com`. 

   ![Vytvořit privátní zónu DNS](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. Vyberte **Zkontrolovat a vytvořit**. Zkontrolujte parametry vaší privátní zóny DNS a pak vyberte **vytvořit** a vytvořte prostředek. 

### <a name="create-a-record"></a>Vytvoření záznamu

1. Přejít do nové **zóny privátní DNS** a vyberte **Přehled**. 
1. Vyberte **+ Sada záznamů** a vytvořte nový záznam a. 
1. Zadejte název vašeho virtuálního počítače SQL Server a privátní interní IP adresu. 

   ![Konfigurace záznamu](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. Vyberte **OK** a vytvořte záznam A. 

### <a name="link-the-virtual-network"></a>Propojit virtuální síť

1. Přejdete do nové **zóny privátní DNS** a vyberete **odkazy virtuální sítě**. 
1. Vyberte **+ Přidat**. 
1. Zadejte název odkazu, například `Pub-link`. 
1. V rozevíracím seznamu vyberte své předplatné a pak vyberte virtuální síť pro spravovanou instanci vydavatele. 
1. Zaškrtněte políčko vedle **Povolit automatickou registraci**. 

   ![Vytvořit propojení virtuální sítě](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. Vyberte **OK** a propojte svoji virtuální síť. 
1. Zopakováním těchto kroků přidejte odkaz pro virtuální síť předplatitele s názvem, například `Sub-link`. 


## <a name="6---create-azure-storage-account"></a>6\. vytvoření účtu Azure Storage

[Vytvořte účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) pro pracovní adresář a pak vytvořte [sdílenou složku](../storage/files/storage-how-to-create-file-share.md) v rámci účtu úložiště. 

Zkopírujte cestu ke sdílené složce ve formátu: `\\storage-account-name.file.core.windows.net\file-share-name`   

Příklad: `\\replstorage.file.core.windows.net\replshare`

Zkopírujte připojovací řetězec přístupového klíče úložiště ve formátu: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

Příklad: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md). 


## <a name="7---create-a-database"></a>7\. vytvoření databáze
Vytvoří novou databázi na vydavateli MI. Chcete-li to provést, postupujte takto:

1. Na SQL Server spusťte SQL Server Management Studio (SSMS). 
1. Připojte se ke spravované instanci `sql-mi-publisher`. 
1. Otevřete **nové okno dotazu** a spusťte následující dotaz T-SQL k vytvoření databáze:

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT *FROM sys.sysdatabases WHERE name = 'ReplTutorial')
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

## <a name="8---configure-distribution"></a>8\. konfigurace distribuce 
Jakmile se naváže připojení a máte ukázkovou databázi, můžete nakonfigurovat distribuci na spravované instanci `sql-mi-distributor`. Chcete-li to provést, postupujte takto:

1. Na SQL Server spusťte SQL Server Management Studio (SSMS). 
1. Připojte se ke spravované instanci `sql-mi-distributor`. 
1. Otevřete **nové okno dotazu** a spusťte následující kód Transact-SQL ke konfiguraci distribuce na spravované instanci distributora: 

   ```sql
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
   > Nezapomeňte pro parametr @working_directory používat jenom zpětná lomítka (`\`). Použití lomítka (`/`) může při připojování ke sdílené složce způsobit chybu. 

1. Připojte se ke spravované instanci `sql-mi-publisher`. 
1. Otevřete **nové okno dotazu** a spusťte následující kód Transact-SQL k registraci distributora na vydavateli: 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9\. vytvoření publikace
Po nakonfigurování distribuce teď můžete vytvořit publikaci. Chcete-li to provést, postupujte takto: 

1. Na SQL Server spusťte SQL Server Management Studio (SSMS). 
1. Připojte se ke spravované instanci `sql-mi-publisher`. 
1. V **Průzkumník objektů**rozbalte uzel **replikace** a klikněte pravým tlačítkem myši na složku **místní publikace** . Vybrat **novou publikaci...** . 
1. Kliknutím na tlačítko **Další** přejdete za úvodní stránku. 
1. Na stránce **databáze publikace** vyberte databázi `ReplTutorial`, kterou jste předtím vytvořili. Vyberte **Next** (Další). 
1. Na stránce **Typ publikace** vyberte **transakční publikace**. Vyberte **Next** (Další). 
1. Na stránce **články** zaškrtněte políčko vedle pole **tabulky**. Vyberte **Next** (Další). 
1. Na stránce **Filtrovat řádky tabulky** vyberte **Další** bez přidání jakýchkoli filtrů. 
1. Na stránce **Agent snímku** zaškrtněte políčko vedle **vytvořit snímek hned a nechte snímek dostupný pro inicializaci předplatných**. Vyberte **Next** (Další). 
1. Na stránce **zabezpečení agenta** vyberte **nastavení zabezpečení.** .. Zadejte SQL Server přihlašovací přihlašovací údaje pro použití pro agenta snímku a připojení k vydavateli. Výběrem **OK** zavřete stránku **zabezpečení agenta snímků** . Vyberte **Next** (Další). 

   ![Konfigurace zabezpečení agenta snímků](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. Na stránce **Akce průvodce** vyberte možnost **vytvořit publikaci** a (volitelně) zvolit, že se má **vygenerovat soubor skriptu s postupem vytvoření publikace** , pokud chcete tento skript Uložit pro pozdější verzi. 
1. Na stránce **dokončení průvodce zadejte** název publikace `ReplTest` a kliknutím na tlačítko **Další** vytvořte publikaci. 
1. Po vytvoření publikace aktualizujte **replikační** uzel v **Průzkumník objektů** a rozbalte **místní publikace** . tím se zobrazí nová publikace. 


## <a name="10---create-the-subscription"></a>10. vytvoření odběru 

Po vytvoření publikace můžete vytvořit předplatné. Chcete-li to provést, postupujte takto: 

1. Na SQL Server spusťte SQL Server Management Studio (SSMS). 
1. Připojte se ke spravované instanci `sql-mi-publisher`. 
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

## <a name="11---test-replication"></a>11. testování replikace 

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
1. Vyberte spravované instance a pak vyberte **Odstranit**. Do textového pole zadejte `yes` a potvrďte tak, že chcete odstranit prostředek, a pak vyberte **Odstranit**. Dokončení tohoto procesu může nějakou dobu trvat na pozadí a až do té doby, nebudete moct *virtuální cluster* ani žádné jiné závislé prostředky odstranit. Sledujte odstranění na kartě aktivita a potvrďte, že se vaše spravovaná instance odstranila. 
1. Po odstranění spravované instance odstraňte *virtuální cluster* tak, že ho vyberete ve vaší skupině prostředků, a pak zvolíte **Odstranit**. Do textového pole zadejte `yes` a potvrďte tak, že chcete odstranit prostředek, a pak vyberte **Odstranit**. 
1. Odstraňte všechny zbývající prostředky. Do textového pole zadejte `yes` a potvrďte tak, že chcete odstranit prostředek, a pak vyberte **Odstranit**. 
1. Odstraňte skupinu prostředků výběrem možnosti **Odstranit skupinu prostředků**, zadáním názvu skupiny prostředků `myResourceGroup`a pak výběrem možnosti **Odstranit**. 

## <a name="known-errors"></a>Známé chyby

### <a name="windows-logins-are-not-supported"></a>Přihlášení Windows nejsou podporovaná.

`Exception Message: Windows logins are not supported in this version of SQL Server.`

Agent byl nakonfigurován s přihlášením systému Windows a musí místo něj použít SQL Server přihlášení. Pomocí stránky **zabezpečení agenta** ve **vlastnostech publikace** změňte přihlašovací údaje na SQL Server přihlašovací jméno. 


### <a name="failed-to-connect-to-azure-storage"></a>Nepovedlo se připojit k Azure Storage


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Azure Storage získal připojovací řetězec pro replstorage 2019-11-19 02:21:05.07 připojení ke službě Azure File Storage '\\replstorage. File. Core. Windows. net\replshare ' 2019-11-19 02:21:31.21 se nepodařilo připojit k Azure Storage ' ' s chybou operačního systému: 53.


Pravděpodobnou příčinou je to, že port 445 je uzavřený buď v bráně firewall Azure, bráně Windows Firewall, nebo v obou. 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

Tato chyba může způsobit použití lomítka místo zpětného lomítka v cestě k souboru pro sdílenou složku. To je v pořádku: `\\replstorage.file.core.windows.net\replshare` to může způsobit chybu operačního systému 55: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Nepovedlo se připojit k předplatiteli.

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Možná řešení:
- Ujistěte se, že je port 1433 otevřený. 
- Zajistěte, aby byl na odběrateli povolen protokol TCP/IP. 
- Potvrďte, že se název DNS použil při vytváření odběratele. 
- Ověřte, že jsou vaše virtuální sítě správně propojené v privátní zóně DNS. 
- Ověřte, jestli je záznam A správně nakonfigurovaný. 
- Ověřte, že je nakonfigurován partnerský vztah VPN správně. 

### <a name="no-publications-to-which-you-can-subscribe"></a>Žádné publikace, ke kterým se můžete přihlásit k odběru

Pokud přidáváte nové předplatné pomocí průvodce **novým odběrem** , můžete na stránce **publikace** zjistit, že v seznamu dostupné možnosti nejsou žádné databáze a publikace, a může se zobrazit následující chybová zpráva:

`There are no publications to which yuo can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
I když je možné, že tato chybová zpráva je přesná a na vydavateli, ke kterému jste se připojili, nejsou k dispozici publikace, nebo nemáte dostatečná oprávnění, může být tato chyba také způsobena starší verzí SQL Server Management Studio. Pokuste se upgradovat na SQL Server Management Studio 18,0 nebo vyšší, abyste toto pravidlo vyzkoušeli jako hlavní příčinu. 


## <a name="next-steps"></a>Další kroky

### <a name="enable-security-features"></a>Povolit funkce zabezpečení

Úplný seznam způsobů, jak zabezpečit databázi, najdete v článku [funkce zabezpečení funkcí spravované instance](sql-database-managed-instance.md#azure-sql-database-security-features) . Projednávají se následující funkce zabezpečení:

- [Auditování spravované instance](sql-database-managed-instance-auditing.md) 
- [Funkce Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detekce hrozeb](sql-database-managed-instance-threat-detection.md) 
- [Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking)
- [Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security) 
- [Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Možnosti spravované instance

Úplný přehled funkcí spravované instance najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Možnosti spravované instance](sql-database-managed-instance.md)
