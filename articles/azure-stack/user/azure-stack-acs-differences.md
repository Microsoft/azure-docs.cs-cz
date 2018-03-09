---
title: "Zásobník úložiště Azure: Rozdíly a aspekty"
description: "Porozumějte rozdílům mezi zásobník úložiště Azure a Azure Storage, společně s aspekty nasazení Azure zásobníku."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviwer: xiaofmao
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: jeffgilb
ms.openlocfilehash: 7c4f030018f388302c3b60a41086bbd97c86513d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Zásobník úložiště Azure: Rozdíly a aspekty

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Azure zásobníku úložiště je sada cloudových služeb úložiště v Microsoft Azure zásobníku. Úložiště Azure zásobníku obsahuje objekt blob, tabulky, fronty a funkce správy účet s Azure konzistentní sémantiku.

Tento článek shrnuje známé rozdíly zásobník úložiště Azure z úložiště Azure. Shrnuje také další důležité informace, třeba vzít v úvahu při nasazování Azure zásobníku. Další informace o nejvýraznějších rozdílů mezi zásobník Azure a Azure, najdete v článku [klíčové aspekty](azure-stack-considerations.md) tématu.

## <a name="cheat-sheet-storage-differences"></a>Tahák: rozdíly úložiště

| Funkce | Azure (global) | Azure Stack |
| --- | --- | --- |
|File Storage|Cloudové sdílené složky SMB podporované|Není dosud podporován.
|Šifrování služby Azure Storage pro neaktivní uložená data|šifrování AES 256 bitů|Nástroj BitLocker šifrování AES 128-bit
|Typ účtu úložiště|Účty úložiště Blob pro obecné účely a Azure|Pro obecné účely jenom.
|Možnosti replikace|Místně redundantní úložiště, geograficky redundantní úložiště, geograficky redundantní úložiště s přístupem pro čtení a zónově redundantní úložiště|Místně redundantní úložiště.
|Storage úrovně Premium|Plně podporovány.|Může být zřízen, ale žádné omezení výkonu nebo záruk.
|Managed Disks|Premium a standard podporováno|Není dosud podporován.
|Název objektu blob|1 024 znaků (2 048 bajtů)|880 znaků (1,760 bajty)
|Maximální velikost objektu blob bloku|4.75 TB (100 MB × 50 000 bloků)|4.75 TB (100 MB × 50 000 bloků) pro aktualizace 1802 nebo novější verze. 50 000 × 4 MB (poli 195 GB) pro předchozí verze.
|Kopie snímků objekt blob stránky|Zálohování nespravované virtuální počítač disky Azure připojené k spuštění virtuálního počítače podporované|Není dosud podporován.
|Kopie přírůstkový snímek objekt blob stránky|Premium a objekty BLOB stránky standardní Azure podporován|Není dosud podporován.
|Vrstvy úložiště pro úložiště objektů blob|Za cool a archivaci vrstev úložiště.|Není dosud podporován.
Obnovitelného odstranění pro úložiště objektů blob|Preview|Není dosud podporován.
|Maximální velikost stránky objektu blob|8 TB|1 TB
|Velikost stránky objektu blob stránky|512 bajtů|4 KB
|Velikost klíče tabulky klíč oddílu a řádku|1 024 znaků (2 048 bajtů)|400 znaků (800 bajtů)

### <a name="metrics"></a>Metriky
Existují také některé rozdíly mezi metriky úložiště:
* Data transakce v úložiště metriky nerozlišuje interních nebo externích šířku pásma sítě.
* Data transakce v úložiště metriky nezahrnuje virtuálnímu počítači přístup k připojené disky.

## <a name="api-version"></a>Verze API
S Azure zásobníku úložiště jsou podporovány následující verze:

Úložiště Azure services rozhraní API:

Aktualizovat 1802 nebo novější:
 - [2017-04-17](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08 ](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-04-05)

Předchozí verze:
 - [2015-04-05](https://docs.microsoft.com/en-us/rest/api/storageservices/version-2015-04-05)


Správa rozhraní API služeb úložiště Azure:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>Verze sady SDK

Zásobník úložiště Azure podporuje následující knihovny klienta:

| Klientská knihovna | Azure zásobníku podporovaná verze | Odkaz                                                                                                                                                                                                                                                                                                                                     | Koncový bod specifikace       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | Z 6.2.0 k 8.7.0.          | Balíček Nuget:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | soubor App.config              |
| Java           | Z 4.1.0 k 6.1.0           | Balíček maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Nastavení připojení řetězce      |
| Node.js        | Z 1.1.0 k 2.7.0           | NPM odkaz:<br>https://www.npmjs.com/package/azure-storage<br>(Příklad: spuštění "npm nainstalujte azure-storage@2.7.0")<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Deklarace instance služby |
| C++            | Z 2.4.0 k 3.1.0           | Balíček Nuget:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Nastavení připojení řetězce      |
| PHP            | Z 0.15.0 k 1.0.0          | Verze Githubu:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Nainstalovat prostřednictvím autora (viz níže podrobnosti)                                                                                                                                                                                                                  | Nastavení připojení řetězce      |
| Python         | Z 0.30.0 k 1.0.0          | Verze Githubu:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Deklarace instance služby |
| Ruby           | Z 0.12.1 k 1.0.1          | Balíček RubyGems:<br>Běžné:<br>https://rubygems.org/gems/azure-storage-common/<br>Blob: https://rubygems.org/gems/azure-storage-blob/<br>Queue: https://rubygems.org/gems/azure-storage-queue/<br>Table: https://rubygems.org/gems/azure-storage-table/<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-ruby/releases | Nastavení připojení řetězce      |

## <a name="next-steps"></a>Další postup

* [Začínáme s Azure Storage zásobníku nástroje pro vývoj](azure-stack-storage-dev.md)
* [Úvod do Azure zásobník úložiště](azure-stack-storage-overview.md)

