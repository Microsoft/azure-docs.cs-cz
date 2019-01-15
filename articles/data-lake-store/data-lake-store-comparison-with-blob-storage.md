---
title: Porovnání Azure Data Lake Storage Gen1 s Azure Storage Blob | Dokumentace Microsoftu
description: Porovnání Azure Data Lake Storage Gen1 s Azure Storage Blob
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 4bdebe415bcd86867bebeb7f03dc6dafa8480169
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259780"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Porovnání Azure Data Lake Storage Gen1 a Azure Blob Storage
  >[!NOTE] 
    >[Azure Data Lake Storage Gen2 Preview](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) byla nedávno jsme oznámili. Verzi Preview služby data Lake Storage Gen2 je výsledkem slučuje možnosti naše dva existující služby storage, Azure Blob Storage a [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index). Funkce z Azure Data Lake Storage Gen1, jako je například sémantiku systému souborů, souboru zabezpečení na úrovni a škálování se sloučí s s nízkými náklady, vrstveného úložiště, možnosti obnovení vysoká dostupnost/zotavení po havárii z [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). <br> Důrazně doporučujeme začít používat Data Lake Storage Gen2 Preview jako základ pro vytváření podniku datová jezera a získáte to nejlepší z úložiště objektů Blob v Azure, stejně jako Azure Data Lake Storage Gen1.

Tabulky v tomto článku shrnuje rozdíly mezi Azure Data Lake Storage Gen1 a Azure Blob Storage podél některé klíčové aspekty zpracování velkých objemů dat. Azure Blob Storage je obecný, škálovatelné úložiště objektů, která je navržená pro širokou škálu scénářů úložiště. Azure Data Lake Storage Gen1 je vysoce škálovatelné úložiště, která je optimalizována pro úlohy analýzy velkých objemů dat.





|  | Azure Data Lake Storage Gen1 | Azure Blob Storage |
| --- | --- | --- |
| Účel |Storage optimalizované pro úlohy analýzy velkých objemů dat |Úložiště objektů pro obecné účely pro nejrůznější scénáře využití služby storage, včetně analýzy velkých objemů dat |
| Případy použití |Dávka, interaktivní, streaming analytics a machine learning data jako například soubory, IoT data protokolu, klikněte na tlačítko streamů, velkých datových sad |Jakýkoli typ textových nebo binárních dat, jako jsou například aplikace back end, zálohování dat, úložiště médií pro streamování a obecné účely – data. Kromě toho plnou podporu pro úlohy související s analýzou; dávka, interaktivní, streaming analytics a machine learning data jako například soubory, IoT data protokolu, klikněte na tlačítko streamů, velkých datových sad |
| Klíčové koncepty |Účet data Lake Storage Gen1 obsahuje složky, který zase obsahuje data uložená jako soubory |Účet úložiště obsahuje kontejnerů, která naopak má data ve formě objektů BLOB |
| Struktura |Hierarchický systém souborů |Úložiště objektů s plochým oborem názvů |
| Rozhraní API |Rozhraní REST API přes protokol HTTPS |Rozhraní REST API přes HTTP/HTTPS |
| Rozhraní API na straně serveru |[Kompatibilních s WebHDFS REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Rozhraní REST API služby Azure Blob Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Klienta systému souborů Hadoop |Ano |Ano |
| Operace s daty – ověření |Na základě [identit Azure Active Directory](../active-directory/develop/authentication-scenarios.md) |Podle sdílené tajné klíče - [přístupové klíče účtu](../storage/common/storage-account-manage.md#access-keys) a [klíče podpisu pro sdílený přístup](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Operace s daty – ověřovací protokol |OAuth 2.0. Volání musí obsahovat platný token JWT (JSON Web Token) vydané službou Azure Active Directory |Hash-based Message Authentication Code (metoda HMAC). Volání musí obsahovat hodnotu hash SHA-256 s kódováním Base64 přes část požadavku HTTP. |
| Operace s daty – autorizace |POSIX seznamy řízení přístupu (ACL).  Seznamy řízení přístupu na základě Azure Active Directory identit můžete nastavit na úrovni souborů a složek. |Pro autorizaci na úrovni účtu – použijte [přístupové klíče účtu](../storage/common/storage-account-manage.md#access-keys)<br>Pro účet, kontejner nebo objekt blob autorizace – použijte [sdílené přístupové klíče podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Operace s daty – auditování |K dispozici. Zobrazit [tady](data-lake-store-diagnostic-logs.md) informace. |K dispozici. |
| Šifrování neaktivních uložených dat |<ul><li>Transparentní, na straně serveru</li> <ul><li>S použitím klíčů spravovaných služeb</li><li>Pomocí klíčů spravovaných zákazníkem v Azure Key Vaultu</li></ul></ul> |<ul><li>Transparentní, na straně serveru</li> <ul><li>S použitím klíčů spravovaných služeb</li><li>Pomocí klíčů spravovaných zákazníkem v Azure Key Vaultu (preview)</li></ul><li>Šifrování na straně klienta</li></ul> |
| Operace správy (například účet vytvořit) |[Řízení přístupu na základě rolí](../role-based-access-control/overview.md) (RBAC) poskytovaný platformou Azure pro správu účtu |[Řízení přístupu na základě rolí](../role-based-access-control/overview.md) (RBAC) poskytovaný platformou Azure pro správu účtu |
| Sady SDK pro vývojáře |.NET, Java, Python, Node.js |.NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Výkon úloh Analytics |Optimalizaci výkonu pro paralelní analýzy úloh. Vysoká propustnost a vstupně-výstupních operací. |Optimalizaci výkonu pro paralelní analýzy úloh. |
| Omezení velikosti |Žádná omezení velikosti účtů, velikosti souborů nebo počet souborů |Zvláštní omezení, které jsou popsány [tady](../storage/common/storage-scalability-targets.md). Větší účet omezuje dostupné kontaktováním [podpory Azure](https://azure.microsoft.com/support/faq/) |
| Geografická redundance |Místně redundantní (více kopií dat v jedné oblasti Azure) |Místně redundantní (LRS), zóna redundantní (ZRS), globálně redundantní (GRS), globálně redundantní jen pro čtení (RA-GRS). Zobrazit [tady](../storage/common/storage-redundancy.md) Další informace |
| Stav služby |Obecně dostupná |Obecně dostupná |
| Regionální dostupnost |Zobrazit [zde](https://azure.microsoft.com/regions/#services) |K dispozici ve všech oblastech Azure |
| Cena |Zobrazit [ceny](https://azure.microsoft.com/pricing/details/data-lake-store/) |Zobrazit [ceny](https://azure.microsoft.com/pricing/details/storage/) |


