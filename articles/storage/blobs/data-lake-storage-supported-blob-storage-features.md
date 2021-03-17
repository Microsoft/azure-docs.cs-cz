---
title: Funkce BLOB Storage dostupné v Azure Data Lake Storage Gen2 | Microsoft Docs
description: Zjistěte, které funkce služby Blob Storage můžete použít s Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.openlocfilehash: 769163dd09ec29c2c206d0273e71de27732fb9db
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516274"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Funkce BLOB Storage dostupné v Azure Data Lake Storage Gen2

Blob Storage funkce, jako je [protokolování diagnostiky](../common/storage-analytics-logging.md), [úrovně přístupu](storage-blob-storage-tiers.md)a [zásady správy životního cyklu BLOB Storage](storage-lifecycle-management-concepts.md) , teď fungují s účty, které mají hierarchický obor názvů. Proto můžete v účtech BLOB Storage povolit hierarchické obory názvů bez ztráty přístupu k těmto funkcím.

V této tabulce jsou uvedené funkce služby Blob Storage, které můžete použít s Azure Data Lake Storage Gen2. Položky, které se zobrazí v těchto tabulkách, se v průběhu času změní, dokud je podpora stále rozbalená. Další informace o konkrétních problémech přidružených ke stavu verze Preview funkce najdete v článku [známé problémy](data-lake-storage-known-issues.md) .

## <a name="supported-blob-storage-features"></a>Podporované funkce Blob Storage

Následující tabulka ukazuje, jak jsou jednotlivé funkce úložiště objektů BLOB podporované v Data Lake Storage Gen2. Existuje sloupec pro úrovně výkonu Standard a [Premium](premium-tier-for-data-lake-storage.md) . 

|Blob Storage funkce |Standard |Premium |Související články |
|---------------|-------------------|---|
|Horká úroveň úložiště|Obecná dostupnost|Nepodporováno|[Azure Blob Storage: Horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md)|
|Studená úroveň úložiště|Obecná dostupnost|Nepodporováno|[Azure Blob Storage: Horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md)|
|Události|Obecná dostupnost|Obecná dostupnost|[Reakce na události služby Blob Storage](storage-blob-event-overview.md)|
|Metriky (klasické)|Obecná dostupnost|Obecná dostupnost|[Metriky Azure Storage Analytics (klasické)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metriky na platformě Azure Monitor|Obecná dostupnost|Preview|[Metriky Azure Storage ve službě Azure Monitor](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Příkazy prostředí PowerShell pro úložiště objektů BLOB|Obecná dostupnost|Obecná dostupnost|[Rychlý Start: nahrání, stažení a výpis objektů BLOB pomocí PowerShellu](storage-quickstart-blobs-powershell.md)|
|Příkazy Azure CLI služby Blob Storage|Obecná dostupnost|Obecná dostupnost|[Rychlý Start: vytvoření, stažení a výpis objektů BLOB pomocí Azure CLI](storage-quickstart-blobs-cli.md)|
|Rozhraní API pro úložiště objektů BLOB|Obecná dostupnost|Obecná dostupnost|[Rychlý start: Klientská knihovna úložiště objektů blob Azure verze 12 pro .NET](storage-quickstart-blobs-dotnet.md)<br>[Rychlý Start: Správa objektů BLOB pomocí sady Java V12 SDK](storage-quickstart-blobs-java.md)<br>[Rychlý Start: Správa objektů BLOB pomocí sady Python V12 SDK](storage-quickstart-blobs-python.md)<br>[Rychlý Start: Správa objektů BLOB pomocí sady JavaScript V12 SDK v Node.js](storage-quickstart-blobs-nodejs.md)|
|Diagnostické protokoly|Obecná dostupnost|Preview |[Protokolování analýz služby Azure Storage](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Archivní úroveň přístupu|Obecná dostupnost|Nepodporováno|[Azure Blob Storage: Horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md)|
|Zásady správy životního cyklu (vrstvení)|Obecná dostupnost|Zatím nepodporováno|[Správa životního cyklu úložiště objektů blob v Azure](storage-lifecycle-management-concepts.md)|
|Zásady správy životního cyklu (odstranit objekt BLOB)|Obecná dostupnost|Obecná dostupnost|[Správa životního cyklu úložiště objektů blob v Azure](storage-lifecycle-management-concepts.md)|
|Přihlašování Azure Monitor|Preview |Preview|[Azure Storage monitorování](./monitor-blob-storage.md)|
|Snímky|Preview<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Preview<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Snímky objektů BLOB](snapshots-overview.md)|
|Statické weby|Preview<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Preview<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Hostování statického webu ve službě Azure Storage](storage-blob-static-website.md)|
|Neměnné úložiště|Preview<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Preview<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Ukládání důležitých podnikových dat objektů blob s využitím neměnného úložiště](storage-blob-immutable-storage.md)|
|Obnovitelné odstranění kontejneru|Preview|Preview|[Obnovitelné odstranění pro kontejnery (Preview)](soft-delete-container-overview.md)|
|Inventář Azure Storage|Preview|Preview|[Použití inventáře Azure Storage pro správu dat objektů BLOB (Preview)](blob-inventory.md)|
|Vlastní domény|Preview<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|Preview<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|[Mapování vlastní domény na koncový bod služby Azure Blob Storage](storage-custom-domain-name.md)|
|Obnovitelné odstranění objektu BLOB|Zatím nepodporováno|Zatím nepodporováno|[Obnovitelné odstranění pro objekty blob](./soft-delete-blob-overview.md)|
|Blobfuse|Obecná dostupnost|Obecná dostupnost|[Postup připojení úložiště objektů BLOB jako systému souborů pomocí blobfuse](storage-how-to-mount-container-linux.md)|
|Anonymní veřejný přístup |Obecná dostupnost|Obecná dostupnost| Viz téma [Konfigurace anonymního veřejného přístupu pro čtení pro kontejnery a objekty blob](anonymous-read-access-configure.md).|
|Převzetí služeb při selhání účtu spravovaného zákazníkem|Zatím nepodporováno|Zatím nepodporováno|[Zotavení po havárii a převzetí služeb při selhání účtu](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Klíče poskytované zákazníky|Zatím nepodporováno|Zatím nepodporováno|[Zadání šifrovacího klíče pro požadavek na úložiště objektů BLOB](encryption-customer-provided-keys.md)|
|Obory šifrování|Zatím nepodporováno|Zatím nepodporováno|[Vytváření a Správa oborů šifrování (Preview)](encryption-scope-manage.md)|
|Změna kanálu|Zatím nepodporováno|Zatím nepodporováno|[Změna podpory kanálu ve službě Azure Blob Storage](storage-blob-change-feed.md)|
|Replikace objektů|Zatím nepodporováno|Zatím nepodporováno|[Konfigurace replikace objektů pro objekty blob bloku](object-replication-configure.md)|
|Správa verzí objektů BLOB|Zatím nepodporováno|Zatím nepodporováno|[Povolení a správa verzí objektů BLOB](versioning-enable.md)|

<div id="preview-form"><sup>1</sup> Pokud chcete používat snímky, neměnné úložiště nebo statické weby s Data Lake Storage Gen2, musíte se zaregistrovat ve verzi Preview, a to tak, že tento <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>formulář</a>dokončíte.  </div>
<div id="preview-form-2"><sup>2</sup> . Vlastní název domény se dá mapovat jenom na koncový bod služby BLOB Service nebo statického webu. Koncový bod úložiště Data Lake není podporován. </a> .  </div>

## <a name="see-also"></a>Viz také

- [Známé problémy s Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Open Source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Přístup k více protokolům na Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)