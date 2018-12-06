---
title: Známé problémy s Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Další informace o omezeních a známých problémech s Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: normesta
ms.openlocfilehash: 83e9dfbe18dd79e8547e6b48daef39a5aed2cced
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975281"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Známé problémy s Azure Data Lake Storage Gen2

Tento článek obsahuje známé problémy a omezení s Azure Data Lake Storage Gen2.

## <a name="api-interoperability"></a>Vzájemná funkční spolupráce rozhraní API

Rozhraní API služby BLOB Storage a Azure Data Lake Gen2 API nejsou interoperabilní mezi sebou.

Pokud budete muset použít nástroje pro práci s veškerý obsah, který nahrajete do vašeho účtu, potom nepovolí hierarchické obory názvů v účtu Blob storage než tato rozhraní API stane interoperabilní mezi sebou. Použití účtu úložiště bez hierarchického oboru názvů pak nebudete mít přístup k Data Lake Storage Gen2 konkrétní funkce, jako je například adresář a systém souborů znamená seznamy řízení přístupu.

## <a name="blob-storage-apis"></a>Úložiště objektů BLOB rozhraní API

Zatím nejsou k dispozici pro účty Azure Data Lake Storage Gen 2 rozhraní API služby BLOB storage.

Tato rozhraní API je zakázané, aby zabránilo problémům s přístup zvyšuje ochranu před nechtěnými daty, které mohou nastat, protože zatím nejsou spolupracují se službou Azure Data Lake Gen2 API rozhraní API služby Blob Storage.

Nespravované disky virtuálních počítačů (VM) závisí na těchto rozhraní API, takže pokud chcete povolit hierarchické obory názvů v účtu úložiště, vezměte v úvahu umístění nespravované disky virtuálních počítačů do účtu úložiště, který nemá povolené hierarchické obory názvů.

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

S Azure Data Lake Storage Gen2 souborové systémy zatím nefungují některé funkce v Průzkumníku služby Storage. Tato omezení platí pro [samostatnou verzi](https://azure.microsoft.com/features/storage-explorer/) Průzkumníka služby Azure Storage, jakož i verzi, která se zobrazí na webu Azure Portal.

## <a name="blob-viewing-tool"></a>Nástroj pro zobrazení objektů BLOB

Nástroje pro zobrazení na webu Azure portal objekt BLOB má omezenou podporu pro Azure Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Aplikace třetí strany

Aplikace jiných výrobců nemusí podporovat Azure Data Lake Storage Gen2.

Podpora je uvážení jednotlivých poskytovatelů aplikací třetích stran. V současné době služba Blob storage API a rozhraní API pro Azure Data Lake Storage Gen2 nelze použít ke správě stejný obsah. Jak pracujeme, abychom zajistili interoperabilitu tohoto, je možné, že mnoho nástrojů třetích stran automaticky podpory Azure Data Lake Storage Gen2.

## <a name="azcopy-support"></a>Podpora nástroje AzCopy

AzCopy verze 8 nepodporuje Azure Data Lake Storage Gen2.

Místo toho použijte nejnovější verzi AzCopy ve verzi preview ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ) podporuje koncové body Azure Data Lake Storage Gen2.

## <a name="oauth-authentication"></a>Ověřování OAuth

Služby, jako je Azure Databricks, HDInsight a Azure Data Factory není dosud integrovat ověřování tokenu nosiče OAuth Azure Active Directory (Azure AD).

## <a name="access-control-lists-acl"></a>Seznamy řízení přístupu (ACL)

Adresář a seznamy řízení přístupu na úrovni souboru (ACL) jsou těžko spravuje. Neexistuje žádný nástroj uživatelského rozhraní, můžete použít k získání a nastavení tyto seznamy řízení přístupu.

## <a name="azure-event-grid"></a>Azure Event Grid

[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) neobdrží události z účtů Azure Data Lake Gen2, protože tyto účty nejsou zatím je vygenerovat.  

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Řízení přístupu na základě rolí nejde použít na objekty systému souborů v účtu služby Azure Data Lake Storage Gen2.

## <a name="sql-data-warehouse-polybase"></a>SQL Data Warehouse PolyBase

Když je povoleno úložiště brány firewall na účtu služby Azure Storage, SQL Data Warehouse [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017) nemá přístup k těmto účtům.

## <a name="soft-delete-and-snapshots"></a>Obnovitelného odstranění a snímky

Nejsou k dispozici pro účty Azure Data Lake Storage Gen2 obnovitelného odstranění a snímků.

Všechny funkce správy verzí, včetně [snímky](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) a [obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) zatím nejsou k dispozici pro účty úložiště, které mají povolené hierarchické obory názvů.

## <a name="object-level-storage-tiers"></a>Vrstvy úložiště na úrovni objektu

Objekt vrstvy úrovně úložiště (Hot, studené a archivní) zatím nejsou k dispozici pro účty Azure Data Lake Storage Gen 2, ale jsou dostupné pro účty úložiště, které nemají hierarchické mezery povoleny.

## <a name="azure-blob-storage-lifecycle-management-preview-policies"></a>Zásady Správa (Preview) životního cyklu Azure Blob Storage

Zatím nejsou k dispozici pro účty Azure Data Lake Storage Gen2 zásady Správa (Preview) životního cyklu Azure Blob Storage.

Tyto zásady jsou dostupné pro účty úložiště, které nemají hierarchické mezery povoleny.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Diagnostické protokoly nejsou k dispozici pro účty Azure Data Lake Storage Gen2.

Chcete-li požádat o diagnostické protokoly, obraťte se na podporu Azure. Jim poskytnout název svého účtu a dobu, pro který požadujete protokoly.
