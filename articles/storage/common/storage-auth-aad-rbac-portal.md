---
title: Přiřazení role Azure pro přístup k datům pomocí Azure Portal
titleSuffix: Azure Storage
description: Naučte se používat Azure Portal k přiřazení oprávnění k objektu zabezpečení Azure Active Directory pomocí řízení přístupu na základě role Azure (Azure RBAC). Azure Storage podporuje integrované a vlastní role Azure pro ověřování prostřednictvím služby Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 40003db9c3bd2c736f9cedd73b8b7a31a77f625f
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854576"
---
# <a name="use-the-azure-portal-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Přiřazení role Azure pro přístup k datům BLOB a Queue pomocí Azure Portal

Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage definuje sadu předdefinovaných rolí Azure, které zahrnují společné sady oprávnění používané pro přístup k datům objektů BLOB nebo front.

Když je role Azure přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup může být vymezený na úrovni předplatného, skupiny prostředků, účtu úložiště nebo jednotlivého kontejneru nebo fronty. Objekt zabezpečení Azure AD může být uživatelem, skupinou, instančním objektem služby nebo [spravovanou identitou pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Tento článek popisuje, jak pomocí Azure Portal přiřadit role Azure. Azure Portal poskytuje jednoduché rozhraní pro přiřazování rolí Azure a správu přístupu k prostředkům úložiště. Role Azure můžete také přiřadit k prostředkům BLOB a Queue pomocí nástrojů příkazového řádku Azure nebo rozhraní API pro správu Azure Storage. Další informace o rolích Azure pro prostředky úložiště najdete v tématu [ověření přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](storage-auth-aad.md).

## <a name="azure-roles-for-blobs-and-queues"></a>Role Azure pro objekty BLOB a fronty

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Určení oboru prostředků

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-azure-roles-using-the-azure-portal"></a>Přiřazení rolí Azure pomocí Azure Portal

Po určení vhodného oboru pro přiřazení role přejděte k tomuto prostředku v Azure Portal. Zobrazit nastavení **Access Control (IAM)** prostředku a podle těchto pokynů můžete spravovat přiřazení rolí:

1. Pokud chcete udělit přístup k objektu zabezpečení služby Azure AD, přiřaďte příslušné Azure Storage roli Azure.

1. Přiřaďte roli Azure Resource Manager [Reader](../../role-based-access-control/built-in-roles.md#reader) uživatelům, kteří potřebují přistupovat k kontejnerům nebo frontám prostřednictvím Azure Portal pomocí svých přihlašovacích údajů Azure AD.

Následující části popisují všechny tyto kroky podrobněji.

> [!NOTE]
> Když vytvoříte účet Azure Storage, nebudete automaticky přiřazovat oprávnění pro přístup k datům prostřednictvím služby Azure AD. Musíte explicitně přiřadit roli Azure pro Azure Storage. Můžete ji přiřadit na úrovni předplatného, skupiny prostředků, účtu úložiště nebo kontejneru nebo fronty.
>
> Předtím, než přiřadíte roli pro přístup k datům, budete mít přístup k datům v účtu úložiště prostřednictvím Azure Portal, protože Azure Portal může k přístupu k datům použít taky klíč účtu. Další informace najdete v tématu [Volba způsobu autorizace přístupu k datům objektu BLOB v Azure Portal](../blobs/authorize-data-operations-portal.md).

### <a name="assign-an-azure-built-in-role"></a>Přiřazení předdefinované role Azure

Předtím, než přiřadíte roli objektu zabezpečení, nezapomeňte zvážit rozsah oprávnění, která udělujete. Projděte si část [určení rozsahu prostředků](#determine-resource-scope) a určete příslušný obor.

Procedura zobrazená tady přiřadí obor role na kontejner, ale stejný postup můžete použít k přiřazení rozsahu role k frontě:

1. V [Azure Portal](https://portal.azure.com)přejdete na svůj účet úložiště a zobrazíte **Přehled** tohoto účtu.
1. V části služby vyberte **objekty blob**.
1. Vyhledejte kontejner, pro který chcete přiřadit roli, a zobrazte nastavení kontejneru.
1. Vyberte **řízení přístupu (IAM)** a zobrazte nastavení řízení přístupu pro kontejner. Vyberte kartu **přiřazení rolí** a zobrazte seznam přiřazení rolí.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/portal-access-control-container.png" alt-text="Snímek obrazovky zobrazující nastavení řízení přístupu k kontejneru":::

1. Kliknutím na tlačítko **Přidat přiřazení role** přidejte novou roli.
1. V okně **Přidat přiřazení role** vyberte roli Azure Storage, kterou chcete přiřadit. Pak vyhledejte objekt zabezpečení, ke kterému chcete přiřadit tuto roli.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/add-rbac-role.png" alt-text="Snímek obrazovky, který ukazuje, jak přiřadit roli Azure":::

1. Klikněte na **Uložit**. Identita, ke které jste přiřadili roli, se zobrazí v seznamu v rámci této role. Například následující obrázek ukazuje, že uživatel přidaný teď má oprávnění číst pro data v kontejneru s názvem *Sample-Container*.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/container-scoped-role.png" alt-text="Snímek obrazovky zobrazující seznam uživatelů přiřazených k roli":::

Podle podobných kroků můžete přiřadit obor role k účtu úložiště, skupině prostředků nebo předplatnému.

### <a name="assign-the-reader-role-for-portal-access"></a>Přiřazení role čtenáře pro přístup k portálu

Když přiřadíte předdefinovanou nebo vlastní roli pro Azure Storage objektu zabezpečení, udělujete tomuto objektu zabezpečení oprávnění k provádění operací s daty v účtu úložiště. Předdefinované role **čtecího modulu dat** poskytují oprávnění ke čtení pro data v kontejneru nebo frontě, zatímco předdefinované role **přispěvatele dat** poskytují oprávnění ke čtení, zápisu a odstraňování kontejneru nebo fronty. Oprávnění jsou vymezena na zadaný prostředek.  
Pokud například přiřadíte roli **Přispěvatel dat objektů BLOB úložiště** k uživateli Marie na úrovni kontejneru s názvem **Sample-Container**, pak je Marie udělen přístup pro čtení, zápis a odstranění ke všem objektům blob v tomto kontejneru.

Pokud chce ale Marie zobrazit objekt BLOB v Azure Portal, neposkytne role **Přispěvatel dat objektu BLOB úložiště** dostatečná oprávnění k procházení portálu na objekt blob, aby ho bylo možné zobrazit. K procházení portálu a zobrazení dalších prostředků, které jsou tam viditelné, se vyžadují další oprávnění služby Azure AD.

Pokud uživatelé potřebují mít přístup k objektům blob v Azure Portal, přiřaďte jim pro tyto uživatele další roli Azure, roli [čtenáře](../../role-based-access-control/built-in-roles.md#reader) , a to na úrovni účtu úložiště nebo výše. Role **Čtenář** je Azure Resource Manager role, která umožňuje uživatelům zobrazovat prostředky účtu úložiště, ale nemění je. Neposkytuje oprávnění ke čtení pro data v Azure Storage, ale pouze k prostředkům správy účtu.

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

- Další informace o rolích Azure pro prostředky úložiště najdete v tématu [ověření přístupu k objektům blob a frontám Azure pomocí Azure Active Directory](storage-auth-aad.md). 
- Další informace o službě Azure RBAC najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)?](../../role-based-access-control/overview.md).
- Další informace o tom, jak přiřadit a spravovat přiřazení rolí Azure pomocí Azure PowerShell, Azure CLI nebo REST API, najdete v těchto článcích:
    - [Přidání nebo odebrání přiřazení rolí Azure pomocí modulu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Přidání nebo odebrání přiřazení rolí v Azure pomocí REST API](../../role-based-access-control/role-assignments-rest.md)
- Informace o tom, jak autorizovat přístup k kontejnerům a frontám v rámci aplikací úložiště, najdete v tématu [použití Azure AD s Azure Storage aplikacemi](storage-auth-aad-app.md).
