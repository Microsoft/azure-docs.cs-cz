---
title: Autorizuje se přístup k Azure Storage | Microsoft Docs
description: Přečtěte si o různých způsobech, jak autorizovat přístup k Azure Storage, včetně Azure Active Directory, ověřování sdíleného klíče nebo podpisů sdíleného přístupu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b7669f9ec804a8fd2801474a845af7e029ee5235
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664042"
---
# <a name="authorizing-access-to-azure-storage"></a>Autorizace přístupu k Azure Storage

Pokaždé, když přistupujete k datům v účtu úložiště, klient vytvoří požadavek přes HTTP/HTTPS, aby Azure Storage. Každý požadavek na zabezpečený prostředek musí být autorizovaný, aby služba zajistila, že má klient oprávnění potřebná pro přístup k datům.

Následující tabulka popisuje možnosti, které Azure Storage nabídky pro autorizaci přístupu k prostředkům:

|  |Sdílený klíč (klíč účtu úložiště)  |Sdílený přístupový podpis (SAS)  |Azure Active Directory (Azure AD)  |Anonymní veřejný přístup pro čtení  |
|---------|---------|---------|---------|---------|
|Objekty blob Azure     |[Podporuje se](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Podporuje se](storage-sas-overview.md)         |[Podporuje se](storage-auth-aad.md)         |[Podporuje se](../blobs/storage-manage-access-to-resources.md)         |
|Soubory Azure (SMB)     |[Podporuje se](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |Nepodporováno         |[Podporováno, pouze s doménovou službou AAD](../files/storage-files-active-directory-overview.md)         |Nepodporováno         |
|Soubory Azure (REST)     |[Podporuje se](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Podporuje se](storage-sas-overview.md)         |Nepodporováno         |Nepodporováno         |
|Fronty Azure     |[Podporuje se](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Podporuje se](storage-sas-overview.md)         |[Podporuje se](storage-auth-aad.md)         |Nepodporováno         |
|Tabulky Azure     |[Podporuje se](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Podporuje se](storage-sas-overview.md)         |Nepodporováno         |Nepodporováno         |

Každá možnost autorizace je stručně popsána níže:

- **Integrace služby Azure Active Directory (Azure AD)** pro objekty BLOB a fronty. Azure AD poskytuje řízení přístupu na základě role (RBAC) pro jemně odstupňovanou kontrolu nad přístupem klienta k prostředkům v účtu úložiště. Další informace o integraci Azure AD pro objekty BLOB a fronty najdete v tématu [ověřování požadavků pro Azure Storage pomocí Azure Active Directory](storage-auth-aad.md).

- **Integrace služby Azure AD Domain Services (DS) pro soubory (Preview)** . Soubory Azure podporují ověřování na základě identity přes protokol SMB (Server Message Block) prostřednictvím Azure služba AD DS. To zajišťuje nestejnou kontrolu nad přístupem klienta k prostředkům v účtu úložiště. Další informace o integraci Azure AD pro soubory pomocí služby Domain Services najdete v tématu [Přehled podpory ověřování služby Azure files Azure Active Directory Domain Service (AAD DS) pro přístup přes protokol SMB (Preview)](../files/storage-files-active-directory-overview.md).

- **Autorizace sdíleného klíče** pro objekty blob, soubory, fronty a tabulky. Klient používající sdílený klíč projde hlavičkou každý požadavek, který je podepsaný pomocí přístupového klíče účtu úložiště. Další informace najdete v tématu [autorizace pomocí sdíleného klíče](/rest/api/storageservices/authenticate-with-shared-key/).
- **Sdílené přístupové podpisy** pro objekty blob, soubory, fronty a tabulky. Sdílené přístupové podpisy (SAS) poskytují omezený delegovaný přístup k prostředkům v účtu úložiště. Přidání omezení v časovém intervalu, pro který je podpis platný, nebo na oprávnění, která uděluje, poskytují flexibilitu při správě přístupu. Další informace najdete v tématu [použití sdílených přístupových podpisů (SAS)](storage-sas-overview.md).
- **Anonymní veřejný přístup pro čtení** pro kontejnery a objekty blob. Autorizace se nevyžaduje. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).  

Ve výchozím nastavení jsou všechny prostředky v Azure Storage zabezpečené a jsou dostupné jenom pro vlastníka účtu. I když můžete použít kteroukoli strategii autorizace uvedenou výše a udělit klientům přístup k prostředkům ve vašem účtu úložiště, Microsoft doporučuje používat Azure AD, pokud je to možné, pro zajištění maximálního zabezpečení a snadného použití.

## <a name="next-steps"></a>Další kroky

- [Dokumentace k Azure Active Directory](/azure/active-directory/)
- [Vývoj platformy Microsoft Identity Platform](/azure/active-directory/develop/about-microsoft-identity-platform)
