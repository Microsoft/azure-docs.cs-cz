---
title: Autorizace operací s daty
titleSuffix: Azure Storage
description: Přečtěte si o různých způsobech autorizace přístupu k Azure Storage, včetně Azure Active Directory, autorizace sdíleného klíče nebo sdílených přístupových podpisů (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 53eca8a0b9e7cc9abb8f89cd56fca5df28f2de0f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521932"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autorizace přístupu k datům ve Službě Azure Storage

Pokaždé, když přistupujete k datům v účtu úložiště, váš klient provede požadavek přes HTTP/HTTPS do Služby Azure Storage. Všechny žádosti o přístup k zabezpečeným prostředkům musí být autorizovány, a proto služba ověří, že daný klient má oprávnění vyžadovaná pro přístup k datům.

Následující tabulka popisuje možnosti, které Azure Storage nabízí pro autorizaci přístupu k prostředkům:

|  |Sdílený klíč (klíč účtu úložiště)  |Sdílený přístupový podpis (SAS)  |Azure Active Directory (Azure AD)  |Služba Active Directory (preview) |Anonymní veřejný přístup pro čtení  |
|---------|---------|---------|---------|---------|---------|
|Objekty blob Azure     |[Podporovány](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporovány](storage-sas-overview.md)         |[Podporovány](storage-auth-aad.md)         |Nepodporuje se|[Podporovány](../blobs/storage-manage-access-to-resources.md)         |
|Soubory Azure (SMB)     |[Podporovány](/rest/api/storageservices/authorize-with-shared-key/)         |Nepodporuje se         |[Podporováno, pouze se službou AAD Domain Services](../files/storage-files-active-directory-overview.md)         |[Podporované, musí být přihlašovací údaje synchronizovány s Azure AD](../files/storage-files-active-directory-overview.md)|Nepodporuje se         |
|Soubory Azure (REST)     |[Podporovány](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporovány](storage-sas-overview.md)         |Nepodporuje se         |Nepodporuje se |Nepodporuje se         |
|Fronty Azure     |[Podporovány](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporovány](storage-sas-overview.md)         |[Podporovány](storage-auth-aad.md)         |Nepodporuje se | Nepodporuje se         |
|Tabulky Azure     |[Podporovány](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporovány](storage-sas-overview.md)         |Nepodporuje se         |Nepodporuje se| Nepodporuje se         |

Každá možnost autorizace je stručně popsána níže:

- **Integrace Azure Active Directory (Azure AD)** pro objekty BLOB a fronty. Azure AD poskytuje řízení přístupu na základě rolí (RBAC) pro řízení nad přístupem klienta k prostředkům v účtu úložiště. Další informace týkající se integrace Azure AD pro objekty BLOB a fronty najdete [v tématu Autorizace přístupu k objektům BLOB azure a frontpomocí Azure Active Directory](storage-auth-aad.md).

- **Ověřování Azure Active Directory Domain Services (Azure AD DS)** pro soubory Azure. Soubory Azure podporuje autorizaci založenou na identitách přes blok zpráv serveru (SMB) prostřednictvím služby Azure AD DS. RBAC můžete použít pro jemně odstupňovanou kontrolu nad klienta přístup k prostředkům Azure Files v účtu úložiště. Další informace o ověřování souborů Azure pomocí doménových služeb najdete v našem [přehledu](../files/storage-files-active-directory-overview.md).

- **Ověřování (AD) služby Active Directory (preview)** pro soubory Azure. Azure Files podporuje autorizaci založenou na identitě přes SMB prostřednictvím služby AD. Vaše služba domény služby AD může být hostovaná na místních počítačích nebo ve virtuálních počítačích Azure. Přístup SMB k souborům je podporován pomocí přihlašovacích údajů služby AD z počítačů spojených s doménou, a to buď místně, nebo v Azure. RBAC můžete použít pro řízení přístupu na úrovni sdílené složky a seznamy DACL ntfs pro vynucení oprávnění na úrovni adresáře nebo souboru. Další informace o ověřování souborů Azure pomocí doménových služeb najdete v našem [přehledu](../files/storage-files-active-directory-overview.md).

- **Autorizace sdíleného klíče** pro objekty BLOB, soubory, fronty a tabulky. Klient používající sdílený klíč předá záhlaví s každým požadavkem podepsaným pomocí přístupového klíče účtu úložiště. Další informace naleznete v [tématu Authorize with Shared Key](/rest/api/storageservices/authorize-with-shared-key/).
- **Sdílené přístupové podpisy** pro objekty BLOB, soubory, fronty a tabulky. Sdílené přístupové podpisy (SAS) poskytují omezený delegovaný přístup k prostředkům v účtu úložiště. Přidání omezení časového intervalu, pro který je podpis platný, nebo oprávnění, která uděluje, poskytuje flexibilitu při správě přístupu. Další informace naleznete [v tématu Použití sdílených přístupových podpisů (SAS).](storage-sas-overview.md)
- **Anonymní veřejný přístup ke čtení** pro kontejnery a objekty BLOB. Autorizace není vyžadována. Další informace naleznete v [tématu Správa anonymního přístupu pro čtení ke kontejnerům a objektům BLOB](../blobs/storage-manage-access-to-resources.md).  

Ve výchozím nastavení jsou všechny prostředky ve službě Azure Storage zabezpečené a jsou k dispozici jenom pro vlastníka účtu. I když můžete použít některou z výše uvedených strategií autorizace k udělení přístupu klientům k prostředkům ve vašem účtu úložiště, Microsoft doporučuje používat Azure AD pokud je to možné pro maximální zabezpečení a snadné použití.

## <a name="next-steps"></a>Další kroky

- [Autorizace přístupu k objektům BLOB a frontám Azure pomocí služby Azure Active Directory](storage-auth-aad.md)
- [Autorizace s využitím sdíleného klíče](/rest/api/storageservices/authorize-with-shared-key/)
- [Udělit omezený přístup k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md)
