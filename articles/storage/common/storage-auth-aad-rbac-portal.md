---
title: Přiřazení role RBAC pro přístup k datům pomocí Azure Portal
titleSuffix: Azure Storage
description: Naučte se používat Azure Portal k přiřazení oprávnění k objektu zabezpečení Azure Active Directory s řízením přístupu na základě role (RBAC). Azure Storage podporuje integrované a vlastní role RBAC pro ověřování prostřednictvím služby Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ec32990513d9199c4aaccf1bcfcbf76f348f877b
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867502"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Přiřazení role RBAC pro přístup k datům BLOB a Queue pomocí Azure Portal

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role (RBAC)](../../role-based-access-control/overview.md). Azure Storage definuje sadu předdefinovaných rolí RBAC, které zahrnují společné sady oprávnění používané pro přístup k datům objektů BLOB nebo front.

Když je role RBAC přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup může být vymezený na úrovni předplatného, skupiny prostředků, účtu úložiště nebo jednotlivého kontejneru nebo fronty. Objekt zabezpečení Azure AD může být uživatelem, skupinou, instančním objektem služby nebo [spravovanou identitou pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Tento článek popisuje, jak použít Azure Portal k přiřazení rolí RBAC. Azure Portal poskytuje jednoduché rozhraní pro přiřazování rolí RBAC a správu přístupu k prostředkům úložiště. Pomocí nástrojů příkazového řádku Azure nebo rozhraní API pro správu Azure Storage taky můžete přiřadit role RBAC pro prostředky BLOB a Queue. Další informace o rolích RBAC pro prostředky úložiště najdete v tématu [ověření přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC pro objekty BLOB a fronty

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Určení oboru prostředků 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Přiřazení rolí RBAC pomocí Azure Portal

Po určení vhodného oboru pro přiřazení role přejděte k tomuto prostředku v Azure Portal. Zobrazit nastavení **Access Control (IAM)** prostředku a podle těchto pokynů můžete spravovat přiřazení rolí:

1. Přiřaďte příslušnou Azure Storage roli RBAC pro udělení přístupu k objektu zabezpečení služby Azure AD.

1. Přiřaďte roli Azure Resource Manager [Reader](../../role-based-access-control/built-in-roles.md#reader) uživatelům, kteří potřebují přistupovat k kontejnerům nebo frontám prostřednictvím Azure Portal pomocí svých přihlašovacích údajů Azure AD. 

Následující části popisují všechny tyto kroky podrobněji.

> [!NOTE]
> Jako vlastník účtu služby Azure Storage nejsou automaticky přiřadit oprávnění pro přístup k datům. Je nutné explicitně přiřadit sami roli RBAC pro Azure Storage. Můžete ji přiřadit na úrovni předplatného, skupiny prostředků, účtu úložiště nebo kontejneru nebo fronty.
>
> Pokud má váš účet úložiště povolený hierarchický obor názvů, nemůžete přiřadit obor rolí k kontejneru nebo frontě.

### <a name="assign-a-built-in-rbac-role"></a>Přiřazení předdefinované role RBAC

Předtím, než přiřadíte roli objektu zabezpečení, nezapomeňte zvážit rozsah oprávnění, která udělujete. Projděte si část [určení rozsahu prostředků](#determine-resource-scope) a určete příslušný obor.

Procedura zobrazená tady přiřadí obor role na kontejner, ale stejný postup můžete použít k přiřazení rozsahu role k frontě: 

1. V [Azure Portal](https://portal.azure.com)přejdete na svůj účet úložiště a zobrazíte **Přehled** tohoto účtu.
1. V části služby vyberte **objekty blob**. 
1. Vyhledejte kontejner, pro který chcete přiřadit roli, a zobrazte nastavení kontejneru. 
1. Vyberte **řízení přístupu (IAM)** a zobrazte nastavení řízení přístupu pro kontejner. Vyberte kartu **přiřazení rolí** a zobrazte seznam přiřazení rolí.

    ![Snímek obrazovky zobrazující nastavení řízení přístupu k kontejneru](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Kliknutím na tlačítko **Přidat přiřazení role** přidejte novou roli.
1. V okně **Přidat přiřazení role** vyberte roli Azure Storage, kterou chcete přiřadit. Pak vyhledejte objekt zabezpečení, ke kterému chcete přiřadit tuto roli.

    ![Snímek obrazovky ukazující, jak přiřadit roli RBAC](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Klikněte na možnost **Uložit**. Identita, ke které jste přiřadili roli, se zobrazí v seznamu v rámci této role. Například následující obrázek ukazuje, že uživatel přidaný teď má oprávnění číst pro data v kontejneru s názvem *Sample-Container*.

    ![Snímek obrazovky zobrazující seznam uživatelů přiřazených k roli](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

Podle podobných kroků můžete přiřadit obor role k účtu úložiště, skupině prostředků nebo předplatnému.

### <a name="assign-the-reader-role-for-portal-access"></a>Přiřazení role čtenáře pro přístup k portálu

Když přiřadíte předdefinovanou nebo vlastní roli pro Azure Storage objektu zabezpečení, udělujete tomuto objektu zabezpečení oprávnění k provádění operací s daty v účtu úložiště. Předdefinované role **čtecího modulu dat** poskytují oprávnění ke čtení pro data v kontejneru nebo frontě, zatímco předdefinované role **přispěvatele dat** poskytují oprávnění ke čtení, zápisu a odstraňování kontejneru nebo fronty. Oprávnění jsou vymezena na zadaný prostředek.  
Pokud například přiřadíte roli **Přispěvatel dat objektů BLOB úložiště** k uživateli Marie na úrovni kontejneru s názvem **Sample-Container**, pak je Marie udělen přístup pro čtení, zápis a odstranění ke všem objektům blob v tomto kontejneru.

Pokud chce ale Marie zobrazit objekt BLOB v Azure Portal, neposkytne role **Přispěvatel dat objektu BLOB úložiště** dostatečná oprávnění k procházení portálu na objekt blob, aby ho bylo možné zobrazit. K procházení portálu a zobrazení dalších prostředků, které jsou tam viditelné, se vyžadují další oprávnění služby Azure AD.

Pokud uživatelé potřebují mít přístup k objektům blob v Azure Portal, přiřaďte jim další roli RBAC, roli [čtenáře](../../role-based-access-control/built-in-roles.md#reader) , těmto uživatelům, na úrovni účtu úložiště nebo výše. Role **Čtenář** je Azure Resource Manager role, která umožňuje uživatelům zobrazovat prostředky účtu úložiště, ale nemění je. Neposkytuje oprávnění ke čtení pro data v Azure Storage, ale pouze k prostředkům správy účtu.

Pomocí těchto kroků přiřaďte roli **Čtenář** , aby uživatel mohl získat přístup k objektům blob z Azure Portal. V tomto příkladu je přiřazení vymezené pro účet úložiště:

1. Na webu [Azure Portal](https://portal.azure.com) přejděte ke svému účtu úložiště.
1. Vyberte **řízení přístupu (IAM)** a zobrazte nastavení řízení přístupu pro účet úložiště. Vyberte kartu **přiřazení rolí** a zobrazte seznam přiřazení rolí.
1. V okně **Přidat přiřazení role** vyberte roli **Čtenář** . 
1. V poli **přiřadit přístup k** vyberte možnost **uživatel, skupina nebo instanční objekt služby Azure AD**.
1. Vyhledejte objekt zabezpečení, ke kterému chcete přiřadit roli.
1. Uložte přiřazení role.

Přiřazení role **čtenáře** je nezbytné jenom pro uživatele, kteří potřebují přístup k objektům blob nebo frontám pomocí Azure Portal.

> [!IMPORTANT]
> Verze Preview Průzkumník služby Storage v Azure Portal nepodporuje použití přihlašovacích údajů Azure AD k zobrazení a úpravě dat objektů BLOB nebo Queue. Průzkumník služby Storage v Azure Portal vždy používá klíče účtu pro přístup k datům. Pokud chcete použít Průzkumník služby Storage v Azure Portal, musíte mít přiřazenou roli, která zahrnuje **Microsoft. Storage/storageAccounts/klíče listkey/Action**.

## <a name="next-steps"></a>Další kroky

- Další informace o rolích RBAC pro prostředky úložiště najdete v tématu [ověření přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](storage-auth-aad.md). 
- Další informace o RBAC najdete v tématu [co je řízení přístupu na základě role (RBAC)?](../../role-based-access-control/overview.md).
- Další informace o tom, jak přiřadit a spravovat přiřazení rolí RBAC pomocí Azure PowerShell, Azure CLI nebo REST API, najdete v těchto článcích:
    - [Správa řízení přístupu na základě role (RBAC) pomocí Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Správa řízení přístupu na základě role (RBAC) pomocí Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Správa řízení přístupu na základě role (RBAC) pomocí REST API](../../role-based-access-control/role-assignments-rest.md)
- Informace o tom, jak autorizovat přístup k kontejnerům a frontám v rámci aplikací úložiště, najdete v tématu [použití Azure AD s Azure Storage aplikacemi](storage-auth-aad-app.md).
