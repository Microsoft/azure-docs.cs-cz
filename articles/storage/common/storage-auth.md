---
title: Autorizace operací s daty
titleSuffix: Azure Storage
description: Přečtěte si o různých způsobech autorizace přístupu k Azure Storage, včetně Azure Active Directory, autorizaci sdílených klíčů nebo podpisů sdíleného přístupu (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b0f2ad7566d0204871a9c6441315d6201662d92b
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616293"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autorizace přístupu k datům v Azure Storage

Pokaždé, když přistupujete k datům v účtu úložiště, klient vytvoří požadavek přes HTTP/HTTPS, aby Azure Storage. Každý požadavek na zabezpečený prostředek musí být autorizovaný, aby služba zajistila, že má klient oprávnění potřebná pro přístup k datům.

Následující tabulka popisuje možnosti, které Azure Storage nabídky pro autorizaci přístupu k prostředkům:

|  |Sdílený klíč (klíč účtu úložiště)  |Sdílený přístupový podpis (SAS)  |Azure Active Directory (Azure AD)  |Active Directory (Preview) |Anonymní veřejný přístup pro čtení  |
|---------|---------|---------|---------|---------|---------|
|Objekty blob Azure     |[Podporuje se](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporuje se](storage-sas-overview.md)         |[Podporuje se](storage-auth-aad.md)         |Nepodporuje se|[Podporuje se](../blobs/storage-manage-access-to-resources.md)         |
|Soubory Azure (SMB)     |[Podporuje se](/rest/api/storageservices/authorize-with-shared-key/)         |Nepodporuje se         |[Podporováno, pouze s doménovou službou AAD](../files/storage-files-active-directory-overview.md)         |[Podporuje se přihlašovací údaje musí synchronizovat do Azure AD.](../files/storage-files-active-directory-overview.md)|Nepodporuje se         |
|Soubory Azure (REST)     |[Podporuje se](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporuje se](storage-sas-overview.md)         |Nepodporuje se         |Nepodporuje se |Nepodporuje se         |
|Fronty Azure     |[Podporuje se](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporuje se](storage-sas-overview.md)         |[Podporuje se](storage-auth-aad.md)         |Nepodporuje se | Nepodporuje se         |
|Tabulky Azure     |[Podporuje se](/rest/api/storageservices/authorize-with-shared-key/)         |[Podporuje se](storage-sas-overview.md)         |Nepodporuje se         |Nepodporuje se| Nepodporuje se         |

Každá možnost autorizace je stručně popsána níže:

- **Integrace služby Azure Active Directory (Azure AD)** pro objekty BLOB a fronty. Azure AD poskytuje řízení přístupu na základě role (RBAC) pro jemně odstupňovanou kontrolu nad přístupem klienta k prostředkům v účtu úložiště. Další informace o integraci Azure AD pro objekty BLOB a fronty najdete v tématu [autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](storage-auth-aad.md).

- **Azure Active Directory Domain Services (azure služba AD DS) ověřování** pro soubory Azure. Soubory Azure podporují autorizaci založenou na identitě přes protokol SMB (Server Message Block) prostřednictvím Azure služba AD DS. Pro jemně odstupňovanou kontrolu nad přístupem klienta k prostředkům služby soubory Azure v účtu úložiště můžete použít RBAC. Další informace týkající se ověřování souborů Azure pomocí služby Domain Services najdete v našem [přehledu](../files/storage-files-active-directory-overview.md).

- **Ověřování služby Active Directory (AD) pro soubory Azure (Preview)** . Soubory Azure podporují autorizaci založenou na identitě přes protokol SMB prostřednictvím služby AD. Vaše Doménová služba AD se může hostovat na místních nebo virtuálních počítačích Azure. Přístup SMB k souborům se podporuje pomocí přihlašovacích údajů služby AD z počítačů připojených k doméně, a to v místním prostředí nebo v Azure. Pro řízení přístupu na úrovni sdílené složky a pro oprávnění NTFS pro vynucení oprávnění na úrovni adresářů a souborů můžete použít RBAC. Další informace týkající se ověřování souborů Azure pomocí služby Domain Services najdete v našem [přehledu](../files/storage-files-active-directory-overview.md).

- **Autorizace sdíleného klíče** pro objekty blob, soubory, fronty a tabulky. Klient používající sdílený klíč projde hlavičkou každý požadavek, který je podepsaný pomocí přístupového klíče účtu úložiště. Další informace najdete v tématu [autorizace pomocí sdíleného klíče](/rest/api/storageservices/authorize-with-shared-key/).
- **Sdílené přístupové podpisy** pro objekty blob, soubory, fronty a tabulky. Sdílené přístupové podpisy (SAS) poskytují omezený delegovaný přístup k prostředkům v účtu úložiště. Přidání omezení v časovém intervalu, pro který je podpis platný, nebo na oprávnění, která uděluje, poskytují flexibilitu při správě přístupu. Další informace najdete v tématu [použití sdílených přístupových podpisů (SAS)](storage-sas-overview.md).
- **Anonymní veřejný přístup pro čtení** pro kontejnery a objekty blob. Autorizace se nevyžaduje. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/storage-manage-access-to-resources.md).  

Ve výchozím nastavení jsou všechny prostředky v Azure Storage zabezpečené a jsou dostupné jenom pro vlastníka účtu. I když můžete použít kteroukoli strategii autorizace uvedenou výše a udělit klientům přístup k prostředkům ve vašem účtu úložiště, Microsoft doporučuje používat Azure AD, pokud je to možné, pro zajištění maximálního zabezpečení a snadného použití.

## <a name="next-steps"></a>Další kroky

- [Autorizace přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](storage-auth-aad.md)
- [Autorizovat se sdíleným klíčem](/rest/api/storageservices/authorize-with-shared-key/)
- [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md)
