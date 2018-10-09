---
title: Ověření přístupu k objektům BLOB Azure a fronty pomocí Azure Active Directory (Preview) | Dokumentace Microsoftu
description: Ověření přístupu k objektům BLOB Azure a fronty pomocí Azure Active Directory (Preview).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/07/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: ef9fdb348a80c050e8c47217f2f5158200ae2f30
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868669"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>Ověření přístupu k objektům BLOB Azure a fronty pomocí Azure Active Directory (Preview)

Azure Storage podporuje ověřování a autorizace pomocí Azure Active Directory (AD) pro služby objektů Blob a fronty. S Azure AD můžete použít řízení přístupu na základě role (RBAC) k udělení přístupu uživatelům, skupinám nebo instančních objektů aplikace. 

Ověřování uživatelů a aplikací s použitím přihlašovacích údajů Azure AD poskytuje nejvyšší zabezpečení a snadné použití přes jiným způsobem autorizace. Když můžete nadále používat povolení sdíleného klíče s vašimi aplikacemi, používání služby Azure AD obchází potřebou ukládání přístupový klíč k účtu s vaším kódem. Můžete taky dál používat sdílené přístupové podpisy (SAS) k udělení velice přesně kontrolovat přístup k prostředkům ve vašem účtu úložiště, ale Azure AD nabízí podobné funkce bez nutnosti spravovat tokeny SAS nebo si dělat starosti o odvolání SAS ohrožení zabezpečení. Microsoft doporučuje používat ověřování Azure AD pro vaše aplikace Azure Storage, pokud je to možné.

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

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="get-started-with-azure-ad-for-storage"></a>Začínáme s Azure AD pro úložiště

Prvním krokem při používání integrace služby Azure AD pomocí služby Azure Storage je přiřadíte role RBAC pro úložiště dat pro instanční objekt (uživatele, skupiny nebo instanční objekt služby aplikace) nebo spravovaných identit pro prostředky Azure. Role RBAC zahrnovat obecné sady oprávnění pro kontejnery a fronty. Další informace o rolích RBAC pro Azure Storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC (Preview)](storage-auth-aad-rbac.md).

K autorizaci přístupu k prostředkům úložiště ve svých aplikacích pomocí služby Azure AD, budete muset požádat o přístupový token OAuth 2.0 z vašeho kódu. Zjistěte, jak požádat o přístupový token a použít ho k autorizaci požadavků ve službě Azure Storage, najdete v článku [ověřování pomocí Azure AD z aplikace služby Azure Storage (Preview)](storage-auth-aad-app.md). Pokud používáte spravovanou identitu, přečtěte si téma [ověřit přístup k objektům BLOB a fronty Azure spravovaných identit pro prostředky Azure (Preview)](storage-auth-aad-msi.md).

Azure CLI a Powershellu teď podporují přihlášení pomocí identity Azure AD. Po přihlášení pomocí identity Azure AD vaší relace běží pod tuto identitu. Další informace najdete v tématu [pomocí identity Azure AD pro přístup k Azure Storage s využitím rozhraní příkazového řádku nebo Powershellu (Preview)](storage-auth-aad-script.md).

## <a name="next-steps"></a>Další postup

Další informace o integraci služby Azure AD pro objekty BLOB Azure a front, najdete v článku na blogu týmu Azure Storage účtovat, [oznamujeme vydání verze Preview služby Azure AD Authentication pro službu Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
