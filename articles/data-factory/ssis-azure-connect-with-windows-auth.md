---
title: Přístup k úložištím dat a sdíleným položkům souborů pomocí ověřování systému Windows
description: Zjistěte, jak nakonfigurovat katalog SSIS v Azure SQL Database a Azure-SSIS Integration Runtime v Azure Data Factory pro spouštění balíčků, které přistupují k úložištím dat a sdíleným souborům pomocí ověřování windows.
ms.date: 3/22/2018
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 0c4cdc3481fb58efd8eaa4cd83e1d6167f203a4e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760183"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Přístup k úložištím dat a sdíleným složkám s využitím ověřování systému Windows z balíčků SSIS v Azure

Ověřování systému Windows můžete použít pro přístup k úložištím dat, jako jsou servery SQL, sdílené složky, soubory Azure atd. Vaše úložiště dat můžou být místní, hostovaná na virtuálních počítačích Azure nebo spuštěná v Azure jako spravované služby. Pokud jsou místní, musíte připojit zařízení Azure-SSIS IR k virtuální síti (Virtuální síť Microsoft Azure) připojené k místní síti, přečtěte si hlavní [informace o připojení k rozhraní Azure-SSIS IR do virtuální sítě Microsoft Azure](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Existují čtyři metody pro přístup k úložištím dat pomocí ověřování systému Windows z balíčků SSIS spuštěných na zařízení Azure-SSIS IR:

| Způsob připojení | Efektivní rozsah | Krok nastavení | Metoda přístupu v balíčcích | Počet sad pověření a připojených prostředků | Typ připojených prostředků | 
|---|---|---|---|---|---|
| Nastavení kontextu provádění na úrovni aktivity | Aktivita balíčku SSIS na spuštění | Nakonfigurujte vlastnost **ověřování systému Windows** tak, aby při spouštění balíčků SSIS jako spuštění balíčků SSIS v kanálech ADF nastavila kontext Spuštění/Spuštění jako.<br/><br/> Další informace naleznete v [tématu Konfigurace spuštění aktivity balíčku SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). | Přístup k prostředkům přímo v balíčcích prostřednictvím cesty UNC, `\\YourFileShareServerName\YourFolderName` například pokud používáte sdílené složky nebo soubory Azure: nebo`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Podpora pouze jedné sady přihlašovacích údajů pro všechny připojené prostředky | - Sdílené složky v místním/virtuálních počítačích Azure<br/><br/> - Soubory Azure, viz [Použití sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - SERVERY SQL v místním/virtuálních počítačích Azure s ověřováním systému Windows<br/><br/> - Další prostředky s ověřováním systému Windows |
| Nastavení kontextu spuštění na úrovni katalogu | Podle Azure-SSIS IR, ale je přepsána při nastavování kontextu provádění na úrovni aktivity (viz výše) | Spusťte uloženou proceduru SSISDB `catalog.set_execution_credential` k nastavení kontextu "Spuštění/Spuštění jako".<br/><br/> Další informace naleznete ve zbytku tohoto článku níže. | Přístup k prostředkům přímo v balíčcích prostřednictvím cesty UNC, `\\YourFileShareServerName\YourFolderName` například pokud používáte sdílené složky nebo soubory Azure: nebo`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Podpora pouze jedné sady přihlašovacích údajů pro všechny připojené prostředky | - Sdílené složky v místním/virtuálních počítačích Azure<br/><br/> - Soubory Azure, viz [Použití sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - SERVERY SQL v místním/virtuálních počítačích Azure s ověřováním systému Windows<br/><br/> - Další prostředky s ověřováním systému Windows |
| Trvalé pověření pomocí `cmdkey` příkazu | Podle Azure-SSIS IR, ale je přepsána při nastavování kontextu spuštění na úrovni aktivity/katalogu (viz výše) | Příkaz `cmdkey` spusťte ve`main.cmd`vlastním instalačním skriptu ( ) při zřizování zařízení Azure-SSIS `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword` `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`IR, například pokud používáte sdílené složky nebo soubory Azure: nebo .<br/><br/> Další informace najdete [v tématu Přizpůsobení nastavení pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). | Přístup k prostředkům přímo v balíčcích prostřednictvím cesty UNC, `\\YourFileShareServerName\YourFolderName` například pokud používáte sdílené složky nebo soubory Azure: nebo`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Podpora více sad pověření pro různé připojené prostředky | - Sdílené složky v místním/virtuálních počítačích Azure<br/><br/> - Soubory Azure, viz [Použití sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - SERVERY SQL v místním/virtuálních počítačích Azure s ověřováním systému Windows<br/><br/> - Další prostředky s ověřováním systému Windows |
| Montážní jednotky v době spuštění balíku (netrvalé) | Na balíček | Příkaz `net use` Spustit v úloze Spustit proces, který je přidán na začátku toku řízení v balíčcích, například`net use D: \\YourFileShareServerName\YourFolderName` | Přístup ke sdíleným položkům prostřednictvím mapovaných jednotek | Podpora více jednotek pro různé sdílené složky | - Sdílené složky v místním/virtuálních počítačích Azure<br/><br/> - Soubory Azure, viz [Použití sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) |
|||||||

> [!WARNING]
> Pokud nepoužijete žádnou z výše uvedených metod pro přístup k úložištím dat pomocí ověřování systému Windows, vaše balíčky, které jsou závislé na ověřování systému Windows, k nim nebudou mít přístup a za běhu se nezdaří. 

Zbývající část tohoto článku popisuje, jak nakonfigurovat katalog SSIS (SSISDB) hostovaný v serveru Azure SQL Database/spravované instanci pro spouštění balíčků v zařízení Azure-SSIS IR, které používají ověřování systému Windows pro přístup k úložištím dat. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Můžete použít pouze jednu sadu přihlašovacích údajů.

Při použití ověřování systému Windows v balíčku SSIS, můžete použít pouze jednu sadu pověření. Pověření domény, které zadáte při postupujte podle kroků v tomto článku platí pro všechny spuštění balíčků – interaktivní nebo naplánované – na Azure-SSIS IR, dokud je změnit nebo odebrat. Pokud váš balíček má připojit k více úložišť dat s různými sadami pověření, měli byste zvážit výše uvedené alternativní metody.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Zadání pověření domény pro ověřování systému Windows

Chcete-li zadat pověření domény, která umožňují balíčkům používat ověřování systému Windows pro přístup k úložištím dat v místním prostředí, proveďte následující akce:

1. Pomocí sql server management studio (SSMS) nebo jiného nástroje, připojte se k serveru Azure SQL Database/spravované instance, která hostuje SSISDB. Další informace najdete v tématu [Připojení k SSISDB v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. S SSISDB jako aktuální databáze, otevřete okno dotazu.

3. Spusťte následující uloženou proceduru a zadejte příslušná pověření domény:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Spusťte balíčky SSIS. Balíčky používají pověření, která jste zadali pro přístup k úložištím dat v místním prostředí pomocí ověřování systému Windows.

### <a name="view-domain-credentials"></a>Zobrazit pověření domény

Chcete-li zobrazit pověření aktivní domény, proveďte následující akce:

1. Pomocí Služby SSMS nebo jiného nástroje se připojte k serveru Azure SQL Database/spravované instanci, která hostuje SSISDB. Další informace najdete v tématu [Připojení k SSISDB v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. S SSISDB jako aktuální databáze, otevřete okno dotazu.

3. Spusťte následující uloženou proceduru a zkontrolujte výstup:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Vymazat pověření domény
Chcete-li vymazat a odebrat pověření, která jste zadali, jak je popsáno v tomto článku, proveďte následující akce:

1. Pomocí Služby SSMS nebo jiného nástroje se připojte k serveru Azure SQL Database/spravované instanci, která hostuje SSISDB. Další informace najdete v tématu [Připojení k SSISDB v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. S SSISDB jako aktuální databáze, otevřete okno dotazu.

3. Spusťte následující uloženou proceduru:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Připojení k místnímu serveru SQL Server

Chcete-li zkontrolovat, zda se můžete připojit k serveru SQL Server místně, proveďte následující akce:

1. Chcete-li spustit tento test, vyhledejte počítač, který není připojen k doméně.

2. V počítači, který není připojen k doméně, spusťte spouštěním služby SSMS s pověřeními domény, která chcete použít, následující příkaz:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. Z SSMS zkontrolujte, zda se můžete připojit k serveru SQL Server místně.

### <a name="prerequisites"></a>Požadavky

Chcete-li získat přístup k sql serveru v místním prostředí z balíčků spuštěné v Azure, postupujte takto:

1.  Ve Správci konfigurace serveru SQL Server povolte protokol TCP/IP.

2. Povolit přístup přes bránu firewall systému Windows. Další informace naleznete [v tématu Konfigurace brány firewall systému Windows pro přístup k serveru SQL Server](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access).

3. Připojte indpus azure-SSIS k virtuální síti Microsoft Azure, která je připojená k místnímu SQL Serveru.  Další informace najdete [v tématu Připojení azure-SSIS IR k virtuální síti Microsoft Azure](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

4. Pomocí uložené procedury SSISDB `catalog.set_execution_credential` zadejte pověření, jak je popsáno v tomto článku.

## <a name="connect-to-a-file-share-on-premises"></a>Připojení ke sdílené složce v místním prostředí

Chcete-li zkontrolovat, zda se můžete připojit ke sdílené složce místně, proveďte následující akce:

1. Chcete-li spustit tento test, vyhledejte počítač, který není připojen k doméně.

2. V počítači, který není připojen k doméně, spusťte následující příkazy. Tyto příkazy otevřou okno příkazového řádku s pověřeními domény, které chcete použít, a poté otestují připojení ke sdílené složce v místním prostředí získáním výpisu adresáře.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Zkontrolujte, zda je výpis adresáře vrácen pro sdílenou složku v místním prostředí.

### <a name="prerequisites"></a>Požadavky

Chcete-li získat přístup ke sdílené složce v místním prostředí z balíčků spuštěné v Azure, postupujte takto:

1. Povolit přístup přes bránu firewall systému Windows.

2. Připojte počítač Azure-SSIS IR k virtuální síti Microsoft Azure, která je připojená ke sdílené složce místně.  Další informace najdete [v tématu Připojení azure-SSIS IR k virtuální síti Microsoft Azure](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

3. Pomocí uložené procedury SSISDB `catalog.set_execution_credential` zadejte pověření, jak je popsáno v tomto článku.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Připojení ke sdílené složce na virtuálním počítači Azure

Pokud chcete získat přístup ke sdílené složce na virtuálním počítači Azure z balíčků spuštěných v Azure, postupujte takto:

1. Pomocí Služby SSMS nebo jiného nástroje se připojte k serveru Azure SQL Database/spravované instanci, která hostuje SSISDB. Další informace najdete v tématu [Připojení k SSISDB v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. S SSISDB jako aktuální databáze, otevřete okno dotazu.

3. Spusťte následující uloženou proceduru a zadejte příslušná pověření domény:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Připojení ke sdílené složce v Azure Files

Další informace o souborech Azure najdete v [tématu Soubory Azure](https://azure.microsoft.com/services/storage/files/).

Pokud chcete získat přístup ke sdílené složce v Azure Files z balíčků spuštěných v Azure, udělejte to takto:

1. Pomocí Služby SSMS nebo jiného nástroje se připojte k serveru Azure SQL Database/spravované instanci, která hostuje SSISDB. Další informace najdete v tématu [Připojení k SSISDB v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. S SSISDB jako aktuální databáze, otevřete okno dotazu.

3. Spusťte následující uloženou proceduru a zadejte příslušná pověření domény:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Další kroky

- Nasaďte své balíčky. Další informace najdete [v tématu Nasazení projektu SSIS do Azure pomocí SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Spusťte své balíčky. Další informace najdete v [tématu Spuštění balíčků SSIS v Azure s SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Naplánujte si balíčky. Další informace najdete v [tématu Plánování balíčků SSIS v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).