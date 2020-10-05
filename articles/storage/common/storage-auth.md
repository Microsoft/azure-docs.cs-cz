---
title: Autorizace operací s daty
titleSuffix: Azure Storage
description: Přečtěte si o různých způsobech autorizace přístupu k Azure Storage, včetně Azure Active Directory, autorizaci sdílených klíčů nebo podpisů sdíleného přístupu (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 806222fc522c548fd58935812d705e12c9b3cee1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714423"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autorizace přístupu k datům v Azure Storage

Pokaždé, když přistupujete k datům v účtu úložiště, klient vytvoří požadavek přes HTTP/HTTPS, aby Azure Storage. Všechny žádosti o přístup k zabezpečeným prostředkům musí být autorizovány, a proto služba ověří, že daný klient má oprávnění vyžadovaná pro přístup k datům.

Následující tabulka popisuje možnosti, které Azure Storage nabídky pro autorizaci přístupu k prostředkům:

| Artefakt Azure | Sdílený klíč (klíč účtu úložiště) | Sdílený přístupový podpis (SAS) | Azure Active Directory (Azure AD) | Místní Active Directory Domain Services (Preview) | Anonymní veřejný přístup pro čtení |
| -------------- | -------------------------------- | ----------------------------- | --------------------------------- | ------------------------------------------------------ | ---------------------------- |
|Objekty blob Azure     |[Podporováno](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporováno](storage-sas-overview.md)         |[Podporováno](storage-auth-aad.md)         |Nepodporováno|[Podporováno](../blobs/storage-manage-access-to-resources.md)         |
|Soubory Azure (SMB)     |[Podporováno](/rest/api/storageservices/authorize-with-shared-key/)         |Nepodporováno         |[Podporováno, pouze s doménovou službou AAD](../files/storage-files-active-directory-overview.md)         |[Podporuje se přihlašovací údaje musí synchronizovat do Azure AD.](../files/storage-files-active-directory-overview.md)|Nepodporováno         |
|Soubory Azure (REST)     |[Podporováno](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporováno](storage-sas-overview.md)         |Nepodporováno         |Nepodporováno |Nepodporováno         |
|Fronty Azure     |[Podporováno](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporováno](storage-sas-overview.md)         |[Podporováno](storage-auth-aad.md)         |Nepodporuje se | Nepodporováno         |
|Tabulky Azure     |[Podporováno](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporováno](storage-sas-overview.md)         |Nepodporováno         |Nepodporováno| Nepodporováno         |

Každá možnost autorizace je stručně popsána níže:

- **Integrace služby Azure Active Directory (Azure AD)** pro objekty BLOB a fronty. Azure poskytuje řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu klienta k prostředkům v účtu úložiště. Další informace o integraci Azure AD pro objekty BLOB a fronty najdete v tématu [autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](storage-auth-aad.md).

- **Azure Active Directory Domain Services (azure služba AD DS) ověřování** pro soubory Azure. Soubory Azure podporují autorizaci založenou na identitě přes protokol SMB (Server Message Block) prostřednictvím Azure služba AD DS. Můžete použít Azure RBAC pro jemně odstupňovanou kontrolu nad přístupem klienta k prostředkům souborů Azure v účtu úložiště. Další informace týkající se ověřování souborů Azure pomocí služby Domain Services najdete v [přehledu](../files/storage-files-active-directory-overview.md).

- **Místní Active Directory Domain Services (služba AD DS nebo místní služba AD DS) ověřování (Preview)** pro soubory Azure. Soubory Azure podporují autorizaci na základě identity přes protokol SMB prostřednictvím služba AD DS. Vaše prostředí služba AD DS se může hostovat na místních nebo virtuálních počítačích Azure. Přístup SMB k souborům se podporuje pomocí služba AD DS přihlašovacích údajů z počítačů připojených k doméně, a to v místním prostředí nebo v Azure. Můžete použít kombinaci služby Azure RBAC pro řízení přístupu na úrovni sdílené složky a DACL pro NTFS pro vynucení oprávnění na úrovni adresářů a souborů. Další informace týkající se ověřování souborů Azure pomocí služby Domain Services najdete v [přehledu](../files/storage-files-active-directory-overview.md).

- **Autorizace sdíleného klíče** pro objekty blob, soubory, fronty a tabulky. Klient používající sdílený klíč projde hlavičkou každý požadavek, který je podepsaný pomocí přístupového klíče účtu úložiště. Další informace najdete v tématu [autorizace pomocí sdíleného klíče](/rest/api/storageservices/authorize-with-shared-key/).
- **Sdílené přístupové podpisy** pro objekty blob, soubory, fronty a tabulky. Sdílené přístupové podpisy (SAS) poskytují omezený delegovaný přístup k prostředkům v účtu úložiště. Přidání omezení v časovém intervalu, pro který je podpis platný, nebo na oprávnění, která uděluje, poskytují flexibilitu při správě přístupu. Další informace najdete v tématu [použití sdílených přístupových podpisů (SAS)](storage-sas-overview.md).
- **Anonymní veřejný přístup pro čtení** pro kontejnery a objekty blob. Autorizace se nevyžaduje. Další informace najdete v tématu [Správa anonymního přístupu pro čtení do kontejnerů a objektů BLOB](../blobs/storage-manage-access-to-resources.md).  

Ve výchozím nastavení jsou všechny prostředky v Azure Storage zabezpečené a jsou dostupné jenom pro vlastníka účtu. I když můžete použít kteroukoli strategii autorizace uvedenou výše a udělit klientům přístup k prostředkům ve vašem účtu úložiště, Microsoft doporučuje používat Azure AD, pokud je to možné, pro zajištění maximálního zabezpečení a snadného použití.

## <a name="next-steps"></a>Další kroky

- [Autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](storage-auth-aad.md)
- [Autorizace s využitím sdíleného klíče](/rest/api/storageservices/authorize-with-shared-key/)
- [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md)
