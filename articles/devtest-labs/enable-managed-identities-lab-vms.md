---
title: Povolení spravovaných identit na virtuálních počítačích v testovacím prostředí v Azure DevTest Labs
description: Tento článek ukazuje, jak vlastník testovacího prostředí může povolit uživatelsky přiřazené identity na virtuálních počítačích testovacího prostředí.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4bf2900acebaeecd5cbc4cb65635aee6de87dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88717624"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Povolte uživatelem přiřazené spravované identity na virtuálních počítačích testovacího prostředí v Azure DevTest Labs
Jako vlastník testovacího prostředí můžete povolit uživatelsky přiřazené spravované identity na virtuálních počítačích testovacího prostředí v Azure DevTest Labs.

Spravovanou identitu lze použít k ověření u jakékoli služby, která podporuje ověřování Azure Active Directory (AD), včetně Key Vault, bez předání jakýchkoli přihlašovacích údajů v kódu. Další informace o spravovaných identitách najdete v tématu [co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Díky této funkci můžou uživatelé testovacího prostředí sdílet prostředky Azure, například Azure SQL Database v kontextu testovacího prostředí. Ověřování prostředku se stará o identitu, kterou sám zabere. Po nakonfigurování se pro tuto identitu povolí každý existující/nově vytvořený testovací virtuální počítač. Uživatelé testovacího prostředí můžou získat přístup k prostředkům po přihlášení ke svým počítačům.

> [!NOTE]
> Pro virtuální počítače v testovacím prostředí můžete přidat více spravovaných identit přiřazených uživateli.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
Pokud chcete přidat spravovanou identitu přiřazenou uživatelem pro virtuální počítače v testovacím prostředí, použijte následující postup:

1. [Vytvoření spravované identity přiřazené uživatelem v předplatném](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Přejděte na stránku **Konfigurace a zásady** pro testovací prostředí.
1. V nabídce vlevo vyberte **identita (Preview)** .
1. Vyberte kartu **virtuální počítač** .
1. Vyberte možnost **Přidat** a vyberte existující identitu z předem vyplněných rozevíracích seznamů. 

    > [!div class="mx-imgBorder"]
    > ![Tlačítko Přidat identitu](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. Z rozevíracího seznamu vyberte existující **identitu spravovanou uživatelem** a vyberte **OK**. 

    > [!div class="mx-imgBorder"]
    > ![Přidat identitu](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Použití rozhraní API

1.  Po vytvoření identity si poznamenejte ID prostředku identity. Měl by vypadat jako v následující ukázce: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Spusťte metodu PUT protokolu HTTPS pro přidání nového prostředku **ServiceRunner** do testovacího prostředí, jak je znázorněno v následujícím příkladu. 

    Prostředek spouštěče služeb je prostředek proxy serveru pro správu a řízení spravovaných identit v DevTest Labs. Název spouštěče služeb může být libovolný platný název, doporučujeme ale použít název spravovaného prostředku identity.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Další kroky
Další informace o spravovaných identitách najdete v tématu [co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md).







