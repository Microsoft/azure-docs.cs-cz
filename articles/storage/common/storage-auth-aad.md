---
title: Autorizace přístupu k objektům BLOB Azure a fronty pomocí Azure Active Directory | Dokumentace Microsoftu
description: Autorizace přístupu k objektům BLOB Azure a fronty pomocí Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8033dda4059a52cea2b775fc8765a9f2a91b96dd
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302418"
---
# <a name="authorize-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Autorizace přístupu k objektům BLOB Azure a fronty pomocí Azure Active Directory

Pomocí Azure Active Directory (AD) k autorizaci požadavků na úložiště objektů Blob a fronty Azure Storage podporuje. S Azure AD můžete použít řízení přístupu na základě rolí (RBAC) k udělení oprávnění objektu zabezpečení, který může být uživatel, skupina nebo instanční objekt služby application. Objekt zabezpečení se ověřit pomocí služby Azure AD k vrácení tokenu OAuth 2.0. Token, který lze použít k autorizaci žádost o přístup k prostředku v úložišti objektů Blob nebo fronty.

Autorizace uživatelů nebo aplikací pomocí tokenu OAuth 2.0, která je vrácena službou Azure AD poskytuje nejvyšší zabezpečení a snadné použití přes sdílený klíč autorizace a sdílené přístupové podpisy (SAS). S Azure AD není nutné ukládat přístupový klíč účtu se váš kód a riziko potenciální ohrožení zabezpečení. Když můžete nadále používat povolení sdíleného klíče s vašimi aplikacemi, používání služby Azure AD obchází potřebou ukládání přístupový klíč k účtu s vaším kódem. Můžete taky dál používat sdílené přístupové podpisy (SAS) k udělení velice přesně kontrolovat přístup k prostředkům ve vašem účtu úložiště, ale Azure AD nabízí podobné funkce bez nutnosti spravovat tokeny SAS nebo si dělat starosti o odvolání SAS ohrožení zabezpečení. Microsoft doporučuje používat ověřování Azure AD s vašimi aplikacemi Azure Storage, pokud je to možné.

Autorizace pomocí služby Azure AD je k dispozici pro všechny pro obecné účely a účty Blob storage ve všech veřejných oblastech a národních cloudech. Jenom účty úložiště s podporou modelu nasazení Azure Resource Manageru vytvořené autorizace Azure AD.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Přehled služby Azure AD pro objekty BLOB a fronty

Když objektu zabezpečení (uživatele, skupinu nebo aplikaci) pokusí o přístup k objektu blob nebo fronty prostředku, musí být žádost ověřena Pokud není k dispozici pro anonymní přístup k objektu blob. S Azure AD přístup k prostředku je dvoustupňový proces. Nejprve je ověřený objekt zabezpečení identity a vrátí se token OAuth 2.0. V dalším kroku je token předat službě objektů Blob nebo fronty jako součást požadavku a používá služba k autorizaci přístupu pro zadaný prostředek.

Krok ověření vyžaduje, že aplikaci požádat o přístupový token OAuth 2.0 za běhu. Pokud je aplikace spuštěna z v rámci Azure entity jako je například virtuální počítač Azure, škálovací sadu virtuálních počítačů nebo aplikaci Azure Functions, můžete použít [se identita spravované](../../active-directory/managed-identities-azure-resources/overview.md) pro přístup k objektům BLOB nebo fronty. Zjistěte, jak autorizovat požadavků provedených spravovanou identitu ve službě Azure Blob nebo fronty, naleznete v tématu [autorizaci přístupu k objektům BLOB a fronty s Azure Active Directory a spravovaným identitám pro prostředky Azure](storage-auth-aad-msi.md).

Krok ověření vyžaduje, že jedna nebo víc rolí RBAC přiřadit k objektu zabezpečení. Azure Storage poskytuje role RBAC, které zahrnuje společné sady oprávnění pro data objektů blob a fronty. Role, které jsou přiřazeny k objektu zabezpečení určit oprávnění, která bude mít objekt zabezpečení. Další informace o přiřazování role RBAC pro Azure Storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).

Nativní a webové aplikace s požadavky na službu Azure Blob nebo fronty může také autorizace přístupu přes Azure AD. Zjistěte, jak požádat o přístupový token a použít ho k autorizaci požadavků na data blob nebo fronty, naleznete v tématu [autorizaci přístupu ke službě Azure Storage s Azure AD z aplikace služby Azure Storage](storage-auth-aad-app.md).

## <a name="assigning-rbac-roles-for-access-rights"></a>Přiřazení role RBAC pro přístupová práva

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md). Azure Storage definuje sadu předdefinované role RBAC, které zahrnuje společné sady oprávnění pro přístup k datům objektu blob a fronty. Můžete také definovat vlastní role pro přístup k datům objektu blob a fronty.

Když roli RBAC je přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup se dají vymezit na úrovni předplatného, skupinu prostředků, účet úložiště nebo kontejner nebo fronty. Objekt zabezpečení Azure AD může být uživatel, skupina, instančního objektu aplikace nebo [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Předdefinované role RBAC pro objekty BLOB a fronty

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Informace o přiřazení předdefinované role RBAC pro objekt zabezpečení, naleznete v následujících článcích:

- [Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md)
- [Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC pomocí rozhraní příkazového řádku Azure](storage-auth-aad-rbac-cli.md)
- [Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC pomocí Powershellu](storage-auth-aad-rbac-powershell.md)

Další informace o tom, předdefinované role jsou definované pro službu Azure Storage, najdete v článku [pochopení definic rolí](../../role-based-access-control/role-definitions.md#management-and-data-operations). Informace o vytváření vlastních rolí RBAC najdete v tématu [vytváření vlastních rolí pro řízení přístupu](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Přístupová oprávnění pro operace s daty

Podrobnosti o oprávněních požadovaných k volání operací konkrétní objekt Blob nebo fronty služby najdete v tématu [oprávnění pro volání operace s daty objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Prostředek oboru

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Účet Azure AD pro přístup k datům

Přístup k datům objektu blob nebo fronty pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku Azure je možné autorizovat pomocí účtu uživatele Azure AD nebo pomocí přístupové klíče účtu (Povolení sdíleného klíče).

### <a name="data-access-from-the-azure-portal"></a>Přístup k datům z portálu Azure portal

Na webu Azure portal můžete použít svůj účet služby Azure AD nebo přístupové klíče účtu pro přístup k datům objektu blob a fronty v účtu služby Azure storage. Schéma autorizace, které používá na webu Azure portal, závisí na role RBAC, které jsou vám přiřazeny.

Při pokusu o přístup k datům objektu blob nebo fronty na webu Azure portal nejprve zkontroluje, zda jste byli přiřazeni roli RBAC s **Microsoft.Storage/storageAccounts/listkeys/action**. Pokud máte přiřazenou roli s touto akcí na webu Azure portal se použije klíč účtu pro přístup k datům objektu blob a fronty pomocí sdíleného klíče autorizace. Pokud nebyly byla přiřazena role s touto akcí, pak na webu Azure portal se pokusí získat přístup k datům pomocí svého účtu Azure AD.

Pro přístup k objektu blob nebo fronty data z webu Azure portal pomocí svého účtu Azure AD, musíte mít oprávnění pro přístup k datům objektu blob a fronty a budete potřebovat oprávnění k procházení prostředků účtu úložiště na webu Azure Portal. Předdefinované role, které poskytuje Azure Storage udělit přístup k prostředkům blob a fronty, ale není udělují oprávnění k prostředkům účtu úložiště. Z tohoto důvodu se přístup k portálu také vyžaduje přiřazení role Azure Resource Manageru, jako [čtečky](../../role-based-access-control/built-in-roles.md#reader) role omezená na úrovni účtu úložiště nebo vyšší. **Čtečky** role uděluje oprávnění nejvíce s omezeným přístupem, ale jiné role Azure Resource Manageru, který uděluje přístup k prostředkům správy účtu úložiště je také přijatelné. Další informace o tom, jak přiřadit oprávnění uživatelům pro přístup k datům na webu Azure Portal pomocí účtu služby Azure AD najdete v tématu [udělit přístup k Azure data objektů blob a fronty pomocí RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md).

Na webu Azure portal Určuje schéma autorizace, které se používá v přejdete na kontejneru nebo fronty. Další informace o přístupu k datům na portálu najdete v tématu [pomocí webu Azure portal pro přístup k datům objektu blob nebo fronty](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Přístup k datům z Powershellu nebo rozhraní příkazového řádku Azure

Azure CLI a Powershellu podporu přihlášení pomocí přihlašovacích údajů Azure AD. Po přihlášení relaci běží pod tyto přihlašovací údaje. Další informace najdete v tématu [příkazy spuštění Azure CLI nebo Powershellu pomocí přihlašovacích údajů Azure AD pro přístup k datům objektu blob nebo fronty](storage-auth-aad-script.md).

## <a name="azure-ad-authorization-over-smb-for-azure-files"></a>Azure AD autorizace přes protokol SMB pro soubory Azure

Služba soubory Azure podporuje autorizace pomocí služby Azure AD prostřednictvím protokolu SMB pro připojené k doméně pouze pro virtuální počítače (preview). Další informace o používání služby Azure AD prostřednictvím protokolu SMB pro soubory Azure najdete v tématu [autorizace přehled o Azure Active Directory přes protokol SMB pro soubory Azure (preview)](../files/storage-files-active-directory-overview.md).

## <a name="next-steps"></a>Další postup

- [Autorizace přístupu k objektům BLOB a fronty s Azure Active Directory a spravovaným identitám pro prostředky Azure](storage-auth-aad-msi.md)
- [Ověřování s využitím Azure Active Directory z aplikace pro přístup k objektům blob a frontám](storage-auth-aad-app.md)
- [Podpora služby Azure Storage pro Azure Active Directory na základě Všeobecná dostupnost řízení přístupu](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
