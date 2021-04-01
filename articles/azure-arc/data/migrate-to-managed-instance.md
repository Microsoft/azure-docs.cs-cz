---
title: Migrace databáze z SQL Server do spravované instance SQL ARC s podporou Azure
description: Migrace databáze z SQL Server do spravované instance SQL ARC s podporou Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 86563b0a44bade2cedaf76af3c247821756111fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90936330"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>Migrace: SQL Server do spravované instance SQL ARC s podporou Azure

Tento scénář vás provede postupem migrace databáze z SQL Server instance do spravované instance Azure SQL ve službě Azure ARC prostřednictvím dvou různých metod zálohování a obnovení.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="use-azure-blob-storage"></a>Použití úložiště objektů BLOB v Azure 

Použijte úložiště objektů BLOB v Azure pro migraci na spravovanou instanci SQL ARC s podporou Azure.

Tato metoda používá Azure Blob Storage jako dočasné umístění úložiště, ze kterého můžete zálohovat a pak obnovit z.

### <a name="prerequisites"></a>Požadavky

- [Nainstalovat Azure Data Studio](install-client-tools.md)
- [Nainstalovat Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/)
- Předplatné Azure

### <a name="step-1-provision-azure-blob-storage"></a>Krok 1: zřízení úložiště objektů BLOB v Azure

1. Postupujte podle kroků popsaných v tématu [Vytvoření účtu Azure Blob Storage](../../storage/blobs/storage-blob-create-account-block-blob.md?tabs=azure-portal) .
1. Spustit Průzkumník služby Azure Storage
1. [Přihlaste se k Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) pro přístup k úložišti objektů BLOB vytvořenému v předchozím kroku.
1. Klikněte pravým tlačítkem na účet úložiště objektů BLOB a vyberte **vytvořit kontejner objektů BLOB** a vytvořte nový kontejner, ve kterém se bude ukládat záložní soubor.

### <a name="step-2-get-storage-blob-credentials"></a>Krok 2: získání přihlašovacích údajů objektu BLOB úložiště

1. V Průzkumník služby Azure Storage klikněte pravým tlačítkem na kontejner objektů blob, který jste právě vytvořili, a vyberte **získat sdílený přístupový podpis** .

1. Výběr **čtení**, **zápisu** a **seznamu**

1. Vyberte **Vytvořit**.

   Poznamenejte si identifikátor URI a řetězec dotazu z této obrazovky. Ty budou potřeba v pozdějších krocích. Klikněte na tlačítko **Kopírovat** a uložte se do programu Poznámkový blok/OneNote atd.

1. Zavřete okno **sdílený přístupový podpis** .

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>Krok 3: zálohování souboru databáze do Azure Blob Storage

V tomto kroku se připojíme ke zdrojovému SQL Server a vytvoříme záložní soubor databáze, kterou chceme migrovat do spravované instance SQL – Azure ARC.

1. Spustit Azure Data Studio
1. Připojte se k instanci SQL Server s databází, kterou chcete migrovat do spravované instance SQL – Azure ARC
1. Klikněte pravým tlačítkem na databázi a vyberte **Nový dotaz** .
1. Připravte dotaz v následujícím formátu nahrazením zástupných symbolů označených `<...>` pomocí informací z sdíleného přístupového podpisu v předchozích krocích.  Po nahrazení hodnot spusťte dotaz.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. Podobně Připravte příkaz **Backup Database** následujícím způsobem, aby se vytvořil záložní soubor do kontejneru objektů BLOB.  Po nahrazení hodnot spusťte dotaz.

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. Otevřete Průzkumník služby Azure Storage a ověřte, že se záložní soubor vytvořený v předchozím kroku zobrazuje v kontejneru objektů BLOB.

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>Krok 4: obnovení databáze z Azure Blob Storage do spravované instance SQL – ARC Azure

1. Z Azure Data Studio Přihlaste se a připojte se ke spravované instanci SQL – ARC Azure.
1. Rozbalte **systémové databáze**, klikněte pravým tlačítkem na **hlavní** databáze a vyberte **Nový dotaz**.
1. V okně editoru dotazů Připravte a spusťte stejný dotaz z předchozího kroku k vytvoření přihlašovacích údajů.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. Připravte a spusťte následující příkaz, který ověří, jestli je záložní soubor čitelný a nedotčený.

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. Příprava a spuštění příkazu **obnovit databázi** následujícím způsobem pro obnovení záložního souboru do databáze ve spravované instanci SQL – ARC Azure

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

Další informace o zálohování na adresu URL najdete tady:

[Zálohování na dokumenty URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[Zálohování na adresu URL pomocí SQL Server Management Studio (SSMS)](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>Metoda 2: zkopírování záložního souboru do spravované instance Azure SQL – ARC Azure pod pomocí kubectl

V této metodě se dozvíte, jak vytvořit záložní soubor, který vytvoříte pomocí libovolné metody, a pak ho zkopírovat do místního úložiště ve spravované instanci Azure SQL pod, abyste se mohli vrátit z nějakého místa, podobně jako v typickém systému souborů v systému Windows nebo Linux. V tomto scénáři budete pomocí příkazu `kubectl cp` Kopírovat soubor z jednoho místa do systému souborů pod.

### <a name="prerequisites"></a>Požadavky

- Nainstalujte a nakonfigurujte kubectl, aby odkazovaly na cluster Kubernetes, ve kterém se nasazují datové služby Azure ARC.
- Máte nástroj, jako je Azure Data Studio nebo server pro správu SQL Server nainstalovaný a připojený k SQL Server, kde chcete vytvořit záložní soubor nebo už máte vytvořený existující soubor. bak v místním systému souborů.

### <a name="step-1-backup-the-database-if-you-havent-already"></a>Krok 1: zálohování databáze (Pokud jste to ještě neudělali)

Zálohujte databázi SQL Server do místní cesty k souboru jako jakékoli typické SQL Server zálohování na disk:

 ```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test’ ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>Krok 2: zkopírování záložního souboru do systému souborů pod

Vyhledejte název pod tím, kde je instance SQL nasazena. Obvykle by měl vypadat nějak takto. `pod/<sqlinstancename>-0`

Seznam všech lusků získáte spuštěním:

 ```console
kubectl get pods -n <namespace of data controller>
```

Příklad:

Zkopírujte záložní soubor z místního úložiště do systému SQL pod v clusteru.

 ```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>Krok 3: obnovení databáze

Příprava a spuštění příkazu RESTORE pro obnovení záložního souboru do spravované instance Azure SQL – ARC Azure

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

Příklad:

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```


## <a name="next-steps"></a>Další kroky

[Další informace o funkcích a možnostech spravované instance SQL ARC s podporou Azure](managed-instance-features.md)

[Začněte vytvořením kontroleru dat](create-data-controller.md)

[Už jste vytvořili řadič dat? Vytvoření spravované instance SQL s povoleným ARC Azure](create-sql-managed-instance.md)