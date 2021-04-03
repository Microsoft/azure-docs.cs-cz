---
title: Přidání instančního objektu do role správce Azure Analysis Services | Microsoft Docs
description: Naučte se přidat instanční objekt služby Automation do role správce serveru Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b1d5f8ec628245756c53e4dfeeecdeb3a4bebc2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100724"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Přidání instančního objektu k roli správce serveru 

 Aby bylo možné automatizovat bezobslužné úlohy PowerShellu, instanční objekt musí mít oprávnění **Správce serveru** na spravovaném serveru Analysis Services. Tento článek popisuje, jak přidat instanční objekt do role Správci serveru na serveru Azure jako. Můžete to provést pomocí SQL Server Management Studio nebo šablony Správce prostředků. 

> [!NOTE]
> Instanční objekty musí být přidané přímo do role správce serveru. Přidání instančního objektu do skupiny zabezpečení a následné přidání této skupiny zabezpečení do role správce serveru není podporováno. 

## <a name="before-you-begin"></a>Než začnete
Před dokončením této úlohy musíte mít v Azure Active Directory zaregistrovaný instanční objekt.

[Vytvoření instančního objektu – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Vytvoření instančního objektu – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Používání SQL Server Management Studia

Správce serveru můžete nakonfigurovat pomocí SQL Server Management Studio (SSMS). K dokončení této úlohy musíte mít oprávnění [Správce serveru](analysis-services-server-admins.md) na serveru Azure as. 

1. V SSMS se připojte k Azure AS serveru.
2. V nabídce zabezpečení **vlastností serveru**  >  klikněte na **Přidat**.
3. V nabídce **Vyberte uživatele nebo skupinu** vyhledejte registrovanou aplikaci podle názvu, vyberte a pak klikněte na **Přidat**.

    ![Hledat hlavní účet služby](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Ověřte ID účtu zabezpečení služby a pak klikněte na **OK**.
    
    ![Snímek obrazovky, který zobrazuje ID hlavního účtu služby a zvýrazní tlačítko OK.](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Použití šablony Resource Manageru

Správce serveru můžete také nakonfigurovat tak, že nasadíte Analysis Services Server pomocí šablony Azure Resource Manager. Identita, která spouští nasazení, musí patřit do role **přispěvatele** pro prostředek v [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md).

> [!IMPORTANT]
> Instanční objekt se musí přidat pomocí formátu `app:{service-principal-client-id}@{azure-ad-tenant-id}` .

Následující Správce prostředků šablona nasadí Server Analysis Services se zadaným objektem služby přidaným do role správce Analysis Services:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>Použití spravovaných identit

Spravovaná identita se dá přidat taky do seznamu Analysis Services Admins. Můžete mít například [aplikaci logiky se spravovanou identitou přiřazenou systémem](../logic-apps/create-managed-service-identity.md)a chcete dát IT možnost spravovat Server Analysis Services.

Ve většině částí Azure Portal a rozhraní API se spravované identity identifikují pomocí jejich ID instančního objektu. Analysis Services ale vyžaduje, aby se identifikovali pomocí svého ID klienta. Pokud chcete získat ID klienta pro instanční objekt, můžete použít rozhraní příkazového řádku Azure:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Případně můžete použít PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Pak můžete pomocí tohoto ID klienta v kombinaci s ID tenanta přidat spravovanou identitu do seznamu Analysis Services Admins, jak je popsáno výše.

## <a name="related-information"></a>Související informace

* [Stáhnout SQL Server modul PowerShellu](/sql/ssms/download-sql-server-ps-module)   
* [Stáhnout SSMS](/sql/ssms/download-sql-server-management-studio-ssms)