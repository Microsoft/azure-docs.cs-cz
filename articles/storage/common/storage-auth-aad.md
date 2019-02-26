---
title: Ověření přístupu k objektům BLOB Azure a fronty pomocí Azure Active Directory (Preview) | Dokumentace Microsoftu
description: Ověření přístupu k objektům BLOB Azure a fronty pomocí Azure Active Directory (Preview).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/13/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f57e793278af7eb03fe49fd657e45b0846db8b1c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817911"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>Ověření přístupu k objektům BLOB Azure a fronty pomocí Azure Active Directory (Preview)

Azure Storage podporuje ověřování a autorizace pomocí Azure Active Directory (AD) pro služby objektů Blob a fronty. S Azure AD můžete použít řízení přístupu na základě role (RBAC) k udělení přístupu uživatelům, skupinám nebo instančních objektů aplikace. 

Ověřování uživatelů a aplikací s použitím přihlašovacích údajů Azure AD poskytuje nejvyšší zabezpečení a snadné použití přes jiným způsobem autorizace. Když můžete nadále používat povolení sdíleného klíče s vašimi aplikacemi, používání služby Azure AD obchází potřebou ukládání přístupový klíč k účtu s vaším kódem. Můžete taky dál používat sdílené přístupové podpisy (SAS) k udělení velice přesně kontrolovat přístup k prostředkům ve vašem účtu úložiště, ale Azure AD nabízí podobné funkce bez nutnosti spravovat tokeny SAS nebo si dělat starosti o odvolání SAS ohrožení zabezpečení. Microsoft doporučuje používat ověřování Azure AD pro vaše aplikace Azure Storage, pokud je to možné.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="about-the-preview"></a>Informace o verzi preview

Mějte na paměti následující skutečnosti o verzi preview:

- Integrace se službou Azure AD je k dispozici pro objekt Blob a fronty služby pouze ve verzi preview.
- Integrace se službou Azure AD je k dispozici pro účty úložiště GPv1, GPv2 a Blob ve všech veřejných oblastech. 
- Jsou podporované jenom účty úložiště vytvořené pomocí modelu nasazení Resource Manager. 
- Podpora pro informace o subjektu volajícím identity v Azure Storage Analytics protokolování je již brzy.
- Azure AD autorizace přístupu k prostředkům v účtech úložiště úrovně standard je aktuálně podporován. Povolení přístupu k objektům BLOB stránky v účtech úložiště úrovně premium bude brzy podporovat.
- Azure Storage podporuje předdefinované i vlastní role RBAC. Můžete přiřadit role omezená na předplatné, skupinu prostředků, účet úložiště nebo kontejner nebo fronty.
- Tyto klientské knihovny Azure Storage, které aktuálně podporují integrace služby Azure AD patří:
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [Objekt BLOB, fronty a soubory](https://github.com/Azure/azure-storage-python)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="overview-of-azure-ad-for-storage"></a>Přehled služby Azure AD pro úložiště

Prvním krokem při používání integrace služby Azure AD pomocí služby Azure Storage je přiřadíte role RBAC pro úložiště dat pro instanční objekt (uživatele, skupiny nebo instanční objekt služby aplikace) nebo spravovaných identit pro prostředky Azure. Role RBAC zahrnovat obecné sady oprávnění pro kontejnery a fronty. Další informace o přiřazování role RBAC pro Azure Storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC (Preview)](storage-auth-aad-rbac.md).

K autorizaci přístupu k prostředkům úložiště ve svých aplikacích pomocí služby Azure AD, budete muset požádat o přístupový token OAuth 2.0 z vašeho kódu. Zjistěte, jak požádat o přístupový token a použít ho k autorizaci požadavků ve službě Azure Storage, najdete v článku [ověřování pomocí Azure AD z aplikace služby Azure Storage (Preview)](storage-auth-aad-app.md). Pokud používáte spravovanou identitu, přečtěte si téma [ověřit přístup k objektům BLOB a fronty Azure spravovaných identit pro prostředky Azure (Preview)](storage-auth-aad-msi.md).

Azure CLI a Powershellu teď podporují přihlášení pomocí identity Azure AD. Po přihlášení pomocí identity Azure AD vaší relace běží pod tuto identitu. Další informace najdete v tématu [pomocí identity Azure AD pro přístup k Azure Storage s využitím rozhraní příkazového řádku nebo Powershellu (Preview)](storage-auth-aad-script.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC pro objekty BLOB a fronty

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md). Azure Storage definuje sadu předdefinované role RBAC, které zahrnuje společné sady oprávnění pro přístup k kontejnery nebo fronty. 

Když roli RBAC je přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup se dají vymezit na úrovni předplatného, skupinu prostředků, účet úložiště nebo kontejner nebo fronty. Objekt zabezpečení Azure AD může být uživatel, skupina, instančního objektu aplikace nebo [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md). 

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

### <a name="access-permissions-granted-by-rbac-roles"></a>Přístupová oprávnění udělená role RBAC 

Následující tabulka shrnuje přístupová práva udělují předdefinované role, v závislosti na rozsahu:

|Rozsah|Vlastník dat objektů BLOB|Blob Data Contributor|Čtenář dat objektu BLOB|Přispěvatel dat fronty služby|Čtenář dat fronty služby|
|---|---|---|---|---|---|
|Úroveň předplatného|Přístup pro čtení a zápis a POSIX přístup k řízení všech kontejnerů a objektů BLOB v rámci předplatného|Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB v rámci předplatného| Přístup pro čtení pro všechny kontejnery a objekty BLOB v rámci předplatného|Přístup pro čtení a zápis pro všechny fronty v rámci předplatného|Přístup pro čtení pro všechny fronty v rámci předplatného|
|Úrovni skupiny prostředků|Přístup pro čtení a zápis a POSIX přístup k řízení ke všem kontejnery a objekty BLOB ve skupině prostředků|Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB ve skupině prostředků|Přístup pro čtení pro všechny kontejnery a objekty BLOB ve skupině prostředků|Přístup pro čtení a zápis pro všechny fronty ve skupině prostředků|Přístup pro čtení pro všechny fronty ve skupině prostředků|
|Úroveň účtu úložiště|Přístup pro čtení a zápis a POSIX přístup k řízení všech kontejnerů a objektů BLOB v účtu úložiště|Přístup pro čtení a zápis pro všechny kontejnery a objekty BLOB v účtu úložiště|Přístup pro čtení pro všechny kontejnery a objekty BLOB v účtu úložiště|Přístup pro čtení a zápis do všech front v účtu úložiště|Přístup pro čtení pro všechny fronty v účtu úložiště|
|Úroveň kontejneru či fronty|Přístup pro čtení a zápis a POSIX přístup k řízení do zadaného kontejneru a jeho objekty BLOB.|Přístup pro čtení a zápis do zadaného kontejneru a jeho objektům BLOB|Přístup pro čtení zadaného kontejneru a jeho objektům BLOB|Přístup pro čtení a zápis do zadané fronty|Přístup pro čtení do zadané fronty|

Podrobnosti o oprávněních požadovaných k volání operací služby Azure Storage najdete v tématu [oprávnění pro volání operace REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

## <a name="next-steps"></a>Další postup

Další informace o integraci služby Azure AD pro objekty BLOB Azure a front, najdete v článku na blogu týmu Azure Storage účtovat, [oznamujeme vydání verze Preview služby Azure AD Authentication pro službu Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
