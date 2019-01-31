---
title: Nastavení nástroje Import/Export Azure | Dokumentace Microsoftu
description: Zjistěte, jak nastavit Příprava jednotky a nástroji pro službu Azure Import/Export pro opravu.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: d2ce6c409ae9cbf99589d11dfc850e2324d1b0c9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452409"
---
# <a name="setting-up-the-azure-importexport-tool"></a>Nastavení nástroje Import/Export Azure

Nástroje Microsoft Azure Import/Export je jednotka přípravy a opravit nástroj, který vám pomůže se služby Microsoft Azure Import/Export. Nástroj můžete použít pro následující funkce:

* Před vytvořením úlohy importu, můžete tento nástroj pro kopírování dat do pevné disky, které se chystáte odešlete ji do datového centra Azure.
* Po dokončení úlohy importu můžete použít tento nástroj opravit všechny objekty BLOB, které došlo k poškození, chyběl nebo konfliktu s další objekty BLOB.
* Po přijetí jednotky z úlohy dokončené export můžete použít tento nástroj opravit všechny soubory, které byly poškozeny nebo chybí na jednotkách.

## <a name="prerequisites"></a>Požadavky

Pokud jste **Příprava disků** pro úlohu importu musí být splněny následující požadavky:

* Musíte mít aktivní předplatné Azure.
* Předplatné musí obsahovat účet úložiště se dostatek volného místa pro uložení souborů, že chcete importovat.
* Budete potřebovat alespoň jeden přístupové klíče účtu úložiště.
* Potřebujete počítač ("kopie počítače") s Windows 7, Windows Server 2008 R2 nebo novějším operačním systému Windows, který je nainstalovaný.
* Rozhraní .NET Framework 4, musí být nainstalován na počítači kopírování.
* Na počítači kopírování musí povolit nástroj BitLocker.
* Budete potřebovat jeden nebo více prázdný 2,5 nebo 3, 5palcová SATAII o rychlosti nebo III nebo SSD disky připojené k počítači kopírování.
* Soubory, které máte v plánu importovat musí být přístupné z počítače kopírování, zda jsou sdílené síťové složky nebo místní pevný disk.

Pokud se pokoušíte **opravit importu** částečně, která selhala, budete potřebovat:

* Kopií souborů protokolu
* Klíč účtu úložiště

Pokud se pokoušíte **opravit export** částečně, která selhala, budete potřebovat:

* Kopií souborů protokolu
* Soubory manifestu (volitelné)
* Klíč účtu úložiště

## <a name="installing-the-azure-importexport-tool"></a>Instalace nástroje Azure Import/Export

Nejprve je potřeba [stažení nástroje Azure Import/Export](https://www.microsoft.com/download/details.aspx?id=55280) a rozbalte ho do adresáře v počítači, třeba `c:\WAImportExport`.

Nástroje Azure Import/Export se skládá z následujících souborů:

* dataset.csv
* driveset.csv
* hddid.dll
* Microsoft.Data.Services.Client.dll
* Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportExport.exe
* WAImportExport.exe.config
* WAImportExport.pdb
* WAImportExportCore.dll
* WAImportExportCore.pdb
* WAImportExportRepair.dll
* WAImportExportRepair.pdb

Dále otevřete okno příkazového řádku v **režimu správce**a změňte na adresář obsahující extrahované soubory.

Na výstupu nápovědu pro příkaz, spusťte nástroj (`WAImportExport.exe`) bez parametrů:

```
WAImportExport, a client tool for Windows Azure Import/Export Service. Microsoft (c) 2013


Copy directories and/or files with a new copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>]
        [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]
        DataSet:<dataset.csv>

Add more drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>

Abort an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession

Resume an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession

List drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListDrives

List copy sessions:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListCopySessions

Repair a Drive:
    WAImportExport.exe RepairImport | RepairExport
        /r:<RepairFile> [/logdir:<LogDirectory>]
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]
        [/PathMapFile:<DrivePathMapFile>]

Preview an Export Job:
    WAImportExport.exe PreviewExport
        [/logdir:<LogDirectory>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>

Parameters:

    /j:<JournalFile>
        - Required. Path to the journal file. A journal file tracks a set of drives and
          records the progress in preparing these drives. The journal file must always
          be specified.
    /logdir:<LogDirectory>
        - Optional. The log directory. Verbose log files as well as some temporary
          files will be written to this directory. If not specified, current directory
          will be used as the log directory. The log directory can be specified only
          once for the same journal file.
    /id:<SessionId>
        - Optional. The session Id is used to identify a copy session. It is used to
          ensure accurate recovery of an interrupted copy session.
    /ResumeSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to resume the session.
    /AbortSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to abort the session.
    /sn:<StorageAccountName>
        - Required. Only applicable for RepairImport and RepairExport. The name of
          the storage account.
    /sk:<StorageAccountKey>
        - Required. The key of the storage account.
    /InitialDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of drives to prepare.
    /AdditionalDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of additional drives to be added.
    /r:<RepairFile>
        - Required. Only applicable for RepairImport and RepairExport.
          Path to the file for tracking repair progress. Each drive must have one
          and only one repair file.
    /d:<TargetDirectories>
        - Required. Only applicable for RepairImport and RepairExport.
          For RepairImport, one or more semicolon-separated directories to repair;
          For RepairExport, one directory to repair, e.g. root directory of the drive.
    /CopyLogFile:<DriveCopyLogFile>
        - Required. Only applicable for RepairImport and RepairExport. Path to the
          drive copy log file (verbose or error).
    /ManifestFile:<DriveManifestFile>
        - Required. Only applicable for RepairExport. Path to the drive manifest file.
    /PathMapFile:<DrivePathMapFile>
        - Optional. Only applicable for RepairImport. Path to the file containing
          mappings of file paths relative to the drive root to locations of actual files
          (tab-delimited). When first specified, it will be populated with file paths
          with empty targets, which means either they are not found in TargetDirectories,
          access denied, with invalid name, or they exist in multiple directories. The
          path map file can be manually edited to include the correct target paths and
          specified again for the tool to resolve the file paths correctly.
    /ExportBlobListFile:<ExportBlobListFile>
        - Required. Path to the XML file containing list of blob paths or blob path
          prefixes for the blobs to be exported. The file format is the same as the
          blob list blob format in the Put Job operation of the Import/Export Service
          REST API.
    /DriveSize:<DriveSize>
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.
          Note: 1 GB = 1,000,000,000 bytes
                1 TB = 1,000,000,000,000 bytes
    /DataSet:<dataset.csv>
        - Required. A .csv file that contains a list of directories and/or a list files
          to be copied to target drives.

    /silentmode
        - Optional. If not specified, it will remind you the requirement of drives and
          need your confirmation to continue.

Examples:

    Copy a data set to a drive:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /InitialDriveSet:driveset1.csv
        /DataSet:data.csv

    Copy another dataset to the same drive following the above command:
    WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#2 /DataSet:dataset2.csv

    Preview how many 1.5 TB drives are needed for an export job:
    WAImportExport.exe PreviewExport
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml
        /DriveSize:1.5TB

    Repair an finished import job:
    WAImportExport.exe RepairImport
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log
```

## <a name="next-steps"></a>Další postup

* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Náhled využití disku pro úlohu exportu](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](../storage-import-export-tool-reviewing-job-status-v1.md)
* [Oprava úlohy importu](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [Oprava úlohy exportu](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
