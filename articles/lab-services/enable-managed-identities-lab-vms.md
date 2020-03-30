---
title: Povolení spravovaných identit na virtuálních počítačích testovacího prostředí v laboratořích Azure DevTest Labs
description: Tento článek ukazuje, jak vlastník testovacího prostředí může povolit uživatelem přiřazené spravované identity na virtuálních počítačích testovacího prostředí.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692664"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Povolení spravovaných identit přiřazených uživatelem na virtuálních počítačích testovacího prostředí v laboratořích Azure DevTest Labs
Jako vlastník testovacího prostředí můžete povolit spravované identity přiřazené uživateli na virtuálních počítačích testovacího prostředí (VM) v azure devtest labs.

Spravovanou identitu lze použít k ověření na libovolné službě, která podporuje ověřování služby Azure Active Directory (AD), včetně trezoru klíčů, bez předání pověření v kódu. Další informace o spravovaných identitách najdete v tématu [Co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Pomocí této funkce mohou uživatelé testovacího prostředí sdílet prostředky Azure, jako je Azure SQL Database, v kontextu testovacího prostředí. Ověřování prostředku je postaráno samotnou identitou. Po nakonfigurování bude s touto identitou povolen každý existující/nově vytvořený virtuální virtuální počítače testovacího prostředí. Uživatelé testovacího prostředí mají přístup k prostředkům po přihlášení ke svým počítačům.

> [!NOTE]
> Můžete přidat více uživatelů přiřazených spravovaných identit, které mají být povoleny na virtuálních počítačích testovacího prostředí.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
Pokud chcete přidat uživatele přiřazenou spravovanou identitu pro testovací virtuální uživatele, postupujte takto:

1. [Vytvoření spravované identity přiřazené uživateli v předplatném](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Přejděte na stránku **Konfigurace a zásady** pro testovací prostředí.
2. V levé nabídce vyberte **Identita (Náhled).**
3. Vyberte kartu **Virtuální počítač.**
4. Vyberte **Přidat,** chcete-li vybrat existující identitu z předem vyplněného rozevíracího seznamu. 

    ![Tlačítko Přidat identitu](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. Vyberte existující **identitu spravovanou uživatelem** ze seznamu vynechaných položek a vyberte **OK**. 

    ![Přidat identitu](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Použití rozhraní API

1.  Po vytvoření identity si poznamenejte ID prostředku identity. Mělo by to vypadat jako následující ukázka: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Spusťte metodu PUT HTTPS a přidejte do testovacího prostředí nový prostředek **ServiceRunner,** jak je znázorněno v následujícím příkladu. 

    Prostředek pro běhání služeb je prostředek proxy pro správu a řízení spravovaných identit v devTest Labs. Název runner služby může být libovolný platný název, ale doporučujeme použít název prostředku spravované identity.

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
Další informace o spravovaných identitách najdete v tématu [Co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md).







