---
title: Autorizace přístupu k objektům blob a frontám pomocí služby Active Directory
titleSuffix: Azure Storage
description: Udělte přístup k objektům blob a frontám Azure pomocí Azure Active Directory (Azure AD). Přiřaďte role Azure pro přístupová práva. Přístup k datům pomocí účtu Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: f681a271dcd64468cb471c81496e489f0ce8413f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91713666"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory

Azure Storage podporuje používání služby Azure Active Directory (Azure AD) k autorizaci požadavků na úložiště objektů BLOB a front. Pomocí Azure AD můžete použít řízení přístupu na základě role Azure (Azure RBAC) k udělení oprávnění objektu zabezpečení, který může být uživatel, skupina nebo instanční objekt aplikace. Služba Azure AD ověřuje objekt zabezpečení, aby vrátil token OAuth 2,0. Token se pak dá použít k autorizaci požadavku na úložiště objektů BLOB nebo front.

Autorizace požadavků proti Azure Storage se službou Azure AD zajišťuje zajištění vysokého zabezpečení a snadného použití při autorizaci pomocí sdíleného klíče. Microsoft doporučuje používat autorizaci Azure AD s vašimi aplikacemi BLOB a Queue, pokud je to možné, aby se minimalizovala potenciální ohrožení zabezpečení, která jsou v podstatě na

Autorizaci s Azure AD je k dispozici pro všechny účty pro obecné účely a BLOB Storage ve všech veřejných oblastech a národních cloudech. Autorizaci Azure AD podporují jenom účty úložiště vytvořené pomocí modelu nasazení Azure Resource Manager.

BLOB Storage dále podporuje vytváření sdílených přístupových podpisů (SAS), které jsou podepsané pomocí přihlašovacích údajů Azure AD. Další informace najdete v tématu [udělení omezeného přístupu k datům pomocí sdílených přístupových podpisů](storage-sas-overview.md).

Služba soubory Azure podporuje autorizaci se službou AD (Preview) nebo Azure služba AD DS (GA) přes protokol SMB jenom pro virtuální počítače připojené k doméně. Další informace o používání služby AD (Preview) nebo Azure služba AD DS (GA) prostřednictvím protokolu SMB pro soubory Azure najdete v tématu [Přehled podpory ověřování na základě identity souborů Azure pro přístup přes protokol SMB](../files/storage-files-active-directory-overview.md).

Autorizace pomocí Azure AD není pro Azure Table Storage podporovaná. Pomocí sdíleného klíče můžete autorizovat požadavky na úložiště tabulek.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Přehled služby Azure AD pro objekty BLOB a fronty

Pokud se objekt zabezpečení (uživatel, skupina nebo aplikace) pokusí o přístup k prostředku BLOB nebo Queue, požadavek musí být autorizovaný, pokud se nejedná o objekt blob, který by měl být k dispozici pro anonymní přístup. S Azure AD je přístup k prostředku v procesu se dvěma kroky. Nejprve je ověřená identita objektu zabezpečení a vrátí se token OAuth 2,0. V dalším kroku se token předává jako součást požadavku objektu BLOB nebo Služba front a služba ji používá k autorizaci přístupu k zadanému prostředku.

Krok ověřování vyžaduje, aby aplikace při běhu požadovala přístupový token OAuth 2,0. Pokud aplikace běží v rámci entity Azure, jako je třeba virtuální počítač Azure, sada škálování virtuálního počítače nebo aplikace Azure Functions, může pro přístup k objektům blob nebo frontám použít [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) . Informace o tom, jak autorizovat požadavky vytvořené spravovanou identitou do objektu BLOB nebo Služba front Azure, najdete v tématu [autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](storage-auth-aad-msi.md).

Autorizační krok vyžaduje, aby se k objektu zabezpečení přiřadila jedna nebo více rolí Azure. Azure Storage poskytuje role Azure, které zahrnují společné sady oprávnění pro data objektů BLOB a front. Role, které jsou přiřazeny objektu zabezpečení, určují oprávnění, která bude mít objekt zabezpečení. Další informace o přiřazování rolí Azure pro Azure Storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí Azure RBAC](storage-auth-aad-rbac.md).

Nativní aplikace a webové aplikace, které vytvářejí požadavky na Azure Blob nebo Služba front, můžou taky autorizovat přístup pomocí Azure AD. Informace o tom, jak požádat o přístupový token a použít ho k autorizaci žádostí o data objektů BLOB nebo front, najdete v tématu [autorizace přístupu k Azure Storage pomocí Azure AD z aplikace Azure Storage](storage-auth-aad-app.md).

## <a name="assign-azure-roles-for-access-rights"></a>Přiřazení rolí Azure pro přístupová práva

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage definuje sadu předdefinovaných rolí Azure, které zahrnují společné sady oprávnění používané pro přístup k datům objektů BLOB a front. Můžete také definovat vlastní role pro přístup k datům objektů BLOB a front.

Když je role Azure přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup může být vymezený na úrovni předplatného, skupiny prostředků, účtu úložiště nebo jednotlivého kontejneru nebo fronty. Objekt zabezpečení Azure AD může být uživatelem, skupinou, instančním objektem služby nebo [spravovanou identitou pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-built-in-roles-for-blobs-and-queues"></a>Předdefinované role pro objekty BLOB a fronty Azure

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Informace o tom, jak přiřadit předdefinovanou roli Azure k objektu zabezpečení, najdete v jednom z následujících článků:

- [Přiřazení role Azure pro přístup k datům BLOB a Queue pomocí Azure Portal](storage-auth-aad-rbac-portal.md)
- [Přiřazení role Azure pro přístup k datům objektů BLOB a front pomocí Azure CLI](storage-auth-aad-rbac-cli.md)
- [Použití modulu Azure PowerShell k přiřazení role Azure pro přístup k datům objektů BLOB a front](storage-auth-aad-rbac-powershell.md)

Další informace o tom, jak jsou předdefinované role definované pro Azure Storage, najdete v tématu [vysvětlení definic rolí](../../role-based-access-control/role-definitions.md#management-and-data-operations). Informace o vytváření vlastních rolí Azure najdete v tématu [vlastní role Azure](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Přístupová oprávnění pro datové operace

Podrobnosti o oprávněních požadovaných pro volání konkrétních operací BLOB nebo Služba front najdete v tématu [oprávnění pro volání operací s daty BLOB a Queue](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Obor prostředku

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Přístup k datům pomocí účtu Azure AD

Přístup k datům objektů BLOB nebo front pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure můžete autorizovat pomocí účtu Azure AD uživatele nebo pomocí přístupových klíčů k účtu (autorizace pomocí sdíleného klíče).

### <a name="data-access-from-the-azure-portal"></a>Přístup k datům z Azure Portal

Azure Portal může použít účet služby Azure AD nebo přístupové klíče účtu k přístupu k datům objektů BLOB a Queue v účtu úložiště Azure. Které autorizační schéma, které Azure Portal používá, závisí na rolích Azure, které jsou vám přiřazeny.

Když se pokusíte o přístup k datům objektů BLOB nebo front, Azure Portal nejprve zkontroluje, jestli vám byla přiřazena role Azure s **Microsoft. Storage/storageAccounts/klíče listkey/Action**. Pokud jste k této akci přiřadili roli, používá Azure Portal klíč účtu pro přístup k datům BLOB a Queue přes autorizaci pomocí sdíleného klíče. Pokud jste k této akci nepřiřadili roli, Azure Portal se pokusí získat přístup k datům pomocí účtu služby Azure AD.

Pokud chcete získat přístup k datům objektů BLOB nebo front z Azure Portal pomocí svého účtu služby Azure AD, potřebujete oprávnění pro přístup k datům objektů BLOB a Queue a Vy taky potřebujete oprávnění k procházení prostředků účtu úložiště v Azure Portal. Předdefinované role poskytované Azure Storage udělují přístup k prostředkům BLOB a Queue, ale neudělují oprávnění k prostředkům účtu úložiště. Z tohoto důvodu přístup k portálu vyžaduje taky přiřazení role Azure Resource Manager, jako je například role [Čtenář](../../role-based-access-control/built-in-roles.md#reader) , vymezená na úrovni účtu úložiště nebo vyšší. Role **Čtenář** uděluje nejvyšší omezení oprávnění, ale také je přijatelná jiná role Azure Resource Manager, která uděluje přístup k prostředkům správy účtu úložiště. Další informace o tom, jak přiřadit oprávnění uživatelům k přístupu k datům v Azure Portal s účtem služby Azure AD, najdete v tématu [použití Azure Portal k přiřazení role Azure pro přístup k datům objektů BLOB a front](storage-auth-aad-rbac-portal.md).

Azure Portal určuje, které schéma autorizace se používá, když přejdete do kontejneru nebo fronty. Další informace o přístupu k datům na portálu najdete v tématu [Volba způsobu autorizace přístupu k datům objektu BLOB v Azure Portal](../blobs/authorize-blob-access-portal.md) a [Výběr způsobu autorizace přístupu k datům ve frontě v Azure Portal](../queues/authorize-queue-access-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Přístup k datům z PowerShellu nebo Azure CLI

Azure CLI a PowerShell podporují přihlašování pomocí přihlašovacích údajů Azure AD. Po přihlášení se relace spustí pod těmito přihlašovacími údaji. Další informace najdete v tématu [spuštění příkazů Azure CLI nebo PowerShellu s přihlašovacími údaji Azure AD pro přístup k datům BLOB nebo Queue](authorize-active-directory-powershell.md).

## <a name="next-steps"></a>Další kroky

- [Autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](storage-auth-aad-msi.md)
- [Autorizace pomocí Azure Active Directory z aplikace pro přístup k objektům blob a frontám](storage-auth-aad-app.md)
- [Azure Storage podpora pro řízení přístupu na základě Azure Active Directory všeobecně dostupná](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
