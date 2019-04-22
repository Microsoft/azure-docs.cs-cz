---
title: Pomocí webu Azure portal přístup k objektu blob nebo fronty dat – Azure Storage
description: Při přístupu k objektu blob nebo fronty s daty pomocí webu Azure portal, na portálu je žádosti do služby Azure Storage pod pokličkou. Tyto žádosti do služby Azure Storage může být ověří a autorizuje buď pomocí účtu Azure AD nebo přístupový klíč účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: b56cbfbc9dfde8b1a7d43d55ee85c34fde385902
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58846387"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Pomocí webu Azure portal pro přístup k datům objektu blob nebo fronty

Při přístupu k objektu blob nebo fronty data s využitím [webu Azure portal](https://portal.azure.com), portálu vytvářejí požadavky do služby Azure Storage na pozadí. Tyto žádosti do služby Azure Storage může být ověří a autorizuje buď pomocí účtu Azure AD nebo přístupový klíč účtu úložiště. Portál bude informovat, kterou metodu ověřování a umožňuje vám přepínat mezi těmito dvěma, pokud máte příslušná oprávnění.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Oprávnění potřebná pro přístup k datům objektu blob nebo fronty

V závislosti na tom, jak chcete ověřit přístup k datům objektu blob nebo fronty na webu Azure Portal potřebujete konkrétní oprávnění. Ve většině případů jsou k dispozici tato oprávnění prostřednictvím řízení přístupu na základě role (RBAC). Další informace o RBAC najdete v tématu [co je řízení přístupu na základě rolí (RBAC)?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Přístupový klíč účtu

Pro přístup k datům objektu blob a fronty se přístupový klíč účtu, musíte mít roli RBAC se vám přiřadí, který se týká také akce RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Tato role RBAC může být vestavěná nebo vlastní roli. Předdefinované role, které podporují **Microsoft.Storage/storageAccounts/listkeys/action** patří:

- Azure Resource Manageru [vlastníka](../../role-based-access-control/built-in-roles.md#owner) role
- Azure Resource Manageru [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) role
- [Přispěvatel účtů úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor) role

Při pokusu o přístup k datům objektu blob nebo fronty na webu Azure Portal, na portálu nejprve zkontroluje, zda jste byli přiřazeni roli s **Microsoft.Storage/storageAccounts/listkeys/action**. Pokud máte přiřazenou roli s touto akcí, portál používá klíč účtu pro přístup k datům objektu blob a fronty. Pokud nebyly byla přiřazena role s touto akcí, následně na portálu se pokusí získat přístup k datům pomocí svého účtu Azure AD.

> [!NOTE]
> Role správce klasickém modelu předplatného Správce služeb a Spolusprávce musí zahrnovat ekvivalent Azure Resource Manageru [vlastníka](../../role-based-access-control/built-in-roles.md#owner) role. **Vlastníka** zahrnuje všechny akce, včetně role **Microsoft.Storage/storageAccounts/listkeys/action**, takže data objektů blob a fronty se dostanete také uživatele s jedním z těchto správních rolí klíč účtu. Další informace najdete v tématu [Classic role správců předplatného](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Účet Azure AD

Pro přístup k datům objektu blob nebo fronty na webu Azure Portal pomocí svého účtu Azure AD, musí být splněné pro vás i následující příkazy:

- Jste byli přiřazeni Azure Resource Manageru [čtečky](../../role-based-access-control/built-in-roles.md#reader) role minimálně s vymezeným oborem úroveň účtu úložiště nebo vyšší. **Čtečky** role uděluje oprávnění nejvíce s omezeným přístupem, ale jiné role Azure Resource Manageru, který uděluje přístup k prostředkům správy účtu úložiště je také přijatelné.
- Jste byli přiřazeni buď vestavěná nebo vlastní roli, která poskytuje přístup k objektům BLOB nebo fronty data.

**Čtečky** přiřazení role nebo jiné přiřazení role Azure Resource Manageru je nutné, aby uživatel můžete zobrazit a procházet prostředky správy účtu úložiště na webu Azure Portal. Role RBAC, které udělují přístup k datům objektu blob nebo fronty bez možnosti udělovat přístup k prostředkům správy účtu úložiště. Uživatel pro přístup k datům objektu blob nebo fronty na portálu, potřebuje oprávnění k procházení prostředků účtu úložiště. Další informace o tomto požadavku najdete v tématu [přiřadit role Čtenář pro přístup k portálu](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Předdefinované role, které podporují přístup k datům objektu blob nebo fronty patří:

- [Vlastník dat objektů Blob úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Pro POSIX řízení přístupu pro Azure Data Lake Storage Gen2 (preview).
- [Přispěvatel dat objektu Blob úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Oprávnění ke čtení/zápis/odstranění pro objekty BLOB.
- [Čtenář dat objektu Blob úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Oprávnění jen pro čtení pro objekty BLOB.
- [Přispěvatel dat fronty služby Storage](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Oprávnění pro čtení/zápis/delete pro fronty.
- [Čtenář dat fronty služby Storage](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Oprávnění jen pro čtení pro fronty.
    
Vlastní role může podporovat různé kombinace stejná oprávnění, poskytuje předdefinované role. Další informace o vytváření vlastních rolí RBAC najdete v tématu [vlastní role pro prostředky Azure](../../role-based-access-control/custom-roles.md) a [pochopení definic rolí pro prostředky Azure](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> Výpis front s rolí správce klasickém modelu předplatného se nepodporuje. Vypsat zařadí do fronty, uživatel musí mít přiřazenou k nim Azure Resource Manageru **čtečky** role, **Čtenář dat fronty služby Storage** role, nebo **Přispěvatel dat fronty služby Storage** role.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Přejděte na objekty BLOB nebo fronty na portálu

Chcete-li zobrazit data objektů blob nebo fronty na portálu, přejděte na **přehled** pro účet úložiště a kliknutím na odkazy pro **objekty BLOB** nebo **fronty**. Můžete také přejít **službu Blob service** a **služba front** oddíly v nabídce. 

![Přejděte k datům objektu blob nebo fronty na webu Azure Portal](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Určete aktuální metodu ověření

Při přechodu ke kontejneru nebo frontu na webu Azure portal označuje, zda používáte přístupový klíč účtu nebo účtu služby Azure AD k ověřování.

Příklady v této části ukazují, přístup ke kontejneru a jeho objekty BLOB, ale na portálu se zobrazí tato zpráva při přístupu k frontě a její zprávy, nebo seznam front.

### <a name="account-access-key"></a>Přístupový klíč účtu

Pokud ověřujete pomocí přístupového klíče účtu, zobrazí se vám **přístupový klíč** zadaný jako metodu ověřování na portálu:

![Nyní přístup k datům kontejneru s klíčem účtu](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Přejít na používání účtu Azure AD, klikněte na odkaz obrázku zvýrazněné. Pokud máte příslušná oprávnění přes role RBAC, které jsou vám přiřazeny, budete moct pokračovat. Ale pokud nemáte správná oprávnění, zobrazí se vám chybová zpráva jako na následující:

![Chyba zobrazí, pokud účet Azure AD nepodporuje přístup](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Všimněte si, že se žádné objekty BLOB se zobrazí v seznamu, pokud váš účet služby Azure AD nemá oprávnění k jejich zobrazení. Klikněte na **přepínač tak, aby přístupový klíč** odkaz se má použít přístupový klíč pro ověřování znovu.

### <a name="azure-ad-account"></a>Účet Azure AD

Pokud ověřujete pomocí svého účtu Azure AD, zobrazí se vám **uživatelského účtu Azure AD** zadaný jako metodu ověřování na portálu:

![Nyní přístup k datům kontejneru účtu služby Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Chcete-li přejít k používání přístupový klíč účtu, klikněte na odkaz obrázku zvýrazněné. Pokud máte přístup ke klíči účtu, potom budete moct pokračovat. Ale pokud nemáte přístup ke klíči účtu, zobrazí se vám chybová zpráva jako na následující:

![Chyba zobrazí, pokud nemáte přístup ke klíči účtu](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Všimněte si, že se žádné objekty BLOB se zobrazí v seznamu, pokud nemáte přístup ke klíčům účtu. Klikněte na **přepnout do uživatelského účtu Azure AD** odkaz použít svůj účet služby Azure AD pro ověřování znovu.

## <a name="next-steps"></a>Další postup

- [Ověření přístupu k objektům BLOB Azure a fronty pomocí Azure Active Directory](storage-auth-aad.md)
- [Udělení přístupu k kontejnery služby Azure a fronty pomocí RBAC na webu Azure Portal](storage-auth-aad-rbac-portal.md)
- [Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC pomocí rozhraní příkazového řádku Azure](storage-auth-aad-rbac-cli.md)
- [Udělení přístupu k Azure data objektů blob a fronty pomocí RBAC pomocí Powershellu](storage-auth-aad-rbac-powershell.md)
