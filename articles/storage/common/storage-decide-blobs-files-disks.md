---
title: Rozhodování o použití objektů BLOB služby Azure, Azure soubory nebo disky Azure
description: Další informace o různé způsoby, jak ukládat a přistupovat k datům v Azure a pomoci, že se že rozhodnete technologii, která má použít.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/28/2018
ms.author: tamram
ms.openlocfilehash: ded0884ff83cc214d78f65fed8cefa646f11d952
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Rozhodování o použití objektů BLOB služby Azure, Azure soubory nebo disky Azure

Microsoft Azure poskytuje několik funkcí ve službě Azure Storage k ukládání a přístup k datům v cloudu. Tento článek popisuje soubory Azure, objekty BLOB a disků a je navržená tak, abyste si mohli vybrat mezi těmito funkcemi.

## <a name="scenarios"></a>Scénáře

Následující tabulka porovnává souborů, objektů BLOB a disků a uvádí příklad scénáře vhodné pro každý.

| Funkce | Popis | Kdy je použít |
|--------------|-------------|-------------|
| **Soubory Azure** | Poskytuje rozhraní protokolu SMB, knihovny klienta a [rozhraní REST](/rest/api/storageservices/file-service-rest-api) umožňuje přístup odkudkoli k uložených souborů. | Chcete "navýšení a posunutí" aplikace do cloudu, což už používá rozhraní API systému nativní soubor sdílet data mezi nimi a dalších aplikací běžících v Azure.<br/><br/>Chcete uložit vývoj a ladicí nástroje, které je třeba získat přístup z více virtuálních počítačů. |
| **Azure Blobs** | Poskytuje klientské knihovny a [rozhraní REST](/rest/api/storageservices/blob-service-rest-api) umožňuje nestrukturovaných dat na ukládat a přistupovat v masivním měřítku v objekty BLOB bloku. | Chcete, aby vaše aplikace pro podporu streamování a scénáře náhodný přístup.<br/><br/>Chcete být schopni přistupovat data aplikací z libovolného místa. |
| **Disky systému Azure** | Poskytuje klientské knihovny a [rozhraní REST](/rest/api/compute/manageddisks/disks/disks-rest-api) data trvale ukládat a přistupovat z připojený virtuální pevný disk, který umožňuje. | Chcete navýšení a posunutí aplikace, které používají rozhraní API systému nativní souboru pro čtení a zápisu dat do trvalého disky.<br/><br/>Chcete uložit data, která nemusí být přístupná z mimo virtuální počítač, ke kterému je disk připojen. |

## <a name="comparison-files-and-blobs"></a>Porovnání: Soubory a objekty BLOB

Následující tabulka porovnává soubory Azure s Azure BLOB.  
  
||||  
|-|-|-|  
|**Atribut**|**Azure Blobs**|**Soubory Azure**|  
|Možnosti odolnost|LRS, ZRS, GRS, RA-GRS|LRS, ZRS, GRS|  
|Usnadnění přístupu|Rozhraní REST API|Rozhraní REST API<br /><br /> SMB 2.1 a SMB 3.0 (systém souborů standardní rozhraní API)|  
|Připojení|Rozhraní REST API – po celém světě|Rozhraní REST API – po celém světě<br /><br /> Protokol SMB 2.1--v rámci oblasti<br /><br /> Protokol SMB 3.0 – po celém světě|  
|Koncové body|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Adresáře|Plochý obor názvů|Hodnota TRUE, directory objekty|  
|Rozlišování názvů|Malá a velká písmena se rozlišují.|Případ malých a velkých písmen, ale případu zachování|  
|Kapacita|Až 500 TiB kontejnery|5 TiB sdílené složky|  
|Propustnost|Až 60 MiB za sekundu na jeden objekt blob bloku|Až 60 MiB nebo s za sdílené složky|  
|Velikost objektu|Až o 4.75 TiB na objekt blob bloku|Až 1 TiB na soubor|  
|Fakturovaná kapacity|Podle zapsaných bajtů|Podle velikosti souboru|  
|Klientské knihovny|Více jazyků|Více jazyků|  
  
## <a name="comparison-files-and-disks"></a>Porovnání: Soubory a disky

Soubory Azure doplňují Azure disky. Disk lze připojit pouze k jedné virtuální počítač Azure v čase. Disky jsou pevného formátu virtuální pevné disky uložené jako objekty BLOB stránky ve službě Azure Storage a se používají pro virtuální počítač k ukládání trvalá data. Sdílené složky v souborech Azure je přístupná stejným způsobem, jako je přistupovat na místní disk (pomocí systému souborů nativní rozhraní API) a můžete sdílet mezi více virtuálních počítačů.  
 
Následující tabulka porovnává soubory Azure s Azure disky.  
 
||||  
|-|-|-|  
|**Atribut**|**Disky systému Azure**|**Soubory Azure**|  
|Rozsah|Výhradně pro jeden virtuální počítač|Sdílený přístup napříč více virtuálních počítačů|  
|Snímků a kopírování|Ano|Ne|  
|Konfigurace|Připojení při spuštění virtuálního počítače|Připojení po spuštění virtuálního počítače|  
|Authentication|Integrované|Nastavit pomocí příkazu net use|  
|Vyčištění|Automaticky|Ručně|  
|Přístup pomocí REST|Nelze získat přístup k souborů v rámci virtuální pevný disk|Soubory uložené ve sdílené složce je přístupná.|  
|Maximální velikost|4 TiB disku|5 TiB sdílené složky a 1 TiB soubor ve složce|  
|Maximální IOps 8KB|500 IOps|1000 IOps|  
|Propustnost|Až 60 MiB/s na Disk|Až 60 MiB/s na základě sdílené složky|  

## <a name="next-steps"></a>Další postup

Při rozhodování o tom, jak je vaše data uložené a získat přístup, měli byste také zvážit náklady související se situací. Další informace najdete v tématu [Azure Storage – ceny](https://azure.microsoft.com/pricing/details/storage/).
  
Některé funkce protokolu SMB se nedají použít do cloudu. Další informace najdete v tématu [funkce nejsou podporovány službou Azure File](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
  
Další informace o discích najdete v tématu [Správa disků a bitové kopie](../../virtual-machines/windows/about-disks-and-vhds.md) a [jak připojit datový Disk do virtuálního počítače s Windows](../../virtual-machines/windows/attach-managed-disk-portal.md).
