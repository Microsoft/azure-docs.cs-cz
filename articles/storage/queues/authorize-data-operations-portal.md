---
title: Vyberte, jak autorizovat přístup k datům ve frontě v Azure Portal
titleSuffix: Azure Storage
description: Při přístupu k datům ve frontě pomocí Azure Portal portál vytvoří žádosti, které se Azure Storage v rámci pokrývání. Tyto požadavky na Azure Storage se dají ověřit a autorizovat pomocí účtu Azure AD nebo přístupového klíče účtu úložiště.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozguns
ms.date: 09/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: contperf-fy21q1
ms.openlocfilehash: 504d2eb939758e6045a2af095c66093c8754cb94
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590745"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>Vyberte, jak autorizovat přístup k datům ve frontě v Azure Portal

Při přístupu k datům ve frontě pomocí [Azure Portal](https://portal.azure.com)portál vytvoří žádosti, které se Azure Storage v rámci pokrývání. Požadavek na Azure Storage může být autorizovaný buď pomocí účtu Azure AD, nebo přístupového klíče účtu úložiště. Portál indikuje, kterou metodu používáte, a umožňuje přepínat mezi nimi, pokud máte příslušná oprávnění.

## <a name="permissions-needed-to-access-queue-data"></a>Oprávnění potřebná pro přístup k datům ve frontě

V závislosti na tom, jak chcete autorizovat přístup k datům ve frontě v Azure Portal, budete potřebovat specifická oprávnění. Ve většině případů jsou tato oprávnění poskytována prostřednictvím řízení přístupu na základě role Azure (RBAC). Další informace o službě Azure RBAC najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Použití přístupového klíče účtu

Pokud chcete získat přístup k datům front pomocí přístupového klíče účtu, musíte mít přiřazenou roli Azure, která zahrnuje akci Azure RBAC `Microsoft.Storage/storageAccounts/listkeys/action` . Tato role Azure může být integrovaná nebo vlastní role. Mezi předdefinované role, které podporují `Microsoft.Storage/storageAccounts/listkeys/action` :

- [Role vlastníka](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager
- [Role přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- [Role Přispěvatel účtu úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Když se pokusíte o přístup k datům ve frontě v Azure Portal, portál nejprve zkontroluje, jestli vám byla přiřazena role `Microsoft.Storage/storageAccounts/listkeys/action` . Pokud jste k této akci přiřadili roli, portál použije klíč účtu pro přístup k datům ve frontě. Pokud jste k této akci nepřiřadili roli, pokusí se portál získat přístup k datům pomocí účtu Azure AD.

> [!NOTE]
> **Správci služby** pro klasický odběr role správce a **spolusprávce** zahrnují ekvivalent [`Owner`](../../role-based-access-control/built-in-roles.md#owner) role Azure Resource Manager. Role **vlastníka** zahrnuje všechny akce, včetně `Microsoft.Storage/storageAccounts/listkeys/action` , takže uživatel s jednou z těchto rolí pro správu může k datům fronty přistupovat také pomocí klíče účtu. Další informace najdete v tématech [role správců klasického předplatného, role Azure a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Použití účtu Azure AD

Pokud chcete získat přístup k datům ve frontě z Azure Portal pomocí účtu Azure AD, musí být pro vás oba následující příkazy pravdivé:

- Přidělili jste Azure Resource Manager roli, která je přinejmenším [`Reader`](../../role-based-access-control/built-in-roles.md#reader) vymezená na úroveň účtu úložiště nebo vyšší. Role **Čtenář** uděluje nejvyšší omezení oprávnění, ale také je přijatelná jiná role Azure Resource Manager, která uděluje přístup k prostředkům správy účtu úložiště.
- Přiřadili jste buď integrovanou, nebo vlastní roli, která poskytuje přístup k datům ve frontě.

Přiřazení role **Čtenář** nebo jiné Azure Resource Manager přiřazení role je nezbytné, aby uživatel mohl zobrazit a procházet prostředky správy účtu úložiště v Azure Portal. Role Azure, které udělují přístup k datům ve frontě, neudělují přístup k prostředkům správy účtu úložiště. Pro přístup k datům ve frontě na portálu potřebuje uživatel oprávnění k procházení prostředků účtu úložiště. Další informace o tomto požadavku najdete v tématu [přiřazení role čtenáře pro přístup k portálu](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Mezi předdefinované role podporující přístup k datům ve frontě patří:

- [Přispěvatel dat fronty úložiště](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): oprávnění ke čtení, zápisu a odstraňování front.
- [Čtecí modul dat fronty úložiště](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): oprávnění jen pro čtení pro fronty.

Vlastní role mohou podporovat různé kombinace stejných oprávnění poskytovaných integrovanými rolemi. Další informace o vytváření vlastních rolí Azure najdete v tématu [vlastní role Azure](../../role-based-access-control/custom-roles.md) a [pochopení definic rolí pro prostředky Azure](../../role-based-access-control/role-definitions.md).

Výpis front s rolí správce předplatného s klasickou sadou není podporován. K vypsání front se musí uživatel přiřadit k rolím **čtenář** Azure Resource Manager, roli **čtečky dat fronty úložiště** nebo k roli **Přispěvatel dat fronty úložiště** .

> [!IMPORTANT]
> Verze Preview Průzkumník služby Storage v Azure Portal nepodporuje použití přihlašovacích údajů Azure AD k zobrazení a úpravě dat fronty. Průzkumník služby Storage v Azure Portal vždy používá klíče účtu pro přístup k datům. Pokud chcete použít Průzkumník služby Storage v Azure Portal, musíte mít přiřazenou roli, která zahrnuje `Microsoft.Storage/storageAccounts/listkeys/action` .

## <a name="navigate-to-queues-in-the-azure-portal"></a>Přejděte do fronty v Azure Portal

Pokud chcete zobrazit data ve frontě na portálu, přejděte na **Přehled** svého účtu úložiště a klikněte na odkazy pro **fronty**. Případně můžete přejít do části **Služba front** v nabídce.

:::image type="content" source="media/authorize-data-operations-portal/queue-access-portal.png" alt-text="Snímek obrazovky ukazující, jak přejít na data ve frontě v Azure Portal":::

## <a name="determine-the-current-authentication-method"></a>Určení aktuální metody ověřování

Když přejdete do fronty, Azure Portal určuje, jestli aktuálně používáte přístupový klíč účtu nebo účet Azure AD k ověřování.

### <a name="authenticate-with-the-account-access-key"></a>Ověřování pomocí přístupového klíče účtu

Pokud ověřujete pomocí přístupového klíče účtu, zobrazí se na portálu **přístupová klávesa** , která se zadala jako metoda ověřování:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Snímek obrazovky uživatele, který aktuálně přistupuje k frontám pomocí klíče účtu":::

Pokud chcete přepnout na používání účtu Azure AD, klikněte na odkaz zvýrazněný v imagi. Pokud máte příslušná oprávnění prostřednictvím rolí Azure, které jsou vám přiřazeny, budete moct pokračovat. Pokud ale nemáte správná oprávnění, zobrazí se chybová zpráva podobná následující:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Pokud účet Azure AD nepodporuje přístup, zobrazí se chyba.":::

Všimněte si, že se v seznamu nezobrazí žádné fronty, pokud váš účet Azure AD nemá oprávnění k jejich zobrazení. Klikněte na odkaz **Přepnout na přístup k klávesám** a znovu použijte přístupovou klávesu pro ověřování.

### <a name="authenticate-with-your-azure-ad-account"></a>Ověřování pomocí účtu Azure AD

Pokud ověřujete pomocí účtu Azure AD, zobrazí se na portálu **uživatelský účet Azure AD** , který jste zadali jako metodu ověřování:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Snímek obrazovky uživatele, který aktuálně přistupuje k frontám s účtem služby Azure AD":::

Chcete-li přepnout na používání přístupového klíče účtu, klikněte na odkaz zvýrazněný v obrázku. Pokud máte přístup k klíči účtu, budete moct pokračovat. Pokud ale nemáte přístup k klíči účtu, Azure Portal zobrazí chybovou zprávu.

Pokud nemáte přístup k klíčům účtu, nejsou fronty na portálu uvedené. Klikněte na odkaz **Přepnout na uživatelský účet Azure AD** , abyste mohli znovu použít účet Azure AD pro ověřování.

## <a name="next-steps"></a>Další kroky

- [Ověřování přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](../common/storage-auth-aad.md)
- [Přiřazení role Azure pro přístup k datům BLOB a Queue pomocí Azure Portal](../common/storage-auth-aad-rbac-portal.md)
- [Přiřazení role Azure pro přístup k datům objektů BLOB a front pomocí Azure CLI](../common/storage-auth-aad-rbac-cli.md)
- [Použití modulu Azure PowerShell k přiřazení role Azure pro přístup k datům objektů BLOB a front](../common/storage-auth-aad-rbac-powershell.md)
