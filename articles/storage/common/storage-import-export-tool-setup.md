---
title: Nastavení nástroje pro import a export Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nastavit nástroj pro přípravu a opravu jednotky pro službu Azure Import/Export.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 01432ab68fc399f3e97eac2de5a7c356bef7078a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74979032"
---
# <a name="setting-up-the-azure-importexport-tool"></a>Nastavení nástroje pro import a export Azure

Nástroj microsoft azure import u exportu je nástroj pro přípravu a opravu jednotky, který můžete použít se službou Import a Export Microsoft Azure. Nástroj můžete použít pro následující funkce:

* Před vytvořením úlohy importu můžete tento nástroj použít ke kopírování dat na pevné disky, které budete doručovat do datového centra Azure.
* Po dokončení úlohy importu můžete tento nástroj použít k opravě všech objektů BLOB, které byly poškozeny, chyběly nebo byly v konfliktu s jinými objekty BLOB.
* Po přijetí jednotek z dokončené úlohy exportu můžete tento nástroj použít k opravě všech souborů, které byly poškozeny nebo na jednotkách chyběly.

## <a name="prerequisites"></a>Požadavky

Pokud **připravujete jednotky** pro úlohu importu, musí být splněny následující požadavky:

* Musíte mít aktivní předplatné Azure.
* Vaše předplatné musí obsahovat účet úložiště s dostatkem volného místa pro ukládání souborů, které chcete importovat.
* Potřebujete alespoň jeden z přístupových klíčů účtu úložiště.
* Potřebujete počítač (dále jen "kopírovací zařízení") se systémem Windows 7, Windows Server 2008 R2 nebo novějším nainstalovaným operačním systémem Windows.
* Rozhraní .NET Framework 4 musí být nainstalováno v kopírovacím počítači.
* Nástroj BitLocker musí být v kopírovacím počítači povolen.
* Potřebujete jeden nebo více prázdných 2,5palcových nebo 3,5palcových pevných disků SATAII nebo III nebo SSD připojených k kopírce.
* Soubory, které chcete importovat, musí být přístupné z kopírovacího zařízení, ať už jsou ve sdílené síťové složce nebo na místním pevném disku.

Pokud se pokoušíte **opravit import,** který částečně selhal, potřebujete:

* Soubory protokolu kopírování
* Klíč účtu úložiště

Pokud se pokoušíte **opravit export,** který částečně selhal, potřebujete:

* Soubory protokolu kopírování
* Soubory manifestu (volitelné)
* Klíč účtu úložiště

## <a name="installing-the-azure-importexport-tool"></a>Instalace nástroje pro import a export Azure

Nejprve [stáhněte nástroj Pro import a export Azure](https://www.microsoft.com/download/details.aspx?id=55280) a extrahujte ho do adresáře v počítači, například `c:\WAImportExport`.

Nástroj pro import a export Azure se skládá z následujících souborů:

* datová sada.csv
* soubor jednotek driveset.csv
* soubor hddid.dll
* Microsoft.Data.Services.Client.dll
* Soubor Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Soubor Microsoft.WindowsAzure.Storage.xml
* WAImportExport.exe
* WAImportExport.exe.config
* WAImportExport.pdb
* Soubor WAImportExportCore.dll
* WAImportExportCore.pdb
* SOUBOR WAImportExportRepair.dll
* WAImportExportRepair.pdb

Dále otevřete okno příkazového řádku v **režimu správce**a změňte na adresář obsahující extrahované soubory.

Chcete-li vytvořit nápovědu pro`WAImportExport.exe`příkaz, spusťte nástroj ( ) bez parametrů:

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

## <a name="next-steps"></a>Další kroky

* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Náhled využití disku pro úlohu exportu](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](../storage-import-export-tool-reviewing-job-status-v1.md)
* [Oprava úlohy importu](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [Oprava úlohy exportu](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
