---
title: Přístup k datům objektu blob nebo fronty pomocí portálu Azure
titleSuffix: Azure Storage
description: Když připřístupujete data objektů blob nebo fronty pomocí portálu Azure, portál bude na Azure Storage pod kryty. Tyto požadavky na Azure Storage můžete ověřit a autorizovat pomocí účtu Azure AD nebo přístupového klíče účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 602be49ef0c60274f1cd016c4f8e870cf033ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75866899"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Přístup k datům objektu blob nebo fronty pomocí portálu Azure

Při přístupu k datům objektu blob nebo fronty pomocí [portálu Azure](https://portal.azure.com)portal provádí požadavky na Azure Storage pod kryty. Požadavek na Azure Storage můžete autorizovat pomocí účtu Azure AD nebo přístupového klíče účtu úložiště. Portál označuje, kterou metodu používáte, a umožňuje přepínat mezi těmito dvěma, pokud máte příslušná oprávnění.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Oprávnění potřebná pro přístup k datům objektu blob nebo fronty

V závislosti na tom, jak chcete autorizovat přístup k datům objektů blob nebo fronty na webu Azure Portal, budete potřebovat konkrétní oprávnění. Ve většině případů jsou tato oprávnění poskytována prostřednictvím řízení přístupu na základě rolí (RBAC). Další informace o RBAC naleznete v tématu [Co je řízení přístupu na základě rolí (RBAC)?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Přístupový klíč účtu

Chcete-li získat přístup k datům objektu blob a fronty pomocí klíče přístupu k účtu, musíte mít přiřazenou roli RBAC, která zahrnuje akci RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Tato role RBAC může být předdefinované nebo vlastní role. Mezi předdefinované role, které podporují **Microsoft.Storage/storageAccounts/listkeys/action,** zahrnují:

- Role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) Správce prostředků Azure
- Role [přispěvatele](../../role-based-access-control/built-in-roles.md#contributor) Správce prostředků Azure
- Role [přispěvatele účtu úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Při pokusu o přístup k datům objektu blob nebo fronty na webu Azure Portal nejprve zkontroluje, zda vám byla přiřazena role u **Microsoft.Storage/storageAccounts/listkeys/action**. Pokud vám byla přiřazena role s touto akcí, pak portál používá klíč účtu pro přístup k datům objektu blob a fronty. Pokud vám nebyla přiřazena role s touto akcí, pak portál pokusí o přístup k datům pomocí účtu Azure AD.

> [!NOTE]
> Klasické role správce předplatného Správce služeb a spolusprávce zahrnují ekvivalent role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) Správce prostředků Azure. Role **Vlastník** zahrnuje všechny akce, včetně **Microsoft.StorageAccounts/listkeys/action**, takže uživatel s jednou z těchto rolí pro správu může také přistupovat k datům objektu blob a fronty pomocí klíče účtu. Další informace naleznete v [tématu Klasické role správce předplatného](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Účet Azure AD

Chcete-li získat přístup k datům objektu blob nebo fronty z portálu Azure pomocí účtu Azure AD, musí pro vás platit oba následující příkazy:

- Byla vám přiřazena role Azure Resource Manager [Reader,](../../role-based-access-control/built-in-roles.md#reader) minimálně s vymezeným oborem na úroveň účtu úložiště nebo vyšší. Role **Čtenář** uděluje nejvíce omezená oprávnění, ale další role Azure Resource Manager, který uděluje přístup k prostředkům pro správu účtu úložiště je také přijatelné.
- Byla vám přiřazena předdefinovaná nebo vlastní role, která poskytuje přístup k datům objektů blob nebo fronty.

Přiřazení role **Reader** nebo jiné přiřazení role Azure Resource Manager je nezbytné, aby uživatel mohl zobrazit a procházet prostředky správy účtu úložiště na webu Azure Portal. Role RBAC, které udělují přístup k datům objektů blob nebo fronty, neudělují přístup k prostředkům pro správu účtu úložiště. Pro přístup k datům objektu blob nebo fronty na portálu potřebuje uživatel oprávnění k navigaci prostředků účtu úložiště. Další informace o tomto požadavku naleznete [v tématu Přiřazení role Čtečka pro přístup k portálu](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Mezi předdefinované role, které podporují přístup k datům objektu blob nebo fronty, zahrnují:

- [Vlastník dat objektu blob úložiště:](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)Pro řízení přístupu POSIX pro Azure Data Lake Storage Gen2.
- [Přispěvatel dat objektů blob úložiště:](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)Oprávnění pro čtení, zápis a odstranění pro objekty BLOB.
- [Čtečka dat objektu blob úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Oprávnění pro objekty BLOB jen pro čtení.
- [Přispěvatel dat fronty úložiště:](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)Oprávnění pro čtení, zápis a odstranění pro fronty.
- [Čtečka dat fronty úložiště](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Oprávnění jen pro čtení pro fronty.
    
Vlastní role mohou podporovat různé kombinace stejných oprávnění poskytovaných předdefinovanými rolemi. Další informace o vytváření vlastních rolí RBAC najdete [v tématu Vlastní role pro prostředky Azure](../../role-based-access-control/custom-roles.md) a [Principy definic rolí pro prostředky Azure](../../role-based-access-control/role-definitions.md).

Výpis front s klasickou rolí správce předplatného není podporován. Aby mohl uživatel vypsat fronty, musí jim přiřadit roli **Čtečky** prostředků Správce prostředků Azure, roli **Čtečka dat fronty úložiště** nebo roli **přispěvatele dat fronty úložiště.**

> [!IMPORTANT]
> Verze preview Průzkumníka úložiště na webu Azure Portal nepodporuje použití přihlašovacích údajů Azure AD k zobrazení a úpravě dat objektu blob nebo fronty. Průzkumník úložiště na webu Azure Portal vždy používá klíče účtu pro přístup k datům. Pokud chcete na webu Azure Portal používat Průzkumníka úložiště, musíte vám přiřadit roli, která zahrnuje **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Přechod na objekty BLOB nebo fronty na portálu

Chcete-li zobrazit data objektu blob nebo fronty na portálu, přejděte na **přehled** účtu úložiště a klikněte na odkazy na **objekty BLOB** nebo **Fronty**. Případně můžete přejít na části **služby blob** a **fronty** v nabídce. 

![Přechod na data objektu blob nebo fronty na webu Azure Portal](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Určení aktuální metody ověřování

Když přejdete do kontejneru nebo fronty, portál Azure označuje, jestli aktuálně používáte klíč pro přístup k účtu nebo účet Azure AD k ověření.

Příklady v této části ukazují přístup ke kontejneru a jeho objektům BLOB, ale portál zobrazí stejnou zprávu při přístupu k frontě a jejím zprávám nebo výpisu front.

### <a name="account-access-key"></a>Přístupový klíč účtu

Pokud ověřujete pomocí přístupového klíče účtu, zobrazí se jako metoda ověřování na portálu jako přístupový **klíč:**

![V současné době přístup k datům kontejneru pomocí klíče účtu](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Pokud chcete přepnout na používání účtu Azure AD, klikněte na odkaz zvýrazněný v bitové kopii. Pokud máte příslušná oprávnění prostřednictvím rolí RBAC, které jsou vám přiřazeny, budete moci pokračovat. Pokud však nemáte správná oprávnění, zobrazí se chybová zpráva, jako je následující:

![Chyba se zobrazí, pokud účet Azure AD nepodporuje přístup](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Všimněte si, že žádné objekty BLOB se zobrazí v seznamu, pokud váš účet Azure AD nemá oprávnění k jejich zobrazení. Kliknutím na **odkaz Přepnout pro přístupke ke klíči** znovu použijete přístupový klíč k ověření.

### <a name="azure-ad-account"></a>Účet Azure AD

Pokud ověřujete pomocí účtu Azure AD, uvidíte **uživatelský účet Azure AD** zadaný jako metoda ověřování na portálu:

![V současné době přístup k datům kontejneru s účtem Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Pokud chcete přepnout na použití přístupového klíče účtu, klikněte na odkaz zvýrazněný na obrázku. Pokud máte přístup ke klíči účtu, budete moci pokračovat. Pokud však nemáte přístup ke klíči účtu, zobrazí se chybová zpráva, jako je následující:

![Pokud nemáte přístup ke klíči účtu, zobrazí se chyba.](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Všimněte si, že žádné objekty BLOB se zobrazí v seznamu, pokud nemáte přístup ke klíčům účtu. Klikněte na odkaz **Přepnout na uživatelský účet Azure AD** a znovu použít svůj účet Azure AD k ověření.

## <a name="next-steps"></a>Další kroky

- [Ověření přístupu k objektům BLOB azure a frontám pomocí služby Azure Active Directory](storage-auth-aad.md)
- [Udělit přístup k kontejnerům Azure a frontám pomocí RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md)
- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC pomocí Azure CLI](storage-auth-aad-rbac-cli.md)
- [Udělení přístupu k datům front a objektů blob Azure s využitím RBAC pomocí PowerShellu](storage-auth-aad-rbac-powershell.md)
