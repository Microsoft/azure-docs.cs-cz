---
title: Autorizace přístupu ke službě Azure Storage | Dokumentace Microsoftu
description: Další informace o různé způsoby, jak autorizovat přístup k úložišti Azure, včetně Azure Active Directory, ověřování pomocí sdíleného klíče nebo signatur sdíleného přístupu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ba4d83d620a597c9a59f1a3c7f2f9d6722ba42d8
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368360"
---
# <a name="authorizing-access-to-azure-storage"></a>Autorizace přístupu ke službě Azure Storage

Pokaždé, když budete přistupovat k datům ve vašem účtu úložiště, klient odešle požadavek přes protokol HTTP/HTTPS do služby Azure Storage. Každý požadavek na zabezpečený prostředek musí být ověřeny, tak, aby služba zajišťuje, že klient má oprávnění potřebná pro přístup k datům. Azure Storage nabízí tyto možnosti pro ověřování přístupu k zabezpečení prostředků:

- **Integrace s Azure Active Directory (Azure AD)** pro objekty BLOB a fronty. Azure AD poskytuje řízení přístupu na základě role (RBAC) pro detailní kontrolu nad klienta přístup k prostředkům v účtu úložiště. Další informace najdete v tématu [ověřování požadavků do služby Azure Storage pomocí Azure Active Directory](storage-auth-aad.md).
- **Sdílený klíč autorizace** pro objekty BLOB, soubory, fronty a tabulky. Klienta pomocí sdíleného klíče předá hlavičku při každé žádosti, která je podepsaná pomocí přístupového klíče účtu úložiště. Další informace najdete v tématu [autorizovat pomocí sdíleného klíče](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/).
- **Sdílené přístupové podpisy** pro objekty BLOB, soubory, fronty a tabulky. Sdílené přístupové podpisy (SAS) poskytují omezené Delegovaný přístup k prostředkům v účtu úložiště. Přidání omezení na časový interval, pro který je platný podpis nebo na oprávněních, která uděluje poskytuje flexibilitu při správě přístupu. Další informace najdete v tématu [použití sdílených přístupových podpisů (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Anonymní veřejný přístup pro čtení** pro kontejnery a objekty BLOB. Ověření se nevyžaduje. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).  

Ve výchozím nastavení všechny prostředky ve službě Azure Storage jsou zabezpečené a jsou k dispozici pouze vlastník účtu. I když můžete použít některý z autorizace strategie uvedené výše pro udělení přístupu k prostředkům ve vašem účtu úložiště klientů, společnost Microsoft doporučuje používat Azure AD Pokud je to možné maximálního zabezpečení a snadné použití. 



