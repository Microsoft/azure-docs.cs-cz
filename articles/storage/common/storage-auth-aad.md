---
title: Ověření přístupu k úložišti Azure pomocí služby Azure Active Directory (Preview) | Microsoft Docs
description: Ověření přístupu k úložišti Azure pomocí služby Azure Active Directory (Preview).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 06/01/2018
ms.author: tamram
ms.openlocfilehash: 9a0782b96b45d27c9b7e603959ecadf5b2632064
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737642"
---
# <a name="authenticate-access-to-azure-storage-using-azure-active-directory-preview"></a>Ověření přístupu k úložišti Azure pomocí služby Azure Active Directory (Preview)

Úložiště Azure podporuje ověřování a autorizace s Azure Active Directory (AD) pro služby objektů Blob a fronty. S Azure AD můžete řízení přístupu na základě role (RBAC) k udělení přístupu pro uživatele, skupiny nebo objekty služby aplikací. 

Autorizace aplikací, které přístup k úložišti Azure pomocí služby Azure AD, poskytuje vyšší zabezpečení a snadné použití přes jiné možnosti autorizace. Zatímco můžete dále používat s vašimi aplikacemi autorizace sdílený klíč, používání služby Azure AD by se obešla potřeba uložit klíč účtu přístup pomocí kódu. Podobně můžete nadále používat sdílené přístupové podpisy (SAS), které zajišťují podrobných přístup k prostředkům ve vašem účtu úložiště, ale Azure AD poskytuje podobné funkce, aniž by bylo nutné spravovat tokeny SAS nebo starat o odvolání ohroženými SAS.

## <a name="about-the-preview"></a>O verzi preview

Mějte na paměti následující body o verzi preview:

- Integrace se službou Azure AD je k dispozici pro služby objektů Blob a fronty pouze ve verzi preview.
- Integrace se službou Azure AD je k dispozici pro GPv1, GPv2 a objektů Blob účty úložiště ve všech oblastech veřejné. 
- Jsou podporovány pouze účty úložiště vytvořené pomocí modelu nasazení Resource Manager. 
- Podpora pro informace o subjektu volajícím identity v Azure Storage Analytics protokolování tu bude brzo dostupná.
- Azure AD autorizace přístupu k prostředkům v účtech úložiště standard storage se aktuálně podporuje. Autorizace přístupu k objekty BLOB stránky v účtech úložiště premium bude brzy podporovaný.
- Úložiště Azure podporuje role RBAC předdefinované a vlastní. Můžete přiřadit role omezená na předplatné, skupinu prostředků, účet úložiště, nebo jednotlivé kontejneru nebo fronty.
- Knihovny klienta Azure Storage, které aktuálně podporují integrace Azure AD patří:
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) (použijte 7.1.x-Preview)
    - Python
        - [Objekt BLOB](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-blob)
        - [Fronty](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-queue)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs))

> [!IMPORTANT]
> Tato verze preview je určeno pouze pro nevýrobní prostředí. Produkční smlouvy o úrovni služeb (SLA) nebudou dostupné, dokud integrace Azure AD pro Azure Storage je deklarovaná všeobecně dostupná. Pokud pro váš scénář není dosud podporován integrace Azure AD, nadále používat sdílený klíč autorizace nebo tokeny SAS ve svých aplikacích.
>
> Ve verzi Preview přiřazení rolí pro RBAC může trvat až pět minut, aby se rozšířily změny.
>
> Integrace služby Azure AD s Azure Storage vyžaduje pomocí protokolu HTTPS pro operace úložiště Azure.

## <a name="get-started-with-azure-ad-for-storage"></a>Začínáme s Azure AD pro úložiště

Prvním krokem při použití integrace Azure AD s Azure Storage je přiřadit role RBAC pro úložiště dat k hlavní službě (uživatele, skupiny nebo aplikace instanční objekt) nebo spravované Identity služby (MSI). Role RBAC zahrnovat společné sady oprávnění pro kontejnery a fronty. Další informace o rolích RBAC pro Azure Storage najdete v tématu [spravovat přístupová práva k úložišti dat pomocí RBAC (Preview)](storage-auth-aad-rbac.md).

Chcete-li autorizace přístupu k prostředkům úložiště ve svých aplikacích pomocí služby Azure AD, budete muset požádat o přístupový token OAuth 2.0 z vašeho kódu. Informace o žádosti o token přístupu a použít ho k autorizaci požadavků na úložiště Azure najdete v tématu [ověřit s Azure AD z aplikace Azure Storage (Preview)](storage-auth-aad-app.md). Pokud používáte Azure spravované služby Identity (MSI), přečtěte si téma [ověřit s Azure AD z Azure virtuálního počítače spravované služby Identity (Preview)](storage-auth-aad-msi.md).

Azure CLI a prostředí PowerShell teď podporuje přihlášení pomocí služby Azure AD identity. Po přihlásit Azure AD identity, kompatibilní se vaše relace danou identitu. Další informace najdete v tématu [používat Azure AD identity pro přístup k Azure Storage pomocí rozhraní CLI nebo Powershellu (Preview)](storage-auth-aad-script.md).

## <a name="next-steps"></a>Další postup

Další informace o integraci služby Azure AD pro front a objektů BLOB služby Azure najdete v tématu blog týmu Azure Storage, post, [uvedení ověřování Preview služby Azure AD pro Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
