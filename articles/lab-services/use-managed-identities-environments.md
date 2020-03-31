---
title: Použití spravovaných identit Azure k vytváření prostředí v laboratořích DevTest | Dokumenty společnosti Microsoft
description: Zjistěte, jak používat spravované identity v Azure k nasazení prostředí v testovacím prostředí v laboratořích Azure DevTest Labs.
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
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931159"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Použití spravovaných identit Azure k nasazení prostředí v testovacím prostředí 
Jako vlastník testovacího prostředí můžete použít spravovanou identitu k nasazení prostředí v testovacím prostředí. Tato funkce je užitečná ve scénářích, kde prostředí obsahuje nebo obsahuje odkazy na prostředky Azure, jako jsou trezory klíčů, sdílené galerie obrázků a sítě, které jsou mimo skupinu prostředků prostředí. Umožňuje vytváření prostředí izolovaného prostoru, které nejsou omezeny na skupinu prostředků tohoto prostředí.

> [!NOTE]
> V současné době je podporována identita přiřazená jednomu uživateli na testovací prostředí. 

## <a name="prerequisites"></a>Požadavky
- [Vytvořte, seznamte, odstraňte nebo přiřaďte roli spravované identitě přiřazené uživateli pomocí webu Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

## <a name="use-azure-portal"></a>Použití webu Azure Portal
V této části můžete jako vlastník testovacího prostředí použít portál Azure k přidání identity spravované uživatelem do testovacího prostředí. 

1. Na stránce testovacího prostředí vyberte **Konfigurace a zásady**. 
1. V části **Nastavení** vyberte **Identita.**
1. Pokud chcete přidat identitu přiřazenou uživateli, vyberte **Přidat** na panelu nástrojů. 
1. Vyberte **identitu** z předem vyplněného rozevíracího seznamu.
1. Vyberte **OK**.

    ![Přidání identity spravované uživatelem](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. V seznamu se zobrazí přidaná identita spravovaná uživatelem. 

    ![Identita spravovaná uživatelem v seznamu](./media/use-managed-identities-environments/identity-in-list.png)

Po uložení bude testovací prostředí používat tuto identitu při nasazování všech testovacích prostředí. K prostředku identity v Azure můžete také získat přístup výběrem identity ze seznamu. 

Vlastník testovacího prostředí nemusí dělat nic zvláštního při nasazování prostředí tak dlouho, dokud identita přidaná do testovacího prostředí má oprávnění k externím prostředkům, ke kterým prostředí potřebuje přístup. 

Chcete-li změnit identitu spravovanou uživatelem přiřazenou k testovacímu prostředí, nejprve odeberte identitu připojenou k testovacímu prostředí a pak přidejte další do testovacího prostředí. Chcete-li odebrat identitu připojenou k testovacímu prostředí, vyberte **... (tři tečky)** a klepněte na **tlačítko Odebrat**. 

![Identita spravovaná uživatelem v seznamu](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>Použití rozhraní API

1. Po vytvoření identity si poznamenejte ID prostředku této identity. Mělo by to vypadat jako následující ukázka: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Proveďte metodu PUT Https `ServiceRunner` a přidejte nový prostředek do testovacího prostředí podobně jako v následujícím příkladu. Prostředek pro běhání služeb je prostředek proxy pro správu a řízení spravovaných identit v devTest Labs. Název servisního běžce může být libovolný platný název, ale doporučujeme použít název prostředku spravované identity. 
 
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
 
Jakmile je uživatel přiřazená identita přidána do testovacího prostředí, služba Azure DevTest Labs ji použije při nasazování prostředí Azure Resource Manageru. Pokud například potřebujete šablonu Správce prostředků pro přístup k externí sdílené bitové kopii galerie obrázků, ujistěte se, že identita, kterou jste přidali do testovacího prostředí, má minimální požadovaná oprávnění pro prostředek sdílené galerie obrázků. 
