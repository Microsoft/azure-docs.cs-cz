---
title: Přiřazení role RBAC pro přístup k datům pomocí portálu Azure
titleSuffix: Azure Storage
description: Zjistěte, jak pomocí portálu Azure přiřadit oprávnění k objektu zabezpečení služby Azure Active Directory pomocí řízení přístupu na základě rolí (RBAC). Azure Storage podporuje integrované a vlastní role RBAC pro ověřování prostřednictvím Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ec32990513d9199c4aaccf1bcfcbf76f348f877b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75867502"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Přiřazení role RBAC pro přístup k datům objektů blob a fronty pomocí portálu Azure

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě rolí (RBAC).](../../role-based-access-control/overview.md) Azure Storage definuje sadu předdefinovaných rolí RBAC, které zahrnují společné sady oprávnění používaných pro přístup k datům objektů blob nebo fronty.

Když je role RBAC přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup může být vymezen na úroveň předplatného, skupiny prostředků, účtu úložiště nebo jednotlivého kontejneru nebo fronty. Zaregistrovaný objekt zabezpečení Azure AD může být uživatel, skupina, instanční objekt aplikační služby nebo [spravovaná identita pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Tento článek popisuje, jak používat portál Azure k přiřazení rolí RBAC. Portál Azure poskytuje jednoduché rozhraní pro přiřazování rolí RBAC a správu přístupu k prostředkům úložiště. Můžete také přiřadit role RBAC pro prostředky objektů blob a fronty pomocí nástrojů příkazového řádku Azure nebo rozhraní API pro správu úložiště Azure. Další informace o rolích RBAC pro prostředky úložiště najdete [v tématu Ověřování přístupu k objektům BLOB azure a frontám pomocí služby Azure Active Directory](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC pro objekty BLOB a fronty

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Určit obor prostředků 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Přiřazení rolí RBAC pomocí portálu Azure

Po určení vhodného oboru pro přiřazení role přejděte na tento prostředek na webu Azure Portal. Zobrazte nastavení **řízení přístupu (IAM)** pro prostředek a podle následujících pokynů spravujte přiřazení rolí:

1. Přiřaďte příslušnou roli RBAC úložiště Azure, abyste udělovali přístup k objektu zabezpečení Azure AD.

1. Přiřaďte roli Azure Resource Manager [Reader](../../role-based-access-control/built-in-roles.md#reader) uživatelům, kteří potřebují přístup k kontejnerům nebo frontám prostřednictvím portálu Azure pomocí jejich přihlašovacích údajů Azure AD. 

Následující části popisují každý z těchto kroků podrobněji.

> [!NOTE]
> Jako vlastník vašeho účtu Azure Storage vám nejsou automaticky přiřazena oprávnění k přístupu k datům. Musíte explicitně přiřadit sami roli RBAC pro Azure Storage. Můžete ji přiřadit na úrovni předplatného, skupiny prostředků, účtu úložiště nebo kontejneru nebo fronty.
>
> Pokud je v účtu úložiště povolen hierarchický obor názvů, nelze kontejneru nebo frontě přiřadit určitou roli s vymezeným oborem.

### <a name="assign-a-built-in-rbac-role"></a>Přiřazení předdefinované role RBAC

Před přiřazením role k objektu zabezpečení zvažte rozsah oprávnění, která udělujete. Zkontrolujte [určit obor prostředků](#determine-resource-scope) části rozhodnout příslušný obor.

Zde uvedený postup přiřazuje kontejneru určitou roli, ale stejným způsobem můžete přiřadit roli vymezenou do fronty: 

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na svůj účet úložiště a zobrazte **přehled** účtu.
1. V části Služby vyberte **Objekty blob**. 
1. Vyhledejte kontejner, ke kterému chcete přiřadit roli, a zobrazte nastavení kontejneru. 
1. Vyberte **řízení přístupu (IAM),** chcete-li zobrazit nastavení řízení přístupu pro kontejner. Výběrem karty **Přiřazení rolí** zobrazíte seznam přiřazení rolí.

    ![Snímek obrazovky s nastavením řízení přístupu k kontejneru](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Kliknutím na tlačítko **Přidat přiřazení role** přidejte novou roli.
1. V okně **Přidat přiřazení role** vyberte roli Úložiště Azure, kterou chcete přiřadit. Potom vyhledejte objekt zabezpečení, ke kterému chcete tuto roli přiřadit.

    ![Snímek obrazovky znázorňující přiřazení role RBAC](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Klikněte na **Uložit**. Identita, které jste roli přiřadili, se zobrazí v seznamu v této roli. Například následující obrázek ukazuje, že uživatel přidal nyní má oprávnění ke čtení dat v kontejneru s názvem *ukázkový kontejner*.

    ![Snímek obrazovky se seznamem uživatelů přiřazených k roli](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

Můžete postupovat podle podobných kroků a přiřadit roli vymezenou účtu úložiště, skupině prostředků nebo předplatnému.

### <a name="assign-the-reader-role-for-portal-access"></a>Přiřazení role Čtečka pro přístup k portálu

Když přiřadíte integrovanou nebo vlastní roli pro Azure Storage k objektu zabezpečení, udělujete oprávnění tomuto objektu zabezpečení k provádění operací s daty ve vašem účtu úložiště. Předdefinované role **čtečky dat** poskytují oprávnění ke čtení dat v kontejneru nebo frontě, zatímco předdefinované role **přispěvatele dat** poskytují oprávnění ke čtení, zápisu a odstranění kontejneru nebo fronty. Oprávnění jsou vymezena na zadaný prostředek.  
Pokud například přiřadíte roli **přispěvatele dat objektů blob úložiště** uživateli Marie na úrovni kontejneru s názvem **ukázkový kontejner**, bude Mary udělena přístup ke čtení, zápisu a odstranění přístupu ke všem objektům BLOB v tomto kontejneru.

Pokud však Mary chce zobrazit objekt blob na webu Azure Portal, pak role **přispěvatele dat objektů blob úložiště** sama o sobě neposkytne dostatečná oprávnění k procházení portálu do objektu blob, aby jej bylo možné zobrazit. Další oprávnění Azure AD jsou nutné procházet portála a zobrazit další prostředky, které jsou viditelné tam.

Pokud vaši uživatelé potřebují mít přístup k objektům BLOB na webu Azure Portal, pak jim přiřaďte další roli RBAC, roli [Čtečka,](../../role-based-access-control/built-in-roles.md#reader) těmto uživatelům na úrovni účtu úložiště nebo vyšší. Role **Čtečka** je role Správce prostředků Azure, která umožňuje uživatelům zobrazit prostředky účtu úložiště, ale ne upravovat je. Neposkytuje oprávnění ke čtení dat ve službě Azure Storage, ale pouze pro prostředky pro správu účtu.

Následujícím postupem přiřaďte roli **Čtečka,** aby měl uživatel přístup k objektům BLOB z portálu Azure. V tomto příkladu je přiřazení vymezeno na účet úložiště:

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na svůj účet úložiště.
1. Vyberte **řízení přístupu (IAM),** chcete-li zobrazit nastavení řízení přístupu pro účet úložiště. Výběrem karty **Přiřazení rolí** zobrazíte seznam přiřazení rolí.
1. V okně **Přidat přiřazení role** vyberte roli **Čtečka.** 
1. V poli **Přiřadit přístup k** vyberte **uživatele, skupinu nebo instanční objekt Azure AD**.
1. Vyhledejte objekt zabezpečení, ke kterému chcete roli přiřadit.
1. Uložte přiřazení role.

Přiřazení role **čtečky** je nezbytné jenom pro uživatele, kteří potřebují přístup k objektům BLOB nebo frontám pomocí portálu Azure.

> [!IMPORTANT]
> Verze preview Průzkumníka úložiště na webu Azure Portal nepodporuje použití přihlašovacích údajů Azure AD k zobrazení a úpravě dat objektu blob nebo fronty. Průzkumník úložiště na webu Azure Portal vždy používá klíče účtu pro přístup k datům. Pokud chcete na webu Azure Portal používat Průzkumníka úložiště, musíte vám přiřadit roli, která zahrnuje **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="next-steps"></a>Další kroky

- Další informace o rolích RBAC pro prostředky úložiště najdete [v tématu Ověřování přístupu k objektům BLOB azure a frontám pomocí služby Azure Active Directory](storage-auth-aad.md). 
- Další informace o RBAC, naleznete v [tématu Co je řízení přístupu na základě rolí (RBAC)?](../../role-based-access-control/overview.md).
- Informace o tom, jak přiřazovat a spravovat přiřazení rolí RBAC pomocí Azure PowerShellu, rozhraní API Azure nebo rozhraní REST API, najdete v těchto článcích:
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí Azure PowerShellu](../../role-based-access-control/role-assignments-powershell.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí azure cli](../../role-based-access-control/role-assignments-cli.md)
    - [Správa řízení přístupu na základě rolí (RBAC) pomocí rozhraní REST API](../../role-based-access-control/role-assignments-rest.md)
- Informace o autorizaci přístupu k kontejnerům a frontám z vašich aplikací úložiště najdete v [tématu Použití Azure AD s aplikacemi Azure Storage](storage-auth-aad-app.md).
