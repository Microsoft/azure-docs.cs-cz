---
title: Ověření přístupu k objektům BLOB Azure a fronty pomocí Azure Active Directory | Dokumentace Microsoftu
description: Ověření přístupu k objektům BLOB Azure a fronty pomocí Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/28/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e72400a759b976b1a2a6864b2fa7d7d91e16c62f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619284"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Ověření přístupu k objektům BLOB Azure a fronty pomocí Azure Active Directory

Azure Storage podporuje ověřování a autorizace pomocí Azure Active Directory (AD) pro služby objektů Blob a fronty. S Azure AD můžete použít řízení přístupu na základě role (RBAC) k udělení přístupu uživatelům, skupinám nebo instančních objektů aplikace. 

Ověřování uživatelů a aplikací s použitím přihlašovacích údajů Azure AD poskytuje nejvyšší zabezpečení a snadné použití přes jiným způsobem autorizace. Když můžete nadále používat povolení sdíleného klíče s vašimi aplikacemi, používání služby Azure AD obchází potřebou ukládání přístupový klíč k účtu s vaším kódem. Můžete taky dál používat sdílené přístupové podpisy (SAS) k udělení velice přesně kontrolovat přístup k prostředkům ve vašem účtu úložiště, ale Azure AD nabízí podobné funkce bez nutnosti spravovat tokeny SAS nebo si dělat starosti o odvolání SAS ohrožení zabezpečení. Microsoft doporučuje používat ověřování Azure AD pro vaše aplikace Azure Storage, pokud je to možné.

Ověřování a autorizace pomocí přihlašovacích údajů Azure AD je k dispozici pro všechny pro obecné účely v2 pro obecné účely v1 a účty úložiště Blob ve všech veřejných oblastech. Jenom účty úložiště s podporou modelu nasazení Azure Resource Manageru vytvořené autorizace Azure AD.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Přehled služby Azure AD pro objekty BLOB a fronty

Prvním krokem při používání integrace služby Azure AD pomocí služby Azure Storage je přiřadíte role RBAC pro úložiště dat pro instanční objekt (uživatele, skupiny nebo instanční objekt služby aplikace) nebo spravovaných identit pro prostředky Azure. Role RBAC zahrnovat obecné sady oprávnění pro kontejnery a fronty. Další informace o přiřazování role RBAC pro Azure Storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).

K autorizaci přístupu k prostředkům úložiště ve svých aplikacích pomocí služby Azure AD, budete muset požádat o přístupový token OAuth 2.0 z vašeho kódu. Zjistěte, jak požádat o přístupový token a použít ho k autorizaci požadavků ve službě Azure Storage, najdete v článku [ověřování pomocí Azure AD z aplikace služby Azure Storage](storage-auth-aad-app.md). Pokud používáte spravovanou identitu, přečtěte si téma [ověřit přístup k objektům BLOB a fronty Azure spravovaných identit pro prostředky Azure](storage-auth-aad-msi.md).

Azure CLI a Powershellu teď podporují přihlášení pomocí identity Azure AD. Po přihlášení pomocí identity Azure AD vaší relace běží pod tuto identitu. Další informace najdete v tématu [pomocí identity Azure AD pro přístup k úložišti Azure pomocí Powershellu nebo rozhraní příkazového řádku](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC pro objekty BLOB a fronty

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md). Azure Storage definuje sadu předdefinované role RBAC, které zahrnuje společné sady oprávnění pro přístup k datům objektu blob a fronty. Můžete také definovat vlastní role pro přístup k datům objektu blob a fronty.

Když roli RBAC je přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup se dají vymezit na úrovni předplatného, skupinu prostředků, účet úložiště nebo kontejner nebo fronty. Objekt zabezpečení Azure AD může být uživatel, skupina, instančního objektu aplikace nebo [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Zjistěte, jak přiřadit předdefinované RBAC pro prostředky služby Azure Storage, najdete v jedné z následujících témat:

- [Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md)
- [Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC pomocí rozhraní příkazového řádku Azure](storage-auth-aad-rbac-cli.md)
- [Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC pomocí Powershellu](storage-auth-aad-rbac-powershell.md)

### <a name="access-permissions-granted-by-rbac-roles"></a>Přístupová oprávnění udělená role RBAC 

Podrobnosti o oprávněních požadovaných k volání operací služby Azure Storage najdete v tématu [oprávnění pro volání operace REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

## <a name="resource-scope"></a>Prostředek oboru

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="next-steps"></a>Další postup

- [Podpora služby Azure Storage pro Azure Active Directory na základě Všeobecná dostupnost řízení přístupu](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
- [Ověřování s využitím Azure Active Directory z aplikace pro přístup k objektům blob a frontám](storage-auth-aad-app.md)
- [Ověření přístupu k objektům BLOB a fronty pomocí spravované identity pro prostředky Azure](storage-auth-aad-msi.md)
- Služba soubory Azure podporuje ověřování pomocí Azure AD prostřednictvím protokolu SMB pro připojené k doméně pouze pro virtuální počítače (preview). Další informace o používání služby Azure AD prostřednictvím protokolu SMB pro soubory Azure najdete v tématu [přehled o Azure Active Directory authentication přes protokol SMB pro soubory Azure (preview)](../files/storage-files-active-directory-overview.md).