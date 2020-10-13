---
title: Použití spravovaných identit Azure k vytváření prostředí v DevTest Labs | Microsoft Docs
description: Naučte se používat spravované identity v Azure k nasazení prostředí v testovacím prostředí v Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e5bac4210afee6db1c7617dac1cd6d2ff9149439
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88718968"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Použití spravovaných identit Azure k nasazení prostředí v testovacím prostředí 

Jako vlastník testovacího prostředí můžete pomocí spravované identity nasadit prostředí v testovacím prostředí. Tato funkce je užitečná ve scénářích, kde prostředí obsahuje nebo obsahuje odkazy na prostředky Azure, jako jsou například trezory klíčů, Galerie sdílených imagí a sítě, které jsou pro skupinu prostředků tohoto prostředí externí. Umožňuje vytváření prostředí izolovaného prostoru, které není omezené na skupinu prostředků tohoto prostředí.

> [!NOTE]
> V současné době je v rámci testovacího prostředí podporována jediná uživatelsky přiřazená identita. 

## <a name="prerequisites"></a>Požadavky

- [Umožňuje vytvořit, vypsat, odstranit nebo přiřadit roli k spravované identitě přiřazené uživatelem pomocí Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    Ujistěte se, že se vaše spravovaná identita vytvořila ve stejné oblasti a předplatném jako vaše testovací prostředí. Spravovaná identita nemusí být ve stejné skupině prostředků.

## <a name="use-azure-portal"></a>Použití webu Azure Portal

V této části jako vlastník testovacího prostředí použijte Azure Portal k přidání identity spravované uživatelem do testovacího prostředí. 

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Vyhledejte **DevTest Labs**.
1. V seznamu cvičení vyberte testovací prostředí, které chcete.
1. Vyberte **Konfigurace a zásady**  ->  **identita (Preview)**. 
1. Chcete-li přidat identitu přiřazenou uživatelem, vyberte kartu **přiřazeno uživateli** .
1. Stiskněte tlačítko **Přidat** .
1. Vyberte stávajícího uživatele, kterého jste vytvořili, nebo k němu máte přístup z rozevírací nabídky.
 
    ![Přidat uživatelsky spravovanou identitu](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. V horní části stránky klikněte na **Uložit** .

    Po uložení testovací prostředí použije tuto identitu při nasazení všech laboratorních prostředí. K prostředku identity v Azure můžete přistupovat taky tak, že v seznamu vyberete identitu. 

Vlastník testovacího prostředí nemusí během nasazování prostředí provádět žádné zvláštní informace, protože identita přidaná do testovacího prostředí má oprávnění k externím prostředkům, ke kterým prostředí potřebuje mít přístup. 

Pokud chcete změnit uživatelsky spravovanou identitu přiřazenou k testovacímu prostředí, odeberte nejdřív identitu připojenou k testovacímu prostředí a potom do testovacího prostředí přidejte další. Pokud chcete odebrat identitu připojenou k testovacímu prostředí, vyberte **... (tři tečky)** a klikněte na tlačítko **Odebrat**. 

## <a name="use-api"></a>Použití rozhraní API

1. Po vytvoření identity si poznamenejte ID prostředku této identity. Měl by vypadat jako v následující ukázce: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Proveďte metodu PUT protokolu HTTPS pro přidání nového `ServiceRunner` prostředku do testovacího prostředí, podobně jako v následujícím příkladu. Prostředek spouštěče služeb je prostředek proxy serveru pro správu a řízení spravovaných identit v DevTest Labs. Název spouštěče služeb může být libovolný platný název, ale doporučujeme použít název spravovaného prostředku identity. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Tady je příklad: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Po přidání identity přiřazené uživateli do testovacího prostředí ji služba Azure DevTest Labs použije při nasazení Azure Resource Manager prostředí. Pokud například potřebujete, aby šablona Správce prostředků měla přístup k obrázku Galerie externích sdílených imagí, ujistěte se, že identita, kterou jste přidali do testovacího prostředí, má minimální požadovaná oprávnění pro prostředek Galerie sdílených imagí. 
