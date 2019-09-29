---
title: Rozhodnutí o použití objektů blob Azure, souborů Azure nebo disků Azure
description: Přečtěte si o různých způsobech ukládání a přístupu k datům v Azure, které vám pomůžou rozhodnout, kterou technologii použít.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671049"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Rozhodnutí o použití objektů blob Azure, souborů Azure nebo disků Azure

Microsoft Azure poskytuje několik funkcí v Azure Storage pro ukládání a přístup k datům v cloudu. Tento článek popisuje soubory, objekty BLOB a disky Azure a je navržený tak, aby vám usnadnil výběr mezi těmito funkcemi.

## <a name="scenarios"></a>Scénáře

Následující tabulka porovnává soubory, objekty BLOB a disky a ukazuje příklady scénářů, které jsou pro každý z nich vhodné.

| Funkce | Popis | Kdy je použít |
|--------------|-------------|-------------|
| **Služba soubory Azure** | Poskytuje rozhraní protokolu SMB, klientské knihovny a [rozhraní REST](/rest/api/storageservices/file-service-rest-api) , které umožňuje přístup odkudkoli do uložených souborů. | Chcete "nazvednutí a posunutí" aplikace do cloudu, který už používá nativní rozhraní API systému souborů ke sdílení dat mezi IT a dalšími aplikacemi běžícími v Azure.<br/><br/>Chcete uložit nástroje pro vývoj a ladění, které potřebují být dostupné z mnoha virtuálních počítačů. |
| **Azure Blobs** | Poskytuje klientské knihovny a [rozhraní REST](/rest/api/storageservices/blob-service-rest-api) , které umožňuje ukládání nestrukturovaných dat a jejich použití v obrovských škálováních objektů blob bloku.<br/><br/>Také podporuje [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) pro řešení pro analýzy velkých objemů dat v podniku. | Chcete, aby vaše aplikace podporovala scénáře streamování a náhodného přístupu.<br/><br/>Chcete mít přístup k datům aplikací odkudkoli.<br/><br/>Chcete vytvořit službu data Lake pro podniky v Azure a provádět analýzy velkých objemů dat. |
| **Disky Azure** | Poskytuje klientské knihovny a [rozhraní REST](/rest/api/compute/manageddisks/disks/disks-rest-api) , které umožňuje trvalé uložení dat a získání jejich pøístupu z připojeného virtuálního pevného disku. | Chcete načítat a přesouvat aplikace, které používají rozhraní API nativního systému souborů ke čtení a zápisu dat na trvalé disky.<br/><br/>Chcete ukládat data, která se nevyžadují pro použití mimo virtuální počítač, ke kterému je disk připojený. |


## <a name="next-steps"></a>Další kroky

Při rozhodování o způsobu, jakým se data ukládají a jsou k nim přistupované, byste měli vzít v úvahu také příslušné náklady. Další informace najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/).
  
Některé funkce protokolu SMB se nevztahují na Cloud. Další informace najdete v tématu [funkce nepodporované službou Azure File Service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
 
Další informace o objektech blob Azure najdete v našem článku [co je Azure Blob Storage?](../blobs/storage-blobs-overview.md).

Další informace o Disk Storage najdete v našem [úvodu ke spravovaným diskům](../../virtual-machines/windows/managed-disks-overview.md).

Další informace o službě soubory Azure najdete v našem článku [Plánování nasazení souborů Azure](../files/storage-files-planning.md).