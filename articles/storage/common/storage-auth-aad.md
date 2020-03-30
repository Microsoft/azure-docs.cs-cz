---
title: Autorizace přístupu k objektům BLOB a frontám pomocí služby Active Directory
titleSuffix: Azure Storage
description: Autorizujte přístup k objektům BLOB azure a frontám pomocí služby Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 2/23/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b8a42723a9b56665160e660c0ea1451253c3d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255363"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Autorizace přístupu k objektům BLOB a frontám pomocí služby Azure Active Directory

Azure Storage podporuje použití Azure Active Directory (Azure AD) k autorizaci požadavků na úložiště objektů Blob a Fronty. Pomocí služby Azure AD můžete pomocí řízení přístupu na základě rolí (RBAC) udělit oprávnění k objektu zabezpečení, který může být uživatel, skupina nebo instanční objekt služby aplikace. Objekt zabezpečení je ověřen službou Azure AD, aby vrátil token OAuth 2.0. Token pak můžete použít k autorizaci požadavku proti úložišti objektů Blob nebo Fronty.

Autorizace požadavků proti Azure Storage pomocí Azure AD poskytuje vynikající zabezpečení a snadné použití oproti autorizaci sdíleného klíče. Společnost Microsoft doporučuje používat autorizaci Azure AD s aplikacemi objektů blob a fronty, pokud je to možné, aby se minimalizovaly potenciální chyby zabezpečení vlastní sdílenému klíči.

Autorizace pomocí Azure AD je dostupná pro všechny účty úložiště pro obecné účely a úložiště objektů Blob ve všech veřejných oblastech a národních cloudech. Autorizaci Azure AD podporují jenom účty úložiště vytvořené pomocí modelu nasazení Azure Resource Manageru.

Úložiště objektů blob navíc podporuje vytváření sdílených přístupových podpisů (SAS), které jsou podepsané přihlašovacími údaji Azure AD. Další informace naleznete v tématu [Udělení omezeného přístupu k datům se sdílenými přístupovými podpisy](storage-sas-overview.md).

Azure Files podporuje autorizaci se službou AD (preview) nebo Azure AD DS (GA) přes SMB pouze pro virtuální počítače připojované k doméně. Další informace o používání služby AD (preview) nebo Azure AD DS (GA) přes SMB pro soubory Azure najdete [v tématu Přehled podpory ověřování na základě identity souborů Azure pro přístup SMB](../files/storage-files-active-directory-overview.md).

Autorizace pomocí Azure AD není pro úložiště Azure Table podporovaná. Pomocí sdíleného klíče autorizujte požadavky na úložiště tabulky.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Přehled Azure AD pro objekty BLOB a fronty

Když se objekt zabezpečení (uživatel, skupina nebo aplikace) pokusí o přístup k prostředku objektu blob nebo fronty, musí být požadavek autorizován, pokud se nejedná o objekt blob, který je k dispozici pro anonymní přístup. S Azure AD, přístup k prostředku je dvoustupňový proces. Nejprve je ověřena identita objektu zabezpečení a je vrácen token OAuth 2.0. Dále je token předán jako součást požadavku na službu Blob nebo Queue a používá ji služba k autorizaci přístupu k zadanému prostředku.

Krok ověřování vyžaduje, aby aplikace za běhu požadovala přístupový token OAuth 2.0. Pokud aplikace běží z entity Azure, jako je například virtuální počítač Azure, škálovací sada virtuálního počítače nebo aplikace Azure Functions, může použít [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) pro přístup k objektům BLOB nebo frontám. Informace o autorizaci požadavků ze spravované identity do služby Azure Blob nebo Queue najdete v [tématu Autorizace přístupu k objektům BLOB a frontám pomocí služby Azure Active Directory a spravovaných identit pro prostředky Azure](storage-auth-aad-msi.md).

Krok autorizace vyžaduje, aby byla k objektu zabezpečení přiřazena jedna nebo více rolí RBAC. Azure Storage poskytuje role RBAC, které zahrnují společné sady oprávnění pro data objektů blob a fronty. Role, které jsou přiřazeny k objektu zabezpečení určit oprávnění, která bude mít objekt zabezpečení. Další informace o přiřazování rolí RBAC pro Azure Storage najdete v [tématu Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).

Nativní aplikace a webové aplikace, které pořizují požadavky na službu Azure Blob nebo Queue, můžou taky autorizovat přístup pomocí Azure AD. Pokud se dozvíte, jak požádat o přístupový token a použít ho k autorizaci požadavků na data objektů blob nebo fronty, [přečtěte si autorizužití přístupu k Azure Storage pomocí Azure AD z aplikace Azure Storage](storage-auth-aad-app.md).

## <a name="assign-rbac-roles-for-access-rights"></a>Přiřazení rolí RBAC pro přístupová práva

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě rolí (RBAC).](../../role-based-access-control/overview.md) Azure Storage definuje sadu předdefinovaných rolí RBAC, které zahrnují společné sady oprávnění používaných pro přístup k datům objektů blob a fronty. Můžete také definovat vlastní role pro přístup k datům objektů blob a fronty.

Když je role RBAC přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup může být vymezen na úroveň předplatného, skupiny prostředků, účtu úložiště nebo jednotlivého kontejneru nebo fronty. Zaregistrovaný objekt zabezpečení Azure AD může být uživatel, skupina, instanční objekt aplikační služby nebo [spravovaná identita pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Předdefinované role RBAC pro objekty BLOB a fronty

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Chcete-li se dozvědět, jak přiřadit předdefinovanou roli RBAC k objektu zabezpečení, podívejte se na jeden z následujících článků:

- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md)
- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC pomocí Azure CLI](storage-auth-aad-rbac-cli.md)
- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC pomocí PowerShellu](storage-auth-aad-rbac-powershell.md)

Další informace o tom, jak jsou definované role definované pro Azure Storage, [najdete v tématu Principy definic rolí](../../role-based-access-control/role-definitions.md#management-and-data-operations). Informace o vytváření vlastních rolí RBAC najdete v [tématu Vytvoření vlastních rolí pro řízení přístupu na základě rolí Azure](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Přístupová oprávnění pro datové operace

Podrobnosti o oprávněních potřebných k volání konkrétních operací služby Objektů blob nebo Fronty naleznete v [tématu Oprávnění pro volání operací dat objektů blob a fronty](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Obor prostředků

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Přístup k datům pomocí účtu Azure AD

Přístup k datům objektů blob nebo fronty prostřednictvím portálu Azure Portal, PowerShellu nebo Azure CLI můžete autorizovat buď pomocí účtu Azure AD uživatele, nebo pomocí přístupových klíčů účtu (autorizace sdíleného klíče).

### <a name="data-access-from-the-azure-portal"></a>Přístup k datům z webu Azure Portal

Portál Azure můžou k přístupu k datům k objektům blob a fronty v účtu úložiště Azure použít buď váš účet Azure AD, nebo přístupové klíče účtu. Které autorizační schéma používá portál Azure, závisí na rolích RBAC, které jsou vám přiřazeny.

Při pokusu o přístup k datům objektu blob nebo fronty portál Azure nejprve zkontroluje, zda vám byla přiřazena role RBAC s **Microsoft.Storage/storageAccounts/listkeys/action**. Pokud vám byla přiřazena role s touto akcí, pak portál Azure používá klíč účtu pro přístup k datům objektů blob a fronty prostřednictvím autorizace sdíleného klíče. Pokud vám s touto akcí přiřazena role, pokusí se portál Azure získat přístup k datům pomocí vašeho účtu Azure AD.

Chcete-li získat přístup k datům objektu blob nebo fronty z portálu Azure pomocí účtu Azure AD, potřebujete oprávnění k přístupu k datům objektů blob a fronty a potřebujete také oprávnění k procházení prostředků účtu úložiště na webu Azure Portal. Předdefinované role poskytované službou Azure Storage udělují přístup k prostředkům objektů blob a fronty, ale neudělují oprávnění prostředkům účtu úložiště. Z tohoto důvodu přístup k portálu také vyžaduje přiřazení role Správce prostředků Azure, jako je například role [Reader,](../../role-based-access-control/built-in-roles.md#reader) vymezené na úroveň účtu úložiště nebo vyšší. Role **Čtenář** uděluje nejvíce omezená oprávnění, ale další role Azure Resource Manager, který uděluje přístup k prostředkům pro správu účtu úložiště je také přijatelné. Další informace o tom, jak přiřadit oprávnění uživatelům pro přístup k datům na webu Azure Portal s účtem Azure AD, najdete v článku [Udělení přístupu k objektům blob Azure a datfronty fronty s RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md).

Portál Azure označuje, které autorizační schéma se používá při přechodu do kontejneru nebo fronty. Další informace o přístupu k datům na portálu najdete v [tématu Přístup k datům objektu blob nebo fronty pomocí portálu Azure](storage-access-blobs-queues-portal.md)Portal .

### <a name="data-access-from-powershell-or-azure-cli"></a>Přístup k datům z PowerShellu nebo azure cli

Azure CLI a PowerShell podporují přihlášení pomocí přihlašovacích údajů Azure AD. Po přihlášení se vaše relace spustí pod těmito pověřeními. Další informace najdete v [tématu Spouštění příkazů Azure CLI nebo PowerShellu s přihlašovacími údaji Azure AD pro přístup k datům objektu blob nebo fronty](authorize-active-directory-powershell.md).

## <a name="next-steps"></a>Další kroky

- [Autorizace přístupu k objektům BLOB a frontám pomocí Služby Azure Active Directory a spravovaných identit pro prostředky Azure](storage-auth-aad-msi.md)
- [Autorizace pomocí služby Azure Active Directory z aplikace pro přístup k objektům BLOB a frontám](storage-auth-aad-app.md)
- [Podpora Azure Storage pro řízení přístupu založené na Azure Active Directory je obecně dostupná](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
