---
title: Přístup k úložištím dat a sdíleným složkám s ověřováním systému Windows
description: Naučte se konfigurovat SSIS Catalog v Azure SQL Database a Azure-SSIS Integration Runtime v Azure Data Factory ke spouštění balíčků, které přistupují k úložištím dat a sdíleným složkám s ověřováním systému Windows.
ms.date: 10/27/2020
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: jburchel
ms.openlocfilehash: 8cb92407954ab5376850e57b0ee91d934480b025
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786885"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Přístup k úložištím dat a sdíleným složkám s využitím ověřování systému Windows z balíčků SSIS v Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ověřování systému Windows můžete použít pro přístup k úložištím dat, jako jsou servery SQL, sdílené složky, soubory Azure atd. z balíčků SSIS běžících na vašem Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF). Vaše úložiště dat můžou být místní, hostovaná na Azure Virtual Machines (virtuální počítače) nebo spuštěná v Azure jako spravované služby. Pokud jsou místní, musíte se připojit k Azure-SSIS IR k Virtual Network (Microsoft Azure Virtual Network) připojenému k vaší místní síti, viz téma [join Azure-SSIS IR to Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md). Existují čtyři metody přístupu k úložištím dat s ověřováním systému Windows z balíčků SSIS běžících na vašem Azure-SSIS IR:

| Způsob připojení | Platný rozsah | Krok nastavení | Metoda přístupu v balíčcích | Počet sad přihlašovacích údajů a připojených prostředků | Typ připojených prostředků | 
|---|---|---|---|---|---|
| Nastavení kontextu spuštění na úrovni aktivity | Aktivita za spuštění balíčku SSIS | Nakonfigurujte vlastnost **ověřování systému Windows** tak, aby se při spouštění balíčků SSIS jako aktivity balíčku SSIS v kanálech ADF spustily kontext spuštění/spuštění jako.<br/><br/> Další informace najdete v tématu [Konfigurace aktivity SSIS balíčku Execute](./how-to-invoke-ssis-package-ssis-activity.md). | K prostředkům přímo v balíčcích použijte například cestu UNC pro přístup ke sdíleným složkám nebo k souborům Azure: `\\YourFileShareServerName\YourFolderName` nebo `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Podporuje jenom jednu sadu přihlašovacích údajů pro všechny připojené prostředky. | – Sdílené složky na místních nebo virtuálních počítačích Azure<br/><br/> – Soubory Azure, viz [použití sdílené složky Azure](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> – Servery SQL na místních nebo virtuálních počítačích Azure s ověřováním systému Windows<br/><br/> – Další prostředky s ověřováním systému Windows |
| Nastavení kontextu spuštění na úrovni katalogu | Za Azure-SSIS IR, ale při nastavení kontextu spuštění na úrovni aktivity je přepsáno (viz výše) | Spusťte `catalog.set_execution_credential` uloženou proceduru SSISDB a nastavte kontext spuštění/spuštění jako.<br/><br/> Další informace najdete v části níže v tomto článku. | K prostředkům přímo v balíčcích použijte například cestu UNC pro přístup ke sdíleným složkám nebo k souborům Azure: `\\YourFileShareServerName\YourFolderName` nebo `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Podporuje jenom jednu sadu přihlašovacích údajů pro všechny připojené prostředky. | – Sdílené složky na místních nebo virtuálních počítačích Azure<br/><br/> – Soubory Azure, viz [použití sdílené složky Azure](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> – Servery SQL na místních nebo virtuálních počítačích Azure s ověřováním systému Windows<br/><br/> – Další prostředky s ověřováním systému Windows |
| Trvalé uchování přihlašovacích údajů prostřednictvím `cmdkey` příkazu | Za Azure-SSIS IR, ale při nastavení kontextu spuštění na úrovni aktivity nebo katalogu se přepsal (viz výše) | Spusťte `cmdkey` příkaz ve skriptu vlastního nastavení ( `main.cmd` ) při zřizování Azure-SSIS IR například při použití sdílených složek, souborů Azure nebo SQL Server:<br/><br/> `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword`,<br/><br/> `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`, nebo<br/><br/> `cmdkey /add:YourSQLServerFullyQualifiedDomainNameOrIPAddress:YorSQLServerPort /user:YourDomainName\YourUsername /pass:YourPassword`.<br/><br/> Další informace najdete v tématu [přizpůsobení nastavení pro Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md). | K prostředkům přímo v balíčcích použijte například cestu UNC pro přístup ke sdíleným složkám nebo k souborům Azure: `\\YourFileShareServerName\YourFolderName` nebo `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Podpora více sad přihlašovacích údajů pro různé připojené prostředky | – Sdílené složky na místních nebo virtuálních počítačích Azure<br/><br/> – Soubory Azure, viz [použití sdílené složky Azure](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> – Servery SQL na místních nebo virtuálních počítačích Azure s ověřováním systému Windows<br/><br/> – Další prostředky s ověřováním systému Windows |
| Připojování jednotek v době spuštění balíčku (netrvalá) | Za balíček | Spustit `net use` příkaz v úloze spustit proces, která je přidána na začátek toku řízení v balíčcích, například `net use D: \\YourFileShareServerName\YourFolderName` | Přístup ke sdíleným složkám souborů prostřednictvím mapovaných jednotek | Podpora více jednotek pro různé sdílené složky | – Sdílené složky na místních nebo virtuálních počítačích Azure<br/><br/> – Soubory Azure, viz [použití sdílené složky Azure](../storage/files/storage-how-to-use-files-windows.md) |
|||||||

> [!WARNING]
> Pokud nepoužíváte žádnou z výše uvedených metod k přístupu k úložištím dat s ověřováním systému Windows, nebudou mít balíčky závislé na ověřování systému Windows přístup k nim a selžou v době běhu. 

Ve zbývající části tohoto článku se dozvíte, jak nakonfigurovat SSIS Catalog (SSISDB), který je hostovaný ve spravované instanci SQL Database/SQL, ke spouštění balíčků na Azure-SSIS IR, které pro přístup k úložištím dat používají ověřování systému Windows. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Můžete použít jenom jednu sadu přihlašovacích údajů.

Když použijete ověřování systému Windows v balíčku SSIS, můžete použít jenom jednu sadu přihlašovacích údajů. Přihlašovací údaje domény, které zadáte po provedení kroků v tomto článku, se vztahují na všechna spuštění balíčků – Interactive nebo naplánované – na Azure-SSIS IR, dokud je nezměníte nebo neodeberete. Pokud se váš balíček musí připojit k více úložištím dat s různými sadami přihlašovacích údajů, měli byste zvážit výše uvedené alternativní metody.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Zadejte přihlašovací údaje domény pro ověřování systému Windows.

K poskytnutí přihlašovacích údajů domény, které umožňují balíčkům používat ověřování systému Windows pro přístup k úložištím dat místně, proveďte následující akce:

1. Pomocí SQL Server Management Studio (SSMS) nebo jiného nástroje se připojte k spravované instanci SQL Database/SQL, která hostuje SSISDB. Další informace najdete v tématu [připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Pomocí SSISDB jako aktuální databáze otevřete okno dotazu.

3. Spusťte následující uloženou proceduru a zadejte příslušné přihlašovací údaje domény:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Spusťte balíčky SSIS. Balíčky používají přihlašovací údaje, které jste zadali pro přístup k úložištím dat místně s ověřováním systému Windows.

### <a name="view-domain-credentials"></a>Zobrazit přihlašovací údaje domény

Chcete-li zobrazit přihlašovací údaje pro aktivní doménu, proveďte následující akce:

1. Pomocí SSMS nebo jiného nástroje se připojte k SQL Database/SQL Managed instance, která hostuje SSISDB. Další informace najdete v tématu [připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Pomocí SSISDB jako aktuální databáze otevřete okno dotazu.

3. Spusťte následující uloženou proceduru a podívejte se na výstup:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Vymazat přihlašovací údaje domény
Pokud chcete vymazat a odebrat přihlašovací údaje, které jste zadali, jak je popsáno v tomto článku, proveďte následující akce:

1. Pomocí SSMS nebo jiného nástroje se připojte k SQL Database/SQL Managed instance, která hostuje SSISDB. Další informace najdete v tématu [připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Pomocí SSISDB jako aktuální databáze otevřete okno dotazu.

3. Spusťte následující uloženou proceduru:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Připojení k SQL Server místním prostředím

Chcete-li ověřit, zda se můžete připojit k SQL Server místně, proveďte následující akce:

1. Pokud chcete spustit tento test, najděte počítač, který není připojený k doméně.

2. Na počítači, který není připojený k doméně, spusťte následující příkaz, který spustí SSMS s přihlašovacími údaji domény, které chcete použít:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. V SSMS ověřte, jestli se můžete připojit k SQL Server místně.

### <a name="prerequisites"></a>Požadavky

Pokud chcete získat přístup k SQL Server místním prostředím z balíčků spuštěných v Azure, udělejte tyto věci:

1.  V SQL Server Configuration Manager povolit protokol TCP/IP.

2. Povolí přístup přes bránu Windows Firewall. Další informace najdete v tématu [Konfigurace brány Windows Firewall pro přístup k SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access).

3. Připojte svůj Azure-SSIS IR k Microsoft Azure Virtual Network připojenému k SQL Server místním.  Další informace najdete v tématu [připojení Azure-SSIS IR k Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md).

4. Použijte `catalog.set_execution_credential` uloženou proceduru SSISDB k zadání přihlašovacích údajů, jak je popsáno v tomto článku.

## <a name="connect-to-a-file-share-on-premises"></a>Připojení ke sdílené složce místně

Chcete-li ověřit, zda se můžete připojit ke sdílené složce místně, proveďte následující akce:

1. Pokud chcete spustit tento test, najděte počítač, který není připojený k doméně.

2. Na počítači, který není připojený k doméně, spusťte následující příkazy. Tyto příkazy otevřou okno příkazového řádku s doménovými přihlašovacími údaji, které chcete použít, a potom otestujte připojení ke sdílené složce místně tím, že získáte výpis adresáře.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Ověřte, jestli se výpis adresáře pro sdílenou složku místně vrátí.

### <a name="prerequisites"></a>Požadavky

Pro přístup ke sdílené složce v místním prostředí z balíčků spuštěných v Azure proveďte následující akce:

1. Povolí přístup přes bránu Windows Firewall.

2. Připojte svůj Azure-SSIS IR k Microsoft Azure Virtual Network připojenému ke sdílené složce místně.  Další informace najdete v tématu [připojení Azure-SSIS IR k Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md).

3. Použijte `catalog.set_execution_credential` uloženou proceduru SSISDB k zadání přihlašovacích údajů, jak je popsáno v tomto článku.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Připojení ke sdílené složce na virtuálním počítači Azure

Pokud chcete získat přístup ke sdílené složce na virtuálním počítači Azure z balíčků běžících v Azure, udělejte tyto věci:

1. Pomocí SSMS nebo jiného nástroje se připojte k SQL Database/SQL Managed instance, která hostuje SSISDB. Další informace najdete v tématu [připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Pomocí SSISDB jako aktuální databáze otevřete okno dotazu.

3. Spusťte následující uloženou proceduru a zadejte příslušné přihlašovací údaje domény:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Připojení ke sdílené složce v souborech Azure

Další informace o službě soubory Azure najdete v tématu [soubory Azure](https://azure.microsoft.com/services/storage/files/).

Pro přístup ke sdílené složce v Azure Files z balíčků běžících v Azure udělejte tyto věci:

1. Pomocí SSMS nebo jiného nástroje se připojte k SQL Database/SQL Managed instance, která hostuje SSISDB. Další informace najdete v tématu [připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Pomocí SSISDB jako aktuální databáze otevřete okno dotazu.

3. Spusťte následující uloženou proceduru a zadejte příslušné přihlašovací údaje domény:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Další kroky

- Nasaďte balíčky. Další informace najdete v tématu [nasazení projektu SSIS do Azure pomocí SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms).
- Spusťte balíčky. Další informace najdete v tématu [spuštění balíčků SSIS v Azure pomocí SSMS](/sql/integration-services/ssis-quickstart-run-ssms).
- Naplánujte balíčky. Další informace najdete v tématu [plánování balíčků SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).
