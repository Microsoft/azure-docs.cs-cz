---
title: Použití spravovaných identit Azure k vytváření prostředí v DevTest Labs | Microsoft Docs
description: Naučte se používat spravované identity v Azure k nasazení prostředí v testovacím prostředí v Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: d1dd059f1a6f9ce96b27d4fe1f214978dfc06a8f
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815989"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Použití spravovaných identit Azure k nasazení prostředí v testovacím prostředí 
Jako vlastník testovacího prostředí můžete pomocí spravované identity nasadit prostředí v testovacím prostředí. Tato funkce je užitečná ve scénářích, kde prostředí obsahuje nebo obsahuje odkazy na prostředky Azure, jako jsou například trezory klíčů, Galerie sdílených imagí a sítě, které jsou pro skupinu prostředků tohoto prostředí externí. Umožňuje vytváření prostředí izolovaného prostoru, které není omezené na skupinu prostředků tohoto prostředí.

> [!NOTE]
> V současné době je v rámci testovacího prostředí podporována jediná uživatelsky přiřazená identita. 

## <a name="prerequisites"></a>Předpoklady
- [Umožňuje vytvořit, vypsat, odstranit nebo přiřadit roli k spravované identitě přiřazené uživatelem pomocí Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

## <a name="use-azure-portal"></a>Použití webu Azure Portal
V této části jako vlastník testovacího prostředí použijte Azure Portal k přidání identity spravované uživatelem do testovacího prostředí. 

1. Na stránce testovací prostředí vyberte **Konfigurace a zásady**. 
1. V části **Nastavení** vyberte **Identita** .
1. Chcete-li přidat identitu přiřazenou uživateli, vyberte možnost **Přidat** na panelu nástrojů. 
1. Vyberte **identitu** z předem vyplněných rozevíracího seznamu.
1. Vyberte **OK**.

    ![Přidat uživatelsky spravovanou identitu](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. V seznamu se zobrazí přidaná uživatelem spravovaná identita. 

    ![Uživatelsky spravovaná identita v seznamu](./media/use-managed-identities-environments/identity-in-list.png)

Po uložení testovací prostředí použije tuto identitu při nasazení všech laboratorních prostředí. K prostředku identity v Azure můžete přistupovat taky tak, že v seznamu vyberete identitu. 

Vlastník testovacího prostředí nemusí během nasazování prostředí provádět žádné zvláštní informace, protože identita přidaná do testovacího prostředí má oprávnění k externím prostředkům, ke kterým prostředí potřebuje mít přístup. 

Pokud chcete změnit uživatelsky spravovanou identitu přiřazenou k testovacímu prostředí, odeberte nejdřív identitu připojenou k testovacímu prostředí a potom do testovacího prostředí přidejte další. Pokud chcete odebrat identitu připojenou k testovacímu prostředí, vyberte **... (tři tečky)** a klikněte na tlačítko **Odebrat**. 

![Uživatelsky spravovaná identita v seznamu](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>Použití rozhraní API

1. Po vytvoření identity si poznamenejte ID prostředku této identity. Měl by vypadat jako v následující ukázce: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Proveďte metodu PUT protokolu HTTPS pro přidání nového prostředku @no__t 0 do testovacího prostředí podobně jako v následujícím příkladu. Prostředek spouštěče služeb je prostředek proxy serveru pro správu a řízení spravovaných identit v DevTest Labs. Název spouštěče služeb může být libovolný platný název, ale doporučujeme použít název spravovaného prostředku identity. 
 
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
    }
    ```
 
Po přidání identity přiřazené uživateli do testovacího prostředí ji služba Azure DevTest Labs použije při nasazení Azure Resource Manager prostředí. Pokud například potřebujete, aby šablona Správce prostředků měla přístup k obrázku Galerie externích sdílených imagí, ujistěte se, že identita, kterou jste přidali do testovacího prostředí, má minimální požadovaná oprávnění pro prostředek Galerie sdílených imagí. 
