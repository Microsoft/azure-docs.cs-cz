---
title: Známé problémy s Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Další informace o omezeních a známých problémech s Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: normesta
ms.openlocfilehash: d42d05046f4ef133aa3dfb52bbe4e2b72255ccce
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322888"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Známé problémy s Azure Data Lake Storage Gen2

Tento článek obsahuje známé problémy a omezení s Azure Data Lake Storage Gen2.

## <a name="api-interoperability"></a>Vzájemná funkční spolupráce rozhraní API

Rozhraní API služby BLOB Storage a Azure Data Lake Gen2 API nejsou interoperabilní mezi sebou.

Pokud budete muset použít nástroje pro práci s veškerý obsah, který nahrajete do vašeho účtu, potom nepovolí hierarchické obory názvů v účtu Blob storage než tato rozhraní API stane interoperabilní mezi sebou. Použití účtu úložiště bez hierarchického oboru názvů pak nebudete mít přístup k Data Lake Storage Gen2 konkrétní funkce, jako je například adresář a systém souborů znamená seznamy řízení přístupu.

## <a name="blob-storage-apis"></a>Úložiště objektů BLOB rozhraní API

Zatím nejsou k dispozici pro Azure Data Lake Storage Gen2 účty úložiště objektů BLOB rozhraní API.

Tato rozhraní API je zakázané, aby zabránilo problémům s přístup zvyšuje ochranu před nechtěnými daty, které mohou nastat, protože zatím nejsou spolupracují se službou Azure Data Lake Gen2 API rozhraní API služby Blob Storage.

Pokud jste použili tato rozhraní API k načtení dat, než byly zakázány, aby bylo produkční požadavek na přístup k datům, obraťte se Microsoft Support s následujícími informacemi:

* ID předplatného (identifikátor GUID, nikoli název)

* Názvy účtů úložišť

* Zda se aktivně to týká v produkčním prostředí a pokud ano, pro které účty úložiště?

* I když nejsou aktivně vliv v produkčním prostředí, dejte nám vědět, jestli tato data, které se mají zkopírovat do jiného účtu úložiště z nějakého důvodu potřebujete a pokud ano, proč?

Za těchto okolností abychom mohli obnovit přístup k rozhraní API objektů Blob po omezenou dobu tak, aby tato data můžete zkopírovat do účtu úložiště, který nemá povolené hierarchické obory názvů.

Nespravované disky virtuálních počítačů (VM) závisí na zakázaném rozhraní API úložiště objektů Blob, takže pokud chcete povolit hierarchické obory názvů v účtu úložiště, vezměte v úvahu umístění nespravované disky virtuálních počítačů do účtu úložiště, který nemá povolené hierarchické obory názvů.

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

K zobrazení nebo Správa účtů Data Lake Storage Gen2 pomocí Průzkumníka služby Azure Storage, potřebujete alespoň verzi `1.6.0` nástroje, která je k dispozici jako [zdarma ke stažení](https://azure.microsoft.com/features/storage-explorer/).

Všimněte si, že verze Průzkumníka služby Storage, který je vložen do portálu Azure Portal se aktuálně podporují zobrazení a Správa účtů Data Lake Storage Gen2 s povolenými hierarchické obory názvů.

## <a name="blob-viewing-tool"></a>Nástroj pro zobrazení objektů BLOB

Nástroje pro zobrazení na webu Azure portal objekt BLOB má omezenou podporu pro Azure Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Aplikace třetí strany

Aplikace jiných výrobců nemusí podporovat Azure Data Lake Storage Gen2.

Podpora je uvážení jednotlivých poskytovatelů aplikací třetích stran. V současné době služba Blob storage API a rozhraní API pro Azure Data Lake Storage Gen2 nelze použít ke správě stejný obsah. Jak pracujeme, abychom zajistili interoperabilitu tohoto, je možné, že mnoho nástrojů třetích stran automaticky podpory Azure Data Lake Storage Gen2.

## <a name="azcopy-support"></a>Podpora nástroje AzCopy

AzCopy verze 8 nepodporuje Azure Data Lake Storage Gen2.

Místo toho použijte nejnovější verzi AzCopy ve verzi preview ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ) podporuje koncové body Azure Data Lake Storage Gen2.

## <a name="oauth-authentication"></a>Ověřování OAuth

Služby, jako je HDInsight a Azure Data Factory není dosud integrovat ověřování tokenu nosiče OAuth Azure Active Directory (Azure AD).

## <a name="azure-event-grid"></a>Azure Event Grid

[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) neobdrží události z účtů Azure Data Lake Gen2, protože tyto účty nejsou zatím je vygenerovat.  

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
