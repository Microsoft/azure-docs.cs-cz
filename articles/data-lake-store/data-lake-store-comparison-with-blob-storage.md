---
title: Porovnání Azure Data Lake Storage Gen1 s objektem blob úložiště Azure | Dokumenty společnosti Microsoft
description: Porovnání Azure Data Lake Storage Gen1 s objektem blob úložiště Azure
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 7c958c3ed4d6ddaabd87f053005fcfc1eba8c842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438722"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Porovnání Azure Data Lake Storage Gen1 a Azure Blob Storage

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

Tabulka v tomto článku shrnuje rozdíly mezi Azure Data Lake Storage Gen1 a Azure Blob Storage podél některé klíčové aspekty zpracování velkých objemů dat. Azure Blob Storage je úložiště škálovatelných objektů pro obecné účely, které je navržené pro širokou škálu scénářů úložiště. Azure Data Lake Storage Gen1 je úložiště s hyperškálovat, které je optimalizované pro úlohy analýzy velkých objemů dat.

|  | Azure Data Lake Storage Gen1 | Azure Blob Storage |
| --- | --- | --- |
| Účel |Optimalizované úložiště pro úlohy analýzy velkých objemů dat |Úložiště objektů pro obecné účely pro širokou škálu scénářů úložiště, včetně analýzy velkých objemů dat |
| Případy použití |Dávkové, interaktivní, streamovací analýzy a data strojového učení, jako jsou soubory protokolu, data IoT, toky kliknutí, velké datové sady |Jakýkoli typ textu nebo binární data, jako je například back-end aplikace, záložní data, úložiště médií pro streamování a data pro obecné účely. Kromě toho úplnou podporu pro analytické úlohy; dávkové, interaktivní, streamovací analýzy a data strojového učení, jako jsou soubory protokolu, data IoT, toky kliknutí, velké datové sady |
| Klíčové koncepty |Účet Data Lake Storage Gen1 obsahuje složky, které zase obsahují data uložená jako soubory |Účet úložiště má kontejnery, které zase mají data ve formě objektů BLOB |
| Struktura |Hierarchický systém souborů |Úložiště objektů s plochým oborem názvů |
| rozhraní API |ROZHRANÍ REST API přes protokol HTTPS |ROZHRANÍ REST API přes PROTOKOL HTTP/HTTPS |
| Rozhraní API na straně serveru |[Rozhraní REST API kompatibilní s WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Rozhraní REST úložiště objektů blob Azure](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Klient souborového systému Hadoop |Ano |Ano |
| Datové operace – ověřování |Na základě [identit služby Azure Active Directory](../active-directory/develop/authentication-scenarios.md) |Na základě sdílených tajných klíčů – [klíče pro přístup k účtu](../storage/common/storage-account-keys-manage.md) a klíče [sdílených podpisů .](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Datové operace – ověřovací protokol |OAuth 2.0. Volání musí obsahovat platný JWT (JSON Webový token) vydaný službou Azure Active Directory. |Kód ověřování zpráv založený na hash (HMAC) . Volání musí obsahovat base64 kódované SHA-256 hash přes část požadavku HTTP. |
| Datové operace – autorizace |Seznamy řízení přístupu POSIX (Seznamy Řízení přístupu).  Seznamy ACL založené na identitách služby Azure Active Directory lze nastavit na úrovni souborů a složek. |Pro autorizaci na úrovni účtu – použít [klíče pro přístup k účtu](../storage/common/storage-account-keys-manage.md)<br>Autorizace účtu, kontejneru nebo objektu blob – použití [klíčů podpisu sdíleného přístupu](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Datové operace - auditování |K dispozici. Informace naleznete [zde.](data-lake-store-diagnostic-logs.md) |K dispozici. |
| Šifrovací data v klidovém stavu |<ul><li>Transparentní, strana serveru</li> <ul><li>S klíči spravovanými službou</li><li>Díky klíčům spravovaným zákazníkem v Azure KeyVault</li></ul></ul> |<ul><li>Transparentní, strana serveru</li> <ul><li>S klíči spravovanými službou</li><li>S klíči spravovanými zákazníky v Azure KeyVault (preview)</li></ul><li>Šifrování na straně klienta</li></ul> |
| Operace řízení (např. vytvoření účtu) |[Řízení přístupu na základě rolí](../role-based-access-control/overview.md) (RBAC) poskytované Azure pro správu účtů |[Řízení přístupu na základě rolí](../role-based-access-control/overview.md) (RBAC) poskytované Azure pro správu účtů |
| Sady SDK pro vývojáře |.NET, Java, Python, Node.js |.Net, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Výkon pracovního vytížení analytics |Optimalizovaný výkon pro úlohy paralelní analýzy. Vysoká propustnost a vipos. |Optimalizovaný výkon pro úlohy paralelní analýzy. |
| Omezení velikosti |Žádné omezení velikosti účtů, velikostí souborů nebo počtu souborů |Konkrétní limity najdete v tématu [Škálovatelnost cíle pro účty standardní úložiště](../storage/common/scalability-targets-standard-account.md) a [škálovatelnost a cíle výkonu pro úložiště objektů blob](../storage/blobs/scalability-targets.md). Větší limity účtů jsou k dispozici tak, že kontaktujete [podporu Azure](https://azure.microsoft.com/support/faq/) |
| Geografická redundance |Místně redundantní (více kopií dat v jedné oblasti Azure) |Místně redundantní (LRS), zóna redundantní (ZRS), globálně redundantní (GRS), přístup pro čtení globálně redundantní (RA-GRS). Více informací naleznete [zde](../storage/common/storage-redundancy.md) |
| Stav služby |Obecná dostupnost |Obecná dostupnost |
| Regionální dostupnost |Viz [zde](https://azure.microsoft.com/regions/#services) |K dispozici ve všech oblastech Azure |
| Price |Viz [Ceny](https://azure.microsoft.com/pricing/details/data-lake-store/) |Viz [Ceny](https://azure.microsoft.com/pricing/details/storage/) |


