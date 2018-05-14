---
title: Kontrola dodržování zásad Security Center pomocí rozhraní REST API Azure | Microsoft Docs
description: Naučte se používat rozhraní API REST Azure ke kontrole aktuální souladu se zásadami Security Center.
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 6c6764eec59633f0bdd0fa396c1581117a0c1e1d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>Security Center kontrola dodržování zásad pomocí rozhraní REST API

Security Center pravidelně ověří proti vaše zásady definované zabezpečení prostředků Azure. Security Center také poskytuje rozhraní REST API, která umožňuje zkontrolovat dodržování předpisů z vaše vlastní aplikace; můžete zadat dotaz na službu nebo výsledky JSON importovat do jiné aplikace. 

Zde dozvíte ze všech prostředků Azure, které jsou spojené s předplatným načíst aktuální sadu doporučení.

Načíst aktuální sadu doporučení:
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Vytvoření žádosti  

`{subscription-id}` Parametr je povinná a musí obsahovat ID předplatného pro předplatné Azure definováním zásad. Pokud máte více předplatných, najdete v části [práce s více předplatných](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions).  

`api-version` Parametr je povinný. V současné době jsou podporovány tyto koncové body pouze u `api-version=2015-06-01-preview`. 

Vyžadují se následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Autorizace:*|Povinná hodnota. Nastavte na platnou `Bearer` [přístupový token](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |  

## <a name="response"></a>Odpověď  

Stavovým kódem 200 (OK) se vrátí pro úspěšné odpovědi, který obsahuje seznam Doporučené úkoly k zabezpečení prostředků Azure.

``` json
{  
  "value": [  
    {  
       "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
       "name": "{task_id}",
       "type": "Microsoft.Security/locations/{region}/tasks",
       "properties": {
       "state": "Active",
       "subState": "NA",
       "creationTimeUtc": "{create-time}",
       "lastStateChangeTimeUtc": "{last-state-change}",
       "securityTaskParameters": "{security-task-properties}"
    } 
  ]  
}  
```  

Každá položka v **hodnota** představuje doporučení:

|Vlastnost Response.|Popis|
|----------------|----------|
|**Stav** | Určuje, zda je doporučení `active` nebo `resolved`. |
|**CreationTimeUtc** | Datum a čas ve standardu UTC, zobrazující vytvoření doporučení. |
|**lastStateChangeUtc** | Datum a čas v UTV poslední změny stavu, pokud existuje. |
|**securityTaskParameters** | Podrobnosti doporučení; Vlastnosti lišit podle základní doporučení. |
||
  
Aktuálně podporované doporučení, najdete v části [implementace doporučení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Další stavové kódy označují chybový stav. V těchto případech objekt odpovědi obsahuje popis vysvětlením, proč zpracování žádosti se nezdařilo.

``` json
{  
  "value": [  
    {  
      "description": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="example-response"></a>Příklad odpovědi  

``` json
{  
  "value": [  
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
            "name": "{task_id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "vmId": "/subscriptions/{subscription-id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "vmName": "{vm_name}",
                    "severity": "{severity}",
                    "isOsDiskEncrypted": {is_os_disk_encrypted},
                    "isDataDiskEncrypted": {is_data_disk_encrypted},
                    "name": "EncryptionOnVm",
                    "uniqueKey": "EncryptionOnVmTaskParameters_/subscriptions/{subscription-id}/resourceGroups/{resoource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "resourceId": "/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}"
                }
            }
        },
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{location}/tasks/{task-id}",
            "name": "{task-id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "serverName": "{sql-server-name}",
                    "serverId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}",
                    "name": "Enable auditing for the SQL server",
                    "uniqueKey": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}/auditingPolicies/Default",
                    "resourceId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}"
                }
            }
        }  ]  
}  
```  

Tato odezva ukazuje dva doporučení; Každá položka v seznamu odpovídá konkrétní doporučení. První doporučuje šifrování úložiště na virtuální počítač s Linuxem a druhý navrhne povolit auditování pro SQL server.

Doporučení se liší podle zásad, které jste povolili. Další informace, včetně aktuálně k dispozici doporučení, najdete v části [Správa doporučení zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).


## <a name="see-also"></a>Další informace najdete v tématech  
- [Nastavení zásad zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [Zprostředkovatel zabezpečení prostředků Azure REST API](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Začínáme s Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
- [Modul prostředí PowerShell Azure Security Center](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22)
