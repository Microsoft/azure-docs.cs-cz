---
title: Azure stack úložiště rozdíly a aspekty | Dokumentace Microsoftu
description: Znát rozdíly mezi úložiště služby Azure stack a Azure storage, spolu s důležité informace o nasazení služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2018
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.openlocfilehash: 6c0c42763ec3d124850555500c3a322073af2479
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139490"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Úložiště služby Azure stack: rozdíly a aspekty

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Úložiště služby Azure stack je sada cloudových služeb úložiště ve službě Microsoft Azure Stack. Úložiště služby Azure stack nabízí objektů blob, tabulky, fronty a funkce správy účtu se sémantikou konzistentních s Azure.

Tento článek shrnuje známé rozdíly Azure Stack Storage ze služby Azure Storage. Také uvádí, co je třeba zvážit při nasazení Azure Stack. Další informace o nejvýraznějších rozdílů mezi globální Azure a Azure Stack, najdete v článku [klíče aspekty](azure-stack-considerations.md) tématu.

## <a name="cheat-sheet-storage-differences"></a>Tahák: úložiště rozdíly

| Funkce | Azure (globální) | Azure Stack |
| --- | --- | --- |
|File Storage|Cloudové sdílené složky SMB podporované|Není dosud podporován.
|Šifrování služby Azure storage pro neaktivní uložená data|256bitového šifrování AES|Šifrování AES 128-bit nástroje BitLocker
|Typ účtu úložiště|Účty pro obecné účely a Azure blob storage|Pro obecné účely pouze.
|Možnosti replikace|Místně redundantní úložiště, geograficky redundantní úložiště, geograficky redundantní úložiště jen pro čtení a zónově redundantní úložiště|Místně redundantní úložiště.
|Premium Storage|Plně podporované.|Je možné zřídit, ale bez omezení výkonu nebo záruk.
|Managed Disks|Premium a standard podporována|Ještě není podporované.
|Název objektu blob|1 024 znaků (2 048 bajtů)|880 znaků (1,760 bajty)
|Maximální velikost objektu blob bloku|4,75 TB (100 MB × 50 000 bloků)|4,75 TB (100 MB × 50 000 bloků) pro verzi 1802 update nebo novější verze. 50 000 × 4 MB (přibližně 195 GB) pro předchozí verze.
|Kopie snímků objektů blob stránky|Zálohování Azure nespravovaných disků virtuálních počítačů připojený spuštěný virtuální počítač nepodporuje|Ještě není podporované.
|Kopie přírůstkový snímek objektu blob stránky|Premium a objekty BLOB stránky standardní Azure, které jsou podporovány|Ještě není podporované.
|Úrovně úložiště pro ukládání objektů blob|Horké studené a archivní úroveň úložiště.|Ještě není podporované.
Obnovitelného odstranění pro úložiště objektů blob|Preview|Ještě není podporované.
|Maximální velikost objektu blob stránky|8 TB|1 TB
|Velikost stránky objektu blob stránky|512 bajtů|4 KB
|Velikost klíče tabulky klíče oddílu a řádku|1 024 znaků (2 048 bajtů)|až 400 znaků (800 bajtů)
|Snímek objektu BLOB|Maximální počet snímků jeden objekt blob není omezený.|Maximální počet snímků jeden objekt blob je 1 000.|

Existují také rozdíly pomocí metrik storage:

* Transakce data v metrikách storage nerozlišují šířky pásma sítě interní nebo externí.
* Transakce data v metrikách storage neobsahuje virtuální počítač přístup k připojené disky.

## <a name="api-version"></a>Verze API

S úložištěm Azure Stack se podporují následující verze:

Rozhraní API pro služby Azure Storage:

aktualizace 1802 nebo novější:

 - [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
 - [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
 - [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
 - [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Předchozí verze:

 - [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Rozhraní API pro správu služby Azure Storage:

 - [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
 - [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="sdk-versions"></a>Verze sady SDK

Úložiště Azure Stack podporuje následujících klientských knihoven:

| Klientská knihovna | Podporovaná verze služby Azure Stack | Odkaz                                                                                                                                                                                                                                                                                                                                     | Koncový bod specifikace       |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET           | Z 6.2.0 k 8.7.0.          | Balíček Nuget:<br>https://www.nuget.org/packages/WindowsAzure.Storage/<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-net/releases                                                                                                                                                                                    | souboru app.config              |
| Java           | Z 4.1.0 k 6.1.0           | Maven balíček:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-java/releases                                                                                                                                                                    | Nastavení připojovací řetězce      |
| Node.js        | Z 1.1.0 k 2.7.0           | Odkaz na NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Příklad: spuštění "npm nainstalujte azure-storage@2.7.0")<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-node/releases                                                                                                                                         | Deklarace instance služby |
| C++            | Z 2.4.0 k 3.1.0           | Balíček Nuget:<br>https://www.nuget.org/packages/wastorage.v140/<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-cpp/releases                                                                                                                                                                                          | Nastavení připojovací řetězce      |
| PHP            | Z 0.15.0 nastavená na 1.0.0          | Verze Githubu:<br>https://github.com/Azure/azure-storage-php/releases<br> <br>Instalace přes Composer (viz podrobnosti níže)                                                                                                                                                                                                                  | Nastavení připojovací řetězce      |
| Python         | Z 0.30.0 nastavená na 1.0.0          | Verze Githubu:<br>https://github.com/Azure/azure-storage-python/releases                                                                                                                                                                                                                                                                | Deklarace instance služby |
| Ruby           | Z 0.12.1 na 1.0.1          | Balíček RubyGems:<br>Běžné:<br>https://rubygems.org/gems/azure-storage-common/<br>Objekt BLOB: https://rubygems.org/gems/azure-storage-blob/<br>Fronta: https://rubygems.org/gems/azure-storage-queue/<br>Tabulka: https://rubygems.org/gems/azure-storage-table/<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-ruby/releases | Nastavení připojovací řetězce      |

## <a name="next-steps"></a>Další postup

* [Začínáme s Azure Stack Storage vývojové nástroje](azure-stack-storage-dev.md)
* [Úvod do úložiště Azure Stack](azure-stack-storage-overview.md)
