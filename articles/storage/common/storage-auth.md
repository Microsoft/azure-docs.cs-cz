---
title: Autorizace operací s daty
titleSuffix: Azure Storage
description: Přečtěte si o různých způsobech autorizace přístupu k Azure Storage, včetně Azure Active Directory, autorizaci sdílených klíčů nebo podpisů sdíleného přístupu (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 783e8666e2602f9251d81e976a27fbce099defa2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460530"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autorizace přístupu k datům v Azure Storage

Pokaždé, když přistupujete k datům v účtu úložiště, klient vytvoří požadavek přes HTTP/HTTPS, aby Azure Storage. Všechny žádosti o přístup k zabezpečeným prostředkům musí být autorizovány, a proto služba ověří, že daný klient má oprávnění vyžadovaná pro přístup k datům.

Následující tabulka popisuje možnosti, které Azure Storage nabídky pro autorizaci přístupu k prostředkům:

|  |Sdílený klíč (klíč účtu úložiště)  |Sdílený přístupový podpis (SAS)  |Azure Active Directory (Azure AD)  |Anonymní veřejný přístup pro čtení  |
|---------|---------|---------|---------|---------|
|Azure Blobs     |[Podporuje se](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporuje se](storage-sas-overview.md)         |[Podporuje se](storage-auth-aad.md)         |[Podporuje se](../blobs/storage-manage-access-to-resources.md)         |
|Soubory Azure (SMB)     |[Podporuje se](/rest/api/storageservices/authorize-with-shared-key/)         |Nepodporováno         |[Podporováno, pouze s doménovou službou AAD](../files/storage-files-active-directory-overview.md)         |Nepodporováno         |
|Soubory Azure (REST)     |[Podporuje se](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporuje se](storage-sas-overview.md)         |Nepodporováno         |Nepodporováno         |
|Fronty Azure     |[Podporuje se](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporuje se](storage-sas-overview.md)         |[Podporuje se](storage-auth-aad.md)         |Nepodporováno         |
|Tabulky Azure     |[Podporuje se](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporuje se](storage-sas-overview.md)         |Nepodporováno         |Nepodporováno         |

Každá možnost autorizace je stručně popsána níže:

- **Integrace služby Azure Active Directory (Azure AD)** pro objekty BLOB a fronty. Azure AD poskytuje řízení přístupu na základě role (RBAC) pro jemně odstupňovanou kontrolu nad přístupem klienta k prostředkům v účtu úložiště. Další informace o integraci Azure AD pro objekty BLOB a fronty najdete v tématu [autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](storage-auth-aad.md).

- **Integrace služby Azure AD Domain Services (DS) pro soubory (Preview)** . Soubory Azure podporují autorizaci založenou na identitě přes protokol SMB (Server Message Block) prostřednictvím Azure služba AD DS. Pro jemně odstupňovanou kontrolu nad přístupem klienta k prostředkům služby soubory Azure v účtu úložiště můžete použít RBAC. Další informace o integraci Azure AD pro soubory pomocí služby Domain Services najdete v tématu [Přehled podpory ověřování služby Azure files Azure Active Directory Domain Service (AAD DS) pro přístup přes protokol SMB (Preview)](../files/storage-files-active-directory-overview.md).

- **Autorizace sdíleného klíče** pro objekty blob, soubory, fronty a tabulky. Klient používající sdílený klíč projde hlavičkou každý požadavek, který je podepsaný pomocí přístupového klíče účtu úložiště. Další informace najdete v tématu [autorizace pomocí sdíleného klíče](/rest/api/storageservices/authorize-with-shared-key/).
- **Sdílené přístupové podpisy** pro objekty blob, soubory, fronty a tabulky. Sdílené přístupové podpisy (SAS) poskytují omezený delegovaný přístup k prostředkům v účtu úložiště. Přidání omezení v časovém intervalu, pro který je podpis platný, nebo na oprávnění, která uděluje, poskytují flexibilitu při správě přístupu. Další informace najdete v tématu [použití sdílených přístupových podpisů (SAS)](storage-sas-overview.md).
- **Anonymní veřejný přístup pro čtení** pro kontejnery a objekty blob. Autorizace se nevyžaduje. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).  

Ve výchozím nastavení jsou všechny prostředky v Azure Storage zabezpečené a jsou dostupné jenom pro vlastníka účtu. I když můžete použít kteroukoli strategii autorizace uvedenou výše a udělit klientům přístup k prostředkům ve vašem účtu úložiště, Microsoft doporučuje používat Azure AD, pokud je to možné, pro zajištění maximálního zabezpečení a snadného použití.

## <a name="next-steps"></a>Další kroky

- [Autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](storage-auth-aad.md)
- [Autorizovat se sdíleným klíčem](/rest/api/storageservices/authorize-with-shared-key/)
- [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md)
