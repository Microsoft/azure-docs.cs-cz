---
title: Autorizace přístupu k Azure Storage | Microsoft Docs
description: Informace o různých způsobech autorizace přístupu k Azure Storage, včetně služby Azure Active Directory, ověřování sdílený klíč nebo sdílených přístupových podpisů.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 46bb332e28d7503e543ca3c99fa1099ef17f34c3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660738"
---
# <a name="authorizing-access-to-azure-storage"></a>Autorizace přístupu k Azure Storage

Pokaždé, když je přístup k datům v účtu úložiště, váš klient odešle požadavek přes protokol HTTP/HTTPS do služby Azure Storage. Každý požadavek na zabezpečený prostředek musí být autorizován, tak, aby služba zajišťuje, že klient má oprávnění požadovaná pro přístup k datům. Úložiště Azure nabízí tyto možnosti pro ověřování přístupu k zabezpečení prostředků:

- **Azure integrace služby Active Directory (Azure AD) (Preview)** pro objekty BLOB a fronty. Azure AD poskytuje řízení přístupu na základě role (RBAC) pro jemně odstupňovanou kontrolu nad klienta přístup k prostředkům v účtu úložiště. Další informace najdete v tématu [ověřování požadavků na úložiště Azure pomocí služby Azure Active Directory (Preview)](storage-auth-aad.md).
- **Sdílený klíč autorizace** pro objekty BLOB, soubory, fronty a tabulky. Klient pomocí sdíleného klíče předá hlavička s každou žádost, která je podepsaná pomocí přístupový klíč účtu úložiště. Další informace najdete v tématu [autorizovat pomocí sdíleného klíče](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key/).
- **Sdílené přístupové podpisy** pro objekty BLOB, soubory, fronty a tabulky. Sdílené přístupové podpisy (SAS) poskytují omezené Delegovaný přístup k prostředkům v účtu úložiště. Přidání omezení na časový interval, pro který podpis je neplatný nebo oprávnění, která uděluje poskytuje flexibilitu při správě přístupu. Další informace najdete v tématu [pomocí sdílené přístupové podpisy (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Anonymní veřejný přístup pro čtení** pro kontejnery a objekty BLOB. Autorizace se nevyžaduje. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).  

Ve výchozím nastavení všechny prostředky ve službě Azure Storage jsou zabezpečené a jsou dostupné jenom pro vlastníka účtu. I když můžete použít některý z uvedených výše pro udělení přístupu k prostředkům ve vašem účtu úložiště klienti strategie autorizace, společnost Microsoft doporučuje pomocí služby Azure AD při možné maximálního zabezpečení a snadné použití. 



