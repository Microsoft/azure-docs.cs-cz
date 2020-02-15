---
title: Přidání instančního objektu do role správce Azure Analysis Services | Microsoft Docs
description: Naučte se přidat instanční objekt služby Automation do role správce serveru Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b75740e9bff714ad68c93bea7e387e60da2f1c59
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212504"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Přidání instančního objektu k roli správce serveru 

 Aby bylo možné automatizovat bezobslužné úlohy PowerShellu, instanční objekt musí mít oprávnění **Správce serveru** na spravovaném serveru Analysis Services. Tento článek popisuje, jak přidat instanční objekt do role Správci serveru na serveru Azure jako. Můžete to provést pomocí SQL Server Management Studio nebo šablony Správce prostředků.
 
> [!NOTE]
> U operací se serverem, které používají rutiny Azure PowerShell, musí instanční objekt taky patřit do role **vlastníka** pro prostředek v [Access Control na základě rolí Azure (RBAC)](../role-based-access-control/overview.md). 

## <a name="before-you-begin"></a>Před zahájením
Před dokončením této úlohy musíte mít v Azure Active Directory zaregistrovaný instanční objekt.

[Vytvoření instančního objektu – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Vytvoření instančního objektu – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Používání aplikace SQL Server Management Studio

Správce serveru můžete nakonfigurovat pomocí SQL Server Management Studio (SSMS). K dokončení této úlohy musíte mít oprávnění [Správce serveru](analysis-services-server-admins.md) na serveru Azure as. 

1. V SSMS se připojte k Azure AS serveru.
2. V **nastavení vlastnosti serveru** > **zabezpečení**klikněte na **Přidat**.
3. V nabídce **Vyberte uživatele nebo skupinu**vyhledejte registrovanou aplikaci podle názvu, vyberte a pak klikněte na **Přidat**.

    ![Hledat hlavní účet služby](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Ověřte ID účtu zabezpečení služby a pak klikněte na **OK**.
    
    ![Hledat hlavní účet služby](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Použití šablony Správce prostředků

Správce serveru můžete také nakonfigurovat tak, že nasadíte Analysis Services Server pomocí šablony Azure Resource Manager. Identita, která spouští nasazení, musí patřit do role **přispěvatele** pro prostředek v [Access Control na základě rolí Azure (RBAC)](../role-based-access-control/overview.md).

> [!IMPORTANT]
> Instanční objekt je potřeba přidat pomocí `app:{service-principal-client-id}@{azure-ad-tenant-id}`formátu.

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

## <a name="related-information"></a>Související informace

* [Stáhnout SQL Server modul PowerShellu](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Stáhnout SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


