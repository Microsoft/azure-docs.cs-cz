---
title: Porovnání nástrojů pro migraci Azure Storage – nestrukturovaná data
description: Základní funkce a srovnání mezi nástroji používanými k migraci nestrukturovaných dat
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 15daeb0e6bf320a0727d8e6ea502063a30e67ad0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231471"
---
# <a name="comparison-matrix"></a>Srovnávací matice

Následující srovnávací tabulka ukazuje základní funkce různých nástrojů, které se dají použít k migraci nestrukturovaných dat. 

## <a name="supported-azure-services"></a>Podporované služby Azure

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Název řešení**  | [Synchronizace souborů Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Mobilita a migrace dat](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentní Správa dat](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Podpora souborů Azure (všechny úrovně)** | Yes                          | Yes                      | Yes            | Yes                            |
| **Podpora Azure NetApp Files**      | No                           | Yes                      | Yes            | Yes                            |
| **Aktivní/studená podpora Azure Blob**   | No                           | Ano (přes systém souborů NFS Preview)    | Yes            | Yes                            |
| **Podpora na úrovni archivu objektů BLOB v Azure** | No                           | No                       | No             | Ano (jako cíl migrace) |
| **Podpora Azure Data Lake Storage** | No                           | No                       | No             | No                             |
| **Podporované zdroje**      | Windows Server 2012 R2 a více | Servery NAS & cloudových systémů souborů | Jakékoli servery NAS a S3 | NAS, BLOB, S3                  |

## <a name="supported-protocols-source--destination"></a>Podporované protokoly (zdroj/cíl)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Název řešení**   | [Synchronizace souborů Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilita a migrace dat](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentní Správa dat](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Yes | Yes | Yes | Yes |
| **SMB 3.0**       | Yes | Yes | Yes | Yes |
| **SMB 3,1**       | Yes | Yes | Yes | Yes |
| **Systém souborů NFS v3**        | No  | Yes | Yes | Yes |
| **Systém souborů NFS v 4.1**      | No  | Yes | No  | Yes |
| **REST API objektů BLOB** | No  | No  | Yes | Yes |
| **S3**            | No  | Yes | Yes | Yes |

## <a name="extended-features"></a>Rozšířené funkce

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Název řešení**  | [Synchronizace souborů Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilita a migrace dat](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentní Správa dat](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Přemapování UID/SID**                   | No  | Yes                        | Yes | No                             |
| **Přemapování seznamu ACL protokolu**                | No  | No                         | No  | No                             |
| **Podpora DFS**                           | Yes | Yes                        | Yes | Yes                            |
| **Podpora omezování**                    | Yes | Yes                        | Yes | Yes                            |
| **Vyloučení vzoru souborů**               | No  | Yes                        | Yes | Ano (pomocí funkce kopírování) |
| **Podpora pro atributy selektivního souboru** | Yes | Yes                        | Yes | Ano (pro rozšířené atributy)  |
| **Odstranit šíření**                   | Yes | Yes                        | Yes | Yes                            |
| **Sledování spojení NTFS**                 | No  | Yes                        | No  | Yes                            |
| **Přepsat vlastníka a vlastníka skupiny SMB**    | Yes | Yes                        | Yes | No                             |
| **Řetězec pro vytváření sestav o úschově**            | No  | Yes                        | No  | Yes                            |
| **Podpora alternativních datových proudů**    | No  | Yes                        | Yes | No                             |
| **Plánování migrace**              | No  | Yes                        | Yes | Yes                            |
| **Zachování seznamu ACL**                        | No  | Yes                        | Yes | Yes                            |
| **Podpora DACL**                          | Yes | Yes                        | Yes | Yes                            |
| **Podpora SACL**                          | Yes | Yes                        | Yes | No                             |
| **Zachování doby přístupu**                | Yes | Yes                        | Yes | Yes                            |
| **Zachování změněného času**              | Yes | Yes                        | Yes | Yes                            |
| **Zachování času vytvoření**              | No  | Yes                        | Yes | Yes                            |
| **Podpora Azure Data Box**       | Yes | Yes                        | No  | No                             |
| **Migrace snímků**                | No  | Ruční                     | Yes | No                             |
| **Podpora symbolického odkazu**                 | No  | Yes                        | No  | Yes                            |
| **Podpora pevných odkazů**                     | No  | Migrováno jako samostatné soubory | Yes | Yes                            |
| **Podpora pro otevřené/zamčené soubory**       | Yes | Yes                        | Yes | Yes                            |
| **Přírůstková migrace**                 | Yes | Yes                        | Yes | Yes                            |
| **Přepínání podpory**                    | No  | Yes                        | Yes | Ne (ruční)               |
| **[Další funkce](#other-features)**         | [Odkaz](#azure-file-sync)| [Odkaz](#dobimigrate) | [Odkaz](#data-mobility-and-migration) | [Odkaz](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Posouzení a vytváření sestav

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Název řešení**   | [Synchronizace souborů Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilita a migrace dat](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentní Správa dat](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Kapacita**                        | No      | Yes | Yes | Yes            |
| **počet souborů/složek**            | No      | Yes | Yes | Yes            |
| **Distribuce stáří v čase**      | No      | Yes | Yes | Yes            |
| **Čas přístupu**                     | No      | Yes | Yes | Yes            |
| **Změněný čas**                   | No      | Yes | Yes | Yes            |
| **Čas vytvoření**                   | No      | Yes | Yes | Ano (jenom SMB) |
| **Stav sestavy podle souboru nebo objektu** | Částečné | Yes | Yes | Yes            |

## <a name="licensing"></a>Licencování

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Název řešení**   | [Synchronizace souborů Azure](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobilita a migrace dat](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Inteligentní Správa dat](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | NENÍ K DISPOZICI | Yes | Yes | Yes |
| **Závazek Azure** | Yes   | Yes | Yes | Yes |

## <a name="other-features"></a>Další funkce

### <a name="azure-file-sync"></a>Synchronizace souborů Azure

- Vnitřní ověřování hodnotou hash

### <a name="dobimigrate"></a>DobiMigrate

- Předběžné kontroly migrace
- Plánování migrace
- Suché spuštění pro vyjmutí přes testování
- Rozpoznání a upozornění na aktivitu uživatele cílové strany před vyjmutím
- Migrace řízené zásadami
- Plánované iterace kopírování
- Konfigurovatelné možnosti pro zpracování zabezpečení kořenového adresáře
- Spuštění ověřování na vyžádání
- Ověření zpětného čtení dat na zdroji a cíli
- Pracovní postup grafického a interaktivního zpracování chyb
- Možnost omezit šíření některých operací, jako jsou odstranění a aktualizace
- Možnost zachovat čas přístupu ve zdroji (kromě cíle)
- Možnost spouštění vrácení zpět ke zdroji během přepínání migrace
- Možnost Migrace vybraných atributů souboru protokolu SMB
- Možnost vyčistit popisovače zabezpečení systému souborů NTFS
- Možnost přepsat NFSv3 oprávnění a zapsat nové bity režimu do cíle
- Možnost převést koncepty seznamů řízení přístupu NFSv3 POSIX na názvů NFSv4 ACL
- SMB 1 (CIFS)

### <a name="data-mobility-and-migration"></a>Mobilita a migrace dat

- Ověřování algoritmem hash

### <a name="intelligent-data-management"></a>Inteligentní Správa dat

- Migrace na základě projektu nebo adresáře
- Automatické opakování selhání
- Vyhodnocení/generování sestav: typy souborů, velikost souboru, projektový základ
- Vyhodnocení/vytváření sestav: vlastní vyhledávání založené na metadatech
- Úplné řešení pro správu životního cyklu dat pro archivaci, replikaci a analýzu
- Přístup k analýze na základě času objektu blob, dat S3
- Označování
- Podpora 24 x 7 x 365
- Ověřování algoritmem hash

*Seznam byl naposledy ověřen dne 31 2021.*

## <a name="see-also"></a>Viz také

- [Přehled migrace úložiště](../../../common/storage-migration-overview.md)
- [Výběr řešení Azure pro přenos dat](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Migrace do sdílených složek Azure](/azure/storage/files/storage-files-migration-overview)
- [Migrace na Data Lake Storage s platformou WANdisco LiveData pro Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Kopírování nebo přesun dat Azure Storage pomocí AzCopy](https://aka.ms/azcopy)
- [Migrace velkých datových sad do Azure Blob Storage pomocí AzReplicate (ukázková aplikace)](https://github.com/Azure/AzReplicate/tree/master/)
