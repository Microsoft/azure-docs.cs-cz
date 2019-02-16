---
title: Rozhodování, jestli použít Azure Blobs, soubory Azure nebo Azure Disks
description: Přečtěte si o různé způsoby, jak ukládat a přistupovat k datům v Azure, která pomáhá že rozhodnete technologii, která má použít.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 869d2105ccf635a46a21e9b7f382ddbef713d68b
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327006"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Rozhodování, jestli použít Azure Blobs, soubory Azure nebo Azure Disks
Microsoft Azure poskytuje několik funkcí ve službě Azure Storage k ukládání a přístup k datům v cloudu. Tento článek popisuje soubory Azure, objekty BLOB a disků a pomůže vám zvolit mezi těmito funkcemi.

## <a name="scenarios"></a>Scénáře
Následující tabulka porovnává souborů, objektů BLOB a disků a ukazuje ukázkové scénáře pro každý odpovídající.

| Funkce | Popis | Kdy je použít |
|--------------|-------------|-------------|
| **Služba soubory Azure** | Poskytuje rozhraní SMB, klientských knihoven a [rozhraní REST](/rest/api/storageservices/file-service-rest-api) , která umožňuje přístup odkudkoli k uložených souborů. | Chcete metodou "lift and shift" aplikace do cloudu, která již používá rozhraní API systému nativní soubor pro sdílení dat mezi ním a dalších aplikací běžících v Azure.<br/><br/>Chcete uložit vývoje a ladicí nástroje, které je potřeba přistupovat z velký počet virtuálních počítačů. |
| **Azure Blobs** | Nabízí klientské knihovny a [rozhraní REST](/rest/api/storageservices/blob-service-rest-api) , který umožňuje nestrukturovaných dat ukládají a získávají ve velkém měřítku v objektech BLOB bloku.<br/><br/>Podporuje také [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) pro řešení pro analýzu velkých objemů dat organizace. | Chcete, aby vaše aplikace podporovat datový proud a scénáře náhodný přístup.<br/><br/>Chcete mít možnost získat přístup k datům aplikace odkudkoli.<br/><br/>Chcete vytvářet data lake enterprise v Azure a provádět analýzy velkých objemů dat. |
| **Disky Azure** | Nabízí klientské knihovny a [rozhraní REST](/rest/api/compute/manageddisks/disks/disks-rest-api) , který umožňuje dat trvale uložena a k němu přistupovat z připojeného virtuálního pevného disku. | Má schopnost přenést aplikace, které používají rozhraní API systému nativní soubor pro čtení a zápis dat do trvalé disky.<br/><br/>Chcete uložit data, která nevyžaduje přístup mimo virtuální počítač, ke kterému je připojený disk. |

## <a name="comparison-files-and-blobs"></a>Porovnání: Soubory a objekty BLOB
Následující tabulka porovnává soubory Azure s využitím objektů BLOB Azure.  
  
||||  
|-|-|-|  
|**Atribut**|**Azure Blobs**|**Služba soubory Azure**|  
|Možnosti odolnosti|LRS, ZRS, GRS, RA-GRS|LRS, ZRS, GRS|  
|Přístupnost|Rozhraní REST API|Rozhraní REST API<br /><br /> Protokol SMB 2.1, tak i SMB 3.0 (standardní systém souborů rozhraní API)|  
|Připojení|Rozhraní REST API – po celém světě|Rozhraní REST API – po celém světě<br /><br /> Protokol SMB 2.1--v rámci oblasti<br /><br /> Protokol SMB 3.0 – po celém světě|  
|Koncové body|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Adresáře|Plochý obor názvů|Hodnota TRUE adresářových objektů|  
|Rozlišování velikosti písmen názvů|Malá a velká písmena se rozlišují.|Malá a velká písmena, ale zachování velikosti písmen|  
|Kapacita|Až 2 omezení PiB účtu |5 TiB sdílené složky|  
|Propustnost|Až 60 MiB/s na objekt blob bloku|Až 60 MiB/s na sdílenou složku|  
|Velikost objektu|Až o 4,75 TB za objekty blob bloku|Až 1 TB na soubor|  
|Billed kapacity|Zapsané bajty podle|Na základě velikosti souborů|  
|Klientské knihovny|Více jazyků|Více jazyků|  
  
## <a name="comparison-files-and-disks"></a>Porovnání: Soubory a disky
Služba soubory Azure doplňují disky Azure. Disk může být připojen pouze na jeden virtuální počítač Azure v čase. Disky jsou pevným formátem virtuální pevné disky uložené jako objekty BLOB stránky ve službě Azure Storage a se používají ve virtuálním počítači k ukládání trvalá data. Sdílené složky ve službě soubory Azure mohou být přístupné stejným způsobem jako přistupovat na místní disk (s použitím rozhraní API systému nativní soubor) a mohou být sdíleny napříč velký počet virtuálních počítačů.  
 
Následující tabulka porovnává soubory Azure pomocí Azure Disks.  
 
||||  
|-|-|-|  
|**Atribut**|**Disky Azure**|**Služba soubory Azure**|  
|Rozsah|Výhradně pro jeden virtuální počítač|Sdílený přístup napříč několika virtuálními počítači|  
|Snímků a kopírování|Ano|Ano|  
|Konfigurace|Připojen při spuštění virtuálního počítače|Připojení po spuštění virtuálního počítače|  
|Authentication|Integrované|Nastavit pomocí příkazu net use|  
|Přístup pomocí rozhraní REST|Soubory virtuálního pevného disku není přístupný.|Soubory uložené ve sdílené složce je přístupný.|  
|Maximální velikost|4 TB disku|5 TiB sdílené složky a 1 TiB souboru v rámci sdílené složky|  
|Maximální počet vstupně-výstupních operací|500 IOps|1 000 IOps|  
|Propustnost|Až 60 MiB/s na Disk|Cíl je 60 MiB/s na sdílenou složku (můžete získat vyšší pro vyšší velikosti vstupně-výstupních operací)|  

## <a name="next-steps"></a>Další postup
Při rozhodování o tom, jak se vaše data ukládají a získávají, měli byste také zvážit náklady na zahrnuté. Další informace najdete v tématu [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
  
Některé funkce protokolu SMB se nevztahují na cloudu. Další informace najdete v tématu [funkce nejsou podporovány službou Azure File](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
  
Další informace o discích najdete v tématu naše [Úvod do managed disks](../../virtual-machines/windows/managed-disks-overview.md) a [jak připojit datový Disk do virtuálního počítače Windows](../../virtual-machines/windows/attach-managed-disk-portal.md).
