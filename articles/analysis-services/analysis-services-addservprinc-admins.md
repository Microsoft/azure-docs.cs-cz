---
title: Přidání instančního objektu do role správce služby Azure Analysis Services | Dokumenty společnosti Microsoft
description: Přečtěte si, jak přidat instanční objekt služby automatizace do role správce serveru Služby Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 1370f65405963ebf825e986e6801607a0d96156e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298084"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Přidání objektu zabezpečení služby do role správce serveru 

 Chcete-li automatizovat bezobslužné úlohy prostředí PowerShell, musí mít instanční objekt oprávnění **správce serveru** na spravovaném serveru Analysis Services. Tento článek popisuje, jak přidat instanční objekt do role správců serveru na serveru Azure AS. Můžete to provést pomocí SQL Server Management Studio nebo šablony Správce prostředků.

## <a name="before-you-begin"></a>Než začnete
Před dokončením tohoto úkolu musíte mít ve službě Azure Active Directory registrovaný objekt zabezpečení služby.

[Vytvoření instančního objektu – portál Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Vytvoření instančního objektu – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Používání aplikace SQL Server Management Studio

Správce serveru můžete konfigurovat pomocí aplikace SQL Server Management Studio (SSMS). K dokončení tohoto úkolu musíte mít oprávnění [správce serveru](analysis-services-server-admins.md) na serveru Azure AS. 

1. V SSMS se připojte k serveru Azure AS.
2. V**okně Zabezpečení** **vlastností** > serveru klepněte na tlačítko **Přidat**.
3. V **pole Vybrat uživatele nebo skupinu**vyhledejte registrovanou aplikaci podle názvu, vyberte a klikněte na **Přidat**.

    ![Hledat účet instančního objektu](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Ověřte ID účtu instančního objektu a klepněte na tlačítko **OK**.
    
    ![Hledat účet instančního objektu](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Použití šablony Správce prostředků

Správce serveru můžete také nakonfigurovat nasazením serveru Analysis Services pomocí šablony Azure Resource Manager. Identita spuštěná v nasazení musí patřit do role **přispěvatele** pro prostředek v [řízení přístupu na základě rolí Azure (RBAC).](../role-based-access-control/overview.md)

> [!IMPORTANT]
> Instanční objekt musí být `app:{service-principal-client-id}@{azure-ad-tenant-id}`přidán pomocí formátu .

Následující šablona Správce prostředků nasazuje server Služby analysis services se zadaným instančním objektem přidaným do role správce služby Analysis Services:

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

Spravovanou identitu lze také přidat do seznamu správci služby Analysis Services. Můžete mít například [aplikaci logiky se systémem přiřazenou spravovanou identitou](../logic-apps/create-managed-service-identity.md)a chcete jí udělit možnost spravovat server služby Analysis Services.

Ve většině částí portálu Azure a api jsou spravované identity identifikovány pomocí ID objektu instančního objektu. Analysis Services však vyžaduje, aby byly identifikovány pomocí jejich ID klienta. Chcete-li získat ID klienta pro instanční objekt, můžete použít azure CLI:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Případně můžete použít Prostředí PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Toto ID klienta pak můžete použít ve spojení s ID klienta k přidání spravované identity do seznamu Správci služby Analysis Services, jak je popsáno výše.

## <a name="related-information"></a>Související informace

* [Stažení modulu powershellu serveru SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Stáhnout SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


