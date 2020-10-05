---
title: Vyberte, jak autorizovat přístup k datům ve frontě v Azure Portal
titleSuffix: Azure Storage
description: Při přístupu k datům ve frontě pomocí Azure Portal portál vytvoří žádosti, které se Azure Storage v rámci pokrývání. Tyto požadavky na Azure Storage se dají ověřit a autorizovat pomocí účtu Azure AD nebo přístupového klíče účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/08/2020
ms.author: tamram
ms.reviewer: ozguns
ms.subservice: queues
ms.custom: contperfq1
ms.openlocfilehash: 2593f1b7ea4cfabe0243fe6f830d718896e68473
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715503"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>Vyberte, jak autorizovat přístup k datům ve frontě v Azure Portal

Při přístupu k datům ve frontě pomocí [Azure Portal](https://portal.azure.com)portál vytvoří žádosti, které se Azure Storage v rámci pokrývání. Požadavek na Azure Storage může být autorizovaný buď pomocí účtu Azure AD, nebo přístupového klíče účtu úložiště. Portál indikuje, kterou metodu používáte, a umožňuje přepínat mezi nimi, pokud máte příslušná oprávnění.  

## <a name="permissions-needed-to-access-queue-data"></a>Oprávnění potřebná pro přístup k datům ve frontě

V závislosti na tom, jak chcete autorizovat přístup k datům ve frontě v Azure Portal, budete potřebovat specifická oprávnění. Ve většině případů jsou tato oprávnění poskytována prostřednictvím řízení přístupu na základě role Azure (RBAC). Další informace o službě Azure RBAC najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>Použití přístupového klíče účtu

Pokud chcete získat přístup k datům front pomocí přístupového klíče účtu, musíte mít přiřazenou roli Azure, která zahrnuje akci Azure RBAC **Microsoft. Storage/storageAccounts/klíče listkey/Action**. Tato role Azure může být integrovaná nebo vlastní role. Předdefinované role, které podporují **Microsoft. Storage/storageAccounts/klíče listkey/Action** , zahrnují:

- Role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager
- Role [přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- Role [Přispěvatel účtu úložiště](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Když se pokusíte o přístup k datům ve frontě v Azure Portal, portál nejprve zkontroluje, jestli vám byla přiřazena role s **Microsoft. Storage/storageAccounts/klíče listkey/Action**. Pokud jste k této akci přiřadili roli, portál použije klíč účtu pro přístup k datům ve frontě. Pokud jste k této akci nepřiřadili roli, pokusí se portál získat přístup k datům pomocí účtu Azure AD.

> [!NOTE]
> Správci služby pro klasický odběr role správce a spolusprávce zahrnují ekvivalent role Azure Resource Manager [Owner](../../role-based-access-control/built-in-roles.md#owner) . Role **vlastníka** zahrnuje všechny akce, včetně **Microsoft. Storage/storageAccounts/klíče listkey/Action**, takže uživatel s jednou z těchto rolí pro správu může také přistupovat k datům z fronty pomocí klíče účtu. Další informace najdete v tématech [role správců klasického předplatného, role Azure a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Použití účtu Azure AD

Pokud chcete získat přístup k datům ve frontě z Azure Portal pomocí účtu Azure AD, musí být pro vás oba následující příkazy pravdivé:

- Je vám přiřazena minimálně role [čtenář](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager, která je vymezená na úroveň účtu úložiště nebo vyšší. Role **Čtenář** uděluje nejvyšší omezení oprávnění, ale také je přijatelná jiná role Azure Resource Manager, která uděluje přístup k prostředkům správy účtu úložiště.
- Přiřadili jste buď integrovanou, nebo vlastní roli, která poskytuje přístup k datům ve frontě.

Přiřazení role **Čtenář** nebo jiné Azure Resource Manager přiřazení role je nezbytné, aby uživatel mohl zobrazit a procházet prostředky správy účtu úložiště v Azure Portal. Role Azure, které udělují přístup k datům ve frontě, neudělují přístup k prostředkům správy účtu úložiště. Pro přístup k datům ve frontě na portálu potřebuje uživatel oprávnění k procházení prostředků účtu úložiště. Další informace o tomto požadavku najdete v tématu [přiřazení role čtenáře pro přístup k portálu](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Mezi předdefinované role podporující přístup k datům ve frontě patří:

- [Přispěvatel dat fronty úložiště](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): oprávnění ke čtení, zápisu a odstraňování front.
- [Čtecí modul dat fronty úložiště](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): oprávnění jen pro čtení pro fronty.

Vlastní role mohou podporovat různé kombinace stejných oprávnění poskytovaných integrovanými rolemi. Další informace o vytváření vlastních rolí Azure najdete v tématu [vlastní role Azure](../../role-based-access-control/custom-roles.md) a [pochopení definic rolí pro prostředky Azure](../../role-based-access-control/role-definitions.md).

Výpis front s rolí správce předplatného s klasickou sadou není podporován. K vypsání front se musí uživatel přiřadit k rolím **čtenář** Azure Resource Manager, roli **čtečky dat fronty úložiště** nebo k roli **Přispěvatel dat fronty úložiště** .

> [!IMPORTANT]
> Verze Preview Průzkumník služby Storage v Azure Portal nepodporuje použití přihlašovacích údajů Azure AD k zobrazení a úpravě dat fronty. Průzkumník služby Storage v Azure Portal vždy používá klíče účtu pro přístup k datům. Pokud chcete použít Průzkumník služby Storage v Azure Portal, musíte mít přiřazenou roli, která zahrnuje **Microsoft. Storage/storageAccounts/klíče listkey/Action**.

## <a name="navigate-to-queues-in-the-azure-portal"></a>Přejděte do fronty v Azure Portal

Pokud chcete zobrazit data ve frontě na portálu, přejděte na **Přehled** svého účtu úložiště a klikněte na odkazy pro **fronty**. Případně můžete přejít na oddíly **Služba front** v nabídce.

:::image type="content" source="media/authorize-queue-access-portal/queue-access-portal.png" alt-text="Snímek obrazovky ukazující, jak přejít na data ve frontě v Azure Portal":::

## <a name="determine-the-current-authentication-method"></a>Určení aktuální metody ověřování

Když přejdete do fronty, Azure Portal určuje, jestli aktuálně používáte přístupový klíč účtu nebo účet Azure AD k ověřování.

### <a name="authenticate-with-the-account-access-key"></a>Ověřování pomocí přístupového klíče účtu

Pokud ověřujete pomocí přístupového klíče účtu, zobrazí se na portálu **přístupová klávesa** , která se zadala jako metoda ověřování:

:::image type="content" source="media/authorize-queue-access-portal/auth-method-access-key.png" alt-text="Snímek obrazovky ukazující, jak přejít na data ve frontě v Azure Portal":::

Pokud chcete přepnout na používání účtu Azure AD, klikněte na odkaz zvýrazněný v imagi. Pokud máte příslušná oprávnění prostřednictvím rolí Azure, které jsou vám přiřazeny, budete moct pokračovat. Pokud ale nemáte správná oprávnění, zobrazí se chybová zpráva podobná následující:

:::image type="content" source="media/authorize-queue-access-portal/auth-error-azure-ad.png" alt-text="Snímek obrazovky ukazující, jak přejít na data ve frontě v Azure Portal":::

Všimněte si, že se v seznamu nezobrazí žádné fronty, pokud váš účet Azure AD nemá oprávnění k jejich zobrazení. Klikněte na odkaz **Přepnout na přístup k klávesám** a znovu použijte přístupovou klávesu pro ověřování.

### <a name="authenticate-with-your-azure-ad-account"></a>Ověřování pomocí účtu Azure AD

Pokud ověřujete pomocí účtu Azure AD, zobrazí se na portálu **uživatelský účet Azure AD** , který jste zadali jako metodu ověřování:

:::image type="content" source="media/authorize-queue-access-portal/auth-method-azure-ad.png" alt-text="Snímek obrazovky ukazující, jak přejít na data ve frontě v Azure Portal":::

Chcete-li přepnout na používání přístupového klíče účtu, klikněte na odkaz zvýrazněný v obrázku. Pokud máte přístup k klíči účtu, budete moct pokračovat. Pokud ale nemáte přístup k klíči účtu, Azure Portal zobrazí chybovou zprávu.

Pokud nemáte přístup k klíčům účtu, nejsou fronty na portálu uvedené. Klikněte na odkaz **Přepnout na uživatelský účet Azure AD** , abyste mohli znovu použít účet Azure AD pro ověřování.

## <a name="next-steps"></a>Další kroky

- [Ověřování přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](../common/storage-auth-aad.md)
- [Přiřazení role Azure pro přístup k datům BLOB a Queue pomocí Azure Portal](../common/storage-auth-aad-rbac-portal.md)
- [Přiřazení role Azure pro přístup k datům objektů BLOB a front pomocí Azure CLI](../common/storage-auth-aad-rbac-cli.md)
- [Použití modulu Azure PowerShell k přiřazení role Azure pro přístup k datům objektů BLOB a front](../common/storage-auth-aad-rbac-powershell.md)
