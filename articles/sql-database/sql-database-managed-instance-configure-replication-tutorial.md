---
title: Konfigurace transakční replikace mezi dvěma spravovanými instancemi a SQL Serverem
description: Kurz, který konfiguruje replikaci mezi spravovanou instancí Vydavatele, instancí spravované distributorem a předplatitelem SERVERU SQL Server na virtuálním počítači Azure, spolu s nezbytnými síťovými součástmi, jako je privátní zóna DNS a partnerský vztah VPN.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: fa6e393500e9deeb91ee84aa5255320003817f08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719887"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>Kurz: Konfigurace transakční replikace mezi dvěma spravovanými instancemi a SQL Serverem


V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Nakonfigurujte spravovanou instanci jako vydavatel replikace. 
> - Nakonfigurujte spravovanou instanci jako distributora replikace. 
> - Nakonfigurujte SQL Server jako odběratele. 

![Replikace mezi hospodou SQL MI, SQL MI Dist a sub SQL Server](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

Tento kurz je určen pro zkušené publikum a předpokládá, že uživatel je obeznámen s nasazením a připojením k oběma spravovaným instancím a virtuálním mandům SQL Server v rámci Azure. Jako takové jsou některé kroky v tomto kurzu přehlédnuty. 

Další informace najdete v [článku přehled spravovaných instancí Azure SQL Database](sql-database-managed-instance-index.yml), články o [možnostech](sql-database-managed-instance.md)a [transakční replikaci SQL.](sql-database-managed-instance-transactional-replication.md)

Informace o konfiguraci replikace mezi vydavatelem spravované instance a předplatitelem spravované instance naleznete v [tématu Konfigurace transakční replikace mezi dvěma spravovanými instancemi](replication-with-sql-database-managed-instance.md). 

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit kurz, ujistěte se, že máte následující předpoklady:

- Předplatné [Azure](https://azure.microsoft.com/free/). 
- Zkušenosti s nasazením dvou spravovaných instancí v rámci stejné virtuální sítě. 
- Odběratel SQL Serveru, místní nebo virtuální počítač Azure. Tento kurz používá virtuální počítač Azure.  
- [SQL Server Management Studio (SSMS) 18.0 nebo vyšší](/sql/ssms/download-sql-server-management-studio-ssms).
- Nejnovější verze [Azure Powershellu](/powershell/azure/install-az-ps?view=azps-1.7.0).
- Port 445 a 1433 povolují přenosy SQL na Azure Firewall i Windows Firewall. 

## <a name="1---create-the-resource-group"></a>1 - Vytvoření skupiny prostředků
K vytvoření nové skupiny prostředků použijte následující fragment kódu Prostředí PowerShell:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2 – Vytvoření dvou spravovaných instancí
Vytvořte dvě spravované instance v rámci této nové skupiny prostředků pomocí [portálu Azure](https://portal.azure.com). 

- Název instance spravované vydavatelem by `sql-mi-publisher` měl být: (spolu s několika znaky pro randomizaci) a název virtuální sítě by měl být `vnet-sql-mi-publisher`.
- Název instance spravované distributorem by `sql-mi-distributor` měl být: (spolu s několika znaky pro randomizaci) a měl by být _ve stejné virtuální síti jako instance spravované vydavatelem_.

   ![Použití virtuální sítě vydavatele pro distributora](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

Další informace o vytvoření spravované instance najdete [v tématu Vytvoření spravované instance na portálu.](sql-database-managed-instance-get-started.md)

  > [!NOTE]
  > Z důvodu jednoduchosti a protože se jedná o nejběžnější konfiguraci, tento kurz navrhuje umístění instance spravované distributorem ve stejné virtuální síti jako vydavatel. Je však možné vytvořit distributora v samostatné virtuální síti. Chcete-li tak učinit, budete muset nakonfigurovat partnerský vztah VPN mezi virtuálními sítěmi vydavatele a distributora a potom nakonfigurovat partnerský vztah VPN mezi virtuálními sítěmi distributora a odběratele. 

## <a name="3---create-a-sql-server-vm"></a>3 – vytvoření virtuálního serveru SQL Server
Vytvořte virtuální počítač SQL Serveru pomocí [portálu Azure](https://portal.azure.com). Virtuální počítač SQL Server by měl mít následující charakteristiky:

- Jméno:`sql-vm-sub`
- Obrázek: SQL Server 2016 nebo vyšší
- Skupina prostředků: stejná jako spravovaná instance
- Virtuální síť:`sql-vm-sub-vnet` 

Další informace o nasazení virtuálního počítače SQL Server do Azure najdete v [tématu Úvodní příručka: Vytvoření virtuálního počítače SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md).

## <a name="4---configure-vpn-peering"></a>4 - Konfigurace partnerského vztahu VPN
Nakonfigurujte partnerský vztah VPN tak, aby umožňoval komunikaci mezi virtuální sítí dvou spravovaných instancí a virtuální sítí SQL Serveru. Chcete-li tak učinit, použijte tento fragment kódu prostředí PowerShell:

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

Po navázání partnerského vztahu VPN otestujte připojení spuštěním sql server management studio (SSMS) na serveru SQL Server a připojením k oběma spravovaným instancím. Další informace o připojení ke spravované instanci pomocí SSMS najdete v [tématu Použití SSMS pro připojení k MI](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance). 

![Testování připojení ke spravovaným instancím](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5 - Vytvoření soukromé zóny DNS

Privátní zóna DNS umožňuje směrování DNS mezi spravovanými instancemi a sql serverem. 

### <a name="create-private-dns-zone"></a>Vytvořit soukromou zónu DNS
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte **Vytvořit prostředek** k vytvoření nového prostředku Azure. 
1. Hledejte `private dns zone` na Azure Marketplace. 
1. Zvolte **prostředek zóny Soukromé DNS** publikovaný společností Microsoft a pak vyberte **Vytvořit,** chcete-li zónu DNS vytvořit. 
1. Z rozevíracího souboru zvolte předplatné a skupinu prostředků. 
1. Zadejte libovolný název zóny DNS, `repldns.com`například . 

   ![Vytvořit soukromou zónu DNS](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. Vyberte **Zkontrolovat a vytvořit**. Zkontrolujte parametry privátní zóny DNS a pak vyberte **Vytvořit,** chcete-li vytvořit prostředek. 

### <a name="create-a-record"></a>Vytvoření záznamu

1. Přejděte do nové **zóny Soukromé DNS** a vyberte **Přehled**. 
1. Chcete-li vytvořit nový záznam A, vyberte **možnost + sada záznamů.** 
1. Zadejte název virtuálního počítače SQL Server a také privátní interní IP adresu. 

   ![Konfigurace záznamu](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. Chcete-li vytvořit záznam A, vyberte **ok.** 

### <a name="link-the-virtual-network"></a>Propojení virtuální sítě

1. Přejděte do nové **zóny Soukromé DNS** a vyberte propojení **virtuálních sítí**. 
1. Vyberte **+ Přidat**. 
1. Zadejte název odkazu, například `Pub-link`. 
1. Vrozené předplatné vyberte z rozevíracího souboru a pak vyberte virtuální síť pro spravovanou instanci vydavatele. 
1. Zaškrtněte políčko **povolit automatickou registraci**. 

   ![Vytvořit propojení virtuálnísítě](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. Chcete-li propojit virtuální síť, vyberte **OK.** 
1. Opakováním těchto kroků přidejte odkaz pro virtuální síť `Sub-link`odběratelů s názvem, například . 


## <a name="6---create-azure-storage-account"></a>6 – Vytvoření účtu úložiště Azure

[Vytvořte účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) pro pracovní adresář a pak [vytvořte sdílenou složku](../storage/files/storage-how-to-create-file-share.md) v rámci účtu úložiště. 

Zkopírujte cestu ke sdílení souborů ve formátu:`\\storage-account-name.file.core.windows.net\file-share-name`   

Příklad: `\\replstorage.file.core.windows.net\replshare`

Zkopírujte připojovací řetězec přístupového klíče úložiště ve formátu:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

Příklad: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


Další informace naleznete v [tématu Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md). 


## <a name="7---create-a-database"></a>7 - Vytvoření databáze
Vytvořte novou databázi na vydavatele MI. Postup je následující:

1. Spusťte sql server management studio (SSMS) na serveru SQL Server. 
1. Připojte se `sql-mi-publisher` ke spravované instanci. 
1. Otevřete okno **Nový dotaz** a vytvořte databázi následujícím dotazem T-SQL:

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

## <a name="8---configure-distribution"></a>8 - Konfigurace distribuce 
Jakmile je připojení navázáno a máte ukázkovou `sql-mi-distributor` databázi, můžete nakonfigurovat distribuci ve spravované instanci. Postup je následující:

1. Spusťte sql server management studio (SSMS) na serveru SQL Server. 
1. Připojte se `sql-mi-distributor` ke spravované instanci. 
1. Otevřete okno **Nový dotaz** a spusťte následující kód Transact-SQL pro konfiguraci distribuce v instanci spravované distributorem: 

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
   > Ujistěte se, že pro`\`parametr @working_directory používáte pouze zpětná lomítka ( ). Použití lomítka (`/`) může způsobit chybu při připojování ke sdílené složce. 

1. Připojte se `sql-mi-publisher` ke spravované instanci. 
1. Otevřete okno **Nový dotaz** a spusťte následující kód Transact-SQL a zaregistrujte distributora u vydavatele: 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9 - Vytvoření publikace
Po konfiguraci distribuce můžete nyní vytvořit publikaci. Postup je následující: 

1. Spusťte sql server management studio (SSMS) na serveru SQL Server. 
1. Připojte se `sql-mi-publisher` ke spravované instanci. 
1. V **Průzkumníkovi objektů**rozbalte uzel **Replikace** a klepněte pravým tlačítkem myši na složku **Místní publikace.** Vyberte **novou publikaci...**. 
1. Chcete-li přejít za úvodní stránku, vyberte **možnost Další.** 
1. Na stránce **Databáze publikace** `ReplTutorial` vyberte databázi, kterou jste vytvořili dříve. Vyberte **další**. 
1. Na stránce **Typ publikace** vyberte **Transakční publikace**. Vyberte **další**. 
1. Na stránce **Články** zaškrtněte políčko vedle **tabulky**. Vyberte **další**. 
1. Na stránce **Řádky tabulky filtrů** vyberte **Další** bez přidání filtrů. 
1. Na stránce **Agent snímku** zaškrtněte políčko Vedle **vytvořit snímek okamžitě a zachovat snímek k dispozici pro inicializaci odběry**. Vyberte **další**. 
1. Na stránce **Zabezpečení agenta** vyberte **Nastavení zabezpečení..**. Zadejte přihlašovací údaje serveru SQL Server, které chcete použít pro agenta snapshot a pro připojení k vydavateli. Chcete-li zavřít stránku **Zabezpečení agenta snímku,** vyberte **ok.** Vyberte **další**. 

   ![Konfigurace zabezpečení agenta snímek](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. Na stránce **Akce průvodce** zvolte **Vytvořit publikaci** a (volitelně) zvolte **Generovat soubor skriptu s kroky k vytvoření publikace,** pokud chcete uložit tento skript na později. 
1. Na stránce **Dokončit průvodce** pojmenujte publikaci `ReplTest` a vyberte **další,** chcete-li publikaci vytvořit. 
1. Po vytvoření publikace aktualizujte uzel **Replikace** v **Průzkumníkovi objektů** a rozbalte **položku Místní publikace,** abyste viděli novou publikaci. 


## <a name="10---create-the-subscription"></a>10 - Vytvoření předplatného 

Po vytvoření publikace můžete vytvořit odběr. Postup je následující: 

1. Spusťte sql server management studio (SSMS) na serveru SQL Server. 
1. Připojte se `sql-mi-publisher` ke spravované instanci. 
1. Otevřete okno **Nový dotaz** a spusťte následující kód Transact-SQL a přidejte agenta předplatného a distribuce. Použijte DNS jako součást názvu odběratele. 

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

## <a name="11---test-replication"></a>11 - Testovací replikace 

Jakmile replikace byla nakonfigurována, můžete otestovat vložením nové položky na vydavatele a sledování změny šířit odběratele. 

Spusťte následující fragment T-SQL, chcete-li zobrazit řádky na odběrateli:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Spusťte následující fragment T-SQL, chcete-li vložit další řádky do vydavatele, a potom řádky znovu zkontrolujte u odběratele. 

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. Přejděte do skupiny prostředků na [webu Azure Portal](https://portal.azure.com). 
1. Vyberte spravované instance a pak vyberte **Odstranit**. Zadáním `yes` textového pole potvrďte, že chcete prostředek odstranit, a pak vyberte **Odstranit**. Tento proces může trvat nějakou dobu na pozadí a dokud není hotovo, nebude možné odstranit *virtuální cluster* nebo jiné závislé prostředky. Sledujte odstranění na kartě Aktivita a potvrďte, že vaše spravovaná instance byla odstraněna. 
1. Po odstranění spravované instance odstraňte *virtuální cluster* tak, že ho vyberete ve skupině prostředků a pak **zvolíte Odstranit**. Zadáním `yes` textového pole potvrďte, že chcete prostředek odstranit, a pak vyberte **Odstranit**. 
1. Odstraňte všechny zbývající prostředky. Zadáním `yes` textového pole potvrďte, že chcete prostředek odstranit, a pak vyberte **Odstranit**. 
1. Skupinu prostředků odstraňte tak, že vyberete **Odstranit skupinu** `myResourceGroup`prostředků , zadáte název skupiny prostředků a pak vyberete **Odstranit**. 

## <a name="known-errors"></a>Známé chyby

### <a name="windows-logins-are-not-supported"></a>Přihlášení systému Windows nejsou podporována

`Exception Message: Windows logins are not supported in this version of SQL Server.`

Agent byl nakonfigurován s přihlášení systému Windows a musí místo toho použít přihlášení serveru SQL Server. Pomocí stránky **Zabezpečení agenta** **vlastností Publikace** změňte přihlašovací pověření na přihlášení serveru SQL Server. 


### <a name="failed-to-connect-to-azure-storage"></a>Připojení k úložišti Azure Storage se nezdařilo.


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Získaný připojovací řetězec úložiště Azure pro replstorage 2019-11-19 02:21:05.07 Připojení k souborům Azure Úložiště '\\replstorage.file.core.windows.net\replshare' 2019-11-19 02:21:31.21 Nepodařilo se připojit k úložišti Azure S chybou operačního systému: 53.


Je to pravděpodobně proto, že port 445 je uzavřen v bráně firewall Azure, brány firewall systému Windows nebo v obou. 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

Použití lomítka namísto zpětného lomítka v cestě k souboru pro sdílenou složku může způsobit tuto chybu. To je `\\replstorage.file.core.windows.net\replshare` v pořádku: To může způsobit chybu operačního spoje 55:`'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Nelze se připojit k odběrateli.

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Možná řešení:
- Zkontrolujte, zda je otevřený port 1433. 
- Ujistěte se, že je u odběratele povolenprotokol TCP/IP. 
- Potvrďte, že název DNS byl použit při vytváření odběratele. 
- Ověřte, zda jsou virtuální sítě správně propojeny v privátní zóně DNS. 
- Ověřte, zda je záznam A správně nakonfigurován. 
- Ověřte, zda je partnerský vztah VPN správně nakonfigurován. 

### <a name="no-publications-to-which-you-can-subscribe"></a>Žádné publikace, ke kterým se můžete přihlásit

Při přidávání nového předplatného pomocí Průvodce **novým odběrem** můžete na stránce **Publikace** zjistit, že nejsou k dispozici žádné databáze a publikace a může se zobrazit následující chybová zpráva:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
I když je možné, že tato chybová zpráva je přesná a opravdu nejsou k dispozici publikace na vydavatele, ke kterému jste připojeni, nebo chybí dostatečná oprávnění, tato chyba může být také způsobena starší verzí SQL Server Management Studio. Zkuste upgradovat na SQL Server Management Studio 18.0 nebo vyšší vyloučit jako hlavní příčinu. 


## <a name="next-steps"></a>Další kroky

### <a name="enable-security-features"></a>Povolení funkcí zabezpečení

Úplný seznam způsobů zabezpečení databáze najdete v následujícím článku [funkce funkce funkce spravované instance.](sql-database-managed-instance.md#azure-sql-database-security-features) Jsou popsány následující funkce zabezpečení:

- [Auditování spravované instance](sql-database-managed-instance-auditing.md) 
- [Funkce Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detekce hrozeb](sql-database-managed-instance-threat-detection.md) 
- [Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking)
- [Zabezpečení na úrovni řádku](/sql/relational-databases/security/row-level-security) 
- [Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Možnosti spravované instance

Úplný přehled možností spravované instance najdete v tématu:

> [!div class="nextstepaction"]
> [Možnosti spravované instance](sql-database-managed-instance.md)
