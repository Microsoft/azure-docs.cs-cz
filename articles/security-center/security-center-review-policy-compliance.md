---
title: Kontrola dodržování zásad Security Center pomocí rozhraní REST API služby Azure | Dokumentace Microsoftu
description: Zjistěte, jak použít rozhraní Azure REST API ke kontrole aktuální dodržování zásad ve službě Security Center.
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 1443486590859aac5591aff2ab0551bed9228d7b
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301745"
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>Security Center kontroly dodržování zásad pomocí rozhraní REST API

Centrum zabezpečení pravidelně ověří proti zásady definované zabezpečení prostředků Azure. Security Center také poskytuje rozhraní REST API, která umožňuje zkontrolovat dodržování předpisů z vlastních aplikací; můžete přímo dotazování na službu nebo importovat výsledky JSON do jiných aplikací. 

Zde zjistíte, jak načíst aktuální sadu doporučení ze všech prostředků Azure, které jsou spojené s předplatným.

Načíst aktuální sadu doporučení:
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Žádost o sestavení  

`{subscription-id}` Parametr je vyžadován a musí obsahovat ID předplatného pro předplatné Azure, definování zásad. Pokud máte více předplatných, přečtěte si téma [práce s několika předplatnými](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions).  

`api-version` Parametr je povinný. V současné době se tyto koncové body podporují pouze pro `api-version=2015-06-01-preview`. 

Vyžadují se následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Typ obsahu:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Autorizace:*|Povinná hodnota. Nastaven na platné `Bearer` [přístupový token](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |  

## <a name="response"></a>Odpověď  

Pro úspěšné odpovědi, který obsahuje seznam doporučených úloh k zabezpečení vašich prostředků Azure se vrátí stavový kód 200 (OK).

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

Každá položka v **hodnotu** představuje doporučení:

|Vlastnost Response.|Popis|
|----------------|----------|
|**Stav** | Určuje, zda je doporučení `active` nebo `resolved`. |
|**CreationTimeUtc** | Datum a čas ve standardu UTC, zobrazuje, kdy byla vytvořena doporučení. |
|**lastStateChangeUtc** | Datum a čas ve UTV poslední změny stavu, pokud existuje. |
|**securityTaskParameters** | Podrobnosti doporučení; Vlastnosti lišit v závislosti základní doporučení. |
||
  
Aktuálně se podporují doporučení, najdete v tématu [implementace doporučení pro zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Jiné stavové kódy označují chybové stavy. V těchto případech objektu odpovědi obsahuje popis s vysvětlením, proč žádost selhala.

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

Tato odpověď značí dva doporučení; Každá položka v seznamu odpovídá konkrétní doporučení. První doporučuje šifrování úložiště na virtuální počítač s Linuxem a druhá navrhuje, abyste povolili auditování pro SQL server.

Doporučení se liší podle zásad, které jste povolili. Další informace, včetně doporučení pro aktuálně k dispozici, najdete v článku [Správa doporučení zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).


## <a name="see-also"></a>Další informace najdete v tématech  
- [Nastavení zásad zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [Azure rozhraní REST API poskytovatele prostředků zabezpečení](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Začínáme s Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
- [Modul PowerShell pro Azure Security Center](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22)
